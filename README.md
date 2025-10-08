# canton-quest-Getting-Started-with-Daml-Development-on-Canton

# 🚀 Canton Quest - Complete Daml Development Guide

This is a step-by-step guide to setting up Daml development on Canton Network using GitHub Codespaces.

## 📋 Prerequisites

- GitHub account
- Basic understanding of command line

## 🛠️ Step-by-Step Instructions

### 1. Creating and Setting Up Codespace

1. Create a new repository on GitHub
2. Add a `README.md` file with any content
3. Open the repository in Codespace ("Code" → "Codespaces")


### 2. Environment Setup in Codespace

In the Codespace terminal, execute the commands:

# System update
sudo apt update

# Install basic utilities
sudo apt install -y curl wget nano jq

# Install Java
sudo apt install -y openjdk-17-jdk

# Install Daml SDK
curl -sSL https://get.daml.com/ | sh -s 2.10.2

# Add Daml to PATH
echo 'export PATH="$HOME/.daml/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Verify installation
daml version
java -version


### 3. Creating Daml Project

# Create new project
daml new json-tests
cd json-tests


### 4. Starting Canton Sandbox and JSON API

# Terminal 1 - Canton Sandbox:
daml build
daml sandbox --wall-clock-time --dar ./.daml/dist/json-tests-0.0.1.dar

# Terminal 2 - JSON API:
cd json-tests
cat > json-api-app.conf << 'CONFIG'
{
  server {
    address = "localhost"
    port = 7575
  }
  ledger-api {
    address = "localhost"
    port = 6865
  }
}
CONFIG

daml json-api --config json-api-app.conf

# Terminal 3 - API Operations:
cd json-tests

export ALICE_JWT='eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJodHRwczovL2RhbWwuY29tL2xlZGdlci1hcGkiOnsibGVkZ2VySWQiOiJzYW5kYm94IiwiYXBwbGljYXRpb25JZCI6IkhUVFAtSlNPTi1BUEktR2F0ZXdheSIsImFjdEFzIjpbIkFsaWNlIl19fQ.FIjS4ao9yu1XYnv1ZL3t7ooPNIyQYAHY3pmzej4EMCM'

# Check readiness
curl -X GET localhost:7575/readyz


### 5. Creating a Party

# Create participant Alice
curl -d '{"identifierHint":"Alice"}' \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $ALICE_JWT" \
     -X POST localhost:7575/v1/parties/allocate | jq

!Save the received partyId!

### 6. Creating a Contract

# Somewhere create create.json file (replace values with yours):
{
  "templateId": "YOUR_PACKAGE_ID:Main:Asset",
  "payload": {
    "issuer": "YOUR_PARTY_ID",
    "owner": "YOUR_PARTY_ID",
    "name": "Example Asset Name"
  }
}

# Get packageId:
curl -H "Authorization: Bearer $ALICE_JWT" -X GET "localhost:7575/v1/packages" | jq

# Create contract:
curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $ALICE_JWT" \
     -d @create.json \
     -X POST localhost:7575/v1/create | jq


### 7. Querying the Ledger

# Create query.json:
{
  "templateIds": ["YOUR_PACKAGE_ID:Main:Asset"]
}

# Execute query:
curl -H "Content-Type: application/json" \
     -H "Authorization: Bearer $ALICE_JWT" \
     -d @query.json \
     -X POST localhost:7575/v1/query | jq


🔒 Security
All confidential data (tokens, IDs) are excluded from the repository via .gitignore     

🔗 Useful Links
Daml Documentation
https://docs.daml.com/

Canton Network
https://www.canton.network/

GitHub Codespaces
https://docs.github.com/en/codespaces


THK and GL
