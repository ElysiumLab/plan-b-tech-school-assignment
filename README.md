# ⚡ Bitcoin Lightning Quest System - Technical Assignment

## 🎯 Overview
Quest system implementation that leverages Bitcoin Lightning Network for automated rewards distribution. The system enables quest creation, validation, and automatic reward distribution through LNURL-withdraw.

## 🏗 System Architecture

### 🧩 Core Components
1. Quest Management System
2. Bitcoin & Lightning Network Integration
3. LNURL-withdraw Implementation
4. API Interface for External Integration

### 🛠 Technical Stack
- Node.js
- Bitcoin Core (testnet)
- LND/Core Lightning
- MongoDB
- Express.js/NestJS (bonus)

## 🔄 System Flows

### Quest Creation Flow
```mermaid
sequenceDiagram
    participant A as Admin
    participant S as Server
    participant DB as Database
    participant LN as Lightning Node

    A->>S: POST /api/quests with quest details
    S->>LN: Verify available funds
    LN-->>S: Confirm funds
    S->>DB: Save new quest
    DB-->>S: Confirm save
    S-->>A: Return quest ID
```

### Quest Validation Flow
```mermaid
sequenceDiagram
    participant A as Admin
    participant S as Server
    participant DB as Database
    participant LN as Lightning Node

    A->>S: POST /api/quests/:id/validate
    S->>DB: Retrieve quest details
    DB-->>S: Quest details
    S->>S: Validate requirements
    S->>DB: Update status
    S-->>A: Confirm validation
```

### Reward Claim Flow
```mermaid
sequenceDiagram
    participant A as Admin
    participant S as Server
    participant DB as Database
    participant LN as Lightning Node
    
    A->>S: POST /api/claim-reward
    S->>DB: Verify claim valid
    DB-->>S: Confirm validity
    S->>LN: Generate LNURL-withdraw
    LN-->>S: LNURL data
    S->>S: Generate QR code
    S->>DB: Update claim status
    S-->>A: Return QR code
    
    A->>LN: Scan QR with wallet
    LN->>S: Callback LNURL-withdraw
    S->>LN: Authorize payment
    LN-->>S: Confirm payment
    S->>DB: Update counters and status
```

## 📊 System Architecture Diagram
```mermaid
classDiagram
    class QuestController {
        +createQuest(QuestDto)
        +getQuests()
        +getQuestById(id)
        +validateQuest(id, ValidationDto)
        +claimReward(ClaimDto)
        +getClaimStatus(id)
    }

    class QuestService {
        -questRepository
        -lightningService
        -claimService
        +createQuest(QuestDto)
        +findQuests()
        +findById(id)
        +validateCompletion(id, data)
    }

    class ClaimService {
        -claimRepository
        -lightningService
        +createClaim(QuestId, data)
        +processClaim(ClaimId)
        +updateClaimStatus(ClaimId, status)
        +getClaimStatus(ClaimId)
    }

    class LightningService {
        -lndClient
        -walletService
        +generateLNURLWithdraw(amount)
        +validateWithdraw(lnurlData)
        +processPayment(withdrawData)
        +getWalletBalance()
    }

    class WalletService {
        -bitcoinClient
        +checkBalance()
        +createAddress()
        +processTransaction()
    }

    class Quest {
        +id string
        +title string
        +description string
        +reward RewardInfo
        +conditions Conditions
        +active boolean
        +validateCompletion(data)
    }

    class Claim {
        +id string
        +questId string
        +status ClaimStatus
        +lnurlData LNURLData
        +completionData object
        +updateStatus(status)
    }

    class QuestRepository {
        +save(Quest)
        +findAll()
        +findById(id)
        +update(Quest)
    }

    class ClaimRepository {
        +save(Claim)
        +findById(id)
        +updateStatus(id, status)
    }

    class LightningClient {
        <<interface>>
        +connect()
        +createInvoice()
        +payInvoice()
        +getNodeInfo()
    }

    class BitcoinClient {
        <<interface>>
        +getBalance()
        +createAddress()
        +sendTransaction()
    }

    QuestController --> QuestService
    QuestController --> ClaimService
    QuestService --> QuestRepository
    QuestService --> LightningService
    QuestService --> ClaimService
    ClaimService --> ClaimRepository
    ClaimService --> LightningService
    LightningService --> LightningClient
    LightningService --> WalletService
    WalletService --> BitcoinClient
    QuestRepository --> Quest
    ClaimRepository --> Claim
```

## 🔌 API Endpoints

### Quest Management
```
POST /api/quests
- Creates new quest
- Body: quest details

GET /api/quests
- Lists all active quests

GET /api/quests/:id
- Gets single quest details

PUT /api/quests/:id
- Updates quest details
```

### Claim & Reward
```
POST /api/quests/:id/validate
- Validates quest completion
- Body: completion data

POST /api/claim-reward
- Generates LNURL-withdraw
- Returns QR code
- Updates reward counters

GET /api/rewards/status/:claimId
- Checks claim status
```

## 💾 Data Models

### Quest Schema
```javascript
{
  id: ObjectId,
  title: String,
  description: String,
  reward_amount: Number,
  total_rewards: Number,
  claimed_rewards: Number,
  active: Boolean,
  start_date: Date,
  end_date: Date,
  conditions: Object
}
```

### Claim Schema
```javascript
{
  id: ObjectId,
  quest_id: ObjectId,
  claim_data: Object,
  status: String, // pending, validated, claimed, paid
  lnurl_data: Object,
  created_at: Date,
  claimed_at: Date
}
```

## ✅ Implementation Requirements

### 🔑 Core Features
1. Quest management system
2. Lightning Network integration
3. LNURL-withdraw implementation
4. External API integration
5. Comprehensive test suite

### ⭐ Bonus Features
- NestJS implementation
- Advanced monitoring
- Admin dashboard
- Webhook system

## 🔒 Security Requirements
- Testnet usage only
- Secure key management
- Comprehensive logging
- Error handling
- Wallet backup system

## 📚 Resources
- [LND API Documentation](https://api.lightning.community/)
- [LNURL Specifications](https://github.com/lnurl/luds)
- [Bitcoin Core RPC](https://developer.bitcoin.org/reference/rpc/)
- [Core Lightning Documentation](https://lightning.readthedocs.io/)
- [NestJS Documentation](https://docs.nestjs.com/)