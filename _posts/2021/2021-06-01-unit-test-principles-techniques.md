---
layout: post
title: 单元测试的原则和技巧
category: Unit Test
tags: [单元测试]
keywords: 单元测试
excerpt: 单元测试的原则和技巧
---

## 变不可测试的方法为可测试的方法

### 变 private 方法为 default 方法

**Business code before change**

```
private void cleanupConsumerGroups(List<MessageListenerContainer> messageListenerContainers) {
    Set<String> consumerGroupIds = messageListenerContainers.stream()
            .map(MessageListenerContainer::getContainerProperties).map(ContainerProperties::getGroupId)
            .collect(Collectors.toSet());

    cleanupConsumerGroupIds(consumerGroupIds);
}
```

**Business code after change**

```
void cleanupConsumerGroups(List<MessageListenerContainer> messageListenerContainers) {
    Set<String> consumerGroupIds = messageListenerContainers.stream()
            .map(MessageListenerContainer::getContainerProperties).map(ContainerProperties::getGroupId)
            .collect(Collectors.toSet());

    cleanupConsumerGroupIds(consumerGroupIds);
}
```

**Test code**

```
@Test
public void testCleanupConsumerGroups() {
    ...
}
```

### 提取内部调用代码为 default 方法

**Business code before change**

```
ConcurrentMessageListenerContainer<String, String> startContainer(SubscriptionEntryBean subscription,
                                                                              String callback,
                                                                              String topicName,
                                                                              DefaultKafkaConsumerFactory<String, String> tokenConsumerFactory,
                                                                              Map<String, Object> tokenConsumerProperties,
                                                                              boolean isLocal,
                                                                              SubCommitData subCommitData) {
    ContainerProperties containerProps = new ContainerProperties(topicName);
    containerProps.setAckMode(AckMode.MANUAL);
    containerProps.setMessageListener(
            generateListener(subscription.getDn(), callback, topicName, isLocal, subCommitData));
    containerProps.setGroupId(TOKEN_PREFIX + subscription.getId());
    containerProps.setClientId(hostname);
    ConcurrentMessageListenerContainer<String, String> container
            = new ConcurrentMessageListenerContainer<String, String>(tokenConsumerFactory, containerProps);
    container.setConcurrency(concurrency);
    container.start();
    return container;
}
```

**Business code after change**

```
ConcurrentMessageListenerContainer<String, String> startContainer(SubscriptionEntryBean subscription,
                                                                              String callback,
                                                                              String topicName,
                                                                              DefaultKafkaConsumerFactory<String, String> tokenConsumerFactory,
                                                                              Map<String, Object> tokenConsumerProperties,
                                                                              boolean isLocal,
                                                                              SubCommitData subCommitData) {
    ContainerProperties containerProps = new ContainerProperties(topicName);
    containerProps.setAckMode(AckMode.MANUAL);
    containerProps.setMessageListener(
            generateListener(subscription.getDn(), callback, topicName, isLocal, subCommitData));
    containerProps.setGroupId(TOKEN_PREFIX + subscription.getId());
    containerProps.setClientId(hostname);
    ConcurrentMessageListenerContainer<String, String> container = getContainer(tokenConsumerFactory, containerProps);
    container.setConcurrency(concurrency);
    container.start();
    return container;
}

ConcurrentMessageListenerContainer<String, String> getContainer(DefaultKafkaConsumerFactory<String, String> consumerFactory,
                                                                ContainerProperties containerProps) {
    return new ConcurrentMessageListenerContainer<>(consumerFactory, containerProps);
}
```

**Test code**

```

@Test
public void testStartContainer() {
    TokenSubscriptionStateKeeper tokenSubscriptionStateKeeper = new TokenSubscriptionStateKeeper(
        ChangelogType.ORACLE.name(),
        subscriptionStore,
        orSubscriptionStore
    );

    spiedSubscriptionStateKeeper = spy(tokenSubscriptionStateKeeper);

    container = Mockito.mock(ConcurrentMessageListenerContainer.class);

    doReturn(container).when(spiedSubscriptionStateKeeper).getContainer(any(),any());

    doNothing().when(container).start();
}
```

## 变不容易测试的方法为容易测试的方法

### 变方法为 static 假如没有类变量使用

**Business code before change**

```
public KFSyncOperationMessageListener(ApplicationContext applicationContext,
                                      OAuthTokenFactory tokenFactory,
                                      SubscriptionEntryBean subscription,
                                      SubscriptionConfig subscriptionConfig,
                                      boolean isOracle ,
                                      String destKeyStoreLoc,
                                      String destKeyStorePwd,
                                      String destKeyPwd,
                                      SubCommitData subCommitData) {
    ...
}

String reportLag(long offset, long endOffset) {
    long lag = endOffset - offset;

    Range<Long> range = RANGE_LAG_MAP.keySet().stream().filter(r -> r.contains(lag)).findFirst().get();

    return RANGE_LAG_MAP.get(range);
}
```

