# HomeFax System Architecture

This document provides an overview of the HomeFax system architecture, including components, data flow, and integration points.

## System Overview

HomeFax is a distributed application (dApp) that combines traditional web technologies with blockchain to provide a secure, transparent platform for property history records. The system consists of four main components:

1. **Frontend Application**: React-based web interface
2. **Backend API**: NestJS server with RESTful endpoints
3. **Blockchain Layer**: Smart contracts on Base blockchain
4. **Database**: PostgreSQL for off-chain data storage

## Architecture Diagram

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│                 │     │                 │     │                 │
│    Frontend     │◄───►│    Backend      │◄───►│   Database      │
│    (React)      │     │    (NestJS)     │     │  (PostgreSQL)   │
│                 │     │                 │     │                 │
└────────┬────────┘     └────────┬────────┘     └─────────────────┘
         │                       │
         │                       │
         ▼                       ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│                 │     │                 │     │                 │
│  Web3 Provider  │◄───►│ Smart Contracts │◄───►│  IPFS Storage   │
│  (MetaMask)     │     │    (Base)       │     │                 │
│                 │     │                 │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

## Component Details

### 1. Frontend Application

The frontend is a React application with TypeScript that provides the user interface for interacting with the HomeFax platform.

**Key Technologies:**

- React with TypeScript
- Web3 libraries for blockchain interaction
- Responsive design for all devices
- State management with Redux

**Key Features:**

- User authentication (email/password and crypto wallet)
- Property search and browsing
- Report viewing and purchasing
- User dashboard
- Admin panel for verification

**Integration Points:**

- Connects to Backend API for data retrieval and manipulation
- Interacts directly with blockchain via Web3 provider for transactions
- Handles wallet connections for authentication and payments

### 2. Backend API

The backend is a NestJS application that serves as the central hub for business logic, data processing, and integration between components.

**Key Technologies:**

- NestJS framework
- TypeORM for database interactions
- JWT for authentication
- Swagger for API documentation
- Mixpanel for analytics

**Key Features:**

- User management
- Property and report data management
- Blockchain transaction orchestration
- IPFS integration for decentralized storage
- AI processing for report standardization

**Integration Points:**

- Connects to PostgreSQL database for data persistence
- Interacts with blockchain through ethers.js
- Uploads and retrieves files from IPFS
- Provides RESTful API endpoints for frontend

### 3. Blockchain Layer

The blockchain layer consists of smart contracts deployed on the Base blockchain that handle the immutable storage of property records and report access control.

**Key Technologies:**

- Solidity smart contracts
- Hardhat development environment
- Base blockchain (Coinbase's L2 solution)

**Key Features:**

- Property record creation and verification
- Report creation and verification
- Report purchasing and access control
- Ownership tracking

**Integration Points:**

- Interacted with by both frontend (direct transactions) and backend (orchestration)
- Emits events that are captured by backend for off-chain processing
- Stores IPFS hashes for report content

### 4. Database

PostgreSQL database stores off-chain data that is either too large or not suitable for blockchain storage.

**Key Technologies:**

- PostgreSQL
- TypeORM migrations and entities

**Key Data Stored:**

- User accounts and profiles
- Extended property details
- Report metadata
- Transaction history
- System configuration

**Integration Points:**

- Accessed exclusively through the backend API
- Synchronized with blockchain events

## Data Flow

### User Registration and Authentication

1. User registers with email/password or connects wallet
2. Backend validates credentials and creates user record
3. JWT token issued for authenticated sessions
4. Wallet address stored and linked to user account

### Property Creation

1. User submits property details via frontend
2. Backend validates and stores initial record in database
3. Backend initiates blockchain transaction to create property on-chain
4. Smart contract emits event upon successful creation
5. Backend updates database record with blockchain transaction details
6. Frontend displays success message and property details

### Report Creation

1. User uploads report via frontend
2. Backend processes report with AI for standardization
3. Report content stored on IPFS
4. IPFS hash and metadata stored in database
5. Backend initiates blockchain transaction to register report on-chain
6. Smart contract emits event upon successful creation
7. Backend updates database record with blockchain transaction details

### Report Purchase

1. User initiates purchase via frontend
2. If using crypto payment:
   a. Frontend initiates direct blockchain transaction
   b. Smart contract handles payment and access rights
   c. Event emitted upon successful purchase
   d. Backend captures event and updates database
3. If using traditional payment:
   a. Backend processes payment via payment processor
   b. Backend initiates blockchain transaction to grant access
   c. Database updated with purchase details

### Report Access

1. User requests report content
2. Backend checks access rights on blockchain
3. If access granted, backend retrieves content from IPFS
4. Content delivered to frontend for display

## Security Considerations

### Authentication and Authorization

- JWT tokens with appropriate expiration
- Wallet signature verification
- Role-based access control
- Smart contract access modifiers

### Data Protection

- Sensitive user data stored only in database, not on blockchain
- HTTPS for all API communications
- Environment variable management for secrets

### Blockchain Security

- Smart contract security best practices
- Reentrancy protection
- Access control modifiers
- Comprehensive test coverage

## Scalability Considerations

### Frontend

- Code splitting for optimized loading
- Caching strategies for blockchain data
- Progressive Web App capabilities

### Backend

- Horizontal scaling with load balancing
- Caching layer for frequent queries
- Background job processing for blockchain interactions

### Blockchain

- Use of Layer 2 solution (Base) for lower fees and faster transactions
- Batch processing where appropriate
- Event-driven architecture to minimize on-chain operations

### Database

- Indexing strategy for common queries
- Partitioning for large tables
- Read replicas for scaling read operations

## Monitoring and Logging

- Structured logging throughout the application
- Centralized log aggregation
- Real-time monitoring dashboard
- Blockchain transaction monitoring
- Mixpanel integration for user analytics

## Deployment Architecture

### Development Environment

- Local development setup with hardhat node
- Docker containers for services
- Environment-specific configuration

### Staging Environment

- Deployed to cloud provider
- Base Goerli testnet for blockchain
- Test database instance
- CI/CD pipeline for automated deployments

### Production Environment

- High-availability cloud deployment
- Base mainnet for blockchain
- Database with backups and failover
- CDN for static assets
- Load balancing and auto-scaling

## Future Architectural Considerations

1. **Microservices**: Potentially breaking down the backend into specialized microservices
2. **Multi-chain Support**: Extending to additional blockchains beyond Base
3. **AI Enhancements**: Deeper integration of AI for property analysis and recommendations
4. **Mobile Applications**: Native mobile apps connecting to the same backend
5. **Decentralized Identity**: Integration with decentralized identity solutions
