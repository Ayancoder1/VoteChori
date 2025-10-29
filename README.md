Here’s a clean and professional **README.md** file for your **VoteChuri** decentralized voting system 👇

---

## 🗳️ VoteChuri - A Decentralized Voting System

### 📜 Overview

**VoteChuri** is a simple beginner-friendly Solidity smart contract that enables decentralized, transparent, and tamper-proof voting on the Ethereum blockchain.
It allows an owner to create and manage elections by adding candidates, starting and ending voting sessions, and letting participants vote securely using their wallet addresses.

---

### ⚙️ Features

* 👑 **Owner-controlled** — Only the contract owner (deployer) can add candidates or control the voting phase.
* 🧩 **Dynamic candidate addition** — Add any number of candidates before voting begins.
* 🗳️ **One person, one vote** — Each address can vote only once.
* 🕒 **Voting lifecycle management** — Owner can start and end the voting session.
* 🏆 **Winner announcement** — Retrieve the winner after voting ends.
* 📢 **Event logs** — Emits events for transparency (`CandidateAdded`, `Voted`, `VotingStarted`, `VotingEnded`).

---

### 🧱 Smart Contract Code

File: `VoteChuri.sol`

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

contract VoteChuri {
    address public owner;

    struct Candidate {
        uint id;
        string name;
        uint voteCount;
    }

    mapping(uint => Candidate) public candidates;
    uint public candidatesCount;

    mapping(address => bool) public hasVoted;
    bool public votingActive;

    event CandidateAdded(uint id, string name);
    event Voted(address voter, uint candidateId);
    event VotingStarted();
    event VotingEnded();

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can perform this action");
        _;
    }

    function addCandidate(string memory _name) public onlyOwner {
        require(!votingActive, "Cannot add candidates after voting started");
        candidatesCount++;
        candidates[candidatesCount] = Candidate(candidatesCount, _name, 0);
        emit CandidateAdded(candidatesCount, _name);
    }

    function startVoting() public onlyOwner {
        require(!votingActive, "Voting already active");
        votingActive = true;
        emit VotingStarted();
    }

    function endVoting() public onlyOwner {
        require(votingActive, "Voting not started yet");
        votingActive = false;
        emit VotingEnded();
    }

    function vote(uint _candidateId) public {
        require(votingActive, "Voting not active");
        require(!hasVoted[msg.sender], "You have already voted");
        require(_candidateId > 0 && _candidateId <= candidatesCount, "Invalid candidate ID");

        hasVoted[msg.sender] = true;
        candidates[_candidateId].voteCount++;
        emit Voted(msg.sender, _candidateId);
    }

    function getWinner() public view returns (string memory winnerName, uint winnerVotes) {
        require(!votingActive, "Voting still active");

        uint winningVoteCount = 0;
        uint winningCandidateId = 0;

        for (uint i = 1; i <= candidatesCount; i++) {
            if (candidates[i].voteCount > winningVoteCount) {
                winningVoteCount = candidates[i].voteCount;
                winningCandidateId = i;
            }
        }

        winnerName = candidates[winningCandidateId].name;
        winnerVotes = candidates[winningCandidateId].voteCount;
    }
}
```

---

### 🚀 How to Deploy & Test

#### 🧠 Prerequisites

* [Remix IDE](https://remix.ethereum.org/)
* [MetaMask Wallet](https://metamask.io/)
* Test Ether on a test network (e.g. **Sepolia**)

#### 🪜 Steps

1. Open [Remix IDE](https://remix.ethereum.org)
2. Create a new file named `VoteChuri.sol`
3. Paste the contract code
4. Compile with **Solidity v0.8.18**
5. Deploy the contract using the **Injected Provider (MetaMask)** environment

---

### 🧩 Example Usage

| Action        | Function                | Who Can Call | Description              |
| :------------ | :---------------------- | :----------- | :----------------------- |
| Add Candidate | `addCandidate("Alice")` | Owner        | Adds a new candidate     |
| Start Voting  | `startVoting()`         | Owner        | Begins the voting period |
| Vote          | `vote(1)`               | Any user     | Vote for candidate ID 1  |
| End Voting    | `endVoting()`           | Owner        | Ends the voting          |
| Get Winner    | `getWinner()`           | Anyone       | View the winner          |

---

### 🔐 Security Features

* Prevents multiple votes from the same address.
* Restricts candidate addition and voting control to contract owner.
* Validates candidate IDs and voting phases.

---

### 🧠 Future Improvements

* Add **voter registration** for verified users.
* Use **block timestamps** to automate start/end time.
* Integrate a **frontend DApp** with React + Web3.js or Ethers.js.
* Store results on **IPFS** for transparency.

---

### 🪪 License

This project is licensed under the [MIT License](https://opensource.org/licenses/MIT).

---

Would you like me to create a **frontend DApp template (React + Ethers.js)** next to interact with this contract (connect wallet, vote, see winner)?
