##  README: STX Exchange Smart Contract

###  Overview

This Clarity smart contract implements a simple yet secure **on-chain auction system for STX tokens** on the Stacks blockchain. The contract allows the owner to initiate an auction for an item with a specified reserve price and duration, while users can place bids in STX. The highest bidder at the end of the auction wins, and their bid is transferred to the auction owner.

---

### 🚀 Features

* **One active auction at a time**
* **Reserve price enforcement**
* **Bid increment enforcement (`BID-STEP = 1 STX`)**
* **Refunds to previous bidders**
* **Owner-controlled auction lifecycle**
* **On-chain auction state visibility**

---

### 📦 Contract Components

#### 🔐 Initialization

```clarity
(initialize-auction (duration uint) (name (string-ascii 50)) (min-price uint))
```

* Only the owner can initialize.
* Sets duration, item name, and reserve price.
* Prevents reinitialization if auction already started.

#### 💰 Bidding

```clarity
(submit-bid (offer uint))
```

* Ensures auction is active.
* Validates bid against the current top bid or reserve.
* Refunds the previous highest bidder.
* Updates the current top bid and bidder.

#### ✅ Concluding Auction

```clarity
(conclude-auction)
```

* Can be called by anyone.
* Transfers winning bid to owner **if** reserve price is met.
* Returns error if no bids or reserve not met.

#### ❌ Canceling Auction

```clarity
(cancel-auction)
```

* Only the owner can cancel.
* Allowed only if **no bids** are placed.

---

### 🔍 Read-only Functions

| Function               | Description                                           |
| ---------------------- | ----------------------------------------------------- |
| `query-top-bid`        | Returns current highest bid                           |
| `query-top-bidder`     | Returns current top bidder                            |
| `query-auction-end`    | Returns auction end block                             |
| `query-item-name`      | Returns item name                                     |
| `query-reserve-price`  | Returns reserve price                                 |
| `query-auction-status` | Returns `"Not started"`, `"In progress"` or `"Ended"` |

---

### ❗ Error Codes

| Code   | Meaning                 |
| ------ | ----------------------- |
| `u100` | Not authorized          |
| `u101` | Auction already started |
| `u102` | Auction not started     |
| `u103` | Auction ended           |
| `u104` | Bid too low             |
| `u105` | Transfer failed         |
| `u106` | Auction not ended       |
| `u107` | No bids placed          |
| `u108` | Invalid duration        |
| `u109` | Reserve not met         |
| `u110` | Auction in progress     |
| `u111` | Invalid item name       |
| `u112` | Invalid reserve price   |

---

### 📖 Example Usage

```clarity
;; Start an auction
(initialize-auction u144 "Rare NFT" u10000000) ;; 1 STX = 1_000_000

;; Place a bid of 2 STX
(submit-bid u2000000)

;; Query top bid
(query-top-bid) ;; returns u2000000

;; Conclude the auction (if ended and reserve met)
(conclude-auction)
```

---

### 📄 Deployment Notes

* Make sure `owner` is correctly set upon deployment.
* All amounts are in micro-STX (1 STX = 1,000,000 micro-STX).
* Use `block-height` for time-based logic (each block \~10 minutes).

---