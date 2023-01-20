# Broken Access Control

Smart contract below is vulnureable to Broken Access Control

```solidity
contract WalletLibrary {
  address public owner;
  mapping (address => uint) public balances;

  function WalletLibrary() public {
    owner = msg.sender;
  }

  function initWallet(address _owner) public {
    require(msg.sender == owner);
    Wallet wallet = new Wallet(_owner);
  }

  function transfer(address _to, uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] += _amount;
  }
}

contract Wallet {
  address public owner;
  mapping (address => uint) public balances;

  function Wallet(address _owner) public {
    owner = _owner;
  }

  function transfer(address _to, uint _amount) public {
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] += _amount;
  }
}
```

# Why it's vulnerable?
In this contract, the ```WalletLibrary``` contract is intended to be used as a library to create new Wallet contracts. The ```initWallet()``` function is only supposed to be called by the contract's owner, and it is used to create a new Wallet contract with the specified owner. However, this contract is vulnerable to an access control vulnerability because the ```initWallet()``` function does not properly check the caller's permissions before creating a new Wallet contract.

# Impact?
An attacker could exploit this vulnerability by calling the ```initWallet()``` function with themselves as the owner, allowing them to create a new Wallet contract that they own and control.

# How to fix?
To prevent this vulnerability, the ```initWallet()``` function should be updated to properly check the caller's permissions before allowing them to create a new ```Wallet``` contract. For example, the function could be updated to only allow the contract's owner to create new Wallet contracts, as shown in the following example:

```solidity
function initWallet(address _owner) public {
  require(msg.sender == owner);
  Wallet wallet = new Wallet(_owner);
}
```

In this updated version of the contract, only the contract's owner will be able to create new Wallet contracts, preventing other users from exploiting the vulnerability.