**Test code before change**

```
@Test
public void testReportLag() {
    KFSyncOperationMessageListener syncOperationMessageListener = new KFSyncOperationMessageListener(...);

    syncOperationMessageListener.reportLag(...);


    ...
}
```

**Business code after change**

```
static String reportLag(long offset, long endOffset) {
    long lag = endOffset - offset;

    Range<Long> range = RANGE_LAG_MAP.keySet().stream().filter(r -> r.contains(lag)).findFirst().get();

    return RANGE_LAG_MAP.get(range);
}
```

**Test code after change**

```
@Test
public void testReportLag() {  
    KFSyncOperationMessageListener.reportLag(...);

    ...
}
```

## 测试方法本身

### Mock 内部直接依赖

**Business code**

```
public void onMessage(ConsumerRecord<String, String> consumerRecord, Acknowledgment ack, Consumer<?, ?> consumer) {
    String msgCSN = StringUtils.EMPTY;
    try {
        final long start = System.currentTimeMillis();

        long endOffset = getEndOffset(consumerRecord, consumer);
        msgCSN = handleMessage(consumerRecord, ack, consumer, start, endOffset);

        final long end = System.currentTimeMillis();
        final long duration = end - start;
        recordOnMessageDuration(duration);
    } catch (Exception e) {
        LOGGER.error("EXP-onMessage, subDN={}, isOracle={}, msgCSN={}", getSubscriptionDN(), isOracleBased, msgCSN,
                e);
    }
}
```

**Test code**

```
@Test
public void testOnMessage() throws Exception {
    ApplicationContext applicationContext = buildMockedApplicationContext(mock(DroppedMessagesHandler.class), 0L);
    SubscriptionEntryBean subscriptionEntryBean = buildTrainSubscription();
    KFSyncOperationMessageListener listener = buildKFSyncOperationMessageListener(applicationContext,
            subscriptionEntryBean, messageCache);

    ConsumerRecord<String, String> consumerRecord = mock(ConsumerRecord.class);
    Acknowledgment ack = mock(Acknowledgment.class);
    Consumer<?, ?> consumer = mock(Consumer.class);

    long endOffset = 10001L;
    String msgCSN = "csn";

    // Successful case
    KFSyncOperationMessageListener spyListener = spy(listener);

    try (MockedStatic<KFSyncOperationMessageListener> mockedStaticListener = Mockito
            .mockStatic(KFSyncOperationMessageListener.class)) {
        mockedStaticListener.when(() -> KFSyncOperationMessageListener.getEndOffset(consumerRecord, consumer))
                .thenReturn(endOffset);
        doReturn(msgCSN).when(spyListener).handleMessage(eq(consumerRecord), eq(ack), eq(consumer), anyLong(),
                eq(endOffset));

        spyListener.onMessage(consumerRecord, ack, consumer);

        verify(spyListener).handleMessage(eq(consumerRecord), eq(ack), eq(consumer), anyLong(), eq(endOffset));
        mockedStaticListener.verify(() -> KFSyncOperationMessageListener.getEndOffset(consumerRecord, consumer));
    }

    // Unsuccessful case
    spyListener = spy(listener);

    try (MockedStatic<KFSyncOperationMessageListener> mockedStaticListener = Mockito
            .mockStatic(KFSyncOperationMessageListener.class)) {
        mockedStaticListener.when(() -> KFSyncOperationMessageListener.getEndOffset(consumerRecord, consumer))
                .thenReturn(endOffset);
        doThrow(new RuntimeException()).when(spyListener).handleMessage(eq(consumerRecord), eq(ack), eq(consumer),
                anyLong(), eq(endOffset));

        spyListener.onMessage(consumerRecord, ack, consumer);

        verify(spyListener).handleMessage(eq(consumerRecord), eq(ack), eq(consumer), anyLong(), eq(endOffset));
        mockedStaticListener.verify(() -> KFSyncOperationMessageListener.getEndOffset(consumerRecord, consumer));
    }
}
```

### Mock 外部直接依赖

**Business code**

```
void cleanupConsumerGroups(List<MessageListenerContainer> messageListenerContainers) {
    Set<String> consumerGroupIds = messageListenerContainers.stream()
            .map(MessageListenerContainer::getContainerProperties).map(ContainerProperties::getGroupId)
            .collect(Collectors.toSet());

    adminClient.deleteConsumerGroups(consumerGroupIds);
}
```

