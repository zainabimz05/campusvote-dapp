# CampusVote
**Student:** Zainab Imtiaz – 24I-5533
**Course:** MG3012 – Blockchain Technology for Business, Assignment 2
## Project overview

CampusVote is a small decentralized voting application for choosing a campus event. It combines a Solidity smart contract on Ethereum Sepolia with a single-page HTML, CSS, and JavaScript interface. MetaMask signs transactions, and ethers.js connects the browser to the contract.

The poll has three choices:

1. Coding workshop
2. Sports tournament
3. Movie night

Each wallet may hold one active vote. A voter can change their choice or withdraw their vote. Results are calculated from the votes stored in the contract and can be refreshed from the frontend.

## Features

- Connect MetaMask and check the Sepolia network.
- Load the poll using its deployed contract address.
- Read the available options, vote totals, and the connected wallet's choice.
- Cast, change, or withdraw a vote.
- Display transaction progress, confirmation information, and explorer links.
- Show errors for invalid inputs, rejected wallet requests, and failed operations.
- Use a responsive single-page interface with accessible form labels.

## Technology

| Component | Technology |
| --- | --- |
| Contract | Solidity, pragma `^0.8.24` |
| Test network | Ethereum Sepolia, chain ID `11155111` |
| Contract development and deployment | Remix IDE |
| Wallet | MetaMask |
| Frontend | HTML5, CSS3, JavaScript |
| Blockchain library | ethers.js 6.15.0 |

## Smart contract functions

`SmartContract.sol` exposes exactly five functions. Option IDs are zero-based: `0` is Coding workshop, `1` is Sports tournament, and `2` is Movie night.

| Function | Type | Description |
| --- | --- | --- |
| `getOptions()` | Read | Returns the three event names. |
| `getResults()` | Read | Returns each option's active vote count and the total number of active votes. |
| `getVote(address voter)` | Read | Returns whether the wallet has an active vote and its option ID. When `hasVoted` is false, the returned option ID must be ignored. |
| `vote(uint256 optionId)` | Write | Casts the caller's first vote or moves their existing vote to another option. Invalid IDs and voting again for the same option are rejected. |
| `withdrawVote()` | Write | Removes the caller's active vote. Rejected if the caller has no active vote. |

The contract identifies voters by `msg.sender`. A wallet cannot change another wallet's vote. Changing a choice decreases the old option's count and increases the new option's count without increasing the total. Withdrawing decreases the selected option's count and the total. A wallet may vote again after withdrawing.

The contract emits `VoteCast` and `VoteWithdrawn` events to record voting activity. It does not accept or transfer ETH; deployment and writes still require Sepolia test ETH for gas.

## Frontend implementation

`index.html` contains headings, paragraphs, lists, links, buttons, an image, and script tags. Application JavaScript is inside an inline `<script>` tag. The ethers.js library is loaded from a version-pinned CDN URL, and `style.css` provides the visual design and responsive layout. The illustration is embedded in the page, so no separate image download is required.

The application uses MetaMask as its provider and signer. The contract address is supplied in the interface after deployment. Reading results does not submit a transaction or charge gas. Voting actions require confirmation in MetaMask and display the resulting transaction status.

There is no backend database. Votes and counts are stored on the blockchain.

## Files

```text
CampusVote/
├── SmartContract.sol
├── index.html
├── style.css
├── README.md
```

| File | Purpose |
| --- | --- |
| `SmartContract.sol` | Poll state, five public functions, voting rules, and events. |
| `index.html` | Single-page interface and inline wallet/contract JavaScript. |
| `style.css` | Page styling and responsive layout. |
| `README.md` | Project description and deployment record. |

## Local verification

The contract compiled with Solidity 0.8.30 for Cancun and Shanghai. Local EVM tests passed for casting, changing, withdrawing and recasting votes; wallet separation; invalid options; duplicate rejection; event emission; and vote-count consistency. The inline frontend passed 12 groups of simulated wallet and page tests, including transaction failures, replacements, and account/network changes. The page layout and missing-wallet message were also checked in a browser preview.

## Deployment record

**Status: Not deployed yet.** A deployment address has not been supplied. Replace these fields only after deploying this contract and verifying its successful transaction.

| Detail | Value |
| --- | --- |
| Network | Ethereum Sepolia |
| Chain ID | 11155111 |
| Contract address | To be recorded after deployment |
| Deployment transaction hash | To be recorded after deployment |
| Sepolia Etherscan contract link | To be recorded after deployment |

## Scope and limitations

This is an educational poll. One wallet does not mean one person: someone controlling multiple wallets could vote more than once. Votes and transaction history are public, so the application does not provide secret ballots. Withdrawing a vote changes the current results but does not erase historical transactions. The poll has no closing time or administrator controls.

The frontend requires an internet connection for ethers.js and MetaMask's network access. This project is not intended for binding elections.

## References

- [Remix: Deploy & Run](https://remix-ide.readthedocs.io/en/latest/run.html)
- [ethers.js v6 documentation](https://docs.ethers.org/v6/)
- [Ethereum networks and Sepolia](https://ethereum.org/en/developers/docs/networks/#sepolia)
- [MetaMask: viewing test networks](https://support.metamask.io/configure/networks/how-to-view-testnets-in-metamask/)
