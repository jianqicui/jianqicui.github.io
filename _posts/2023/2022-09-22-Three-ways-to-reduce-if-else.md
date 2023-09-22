---
layout: post
title: Three ways to reduce if/else
category: Java
tags: [reduce if/else]
keywords: reduce if/else
excerpt: Three ways to reduce if/else
---

## Techniques

### Use Guard Clause

**Before using Guard Clause**

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

**After using Guard Clause**

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

### Use Optional

**Before using Optional**

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

**After using Optional**

```
String result = Optional.ofNullable(user)
    .flatMap(User::getAddress)
    .flatMap(Address::getCountry)
    .map(Country::getIsocode)
    .orElse("default");
```

### Use Strategy Pattern

**Before using Strategy Pattern**

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

**After using Strategy Pattern**

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

## Conclusion

* If `quick return` can be used in a method, `Guard Clause` is suitable.
* If there is `null check` in a method, `Optional` is suitable.
* If there is `different logic` for the `same parameters`, `Strategy Pattern` is suitable.