**Test code**

```
@Test
public void testCleanupConsumerGroups() {
    ContainerProperties containerProperties1 = Mockito.mock(ContainerProperties.class);
    doReturn("groupId1").when(containerProperties1).getGroupId();
    MessageListenerContainer messageListenerContainer1 = Mockito.mock(MessageListenerContainer.class);
    doReturn(containerProperties1).when(messageListenerContainer1).getContainerProperties();

    ContainerProperties containerProperties2 = Mockito.mock(ContainerProperties.class);
    doReturn("groupId2").when(containerProperties2).getGroupId();
    MessageListenerContainer messageListenerContainer2 = Mockito.mock(MessageListenerContainer.class);
    doReturn(containerProperties2).when(messageListenerContainer2).getContainerProperties();

    List<MessageListenerContainer> messageListenerContainers = Arrays.asList(messageListenerContainer1,
            messageListenerContainer2);

    doNothing().when(adminClient)
            .deleteConsumerGroups(ImmutableSet.of("groupId1", "groupId2"));

    subscriptionKeeper.cleanupConsumerGroups(messageListenerContainers);

    verify(adminClient)
            .deleteConsumerGroups(ImmutableSet.of("groupId1", "groupId2"));

    verify(messageListenerContainer2).getContainerProperties();
    verify(messageListenerContainer1).getContainerProperties();
}
```

## 做细粒度的验证

### 校验结果的细节

**Test code with bad smell**

```
String changeLogWithoutManagedByFile = "/json/org/cOrg-create-with-managedBy-org-1.json";
changeDetailMessage = ChangelogOperationUtils
        .toChangeDetailMessage(TestResourceReader.loadResourceAsString(changeLogWithoutManagedByFile));
retMsgs = orgProcessor.generateMessages(changeDetailMessage);
Assert.assertNull(retMsgs);
```

**Test code with good smell**

```
String changeLogDelFile = "/json/org/cOrg-del-with-managedBy-1.json";
changeDetailMessage = ChangelogOperationUtils
        .toChangeDetailMessage(TestResourceReader.loadResourceAsString(changeLogDelFile));
retMsgs = orgProcessor.generateMessages(changeDetailMessage);
logger.info("changeLogDelFile Messages = {}", Arrays.toString(retMsgs));
Assert.assertEquals(2, retMsgs.length);
expectedRetMsgs = TestResourceReader.loadResourceAsString("/json/org/cOrg-del-with-managedBy-user-org-1-result.txt");
Assert.assertEquals(expectedRetMsgs, retMsgs[0] + " " + retMsgs[1]);
```

### 让 Mockito.verify 的次数 不小于 Mockito.when 的次数

**Test code**

```
ApplicationContext applicationContext = buildMockedApplicationContext();
SubscriptionEntryBean subscriptionEntryBean = buildTrainSubscription();
KFSyncOperationMessageListener listener = buildKFSyncOperationMessageListener(applicationContext,
        subscriptionEntryBean, messageCache);

ConsumerRecord<String, String> consumerRecord = mock(ConsumerRecord.class);
Acknowledgment ack = mock(Acknowledgment.class);
Consumer<?, ?> consumer = mock(Consumer.class);

ConsumerDataBean consumerDataBean = new ConsumerDataBean();
consumerDataBean.setCompressed(true);

KFSyncOperationMessageListener spyListener = spy(listener);

try (MockedStatic<KFSyncOperationMessageListener> mockedStaticListener = Mockito
        .mockStatic(KFSyncOperationMessageListener.class)) {
    mockedStaticListener.when(() -> KFSyncOperationMessageListener.getChangelogData(consumerRecord))
            .thenReturn(consumerDataBean);
    doReturn(true).when(spyListener).isInterestedChangedAttrsIncluded(consumerDataBean);
    mockedStaticListener
            .when(() -> KFSyncOperationMessageListener.decompressBase64(any(), eq(consumerDataBean)))
            .thenReturn(null);

    String csn = spyListener.handleMessage(consumerRecord, ack, consumer, Instant.now().toEpochMilli(), 10001L);
    Assert.assertEquals(StringUtils.EMPTY, csn);

    mockedStaticListener
            .verify(() -> KFSyncOperationMessageListener.decompressBase64(any(), eq(consumerDataBean)));
    verify(spyListener).isInterestedChangedAttrsIncluded(consumerDataBean);
    mockedStaticListener.verify(() -> KFSyncOperationMessageListener.getChangelogData(consumerRecord));
}
```
