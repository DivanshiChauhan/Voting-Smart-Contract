# Voting Smart Contract

This Solidity program is a smart contract that enables a simple voting system. It allows an owner to initialize the contract, add candidates, start and end the voting process, and cast votes. The contract includes various checks and error handling mechanisms to ensure the integrity of the voting process.

## Description

This program is a smart contract written in Solidity, a programming language used for developing smart contracts on the Ethereum blockchain. The contract allows the owner to add candidates, start and stop the voting process, and for users to vote for their preferred candidates. It includes error handling using `require()`, `assert()`,`revert()`, and custom errors. This contract demonstrates basic voting functionalities and the implementation of access control and state management in Solidity.

## Getting Started

### Executing Program

To run this program, you can use Remix, an online Solidity IDE. To get started, go to the Remix website at [https://remix.ethereum.org/](https://remix.ethereum.org/).

Once you are on the Remix website, create a new file by clicking on the "+" icon in the left-hand sidebar. Save the file with a `.sol` extension (e.g., `Voting.sol`). Copy and paste the following code into the file:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract Voting {
    struct Candidate {
        string name;
        uint256 voteCount;
    }

    mapping(address => bool)  hasVoted;
    mapping(uint256 => Candidate)  candidate_info;
    uint256  candidateCount;
    uint256  totalVotes;
    address  owner;
    bool public votingOpen;
    bool public initialized;

    event CandidateAdded(uint256 candidateId, string name);
    event VoteCasted(uint256 candidateId, address voter);
    event VotingStarted();
    event VotingEnded();

    // Custom errors
    error NotOwner(address caller);
    error VotingNotOpen();
    error AlreadyVoted(address voter);
    error InvalidCandidate(uint256 candidateId);
    error AlreadyInitialized();

    modifier onlyOwner() {
        if (msg.sender != owner) {
            revert NotOwner(msg.sender);
        }
        _;
    }

    modifier isVotingOpen() {
        if (!votingOpen) {
            revert VotingNotOpen();
        }
        _;
    }

    function initialize() public {
        if (initialized) {
            revert AlreadyInitialized();
        }
        owner = msg.sender;
        votingOpen = false;
        totalVotes = 0;
        initialized = true;
    }

    function addCandidate(string memory _name) public onlyOwner {
        require(bytes(_name).length > 0, "Candidate name must not be empty");

        candidateCount++;
        candidate_info[candidateCount] = Candidate(_name, 0);

        emit CandidateAdded(candidateCount, _name);
    }

    function startVoting() public onlyOwner {
        require(!votingOpen, "Voting is already open");

        votingOpen = true;

        emit VotingStarted();
    }

    function endVoting() public onlyOwner {
        require(votingOpen, "Voting is not open");

        votingOpen = false;

        emit VotingEnded();
    }

    function vote(uint256 _candidateId, address Voter_Address) public isVotingOpen {
        require(!hasVoted[Voter_Address], "You have already voted");
        if (_candidateId == 0 || _candidateId > candidateCount) {
            revert InvalidCandidate(_candidateId);
        }

        candidate_info[_candidateId].voteCount++;
        hasVoted[Voter_Address] = true;
        totalVotes++;

        assert(totalVotes == getTotalVotes());

        emit VoteCasted(_candidateId, msg.sender);
    }

    function getCandidate(uint256 _candidateId) public view returns (Candidate memory) {
        require(_candidateId > 0 && _candidateId <= candidateCount, "Invalid candidate ID");
        return candidate_info[_candidateId];
    }

    function totalVotesForCandidate(uint256 _candidateId) public view returns (uint256) {
        require(_candidateId > 0 && _candidateId <= candidateCount, "Invalid candidate ID");
        return candidate_info[_candidateId].voteCount;
    }

    function getTotalVotes() public view returns (uint256) {
        uint256 total = 0;
        for (uint256 i = 1; i <= candidateCount; i++) {
            total += candidate_info[i].voteCount;
        }
        return total;
    }
}
```

To compile the code, click on the "Solidity Compiler" tab in the left-hand sidebar. Make sure the "Compiler" option is set to a compatible version (e.g., `0.8.17`), and then click on the "Compile Voting.sol" button.

Once the code is compiled, you can deploy the contract by clicking on the "Deploy & Run Transactions" tab in the left-hand sidebar. Select the `Voting` contract from the dropdown menu, and then click on the "Deploy" button.

Once the contract is deployed, you can interact with it by calling the functions:
- **Initialize Contract**: Call the `initialize` function.
- **Add Candidate**: Call the `addCandidate` function with the desired candidate name.
- **Start Voting**: Call the `startVoting` function to open the voting process.
- **End Voting**: Call the `endVoting` function to close the voting process.
- **Vote**: Call the `vote` function with the candidate ID and voter's address.
- **Get Candidate Info**: Call the `getCandidate` function with the candidate ID.
- **Get Total Votes**: Call the `getTotalVotes` function to get the total number of votes cast.

### Example Interactions

1. **Initialize Contract**:
   - Call `initialize();`

2. **Add Candidate**:
   - Call `addCandidate("A");`

3. **Start Voting**:
   - Call `startVoting();`

4. **Vote**:
   - Call `vote(1, 0xYourAddress);`

5. **End Voting**:
   - Call `endVoting();`

6. **Get Candidate Info**:
   - Call `getCandidate(1);`

7. **Get Total Votes**:
   - Call `getTotalVotes();`

## Authors

Divanshi  
[DivanshiChauhan](https://github.com/DivanshiChauhan)

## License

This project is licensed under the MIT License - see the LICENSE.md file for details.
