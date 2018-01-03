**##BlockChain Readme**

**Summary:**

This is a simple blockchain project using Python. Users can download and run the program on their computers and simulate participating in a cryptocurrency blockchain. 

The downloadable zip file contains an executable Python program. Running the program launches a "node", an API web service on the localhost of the user. A node can mine Blocks to add to the chain, add transactions, and copy chains of other registered nodes.

The intended user does not need to have Python3 installed in their computer as the zip file contains all the necessary Python libraries required.

The current version of this program will only be suited to Mac OS X users. 


**Requirements:**

- a tool such as cURL or Postman to communicate and post to the API

**Downloading and Running the Program**

1. Download the zip file [here](https://github.com/laurenkwa/SimplePythonBlockChain/blob/master/download/blockchain.zip)

2. Unzip the file and look for the executable called "blockchain". Run "blockchain" and your computer will execute the Python script and host the program at "http://localhost:5000". Make sure that port 5000 in your computer is not in use before running the program.

3. Make requests to the API on your localhost using cURL or Postman. The complete API documentation is found [here](https://documenter.getpostman.com/view/3449197/blockchain/7LqA31d)

4. A registered node that you can add to your BlockChain is hosted at [http://laurenkwa.pythonanywhere.com/](http://laurenkwa.pythonanywhere.com). You can make API requests to this address and sync to it as well.  

**Methodology:**

**I. Theory:**

  **Program Concept:**

  Every computer that runs the program is functioning as a independent "node", capable of creating their own BlockChain. This is done by launching the API on the local host of the user's computer. The user will then make GET and POST requests to the API. 
  
  **The BlockChain**
  
  The BlockChain is made up of Blocks containing transactional information. Each Block has a hashcode that is linked to the previous Block before it. 
  
  A separate Block class and BlockChain class was created for the program. The BlockChain is simply made up of individual Block class objects.
  
  Block is a simple class:
  
    #Defines the Block class
    #a singular Block that makes up a chain
    
    class Block:

    #constructor for a Block
    #parameters::
    #index: index of this block in the BlockChain
    #timestamp: when was this block created
    #transaction: the info for the transaction that the block contains
    #previous_hash: the hash code of the previous block in the chain
    #proof: proof of work
    def __init__(self, index, timestamp, transaction, previous_hash, proof):
        self.index = index
        self.timestamp = timestamp
        self.transaction = transaction
        self.previous_hash = previous_hash
        self.proof = proof
        self.hash = self.hash_block()
        
    #hash_block method hashes the string containing
    #timestamp, transaction info, previous_hash, and proof of this block
    #and creates a hexadecimal hash code 
    def hash_block(self):
        hasher = hashlib.sha256()
        
        string = (str(self.timestamp) + str(self.transaction) + str(self.previous_hash) + str(self.proof))

        block_string = json.dumps(string, sort_keys=True).encode('utf-8') 
        
        return hashlib.sha256(block_string).hexdigest()
    
    #Takes the characteristics of this Block and places it into a Dictionary object
    #that will be stored in the chain
    def toDict(self):
        response = {
            "index": self.index,
            "timestamp": str(self.timestamp),
            "proof": self.proof,
            "transaction": self.transaction,
            "hash": self.hash,
            "previous_hash": self.previous_hash,
        }
        
        return response
        
  
  In creating a new Block, you must provide a Proof of Work(PoW). This is a calculation done when a user "mines" for a Block. The program will then validate if that proof is valid. If valid, your Block is added to the chain and you will be rewarded with one coin for your effort.
  
  The calculation done to find the PoW is simple for this program, unlike the calculations used by Bitcoin and production BlockChains. This is to avoid burdening user computers that want to mine.
  
  Here is the code snippet used to validate the Proof of Work:
  
    #Returns the Proof of Work given the parameters 
    #parameters: 
    ##last_proof: the Proof of Work of the previous block in the chain
    ##previous_hash: the hashcode of the previous block in the chain
    
    def proof_of_work(self, last_proof, previous_hash):

        proof = 0      
        
        #while validate_proof is false, increment proof by 1 until we find the correct proof 
        
        while self.validate_proof(last_proof, previous_hash, proof) is False:
            proof = proof + 1

        return proof
    
    ##Calculates the Proof of Work using the following algorithm: find a number x where if the previous proof, previous hash, and x are hashed together, the hash will begin with 3 leading zeros '000'
    
    def validate_proof(self, last_proof, previous_hash, proof):
        proof_guess = (str(last_proof) + str(previous_hash) + str(proof)).encode('UTF-8') 
        guess_hash = hashlib.sha256(proof_guess).hexdigest()
        
        return guess_hash[:3] == "000"
        
  
  The complete code for the classes with detailed comments can be found [here](https://github.com/laurenkwa/SimplePythonBlockChain/blob/master/Code/classes.py).
  
  
  **Connecting your BlockChain:**
  
  BlockChain requires many independent nodes to communicate with each other. Registering and connecting nodes with your own computer allows you to compare your current BlockChain with others. 
  
  If you do not sync with existing nodes at the beginning and continue mining on your own, you will create your own unique BlockChain that is completely different from other existing nodes. 
  
  This is because one BlockChain only has one starting point or "genesis block", the first block in the BlockChain. To be part of the same BlockChain, nodes must have the same genesis block. 
  
  To sync with others, get their IP address and use the "nodes/register" API request to add them. When done, you can copy their BlockChain, validate your BlockChain against theirs and get a consensus of whose is the "more correct" BlockChain. As of now, the consensus is that longer BlockChains are more authoritative. 
  
  
  **Storing the Chain**
  
  When a node computer stops the program and resumes again, it must retrieve the BlockChain it was working on.
  
  To accomplish this, the chain was stored in a file in the program as bytes using Pickle library and can be retrieved later on again.
  
  Code snippet wherein "text_path" is the file storing the previous chain:
  
    #check if the text file storing previous BlockChain is not empty 
    #if there is an existing chain saved in text file, we will copy that as our current chain
    if os.stat(text_path).st_size > 0:
        file_object = open(text_path, 'rb')
        blockchain.chain = pickle.load(file_object)
        
        
**II. Tools used to build the program:**

  1. [Flask](http://flask.pocoo.org/) - a Python microframework used to build web applications
  
  2. [PyInstaller](http://www.pyinstaller.org/) - a tool to package Python programs into stand-alone executables
    
  
  
  
  
  
  
  






