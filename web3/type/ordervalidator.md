# Selling more tokens in OrderValidator

Smart contract below is vulnureable to Selling more tokens in OrderValidator

```solidity
uint120 totalSize = (uint120(order.size) * uint120(fill.fraction)) / uint120(fill.denominator);
uint120 totalFilled = (uint120(order.filled) * uint120(fill.denominator)) / uint120(fill.fraction);
if (totalSize == totalFilled) {
    emit OrderFilled(order.hash);
    delete orders[order.hash];
} else {
    order.filled = totalFilled;
    orders[order.hash] = order;
}
```

# Why it's vulnerable?

A truncation vulnerability in OrderValidator contract can allow an attacker to reset a partial order's fractions to 0, allowing them to sell more tokens than intended. This vulnerability can be exploited by an attacker to drain a user's approved tokens. The vulnerability affects OrderValidator.sol code on lines 228, 231, 237, and 238.

# Impact?
The vulnerability can allow an attacker to sell more tokens than intended and drain a user's approved tokens, leading to potential financial losses. This can be particularly harmful during a restricted token sale, where allocations intended for investors and the team may no longer be available.

# How to fix?

To fix the vulnerability is to add additional checks to ensure that the numerator and denominator values of the ```fill``` fraction are within the range of ```uint120```. Specifically, the following lines of code should be added to the ```validateAndStore``` function. These lines ensure that the sum of the ```filledNumerator``` and ```numerator``` values of the ```fill``` fraction, as well as the ```denominator``` value, are within the range of ```uint120```.

```solidity

```

Similarly, the following lines should be added to the```validateAndStore``` function when handling new orders. These lines ensure that the ```numerator``` and ```denominator``` values of the order are within the range of ```uint120```.

```solidity
require(numerator <= type(uint120).max, "overflow");
require(denominator <= type(uint120).max, "overflow");
```
