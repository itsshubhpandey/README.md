# README.md
import hashlib
import time

# Define the Block structure
class Block:
    def __init__(self, index, transactions, previous_hash):
        self.index = index
        self.timestamp = time.time()  # Current time
        self.transactions = transactions
        self.previous_hash = previous_hash
        self.nonce = 0  # Used for proof-of-work
        self.hash = self.calculate_hash()
    
   def calculate_hash(self):
        # Combine block data and hash it
        block_data = (str(self.index) + 
                      str(self.timestamp) + 
                      str(self.transactions) + 
                      str(self.previous_hash) + 
                      str(self.nonce))
        return hashlib.sha256(block_data.encode()).hexdigest()

  def mine_block(self, difficulty):
        # Proof-of-work: Find a hash starting with 'difficulty' number of zeros
        target = "0" * difficulty
        while self.hash[:difficulty] != target:
            self.nonce += 1
            self.hash = self.calculate_hash()

# Define the Blockchain
class Blockchain:
    def __init__(self):
        self.chain = [self.create_genesis_block()]
        self.difficulty = 4  # Adjust difficulty for mining
    
   def create_genesis_block(self):
        # The first block in the chain, with no previous hash
        return Block(0, "Genesis Block", "0")
    
  def get_latest_block(self):
        return self.chain[-1]
    
   def add_block(self, transactions):
        # Add a new block to the chain
        latest_block = self.get_latest_block()
        new_block = Block(len(self.chain), transactions, latest_block.hash)
        new_block.mine_block(self.difficulty)  # Mine the block
        self.chain.append(new_block)
    
  def is_chain_valid(self):
        # Check if all blocks link correctly
        for i in range(1, len(self.chain)):
            current_block = self.chain[i]
            previous_block = self.chain[i - 1]
            
            # Check if current block's hash is correct
   if current_block.hash != current_block.calculate_hash():
                return False
            
            # Check if current block's previous hash matches the previous block's hash
   if current_block.previous_hash != previous_block.hash:
                return False
        
  return True

# Simulate the Blockchain
my_blockchain = Blockchain()

# Add some blocks with dummy transactions
my_blockchain.add_block("Transaction 1")
my_blockchain.add_block("Transaction 2")
my_blockchain.add_block("Transaction 3")

# Print the blockchain
for block in my_blockchain.chain:
    print(f"Block {block.index}:")
    print(f"  Timestamp: {block.timestamp}")
    print(f"  Transactions: {block.transactions}")
    print(f"  Previous Hash: {block.previous_hash}")
    print(f"  Current Hash: {block.hash}\n")

# Tamper with the blockchain and validate
my_blockchain.chain[1].transactions = "Tampered Data"
print("Is blockchain valid after tampering?", my_blockchain.is_chain_valid())
