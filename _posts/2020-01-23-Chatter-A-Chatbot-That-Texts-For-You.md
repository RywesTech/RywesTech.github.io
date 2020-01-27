---
layout: post
Title: Chatter: A chatbot that texts for you.
---
# Chatter: A chatbot that texts for you.

If you have ever used Siri, Amazon Alexa, or Cortana before, then you have interacted with a chatbot. According to Wikipedia, a chatbot is a "piece of software that conducts a conversation via auditory or textual methods. Such programs are often designed to convincingly simulate how a human would behave as a conversational partner." Many companies are starting to use chatbots as a more efficient method for interacting with customers, which makes then an incredibly interesting area of research. However, I'm going to be taking a much less commercial approach. I'm going to be creating a chatbot that responds to people over text for me.

The two largest types of learning-based chatbots are either **generative** or **retrieval-based**. Generative chatbot are more complex to develop, and typically use neural networks to create their own language. Retrieval-based chatbots use a large corpus to learn how to respond to queries and prompts. Due to the timespan of this project, we will be building a retrieval-based bot.

## Import NLTK

NLTK is the framework that we're going to be using to build out chatbot on. It contains functions to lemmatize tokens, generate token vectors, and pre-process our data. If these terms don't make sense, keep on reading! If you want to check out the NLTK docs, [click here](https://www.nltk.org/). Here's a full list of the libraries we'll be using:

```python
import os # file loading
import io # file loading
import nltk # our core library
import random
import string
import warnings
import numpy as np
import pandas as pd

from sklearn.feature_extraction.text import TfidfVectorizer # TFIDF
from sklearn.metrics.pairwise import cosine_similarity # How we find similar texts (more on this later)
```

## Pre-processing the Corpus

The first step in a retrieval-based chatbot is to pre-process the corpus. Since english is a crazy language, full of weird words and sentence structures, we have to simplify the language down for the computer. Additionally, computers understand numbers, not text. Because of this, we need to convert each of the tokens (words) in the corpus to vectors. This pre-processing phase can be broken down into five steps:

### Normalization
The first step is to make the entire corpus one case. This is due to the fact that computers will read "Hello World" and "hello world" as different strings. However, in reality, they will mean the same thing. We can achieve this with one simple line of code (thanks, Python!):
```python
raw = raw.lower() # Raw is our corpus
```

### Tokenization
This step is really pretty simple. In tokenization, we split up each token (word) in the corpus into a big list. Sometimes, this is done with sentences too so they can be processed on their own.

### Noise removal
For a computer to understand the sentence, "Hey man! What's up??" it really only needs to see "hey man whats up". Although this looks grammatically incorrect to us, the computer sees it as being simpler. The NLTK package has a built-in function to do this for us.

### Remove stop words
Similar to the previous step, we want to remove words that don't add much to the meaning of the sentence. For example, the sentence "the quick brown fox jumped over the lazy dog" could be understood with just "quick brown fox jumped over lazy dog." This removes noise from common words like "the," "it," "she," etc...

### Stemming/Lemmatization
In this step, we replace words with their lemmas. A lemma is similar to a synonym, and one lemma will relate to multiple words. For example, Instruct, Informed, Teaching, Educated, Taught will all have the same lemma of "teach." Doing this simplifies our corpus, and makes it easier for the computer to understand. We can do this with the following code:

```python
lemmer = nltk.stem.WordNetLemmatizer() # The built-in NLTK lemmer

def LemTokens(tokens): # recursive function that returns the lemma for each word in the list
    return [lemmer.lemmatize(token) for token in tokens]

remove_punct_dict = dict((ord(punct), None) for punct in string.punctuation) # built-in-function to remove punctuation.
# Sice this is a texting bot, we're going to leave in other things like emojis.

def LemNormalize(text): # function to normalize the case of the text using the built-in .lower() function.
    return LemTokens(nltk.word_tokenize(text.lower().translate(remove_punct_dict)))
```

### Project-specific pre-processing
Depending on what the chatbot is being built for, further processing may be required. In my case, I'm building a chatbot that talks like me based on the texts that I've sent. Because of this, we need some additional processing. My first step was to extract the texts and load them into a CSV file. This was done mostly with open-source scripts and the scope of it is outside of this project (it may be a blog post for a later time, though)! Here is the heart of my pre-processing code, where the other person's texts and my texts are treated separately.

```python
msgs_them = person_msgs[person_msgs['is_sent'] == 0] # other person's messages are is_sent = 0
msgs_me = person_msgs[person_msgs['is_sent'] == 1] # my messages are is_sent = 1

# Normalize the texts using .lower():
msgs_them['text'] = msgs_them['text'].str.lower()
msgs_me['text'] = msgs_me['text'].str.lower()

# Split into sentence tokens (each text message is treated as one sentence):
sent_tokens_them = msgs_them['text'].to_list()
sent_tokens_me = msgs_me['text'].to_list()

# Put into new-line seperated lists:
raw_them = '.\n'.join(sent_tokens_them)
raw_me = '.\n'.join(sent_tokens_me)
separately
# Take out each word and make their own list:
word_tokens_them = nltk.word_tokenize(raw_them)# converts to list of words
word_tokens_me = nltk.word_tokenize(raw_me)# converts to list of words
```

## Developing the response function
Now that we have our processed text, we need to create the heart of our chatbot. In a nutshell, this function receives the text input in the form of a string, finds a similar text message that that person has previously sent me, finds out how I responded to that message, and then returns that response in the form of a string.

So first we define our function, append the latest message to the list of messages that the other person has said, and then use TF-IDF to find similar messages. TF-IDF, also knows as "Term frequency, Inverse term frequency," is the method we use to find similar vectors. The TF term is equal the number of times a specific word appears in a corpus divided by the total number of words in the corpus. The problem with just using a TF term is that common words will obscure less common words. To overcome this, we also look at the IDF (inverse document frequency). IDF is equal to 1+log(N/n), where N = total number of documents and n = number of documents that word appears in.

```python
def response(user_response):
    nlp_response=''
    sent_tokens_them.append(user_response)

    # Same TFIDF code as above
    TfidfVec = TfidfVectorizer(tokenizer=LemNormalize, stop_words='english')
    tfidf = TfidfVec.fit_transform(sent_tokens_them)
```

Once TF-IDF creates our two vectors, we need to find the similarities between vectors. This can be achieved through cosine similarity. According to Wikipedia, we can obtain the cosine similarity of any pair of vectors by taking their dot product and dividing that by the product of their norms. That yields the cosine of the angle between the vectors. In result, this is a measure of their similarity. The image below illustrates this concept. To learn more, check out the really great document [here](https://janav.wordpress.com/2013/10/27/tf-idf-and-cosine-similarity/).

![Oops! This didn't load.](/images/cosine.png)

```python
vals = cosine_similarity(tfidf[-1], tfidf)
idx = vals.argsort()[0][-2]
```

Now that we have a list with similar messages, we'll look at the most similar one and find out how I responded to that message. We'll look back into the data tables that we created earlier, and match the `sent_date`s.
```python
sentdate = msgs_them.iloc[idx]['message_date'] # Now that we have the index of their closest message, find when it was sent

# Find the message that I used to respond to that text, and return it:
messages_after = msgs_me[msgs_me['message_date'] >= sentdate]
my_response = messages_after.iloc[0]['text']

flat = vals.flatten() # flatten the 2D list to a 1D list
flat.sort() # sort the list
```

Now we just return our response, and we're done!
```python
return my_response
```

The full source code can be found here: https://github.com/RywesTech/Chatter

### Testing
“The Turing test, developed by Alan Turing in 1950, is a test of a machine's ability to exhibit intelligent behaviour equivalent to, or indistinguishable from, that of a human.” Unfortuantly, my chatbot did not pass the Turing Test. I will leave the following results for you to interpret for yourself 😆

![Oops! This didn't load.](/images/chat1.png)
![Oops! This didn't load.](/images/chat2.png)
![Oops! This didn't load.](/images/chat3.png)

Note: some responses have been removed as they are personal or could be taken the wrong way out of context. There are some that are so incredibly funny but I just don't think they're appropriate for this blog. Because of that, you should check out my code above and go make your own chatbot!
