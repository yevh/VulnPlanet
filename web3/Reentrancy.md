# Reentrancy

Smart contract below is vulnureable to Reentrancy (Race to empty)

```
contract FundTransfer {
  address public owner;
  mapping (address => uint) public balances;

  function FundTransfer() public {
    owner = msg.sender;
  }

  function transfer(address _to, uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] += _amount;
  }

  function withdraw() public {
    uint amount = balances[msg.sender];
    balances[msg.sender] = 0;
    msg.sender.transfer(amount);
  }
}
```

# Why it's vulnerable?
This contract allows users to transfer funds to each other, and also allows them to withdraw their funds. However, the withdraw() function is vulnerable to a reentrancy attack.

# Impact?
An attacker could exploit this vulnerability as follows:

1. The attacker creates a malicious contract that calls the transfer() function of the FundTransfer contract, transferring funds from the attacker's account to the malicious contract's account.
2. The attacker then calls the withdraw() function on the FundTransfer contract, which will transfer all of the attacker's funds to the attacker's external address.
3. Since the withdraw() function does not check if the caller is a contract or an external address, the malicious contract is able to receive the funds that were just transferred to it.
4. The malicious contract then calls the withdraw() function again, repeating the process and allowing the attacker to repeatedly withdraw funds from the FundTransfer contract.

# How to fix?
To prevent this vulnerability, the withdraw() function should be updated to check if the caller is a contract, and if so, it should prevent the contract from calling the withdraw() function again. This can be done using the require() function, as shown in the following example:

```
function withdraw() public {
  require(!msg.sender.isContract());
  uint amount = balances[msg.sender];
  balances[msg.sender] = 0;
  msg.sender.transfer(amount);
}
```

In this updated version of the contract, the withdraw() function will only allow external addresses to withdraw funds. If a contract attempts to call the withdraw() function, the require() statement will cause the transaction to revert and the contract will not be able to withdraw funds.
