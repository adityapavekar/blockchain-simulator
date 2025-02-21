#Project Name : 
               Blockchain Simulator
#Table of content:
               Genesis block creation - Successfully created with the correct proof-of-work
               Transaction handling - Successfully added and mined multiple transactions
               Chain validation - Working correctly, detecting valid and invalid states
               Tampering detection - Successfully detected tampering attempts
               Hash verification - Properly demonstrates how changes affect block hashes

//blockchain.py
import hashlib
import json
import time
from datetime import datetime

class Block:
    def __init__(self, index, transactions, previous_hash):
        """
        Initialize a new block in the blockchain.
        
  Args:
            index (int): Block number in the chain
            transactions (list): List of transactions in the block
            previous_hash (str): Hash of the previous block
        """
        self.index = index
        self.timestamp = datetime.utcnow().isoformat()
        self.transactions = transactions
        self.previous_hash = previous_hash
        self.nonce = 0
        self.hash = self.calculate_hash()

   def calculate_hash(self):
        """Calculate the hash of the block using SHA-256."""
        block_string = json.dumps({
            "index": self.index,
            "timestamp": self.timestamp,
            "transactions": self.transactions,
            "previous_hash": self.previous_hash,
            "nonce": self.nonce
        }, sort_keys=True)
        
   return hashlib.sha256(block_string.encode()).hexdigest()

   def mine_block(self, difficulty):
        """
        Implement proof of work by finding a hash with leading zeros.
        
   Args:
            difficulty (int): Number of leading zeros required
        """
        target = "0" * difficulty
        
  while self.hash[:difficulty] != target:
            self.nonce += 1
            self.hash = self.calculate_hash()
        
   print(f"Block {self.index} mined! Hash: {self.hash}")

class Blockchain:
    def __init__(self, difficulty=4):
        """
        Initialize the blockchain with genesis block.
        
  Args:
            difficulty (int): Mining difficulty (number of leading zeros)
        """
        self.chain = []
        self.difficulty = difficulty
        self.pending_transactions = []
        self.create_genesis_block()

   def create_genesis_block(self):
        """Create the first block in the chain."""
        genesis_block = Block(0, ["Genesis Block"], "0")
        genesis_block.mine_block(self.difficulty)
        self.chain.append(genesis_block)

   def get_latest_block(self):
        """Return the most recent block in the chain."""
        return self.chain[-1]

   def add_transaction(self, transaction):
        """Add a transaction to pending transactions."""
        self.pending_transactions.append(transaction)

   def mine_pending_transactions(self):
        """Create a new block with pending transactions and add it to the chain."""
        block = Block(
            len(self.chain),
            self.pending_transactions,
            self.get_latest_block().hash
        )
        
   block.mine_block(self.difficulty)
        self.chain.append(block)
        self.pending_transactions = []

 def is_chain_valid(self):
        """
   Validate the integrity of the blockchain.
        
  Returns:
            bool: True if chain is valid, False otherwise
        """
  for i in range(1, len(self.chain)):
            current_block = self.chain[i]
            previous_block = self.chain[i-1]

    # Verify current block's hash
  if current_block.hash != current_block.calculate_hash():
                print(f"Invalid hash in block {i}")
                return False

    # Verify chain linkage
   if current_block.previous_hash != previous_block.hash:
                print(f"Chain break between blocks {i-1} and {i}")
                return False

   return True

  def print_chain(self):
        """Print the entire blockchain in a readable format."""
        for block in self.chain:
            print("\n" + "="*50)
            print(f"Block #{block.index}")
            print(f"Timestamp: {block.timestamp}")
            print(f"Transactions: {block.transactions}")
            print(f"Previous Hash: {block.previous_hash}")
            print(f"Hash: {block.hash}")
            print(f"Nonce: {block.nonce}")




#cli.py
from blockchain import Blockchain
import sys

def print_menu():
    """Print the main menu options."""
    print("\n=== Blockchain Simulator Menu ===")
    print("1. Add new transaction")
    print("2. Mine block")
    print("3. Print blockchain")
    print("4. Validate chain")
    print("5. Tamper with block (demo)")
    print("6. Exit")

def main():
    # Initialize blockchain with difficulty 4
    blockchain = Blockchain(4)
    
 while True:
        print_menu()
        choice = input("Enter your choice (1-6): ")
        
  if choice == "1":
            transaction = input("Enter transaction data: ")
            blockchain.add_transaction(transaction)
            print("Transaction added to pending transactions")
            
  elif choice == "2":
            if not blockchain.pending_transactions:
                print("No pending transactions to mine!")
  else:
                print("Mining new block...")
                blockchain.mine_pending_transactions()
                print("Block mined and added to chain!")
                
  elif choice == "3":
            blockchain.print_chain()
            
  elif choice == "4":
            if blockchain.is_chain_valid():
                print("Blockchain is valid!")
   else:
                print("Blockchain is invalid!")
                
  elif choice == "5":
            if len(blockchain.chain) < 2:
                print("Need at least 2 blocks to demonstrate tampering!")
                continue
                
            # Tamper with the second block's transactions
          
  blockchain.chain[1].transactions.append("Tampered Transaction!")
            print("Block 1 has been tampered with!")
            print("Validating chain...")
            blockchain.is_chain_valid()
            
  elif choice == "6":
            print("Exiting...")
            sys.exit(0)
            
  else:
           print("Invalid choice! Please try again.")

if __name__ == "__main__":
    main()




#test_scenario.py
from blockchain import Blockchain

def run_test_scenarios():
    """Run various test scenarios to demonstrate blockchain functionality."""
    print("=== Running Blockchain Test Scenarios ===")
    
    # Initialize blockchain
  print("\nScenario 1: Creating blockchain with genesis block")
    blockchain = Blockchain(difficulty=2)  # Lower difficulty for faster tests
    blockchain.print_chain()
    
    # Add and mine blocks
  print("\nScenario 2: Adding transactions and mining blocks")
    blockchain.add_transaction("Alice sends 10 BTC to Bob")
    blockchain.add_transaction("Bob sends 5 BTC to Charlie")
    blockchain.mine_pending_transactions()
    
   blockchain.add_transaction("Charlie sends 3 BTC to David")
    blockchain.mine_pending_transactions()
    blockchain.print_chain()
    
    # Validate chain
   print("\nScenario 3: Validating blockchain integrity")
    print(f"Is chain valid? {blockchain.is_chain_valid()}")
    
    # Demonstrate tampering detection
   print("\nScenario 4: Demonstrating tampering detection")
    print("Tampering with block 1 transactions...")
    blockchain.chain[1].transactions.append("Evil transaction!")
    print(f"Is chain valid after tampering? {blockchain.is_chain_valid()}")
    
    # Demonstrate hash changes
   print("\nScenario 5: Demonstrating hash changes")
    original_hash = blockchain.chain[1].hash
    blockchain.chain[1].transactions.append("Another transaction")
    blockchain.chain[1].hash = blockchain.chain[1].calculate_hash()
    print(f"Original hash: {original_hash}")
    print(f"New hash after modification: {blockchain.chain[1].hash}")

if __name__ == "__main__":
    run_test_scenarios()



