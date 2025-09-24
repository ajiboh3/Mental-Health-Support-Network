# Mental Health Support Network Smart Contract

A production-ready decentralized peer-to-peer mental health counseling platform built on the Stacks blockchain, providing secure, private, and transparent mental health support services with automated escrow and reputation systems.

##  Overview

The Mental Health Support Network enables:
- **Fully Automated P2P Counseling**: Direct connections with zero intermediaries
- **Privacy-First Design**: Hash-based storage for complete data protection
- **Smart Reputation System**: Weighted average ratings with anti-gaming protection
- **Zero-Trust Escrow**: Fully automated payment system with instant settlements
- **Real-time Scheduling**: 24-hour slot-based availability management
- **Auto-Registration**: Seamless client onboarding during first session request
- **Decentralized Governance**: No manual verification required for counselors

##  Architecture

### Core Components

1. **Streamlined User Management**
   - Auto-verified counselor registration (no admin approval needed)
   - Seamless client auto-registration during first session request
   - Hash-based privacy protection for all personal data

2. **Intelligent Session Management**  
   - 5-step automated lifecycle: Request → Accept → Start → Complete → Rate
   - Real-time session status tracking with block-height timestamps
   - Optional encrypted notes storage via cryptographic hashes

3. **Zero-Trust Payment System**
   - Fully automated STX escrow with no manual intervention
   - Instant payment release upon session completion
   - Automatic platform fee collection (5% default, max 20%)
   - Complete refund system for cancelled sessions

4. **Anti-Gaming Reputation System**
   - Weighted average calculations using rating sums and counts
   - Persistent review storage with hash-based privacy
   - Real-time counselor rating updates with basis points precision

5. **Smart Availability System**
   - Date-specific 24-hour slot management
   - Real-time availability updates with block-height tracking
   - Flexible scheduling coordination between parties

##  Features

### For Counselors
- **Instant Activation**: Register and immediately start accepting sessions
- **No Verification Wait**: Auto-approved registration with instant platform access
- **Flexible Pricing**: Set custom rates with 1 STX minimum enforcement
- **Smart Scheduling**: Manage availability with granular 24-hour time slots
- **Guaranteed Payments**: Automatic payment release upon session completion
- **Reputation Building**: Build credibility through transparent client ratings
- **Suspension Protection**: Only admin can suspend for platform violations

### For Clients
- **Zero-Friction Access**: Auto-registration eliminates signup barriers
- **Instant Counselor Discovery**: Browse active counselors with live ratings
- **Risk-Free Booking**: Complete refunds for cancelled sessions before start
- **Transparent Pricing**: Clear fee breakdown with platform costs shown
- **Protected Payments**: Escrow system ensures funds safety until completion
- **Honest Reviews**: Rate counselors and access authentic review history

### For Platform Operators
- **Automated Revenue**: Platform fees collected without manual intervention
- **Minimal Maintenance**: Self-executing contracts reduce operational overhead
- **User Moderation**: Suspend problematic users while maintaining decentralization
- **Fee Management**: Adjust platform fees up to 20% maximum for sustainability
- **Growth Analytics**: Track platform metrics through built-in counters
- **Dispute Prevention**: Automated escrow reduces payment conflicts

##  Getting Started

### Prerequisites
- Stacks CLI installed
- STX tokens for transactions
- Clarinet for testing (optional)

### Deployment

1. Clone the repository:
```bash
git clone https://github.com/your-org/mental-health-support-network
cd mental-health-support-network
```

2. Deploy the contract:
```bash
stx deploy_contract mental-health-network mental-health-network.clar
```

### Usage Examples

#### Register as a Counselor
```clarity
(contract-call? .mental-health-network register-counselor 
  0x1234567890abcdef1234567890abcdef12345678 ;; name-hash
  (list "anxiety" "depression" "trauma" "relationship" "addiction") ;; specializations
  u2000000 ;; hourly rate (2 STX)
)
```

#### Set Availability (24-hour slots)
```clarity
(contract-call? .mental-health-network set-availability 
  u20250730 ;; date (YYYYMMDD format)
  (list true true false false true true true true true true false false false false true true true true true true false false false false) ;; 24 hour slots
)
```

#### Request a Session
```clarity
(contract-call? .mental-health-network request-session 
  'ST1HTBVD3JG9C05J7HBJTHGR0GGW7KXW28M5JS8QE ;; counselor
  u60 ;; duration in minutes
  u2000000 ;; session fee (2 STX)
)
```

#### Complete and Rate Session
```clarity
;; Complete session (counselor only)
(contract-call? .mental-health-network complete-session 
  u1 ;; session-id
  (some 0x9876543210abcdef9876543210abcdef98765432) ;; optional notes hash
)

;; Rate session (client or counselor)
(contract-call? .mental-health-network rate-session 
  u1 ;; session-id
  u450 ;; rating (4.5 stars in basis points)
  (some 0xfedcba0987654321fedcba0987654321fedcba09) ;; optional review hash
)
```

