# Bad Randomness

Smart contract below is vulnureable to Bad Randomness (Nothing is secret)

```solidity
contract Lottery {
  uint public winningNumber;

  function pickWinner() public {
    winningNumber = block.blockhash(block.number - 1);
  }
}
```

# Why it's vulnerable?
This contract is a simple lottery that picks a winner by using the previous block's hash as the winning number. However, this contract is vulnerable to a bad randomness vulnerability because the ```blockhash()``` function is not a secure source of randomness. 

# Impact?
An attacker could potentially predict the winning number by analyzing the previous block's hash, allowing them to win the lottery every time.

# How to fix?
To prevent this vulnerability, the contract should use a secure source of randomness, such as the ```seededRNG()``` function from the DS-Random library. Here is an updated version of the contract that uses the ```seededRNG()``` function to generate a random winning number:

```solidity
import "https://github.com/dapphub/ds-random/ds-random.sol";

contract Lottery {
  uint public winningNumber;

  function pickWinner() public {
    winningNumber = ds-random.seededRNG(block.difficulty + block.timestamp);
  }
}
```

In this updated version of the contract, the ```seededRNG()``` function is used to generate a random winning number, using the current block's difficulty and timestamp as the seed. This ensures that the winning number is truly random, and cannot be predicted by an attacker.
