// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

contract VoteChuri {
    // 🧑‍⚖️ Owner of the voting system
    address public owner;

    // ✅ Structure for a candidate
    struct Candidate {
        uint id;
        string name;
        uint voteCount;
    }

    // 🔢 Mapping to store candidates
    mapping(uint => Candidate) public candidates;
    uint public candidatesCount;

    // 👤 Mapping to track who has voted
    mapping(address => bool) public hasVoted;

    // 🕒 Voting open or closed
    bool public votingActive;

    // 🧾 Event logs
    event CandidateAdded(uint id, string name);
    event Voted(address voter, uint candidateId);
    event VotingStarted();
    event VotingEnded();

    // 🏗️ Constructor runs once
    constructor() {
        owner = msg.sender; // deployer becomes owner
    }

    // 🔒 Modifier: only owner can call
    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can perform this action");
        _;
    }

    // 🧩 Add candidate (only by owner before voting starts)
    function addCandidate(string memory _name) public onlyOwner {
        require(!votingActive, "Cannot add candidates after voting started");
        candidatesCount++;
        candidates[candidatesCount] = Candidate(candidatesCount, _name, 0);
        emit CandidateAdded(candidatesCount, _name);
    }

    // ▶️ Start voting
    function startVoting() public onlyOwner {
        require(!votingActive, "Voting already active");
        votingActive = true;
        emit VotingStarted();
    }

    // ⏹️ End voting
    function endVoting() public onlyOwner {
        require(votingActive, "Voting not started yet");
        votingActive = false;
        emit VotingEnded();
    }

    // 🗳️ Vote for a candidate
    function vote(uint _candidateId) public {
        require(votingActive, "Voting not active");
        require(!hasVoted[msg.sender], "You have already voted");
        require(_candidateId > 0 && _candidateId <= candidatesCount, "Invalid candidate ID");

        hasVoted[msg.sender] = true;
        candidates[_candidateId].voteCount++;
        emit Voted(msg.sender, _candidateId);
    }

    // 🏆 Get the winner (only after voting ends)
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
