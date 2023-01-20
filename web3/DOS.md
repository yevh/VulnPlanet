# Denial of Service

Smart contract below is vulnureable to Denial of Service (Gas limit reached)

```solidity
contract FundTransfer {
  mapping (address => uint) public balances;

  function transfer(address _to, uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] += _amount;

    // perform expensive computations
    for (uint i = 0; i < 10**6; i++) {
      // do something expensive
    }
  }
}
```

# Why it's vulnerable?
This contract allows users to transfer funds to each other. However, it is vulnerable to a DoS attack because the ```transfer()``` function performs a large number of expensive computations, which can cause the gas limit to be reached.

# Impact?
If the gas limit is reached, the transaction will fail and the funds will not be transferred.

# How to fix?
To prevent this vulnerability, the ```transfer()``` function should be updated to avoid performing expensive computations that could cause the gas limit to be reached. Here is an updated version of the contract that avoids expensive computations:

```solidity
contract FundTransfer {
  mapping (address => uint) public balances;

  function transfer(address _to, uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] += _amount;
  }
}
```

In this updated version of the contract, the ```transfer()``` function does not perform any expensive computations, which means that it will not cause the gas limit to be reached. This prevents the contract from being vulnerable to a DoS attack.
