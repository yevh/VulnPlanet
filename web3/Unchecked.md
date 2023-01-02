# Silent failing sends

Smart contract below is vulnureable to Silent failing sends (Unchecked Return Values For Low Level Calls)

```solidity
contract FundTransfer {
  mapping (address => uint) public balances;

  function transfer(address _to, uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    _to.transfer(_amount);
  }
}
```

# Why it's vulnerable?
This contract allows users to transfer funds to each other. However, it is vulnerable to a silent failing sends vulnerability because the transfer() function uses the transfer() function of the address type to send funds to the recipient. 

# Impact?
This function will fail silently if the recipient's contract does not have a payable fallback function, which means that the funds will be lost and the contract's balance will be incorrect.

# How to fix?
To prevent this vulnerability, the transfer() function should be updated to use the send() function instead of the transfer() function. The send() function will return a boolean value indicating whether the transfer was successful, which allows the contract to handle failed transfers properly. Here is an updated version of the contract that uses the send() function:

```solidity
contract FundTransfer {
  mapping (address => uint) public balances;

  function transfer(address _to, uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    if (!_to.send(_amount)) {
      // handle failed transfer
    }
  }
}
```

In this updated version of the contract, the transfer() function uses the send() function to send funds to the recipient. If the transfer fails, the contract can handle the failure properly, for example by reverting the transaction or by returning the funds to the sender. This prevents the silent failing sends vulnerability and ensures that the contract's balances are accurate.
