---
layout: post
title:  "Using Annoy to Cluster GloVe Vectors"
date:   2019-02-05
---

Annoy can efficiently cluster any arbitrary float-valued vector of around 100 dimensions or less. We create a simple example to demonstrate how this library can be used to find similar words based on GloVe vectors.

Usually with k-means, you cannot work with vectors of larger than 5 or so dimensions. And unlike with MinHashLSH, this method works with float values instead of being limited by bit vectors.

## Prepare the GloVe vectors


```python
from annoy import AnnoyIndex
import numpy as np
```

Download the GloVe vectors.


```python
!wget http://nlp.stanford.edu/data/glove.6B.zip && unzip glove.6B.zip
```

Load the GloVe vectors.


```python
VECTOR_DIM = 50

word_vectors = {}
with open(f'glove.6B.{VECTOR_DIM}d.txt') as fp:
    for line in fp:
        word = line.split()[0]
        values = np.array(line.split()[1:]).astype(float)
        word_vectors[word] = values
```

Set aside some words for testing. These words will not be seen by the Annoy index.


```python
test_vectors = {}
for word in ('cat', 'toaster', 'nintendo'):
    test_vectors[word] = word_vectors[word]
    del word_vectors[word]
```

## Create the index


```python
words, vectors = zip(*word_vectors.items())

index = AnnoyIndex(VECTOR_DIM)

for idx, vector in enumerate(vectors):
    index.add_item(idx, vector)

index.build(30)
index.save('test.ann')
```

```python
index = AnnoyIndex(VECTOR_DIM)
index.load('test.ann')
```




## Results


```python
def print_nearest(word):
    for idx in index.get_nns_by_vector(test_vectors[word], 10):
        print(words[idx])
```


```python
print_nearest('cat')
```

    dog
    rabbit
    monkey
    rat
    cats
    snake
    pet
    mouse
    bite
    shark



```python
print_nearest('toaster')
```

    bakes
    self-replicating
    fondue
    souffle
    ssaa
    13-inch
    pomerelle
    espresso
    blenders
    burners



```python
print_nearest('nintendo')
```

    playstation
    xbox
    gamecube
    sega
    wii
    consoles
    ds
    ps3
    dreamcast
    ps2


We can also see that the Annoy index is fast!


```python
%timeit index.get_nns_by_vector(test_vectors['cat'], 10);
```

    12.1 µs ± 289 ns per loop (mean ± std. dev. of 7 runs, 100000 loops each)
