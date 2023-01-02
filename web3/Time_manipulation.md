# Time manipulation

Smart contract below is vulnureable to Time manipulation (Timestamp dependence)

```solidity
contract FundTransfer {
  uint public deadline;

  function transfer(address _to, uint _amount) public {
    require(now >= deadline);
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] += _amount;
  }
}
```

# Why it's vulnerable?
This contract allows users to transfer funds to each other, but only if the current time is greater than or equal to the deadline specified in the contract. However, this contract is vulnerable to a time manipulation (timestamp dependence) vulnerability because the now built-in variable, which is used to check the current time, can be manipulated by the miner who mines the block containing the transaction.

# Impact?
To exploit this vulnerability, an attacker could send a transaction to the contract with a low gasPrice, making it unlikely to be included in the next block. They could then manipulate the timestamp of the next block to be greater than or equal to the deadline, which would allow their transaction to be included and executed in the next block. This would allow the attacker to transfer funds even if the deadline has passed, allowing them to bypass the contract's restrictions.

# How to fix?
To prevent this vulnerability, the contract should uses a secure source of randomness to prevent the time manipulation (timestamp dependence) vulnerability:

```solidity
import "https://github.com/dapphub/ds-random/ds-random.sol";

contract FundTransfer {
  uint public deadline;

  function transfer(address _to, uint _amount) public {
    require(ds-random.seededRNG(block.difficulty + block.timestamp) >= deadline);
    require(balances[msg.sender] >= _amount);
    balances[msg.sender] -= _amount;
    balances[_to] += _amount;
  }
}
```

In this updated version of the contract, the transfer() function uses the seededRNG() function from the DS-Random library to generate a random number using the current block's difficulty and timestamp as the seed. This random number is then compared to the deadline to determine whether the transfer should be allowed. Because the random number is generated using a secure source of randomness, it cannot be manipulated by the miner who mines the block containing the transaction. This prevents the time manipulation (timestamp dependence) vulnerability and ensures that the contract's restrictions are enforced correctly.
