// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

/// @title CampusVote - a public campus-event poll
/// @notice One active vote per wallet, not per person. Votes are public.
/// @dev Educational testnet example; no identity checks or election deadline.
contract SmartContract {
    string[3] private options = ["Coding workshop", "Sports tournament", "Movie night"];
    uint256[3] private voteCounts;
    uint256 private activeVoteCount;

    // Zero means no vote; otherwise the stored value is optionId + 1.
    mapping(address => uint256) private selections;

    event VoteCast(address indexed voter, uint256 optionId);
    event VoteWithdrawn(address indexed voter, uint256 optionId);

    /// @notice Returns the three poll options in their option-ID order (0-2).
    function getOptions() external view returns (string[3] memory names) {
        return options;
    }

    /// @notice Returns current totals. Changing a vote preserves totalVotes.
    function getResults() external view returns (uint256[3] memory counts, uint256 totalVotes) {
        return (voteCounts, activeVoteCount);
    }

    /// @notice Returns a wallet's current choice. Ignore optionId if hasVoted is false.
    function getVote(address voter) external view returns (bool hasVoted, uint256 optionId) {
        uint256 selection = selections[voter];
        return selection == 0 ? (false, 0) : (true, selection - 1);
    }

    /// @notice Cast a vote or move your existing vote to another option.
    function vote(uint256 optionId) external {
        require(optionId < options.length, "Invalid option");
        uint256 previous = selections[msg.sender];
        require(previous != optionId + 1, "Already voted for this option");

        if (previous == 0) {
            activeVoteCount++;
        } else {
            voteCounts[previous - 1]--;
        }

        selections[msg.sender] = optionId + 1;
        voteCounts[optionId]++;
        emit VoteCast(msg.sender, optionId);
    }

    /// @notice Withdraw only your own current vote. Historical votes remain public.
    function withdrawVote() external {
        uint256 previous = selections[msg.sender];
        require(previous != 0, "No active vote");
        uint256 optionId = previous - 1;
        voteCounts[optionId]--;
        activeVoteCount--;
        delete selections[msg.sender];
        emit VoteWithdrawn(msg.sender, optionId);
    }
}
