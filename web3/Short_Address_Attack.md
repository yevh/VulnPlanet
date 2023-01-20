# Short Address Attack

Smart contract below is vulnureable to Short Address Attack (Off-chain issues)

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

# Why it's vulnerable?
This contract allows users to transfer funds to each other. However, it is vulnerable to a short address attack (off-chain issues) because it does not check the length of the ```_to address```, which is passed as a parameter to the ```transfer()``` function.

# Impact?
If the _to address is shorter than 20 bytes, it will be treated as a zero-padded address, which could cause the funds to be transferred to an unintended recipient.

# How to fix?
To prevent this vulnerability, the contract should be updated to check the length of the ```_to address``` before executing the ```transfer()``` function. Here is an updated version of the contract that checks the length of the ```_to address```:

```solidity
contract FundTransfer {
  mapping (address => uint) public balances;

  function transfer(address _to, uint _amount) public {
    require(bytes(_to).length == 20);
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] += _amount;
  }
}
```

In this updated version of the contract, the ```transfer()``` function uses the ```bytes()``` built-in function to convert the _to address to a bytes data type, and then checks the length of the resulting bytes value. If the length is not equal to 20, the transaction is reverted and the funds are not transferred. This prevents the short address attack (off-chain issues) vulnerability and ensures that the funds are transferred only to valid, 20-byte addresses.
