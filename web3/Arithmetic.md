# Arithmetic Issues

Smart contract below is vulnureable to Arithmetic Issues (Integer overflow)

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
This contract allows users to transfer funds to each other. However, it is vulnerable to an integer overflow because the balances mapping uses a uint data type, which has a maximum value of 2^256 - 1.

# Impact?
If a user's balance exceeds this maximum value, and they try to transfer more funds, the balance will overflow and become a very small number, allowing the attacker to steal funds from the contract.

# How to fix?
To prevent this vulnerability, the balances mapping should be updated to use a data type that can support larger values, such as uint256. Here is an updated version of the contract that uses a uint256 data type:

```solidity
contract FundTransfer {
  mapping (address => uint256) public balances;

  function transfer(address _to, uint256 _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] += _amount;
  }
}
```

In this updated version of the contract, the balances mapping uses a uint256 data type, which can support values up to 2^256 - 1. This prevents the integer overflow vulnerability, and ensures that the contract can accurately track and transfer large amounts of funds.
