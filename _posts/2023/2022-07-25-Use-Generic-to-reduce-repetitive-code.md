---
layout: post
title: Use Generic to reduce repetitive code
category: Java
tags: [Generic, reduce repetitive code]
keywords: Generic,reduce repetitive code
excerpt: Use Generic to reduce repetitive code
---

## Use Generic classes

### Requirement

Define classes for below json.

The value of `objectType` is `Enum ObjectType`, `user/group/machine/org/token`.

The vaule of `changeType` is `Enum ChangeType`, `create/modify/delete`.

The value of `changedAttribute` is `String`.

```
"objectType": {
    "value": <ObjectType>,
    "operation": <Operation>
}

"changeType": {
    "value": <ChangeType>,
    "operation": <Operation>
}

"changedAttribute": {
    "value": <String>,
    "operation": <Operation>
}
```

### Without using Generic classes

```
public class ObjectTypeValueOperation {

    private final ObjectType value;
    private final Operation operation;

    private ObjectTypeValueOperation(final Builder builder) {
        this.value = builder.value;
        this.operation = builder.operation;
    }

    public static Builder builder() {
        return new Builder();
    }


    ...
}


public class ChangeTypeValueOperation {

    private final ChangeType value;
    private final Operation operation;

    private ChangeTypeValueOperation(final Builder builder) {
        this.value = builder.value;
        this.operation = builder.operation;
    }

    public static Builder builder() {
        return new Builder();
    }


    ...
}


public class StringValueOperation {

    private final String value;
    private final Operation operation;

    private StringValueOperation(final Builder builder) {
        this.value = builder.value;
        this.operation = builder.operation;
    }

    public static Builder builder() {
        return new Builder();
    }


    ...
}
```

### With using Generic classes

```
public class ValueOperation<T> {

    private final T value;
    private final Operation operation;

    private ValueOperation(final Builder<T> builder) {
        this.value = builder.value;
        this.operation = builder.operation;
    }

    public static <T> Builder<T> builder() {
        return new Builder<>();
    }


    ...
}
```

## Use Generic methods

### Requirement

Partition deleted value list or not for below json.

For `changeTypes`, the `deleted` value list is [`delete`], the `not deleted` value list is [`create`].

For `changedAttributesis`, the `deleted` value list [`licenseID`], the `not deleted` value list is [`adminTrainSiteNames`, `trainSiteNames`].

```
"changeTypes": [
    {
        "value": "create"
    },
    {
        "value": "delete",
        "operation": "delete"
    }
]

"changedAttributes": [
    {
        "value": "licenseID",
        "operation": "delete"
    },
    {
        "value": "adminTrainSiteNames"
    },
    {
        "value": "trainSiteNames"
    }
]
```

### Without using Generic methods

```
public static void partitionChangeTypeValues(List<ValueOperation<ChangeType>> changeTypeValueOperations) {
    fianl Map<Boolean, Set<ChangeType>> changeTypeValueOperationMap = changeTypeValueOperations.stream()
            .collect(Collectors.partitioningBy(valueOperation -> Operation.delete == valueOperation.getOperation(),
                    Collectors.mapping(ValueOperation::getValue, Collectors.toSet())));

    Set<ChangeType> deletedValues = changeTypeValueOperationMap.get(true);
    deletedValues = Optional.ofNullable(deletedValues).orElse(Collections.emptySet());

    Set<ChangeType> notDeletedValues = changeTypeValueOperationMap.get(false);
    notDeletedValues = Optional.ofNullable(notDeletedValues).orElse(Collections.emptySet());
}


public static void partitionChangedAttributeValues(List<ValueOperation<String>> changedAttributeValueOperations) {
    final Map<Boolean, Set<String>> changedAttributeValueOperationMap = changedAttributeValueOperations.stream()
            .collect(Collectors.partitioningBy(valueOperation -> Operation.delete == valueOperation.getOperation(),
                    Collectors.mapping(ValueOperation::getValue, Collectors.toSet())));

    Set<String> deletedValues = changedAttributeValueOperationMap.get(true);
    deletedValues = Optional.ofNullable(deletedValues).orElse(Collections.emptySet());

    Set<String> notDeletedValues =changedAttributeValueOperationMap.get(false);
    notDeletedValues = Optional.ofNullable(notDeletedValues).orElse(Collections.emptySet());
}
```

### With using Generic methods

```
public static <T> void partitionChangeTypeValues(List<ValueOperation<T>> valueOperations) {
    final Map<? extends Boolean, ? extends Set<? extends T>> valueOperationMap = valueOperations.stream()
            .collect(Collectors.partitioningBy(valueOperation -> Operation.delete == valueOperation.getOperation(),
                    Collectors.mapping(ValueOperation::getValue, Collectors.toSet())));

    Set<? extends T> deletedValues = valueOperationMap.get(true);
    deletedValues = Optional.ofNullable(deletedValues).orElse(Collections.emptySet());

    Set<? extends T> notDeletedValues = valueOperationMap.get(false);
    notDeletedValues = Optional.ofNullable(notDeletedValues).orElse(Collections.emptySet());
}
```
