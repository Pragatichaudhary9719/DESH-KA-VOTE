# DESH-KA-VOTE
import hashlib
import json
from time import time
from uuid import uuid4

class Blockchain:
    def __init__(self):
        self.chain = []
        self.current_identities = []

        # Create the genesis block
        self.new_block(previous_hash='1', proof=100)

    def new_block(self, proof, previous_hash=None):
        block = {
            'index': len(self.chain) + 1,
            'timestamp': time(),
            'identities': self.current_identities,
            'proof': proof,
            'previous_hash': previous_hash or self.hash(self.chain[-1]),
        }

        # Reset the current list of identities
        self.current_identities = []
        self.chain.append(block)
        return block

    def new_identity(self, name, date_of_birth):
        identity_id = str(uuid4())
        self.current_identities.append({
            'id': identity_id,
            'name': name,
            'date_of_birth': date_of_birth,
        })
        return self.last_block['index'] + 1, identity_id

    @staticmethod
    def hash(block):
        block_string = json.dumps(block, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()

    @property
    def last_block(self):
        return self.chain[-1]

    def verify_identity(self, identity_id):
        for block in self.chain:
            for identity in block['identities']:
                if identity['id'] == identity_id:
                    return identity
        return None

# Example usage:
blockchain = Blockchain()
_, identity_id1 = blockchain.new_identity('Alice', '1990-01-01')
_, identity_id2 = blockchain.new_identity('Bob', '1992-02-02')
blockchain.new_block(proof=12345)

print(json.dumps(blockchain.chain, indent=4))

# Verify identities
print(blockchain.verify_identity(identity_id1))
print(blockchain.verify_identity(identity_id2))



from flask import Flask, request, jsonify, render_template
from blockchain import Blockchain

app = Flask(__name__)
blockchain = Blockchain()

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/register', methods=['POST'])
def register():
    name = request.form['name']
    date_of_birth = request.form['date_of_birth']
    address = request.form['address']
    
    _, identity_id = blockchain.new_identity(name, date_of_birth, address)
    blockchain.new_block(proof=12345)
    
    return jsonify({'message': 'Identity registered', 'identity_id': identity_id})

if __name__ == '__main__':
    app.run(debug=True)





import hashlib
import json
from time import time
from uuid import uuid4

class Blockchain:
    def __init__(self):
        self.chain = []
        self.current_identities = []

        # Create the genesis block
        self.new_block(previous_hash='1', proof=100)

    def new_block(self, proof, previous_hash=None):
        block = {
            'index': len(self.chain) + 1,
            'timestamp': time(),
            'identities': self.current_identities,
            'proof': proof,
            'previous_hash': previous_hash or self.hash(self.chain[-1]),
        }

        # Reset the current list of identities
        self.current_identities = []
        self.chain.append(block)
        return block

    def new_identity(self, name, date_of_birth, address):
        identity_id = str(uuid4())
        self.current_identities.append({
            'id': identity_id,
            'name': name,
            'date_of_birth': date_of_birth,
            'address': address,
        })
        return self.last_block['index'] + 1, identity_id

    @staticmethod
    def hash(block):
        block_string = json.dumps(block, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()

    @property
    def last_block(self):
        return self.chain[-1]

    def verify_identity(self, identity_id):
        for block in self.chain:
            for identity in block['identities']:
                if identity['id'] == identity_id:
                    return identity
        return None


