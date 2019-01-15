---
layout: post
title:  "Extract Keywords from Text Snippets using TF-IDF and Scikit-Learn"
date:   2019-01-15
---

TF-IDF, which stands for **term frequency–inverse document frequency**, is a statistical method that determines the relative importance of a word in a snippet in the context of a list of snippets. We can use it to find keywords.

TF-IDF will give low scores to words in a snippet that are too frequent (eg. stopwords like "a", "and", "the") and also words that do not occur too often in the snippet. TF-IDF will give high scores to words that are relatively rare in the context of other snippets but also occur often in the given snippet.

TF-IDF keyword extraction is useful for:
- Automatic tag generation of a social media post
- Finding websites similar to a given website (document similarity)
- Bag-of-word text classification models
- ...and many more!

We will use the IMDB movie dataset that you can download from [Kaggle](https://www.kaggle.com/PromptCloudHQ/imdb-data).


```python
import numpy as np
import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer
```


```python
df = pd.read_csv('IMDB-Movie-Data.csv')[['Title', 'Description']]
df.sample(5)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Title</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>642</th>
      <td>The Ridiculous 6</td>
      <td>An outlaw who was raised by Native Americans d...</td>
    </tr>
    <tr>
      <th>733</th>
      <td>The Da Vinci Code</td>
      <td>A murder inside the Louvre and clues in Da Vin...</td>
    </tr>
    <tr>
      <th>951</th>
      <td>The Descendants</td>
      <td>A land baron tries to reconnect with his two d...</td>
    </tr>
    <tr>
      <th>938</th>
      <td>The Siege of Jadotville</td>
      <td>Irish Commandant Pat Quinlan leads a stand off...</td>
    </tr>
    <tr>
      <th>639</th>
      <td>American Reunion</td>
      <td>Jim, Michelle, Stifler, and their friends reun...</td>
    </tr>
  </tbody>
</table>
</div>



## Snippet vectorization (preprocessing + tokenization)

Raw descriptions aren't too useful. We need to process the descriptions into a usable format, ie. clean and tokenize. Luckily for us, `scikit-learn` takes care of this for us. When we call `vectorizer.fit_transform()` on our raw descriptions, it will clean and tokenize the text under-the-hood.


```python
vectorizer = TfidfVectorizer()
snippet_scores = vectorizer.fit_transform(df.Description)

# Note we have 1000 snippets with 5897 unique words (or tokens)
snippet_scores.shape
```




    (1000, 5897)



## Keyword extraction

Features in the below cell means the words (or tokens) found in the text. We call `vectorizer.get_feature_names()` so we can save them into an array which we will use later to retrieve the keyword from its index.


```python
features = np.array(vectorizer.get_feature_names())
np.random.choice(features, 5)
```




    array(['cleanses', 'sky', 'inflatable', 'except', 'pandemic'], dtype='<U17')



Next we do a bit of array mangling in order to extract out the keywords.


```python
def top_n_keywords(scores, features, n=5):
    tfidf_sorting = np.argsort(scores.toarray()).flatten()[::-1]
    return features[tfidf_sorting][:n]
```


```python
df['Top 5 Keywords'] = [top_n_keywords(scores, features) for scores in snippet_scores]
df.sample(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Title</th>
      <th>Description</th>
      <th>Top 5 Keywords</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>16</th>
      <td>Hacksaw Ridge</td>
      <td>WWII American Army Medic Desmond T. Doss, who ...</td>
      <td>[american, desmond, medic, medal, doss]</td>
    </tr>
    <tr>
      <th>756</th>
      <td>Lavender</td>
      <td>After losing her memory, a woman begins to see...</td>
      <td>[her, suggests, psychiatrist, unexplained, visit]</td>
    </tr>
    <tr>
      <th>744</th>
      <td>We Are Your Friends</td>
      <td>Caught between a forbidden romance and the exp...</td>
      <td>[cole, dj, fame, carter, fortune]</td>
    </tr>
    <tr>
      <th>607</th>
      <td>Horrible Bosses</td>
      <td>Three friends conspire to murder their awful b...</td>
      <td>[awful, they, bosses, conspire, standing]</td>
    </tr>
    <tr>
      <th>982</th>
      <td>Across the Universe</td>
      <td>The music of the Beatles and the Vietnam War f...</td>
      <td>[beatles, upper, liverpudlian, backdrop, poor]</td>
    </tr>
  </tbody>
</table>
</div>
