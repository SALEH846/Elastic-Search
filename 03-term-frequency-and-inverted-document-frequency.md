### What is an inverted index?
    - Let's say we have two documents for exmaple for which we are trying to build index
        1. Space: The final frontier. These are the voyages...
        2. He's bad, he's number one. He's the space cowboy with the laser gun!
    - Now, an inverted index would not store those strings directly. Instead it sorts of flips on its head.
    - A search engine such as elasticsearch actually splits each document up into its individual search terms.
    - In this example, we will just split it up for each word and convert them to lower case, just to normalize things.
    - Then map each search term to the document that those search terms occur within:
        - space: 1, 2
        - the: 1, 2
        - final: 1
        - frontier: 1
        - he: 2
        - bad: 2
        - ...
    - In practice, it's a little more complicated than this. In reality, it actually stores not only what documents it's in but also the position within the document that it's in.

### How to deal with the concept of relevance?
- TF-IDF means Term Frequency * Inverse Document Frequency.
- Term Frequency is how often a term appears in a given document.
- Document Frequency is how often a term appears in all documents.
- Term Frequency/Document Frequency measures the relevance of a term in a document.