# PySparNN
Sparse (approximate) nearest neighbor search for python! This library is well suited to finding nearest neighbors in sparse, high dimensional spaces (like a text documents). 

Out of the box, PySparNN supports Cosine Similarity.

PySparNN can be easily extended with abritrary similarity metrics (Manhattan, Eculidian, Jaccard, etc).

If your data is NOT SPARSE & you don't require a custom distance function - please consider [annoy](https://github.com/spotify/annoy). Annoy uses a similar-ish method and I am a big fan of it. As of this writing, annoy performs ~8x faster on their introductory example. 
General rule of thumb - annoy performs better if you can get your data to fit into memory (as a dense vector).


The most comparable library to PySparNN is scikit-learn's LSHForrest module. As of this writing, PySparNN is ~25% faster on the 20newsgroups dataset. [Here is the comparison.](https://github.com/facebookresearch/pysparnn/blob/master/sparse_search_comparison.ipynb)

Notes:
* A future update may allow incremental insertions.

## Example Usage
```
import pysparnn as snn

data = [
    'hello world',
    'oh hello there',
    'Play it',
    'Play it again Sam',
]    

# build a feature representation for each sentence
def scentence2features(scentence):
    features = dict()
    for word in scentence.split():
        features[word] = 1
    return features

features_list = []
for sentence in data:
    features_list.append(scentence2features(sentence))

# build the search index!
cp = snn.ClusterIndex(features_list, data)

# search the index
search_items = [
    scentence2features('oh there'),
    scentence2features('Play it again Frank')
]

cp.search(search_items, threshold=0.50, k=1, return_similarity=False)
>> [['oh hello there'], ['Play it again Sam']]

```

## Requirements
PySparNN requires numpy and scipy. Tested with numpy 1.10.4 and scipy 0.17.0.

## How PySparNN works
Searching for a document in an collection of K documents is naievely O(K) (assuming documents are constant sized). 

However! we can create a tree structure where the first level is O(sqrt(K)) and each of the leaves are also O(sqrt(K)) - on average.

We randomly pick sqrt(K) candidate items to be in the top level. Then -- each document in the full list of K documents is assigned to the closest candidate in the top level.

This breaks up one O(K) search into two O(sqrt(K)) searches which is much much faster when K is big!

## Further Information
http://nlp.stanford.edu/IR-book/html/htmledition/cluster-pruning-1.html

See the CONTRIBUTING file for how to help out.

## License
PySparNN is BSD-licensed. We also provide an additional patent grant.