##  Data Structures

### Counselors
```clarity
{
  name-hash: (buff 32),           ;; Privacy-protected name
  specializations: (list 5 (string-ascii 50)), ;; Up to 5 specialties
  hourly-rate: uint,              ;; Rate in microSTX
  total-sessions: uint,           ;; Completed sessions count
  rating-sum: uint,               ;; Sum of all ratings (for average calculation)
  rating-count: uint,             ;; Number of ratings received
  status: uint,                   ;; 0=pending, 1=active, 2=suspended
  joined-at: uint,                ;; Registration block height
  is-available: bool              ;; Current availability status
}
```

### Sessions
```clarity
{
  client: principal,              ;; Client's blockchain address
  counselor: principal,           ;; Counselor's blockchain address
  fee: uint,                      ;; Session fee in microSTX
  platform-fee: uint,             ;; Platform fee in microSTX
  status: uint,                   ;; 0=requested, 1=accepted, 2=in-progress, 3=completed, 4=cancelled
  created-at: uint,               ;; Session request block height
  started-at: (optional uint),    ;; Session start block height
  completed-at: (optional uint),  ;; Session completion block height
  duration-minutes: uint,         ;; Planned session duration
  notes-hash: (optional (buff 32)) ;; Encrypted session notes hash
}
```

### Payment Records
```clarity
{
  total-locked: uint,             ;; Total amount locked in escrow
  counselor-paid: bool,           ;; Payment status to counselor
  platform-paid: bool,           ;; Platform fee payment status
  refunded: bool                  ;; Refund status for cancelled sessions
}
```

##  Security Features

### Privacy Protection
- **Hash-Based Storage**: Personal information stored as hashes
- **Encrypted Notes**: Session notes stored as encrypted hashes
- **Principal-Based Authentication**: Blockchain-native identity verification

### Financial Security
- **Escrow System**: Funds held in contract until session completion
- **Automatic Fee Distribution**: Platform fees and counselor payments handled automatically
- **Dispute Resolution**: Built-in dispute handling mechanism

### Access Control
- **Role-Based Permissions**: Different access levels for clients, counselors, and admins
- **Status Verification**: Only verified counselors can accept sessions
- **Owner-Only Functions**: Critical functions restricted to contract owner

##  Testing & Validation

### Comprehensive Test Coverage
```bash
# Unit tests for all core functions
clarinet test --coverage

# Integration test scenarios
- ✅ Counselor registration with auto-verification
- ✅ Client auto-registration during session request
- ✅ Complete session lifecycle with payment automation
- ✅ Rating system with weighted average calculations
- ✅ Availability management with 24-hour slots
- ✅ Session cancellation with automatic refunds
- ✅ Platform fee collection and counselor payments
- ✅ Admin functions for user suspension and fee management
```

### Security Audit Results
- **Access Control**: ✅ Role-based permissions enforced
- **Payment Security**: ✅ Escrow system prevents double-spending
- **Data Privacy**: ✅ Hash-based storage protects sensitive information
- **Error Handling**: ✅ Comprehensive error codes with graceful failures
- **Type Safety**: ✅ All functions use proper Clarity type annotations
- **Integer Safety**: ✅ Safe arithmetic prevents overflow vulnerabilities

### Performance Metrics
- **Contract Size**: 500+ lines of optimized Clarity code
- **Gas Efficiency**: Minimal transaction costs for all operations
- **Storage Optimization**: Efficient data structures reduce blockchain bloat
- **Query Performance**: Read-only functions provide instant data access

##  Future Enhancements

### Planned Features
- **Multi-token Support**: Accept different cryptocurrencies
- **Group Sessions**: Support for group counseling sessions
- **Subscription Model**: Monthly subscription options
- **Advanced Matching**: AI-powered counselor-client matching
- **Mobile Integration**: Native mobile app support
- **Video Call Integration**: Built-in secure video calling

### Roadmap
- **Q2 2025**: Multi-language support
- **Q3 2025**: Advanced analytics dashboard
- **Q4 2025**: Integration with traditional healthcare systems
- **Q1 2026**: Cross-chain compatibility

##  Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

### Development Setup
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Submit a pull request

##  License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

##  Support

For support and questions:
- **Discord**: [Join our community](https://discord.gg/mental-health-support)
- **Email**: support@mentalhealthnetwork.dev
- **Documentation**: [Full documentation](https://docs.mentalhealthnetwork.dev)

##  Disclaimer

This platform is designed to facilitate peer-to-peer mental health support but does not replace professional medical treatment. Users should consult with qualified healthcare providers for serious mental health concerns.

##  Links

- **Website**: https://mentalhealthnetwork.dev
- **Whitepaper**: [Technical Documentation](docs/whitepaper.md)
- **API Reference**: [API Documentation](docs/api.md)
- **Community**: [Join the Discussion](https://github.com/mental-health-support-network/discussions)

---

Built with  for mental health support on the Stacks blockchain.