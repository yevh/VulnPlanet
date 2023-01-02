# Front-Running

Smart contract below is vulnureable to Front Running (Race condition)

```
contract Exchange {
  mapping (address => uint) public balances;

  function buyToken(uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[this] += _amount;
  }

  function sellToken(uint _amount) public {
    require(balances[this] >= _amount);
    balances[this] -= _amount;
    balances[msg.sender] += _amount;
  }
}
```

# Why it's vulnerable?
This contract is a simple token exchange, where users can buy and sell tokens using their Ethereum balance. However, it is vulnerable to a front running (race condition) vulnerability because the buyToken() and sellToken() functions do not check if the caller's balance has changed between the time the transaction is sent and the time it is executed.

# Impact?
To exploit this vulnerability, an attacker could first call the buyToken() function, transferring some funds to the contract. They could then quickly call the sellToken() function, transferring the funds back to themselves before the buyToken() transaction is executed. This would allow the attacker to profit from the price difference between the two transactions.

# How to fix?
To prevent this vulnerability, the contract should be updated to check the caller's balance before executing a transaction. Here is an updated version of the contract that checks the caller's balance before executing the buyToken() and sellToken() functions:

```
contract Exchange {
  mapping (address => uint) public balances;

  function buyToken(uint _amount) public {
    require(balances[msg.sender] >= _amount);
    require(balances[msg.sender] == this.balanceOf(msg.sender));
    balances[msg.sender] -= _amount;
    balances[this] += _amount;
  }

  function sellToken(uint _amount) public {
    require(balances[this] >= _amount);
    require(balances[msg.sender] == this.balanceOf(msg.sender));
    balances[this] -= _amount;
    balances[msg.sender] += _amount;
  }
}
```

In this updated version of the contract, the buyToken() and sellToken() functions check the caller's balance using the this.balanceOf() function before executing the transaction. This ensures that the transaction is executed only if the caller's balance has not changed, which prevents the front running (race condition) vulnerability.
