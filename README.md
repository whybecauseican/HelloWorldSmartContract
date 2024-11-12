# HelloWorld Smart Contract

## Overview
The `HelloWorld` smart contract is a simple Solidity-based contract designed to manage and update a greeting message. This contract demonstrates the use of common Solidity error-handling mechanisms (`require`, `assert`, and `revert`) to enforce permissions and validate conditions within the contract.

## Features
- **Greeting Message Management**: Allows the contract owner to set and update a greeting message.
- **Access Control**: Restricts the ability to update the greeting to the contract's owner.
- **Update Count Tracking**: Tracks the number of times the greeting has been updated.
- **Error Handling**: Uses `require`, `assert`, and `revert` to enforce specific constraints.

## Contract Details

### State Variables
- `string public greet`: Stores the current greeting message, defaulting to "Hello World!".
- `address public owner`: Stores the address of the contract owner (assigned at deployment).
- `uint public updateCount`: Tracks the number of times the greeting message has been updated.

### Constructor
The constructor sets the `owner` variable to the address that deployed the contract.

### Functions

#### `updateGreeting(string memory _newGreet)`
- **Purpose**: Allows the owner to update the greeting message.
- **Access Control**: Only the contract owner can call this function.
- **Parameters**:
  - `_newGreet`: The new greeting message to be set.
- **Error Handling**: Uses `require` to ensure only the owner can update the greeting.
- **Side Effects**: Increments `updateCount` with each successful update.

#### `checkUpdateCount() public view returns (string memory)`
- **Purpose**: Checks that the update count is within a reasonable limit.
- **Return**: Returns a message confirming that the update count is within range.
- **Error Handling**: Uses `assert` to ensure that `updateCount` is less than 10, helping identify if any unexpected changes occur in the update process.

#### `resetGreeting() public`
- **Purpose**: Resets the greeting message back to "Hello World!" and resets `updateCount`.
- **Constraints**: Can only be called if the `updateCount` is less than 5.
- **Error Handling**: Uses `revert` to prevent resetting if the greeting has been updated 5 or more times.

## Error-Handling Mechanisms
The contract showcases three primary Solidity error-handling mechanisms:
- **require**: Ensures that only the contract owner can update the greeting message.
- **assert**: Checks that the `updateCount` remains below a reasonable threshold.
- **revert**: Prevents resetting the greeting message after a certain number of updates.

## License
This project is licensed under the MIT License.
