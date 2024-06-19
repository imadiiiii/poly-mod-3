# poly-mod-3

This guide provides step-by-step instructions to implement a circuit using circom, compile it, generate a proof, deploy a solidity verifier contract to a testnet, and verify the proof using the deployed contract.

## Prerequisites

1. **Node.js and npm**: Ensure you have Node.js (version 12 or higher) and npm installed on your machine.
2. **circom**: Install the circom compiler globally:
   ```bash
   npm install -g circom
   ```
3. **snarkjs**: Install snarkjs globally for proof generation:
   ```bash
   npm install -g snarkjs
   ```
4. **Solidity Compiler**: Ensure you have the Solidity compiler (solc) installed to compile Solidity contracts.

## Steps

### 1. Implementing the Circuit

Create a new file named `circuit.circom` and define your circuit logic using circom syntax. For example:

```circom
template Main() {
    signal input A;
    signal input B;
    signal output C;

    C <== A * B;
}

component main = Main();
```

Save the file with your circuit definition.

### 2. Compiling the Circuit

Compile the circom circuit to generate the required artifacts (`.json` and `.wasm`):

```bash
circom circuit.circom -o circuit
```

This command generates `circuit.json` and `circuit.wasm` in the `circuit` directory.

### 3. Generating a Proof

Use snarkjs to generate a proof for specific inputs. Create a `input.json` file with the input values:

```json
{
    "A": 0,
    "B": 1
}
```

Generate the proof:

```bash
snarkjs groth16 prove circuit/circuit.json circuit/ptau_final.ptau input.json witness.wtns proof.json public.json
```

This creates `proof.json` which contains the proof and `public.json` which contains the public inputs.

### 4. Deploying a Solidity Verifier Contract

Write a Solidity contract that verifies the proof using the generated proof and public inputs. Compile the contract using `solc`.

Example Verifier Contract (`Verifier.sol`):

```solidity
pragma solidity ^0.8.0;

contract Verifier {
    function verifyProof(bytes memory proof, bytes memory publicInputs) public returns (bool) {
        // Implement proof verification logic
        // Use snarkjs helper methods or custom Solidity functions to verify the proof
        // Return true if proof is valid, false otherwise
    }
}
```

Compile `Verifier.sol`:

```bash
solc Verifier.sol --bin --abi --optimize -o ./build
```

This creates `Verifier.bin` and `Verifier.abi` in the `build` directory.

### 5. Deploying and Verifying the Proof

Deploy the `Verifier` contract to a testnet (e.g., Sepolia or Mumbai Testnet). Use a suitable Ethereum development tool (like Remix, Truffle, or Hardhat) to deploy the contract.

Call the `verifyProof()` method on the deployed contract instance with the proof and public inputs:

```solidity
Verifier verifier = Verifier(/* constructor arguments */);
bool verified = verifier.verifyProof(proof, publicInputs);
require(verified, "Proof verification failed");
```

Ensure the output (`verified`) is `true` to assert that the proof is valid.

---
