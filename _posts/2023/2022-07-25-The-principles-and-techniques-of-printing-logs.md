---
layout: post
title: The principles and techniques of printing logs
category: Java
tags: [principles, techniques, printing logs]
keywords: principles,techniques,printing logs
excerpt: The principles and techniques of printing logs
---

## Where to print logs

### Use INFO logs to reply upstream or downstream caller

* `INFO` logs represent main cases.
* If more `INFO` logs printed, the volume of logs will be increased rapidly.
* But in the entrance and exit of component, `INFO` logs should be required to reply the question from upstream or downstream caller, for the running status.

**Component entrance log**

```
LOGGER.info("Kafka-OnMessage isOracleBased={}, subscriptionID={}, " +
        ", objectType={} " +
        ", key={}, csn={}, consumer-partition={}, consumer-offset={} " +
        ", topic-endOffset={} " + " onMsgTime={}. decompressedTime={}" ,
        isOracleBased,
        subscriptionId,
        objType,
        consumerRecord.key(),
        msgCSN,
        consumerRecord.partition(),
        consumerRecord.offset(),
        endOffset,
        startTime,
        (System.currentTimeMillis() - startTime));
```

**Component exit log**

```
LOGGER.info("DONE-Process-CSN =" + msgCSN + ", subDN=" + getSubscriptionDN() + ", duration=" + duration +
        ", isOracle=" + isOracleBased + ", partition=" + consumerRecord.partition() +
        ", comittedOffset=" + consumerRecord.offset() +
        ", partitionEndOffset=" + endOffset);
```

### Use WARN logs to handle normal exception cases

* The opposite of main cases are exception cases. E.g, in Push service, message consumed from Kafka, then sent out, which called main case, the rest of cases are all exception cases.
* The exception cases occurred, but `nothing can be done`, which called `normal exception` cases, `WARN` can be used. E.g, message is filtered out for changed attributes not interested.

```
LOGGER.warn("Oracle changedAttrs not interested, msgCSN: {}, changedAttrs: {}", msgCSN, changedAttrList);
```

### Use ERROR logs to handle abnormal exception cases

* The exception cases occurred, but `something can be done to fix it`, which called `abnormal exception` cases, `ERROR` can be used. E.g, Kafka connection failed.

```
LOGGER.error("IDFY_KAFKA_SSL_ERROR : SSL Exception on delivering to '{}' Sub= {} ,"+ "CSN= {} '{}' - {}",
        endpoint,
        subscriptionIdentifier,
        csn,
        LogUtils.string2LogString(message), t.getMessage());
```

## How to print logs

### Must print business words

**Bad smell**

```
private void set(String key, byte[] value, long expiryMills) {
    if (proxy != null) {
        try {
            proxy.set(key, value, expiryMills);
        } catch (RedisException e) {
            LOGGER.error(e.getMessage(), e);
        }
    }
}
```

### Better to print unique words

**Good smell**

```
LOGGER.error("Cannot generate pusher for {} - {}", objectType, subscriptionDN);
```

**Bad smell**

```
LOGGER.error("missing objectType in subscriptionBody");
```

### Better to use "{}" to print logs
