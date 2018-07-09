
# Merkle Roots


```python
# Block Merkle Root Example

from helper import double_sha256, merkle_parent_level, merkle_root
tx_hex_hashes = [
    '42f6f52f17620653dcc909e58bb352e0bd4bd1381e2955d19c00959a22122b2e',
    '94c3af34b9667bf787e1c6a0a009201589755d01d02fe2877cc69b929d2418d4',
    '959428d7c48113cb9149d0566bde3d46e98cf028053c522b8fa8f735241aa953',
    'a9f27b99d5d108dede755710d4a1ffa2c74af70b4ca71726fa57d68454e609a2',
    '62af110031e29de1efcad103b3ad4bec7bdcf6cb9c9f4afdd586981795516577',
    '766900590ece194667e9da2984018057512887110bf54fe0aa800157aec796ba',
    'e8270fb475763bc8d855cfe45ed98060988c1bdcad2ffc8364f783c98999a208',
]
current_level = [bytes.fromhex(x)[::-1] for x in tx_hex_hashes]
print(merkle_root(current_level)[::-1].hex())
```

### Try it

#### Validate the merkle root for this block on Testnet:
Block Hash:
```
0000000000000451fa80fcdb243b84c35eaae215a85a8faa880559e8239e6f20
```

https://api.blockcypher.com/v1/btc/test3/blocks/0000000000000451fa80fcdb243b84c35eaae215a85a8faa880559e8239e6f20

Transaction Hashes:
```
42f6f52f17620653dcc909e58bb352e0bd4bd1381e2955d19c00959a22122b2e
94c3af34b9667bf787e1c6a0a009201589755d01d02fe2877cc69b929d2418d4
959428d7c48113cb9149d0566bde3d46e98cf028053c522b8fa8f735241aa953
a9f27b99d5d108dede755710d4a1ffa2c74af70b4ca71726fa57d68454e609a2
62af110031e29de1efcad103b3ad4bec7bdcf6cb9c9f4afdd586981795516577
766900590ece194667e9da2984018057512887110bf54fe0aa800157aec796ba
e8270fb475763bc8d855cfe45ed98060988c1bdcad2ffc8364f783c98999a208
921b8cfd3e14bf41f028f0a3aa88c813d5039a2b1bceb12208535b0b43a5d09e
15535864799652347cec66cba473f6d8291541238e58b2e03b046bc53cfe1321
1c8af7c502971e67096456eac9cd5407aacf62190fc54188995666a30faf99f0
3311f8acc57e8a3e9b68e2945fb4f53c07b0fa4668a7e5cda6255c21558c774d
```


```python
# Exercise 5.1

from helper import double_sha256, merkle_root

tx_hex_hashes = [
    '42f6f52f17620653dcc909e58bb352e0bd4bd1381e2955d19c00959a22122b2e',
    '94c3af34b9667bf787e1c6a0a009201589755d01d02fe2877cc69b929d2418d4',
    '959428d7c48113cb9149d0566bde3d46e98cf028053c522b8fa8f735241aa953',
    'a9f27b99d5d108dede755710d4a1ffa2c74af70b4ca71726fa57d68454e609a2',
    '62af110031e29de1efcad103b3ad4bec7bdcf6cb9c9f4afdd586981795516577',
    '766900590ece194667e9da2984018057512887110bf54fe0aa800157aec796ba',
    'e8270fb475763bc8d855cfe45ed98060988c1bdcad2ffc8364f783c98999a208',
    '921b8cfd3e14bf41f028f0a3aa88c813d5039a2b1bceb12208535b0b43a5d09e',
    '15535864799652347cec66cba473f6d8291541238e58b2e03b046bc53cfe1321',
    '1c8af7c502971e67096456eac9cd5407aacf62190fc54188995666a30faf99f0',
    '3311f8acc57e8a3e9b68e2945fb4f53c07b0fa4668a7e5cda6255c21558c774d',
]

# bytes.fromhex and reverse ([::-1]) to get all the hashes in binary
hashes = [bytes.fromhex(h)[::-1] for h in tx_hex_hashes]
# get the merkle root
root = merkle_root(hashes)
# hex() the reversed root
print(root[::-1].hex())
```

### Test Driven Exercise


```python
from block import Block
from io import BytesIO

class Block(Block):
    
    def validate_merkle_root(self):
        '''Gets the merkle root of the tx_hashes and checks that it's
        the same as the merkle root of this block.
        '''
        # reverse all the transaction hashes (self.tx_hashes)
        hashes = [h[::-1] for h in self.tx_hashes]
        # get the Merkle Root
        root = merkle_root(hashes)
        # reverse the Merkle Root
        # return whether self.merkle root is the same as 
        # the reverse of the calculated merkle root
        return root[::-1] == self.merkle_root

    def calculate_merkle_tree(self):
        '''Calculate and store the entire Merkle Tree'''
        # store the result in self.merkle_tree, an array, 0 representing
        # the bottom level and 1 the parent level of level 0 and so on.
        # initialize self.merkle_tree to be an empty list
        self.merkle_tree = []
        # reverse all the transaction hashes (self.tx_hashes) store as current level
        current_level = [h[::-1] for h in self.tx_hashes]
        # while there is more than 1 hash:
        while len(current_level) > 1:
            # store current level in self.merkle_tree
            self.merkle_tree.append(current_level)
            # Make current level Merkle Parent level
            current_level = merkle_parent_level(current_level)
```
