---
creation date: 2022-04-15 17:16
aliases: 
tags: 🖥️
---

###### Description [Trie Implementation](Trie%20Implementation.md)


###### Implementation
```js

class TrieNode {
  constructor() {
    this.children = {};
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }

  // takes a word to search for in trie and returns node containing with last letter of word
  search(word) {
    let currentNode = this.root
    for (const char of word) {
      if (!currentNode.children[char]) return null
      currentNode = currentNode.children[char]
    }
    return currentNode
  }

  insert(word) {
    let currentNode = this.root
    const letters = word.split('')

    for (let i = 0; i < letters.length; i++) {
      if (!currentNode.children[letters[i]]) {
        let newNode = new TrieNode()
        currentNode.children[letters[i]] = newNode
      }
      currentNode = currentNode.children[letters[i]]
    }
    currentNode.children['*'] = null
    return currentNode

  }

  // collects trie words in array starting from a particular node, stops when it reaches *
  collectAllWords(node=null, word="", words=[]) {
    let currentNode = node || this.root // start at given node or root

    // iterate through all children, building up a 'word'
    for (let [key, value] of Object.entries(currentNode.children)) {
      if (key === '*') {
        words.push(word) // add completed word to array
      } else {
        this.collectAllWords(currentNode.children[key], word+key, words)
      }
    }
    return words
  }

  autocomplete(prefix) {
    // returns node in trie that is final character in prefix, if exists
    let currentNode = this.search(prefix)

    if (!currentNode) return null
    return this.collectAllWords(currentNode)
  }

  traverse(currentNode=this.root) {

    for (let [key, value] of Object.entries(currentNode.children)) {
      console.log('letter', key);
      if (key !== '*') {
        return this.traverse(currentNode.children[key])
      } 
    }
  }

  autocorrect(word) {
    let wordFoundSoFar = ""
    let currentNode = this.root
    
    for (const char of word) {
      if (!currentNode.children[char]) {
        // concatenate user's word found in trie with first suffix that descend from the current node
        return wordFoundSoFar + this.collectAllWords(currentNode)[0]
      }
      // loop through letters in trie and build word for letters in trie
      currentNode = currentNode.children[char]
      wordFoundSoFar += char
    }
    // if user's word exists in trie, return it.
    return word
  }
}

let trie = new Trie()
trie.insert('cat')
trie.insert('catnip')
trie.insert('catnap')

console.log(trie.autocorrect('catnar')); // returns 'catnap

```

