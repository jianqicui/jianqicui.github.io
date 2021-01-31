---
layout: post
title: 减少 if/else 的三种方式
category: Java
tags: [if, else]
keywords: if,else
excerpt: 减少 if/else 的三种方式
---

## 技巧

### 使用卫语句

**使用卫语句前**

```
public double getPayAmount() {
    double result;

    if (isDead()) {
        result = deadAmount();
    } else {
        if (isSeparated()) {
            result = separatedAmount();
        } else {
            if (isRetired()) {
                result = retiredAmount();
            } else{
                result = normalPayAmount();
            }
        }
    }

    return result;
}
```

**使用卫语句后**

```
public double getPayAmount() {
    if (isDead()) {
        return deadAmount();
    }

    if (isSeparated()) {
        return separatedAmount();
    }

    if (isRetired()) {
        return retiredAmount();
    }

    return normalPayAmount();
}
```

### 使用 Optional

**使用 Optional 前**

```
if (user != null) {
    Address address = user.getAddress();

    if (address != null) {
        Country country = address.getCountry();

        if (country != null) {
            String isocode = country.getIsocode();

            if (isocode != null) {
                isocode = isocode.toUpperCase();
            }
        }
    }
}
```

**使用 Optional 后**
 前
```
String result = Optional.ofNullable(user)
    .flatMap(User::getAddress)
    .flatMap(Address::getCountry)
    .map(Country::getIsocode)
    .orElse("default");
```

## 使用策略模式

### 使用策略模式前

```
public Double calculateRecharge(RechargeType rechargeType, Double charge) {
    if (RechargeType.E_BANK.equals(rechargeType)) {
        return charge * 0.85;
    } else if (RechargeType.BUSINESS_ACCOUNT.equals(rechargeType)) {
        return charge * 0.90;
    } else if (RechargeType.MOBILE.equals(rechargeType)) {
        return charge;
    } else if (RechargeType.CARD.equals(rechargeType)) {
        return charge + charge * 0.01;
    } else {
        return null;
    }
}
```

### 使用策略模式后

```
Map<RechargeType, Function<Double, Double>> RECHARGE_MAP;

static {
    RECHARGE_MAP = new HashMap<>();

    RECHARGE_MAP.put(RechargeType.E_BANK, charge -> (charge * 0.85));
    RECHARGE_MAP.put(RechargeType.BUSINESS_ACCOUNT, charge -> (charge * 0.90));
    RECHARGE_MAP.put(RechargeType.MOBILE, charge -> charge);
    RECHARGE_MAP.put(RechargeType.CARD, charge -> (charge + charge * 0.01));

    RECHARGE_MAP = Collections.unmodifiableMap(RECHARGE_MAP);
}

public Double calculateRecharge(RechargeType rechargeType, Double charge) {
    final Function<Double, Double> function = RECHARGE_MAP.get(rechargeType);

    return Optional.ofNullable(function).map(f -> f.apply(charge)).orElse(null);
}
```

## 总结

* 假如`快速返回`可以在方法中使用，`卫语句`很合适。

* 假如 `null 检查`在方法中使用，`Optional` 很合适。

* 假如`相同的参数对应不同的逻辑`，`策略模式`很合适。
