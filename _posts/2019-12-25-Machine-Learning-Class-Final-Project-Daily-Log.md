---
layout: post
Title: Final Project Daily Log
---

Below is a log of my daily progress, including:

1.) What I worked on today
2.) What I learned
3.) Any road blocks or questions that I need to get answered
4.) What my goals are for tomorrow

**January 7th**

Today I read these three articles, explored the Python NLTK framework, and initiated my Git repo:
- <https://towardsdatascience.com/creating-bots-that-sound-like-humans-with-natural-language-processing-d9581905de>
- <https://towardsdatascience.com/build-your-first-chatbot-using-python-nltk-5d07b027e727>
- <https://medium.com/analytics-vidhya/building-a-simple-chatbot-in-python-using-nltk-7c8c8215ac6e>

The first article described the use of Seq2Seq Models, which uses two recurrent neural networks to encode the data, focus on each particular part, then decode the data. Basically, this type of model takes in a sentence, processes it one word at a time, and outputs a sentence one word at a time. Each part is accomplished by the encoder, the attention mechanism, and the decoder.
The second article takes a very high-level approach to chatbots, but it seems like another good starting point.
The third article talks about the two ways chatbots are build today: rule-based (normally used for answering specific questions), and self-learning (generates their own replies based on a model). It also provides links to [NLTK](https://www.nltk.org/), which seems like a helpful framework.

Some of the terminology was difficult, but I think I answered all my questions through Google. Tomorrow my goal is to take what I've learned today and start implementing it into code! I want to have a very basic chatbot this week, and improve upon it starting next week.

**January 8th**

Today I started to build a simple chatbot using the NLTK framework. It took a while to get NLTK installed and configured, so that took up most of today's work. Running the UI installer crashed my computer (as soon as I tried to open the installer the screen went blank and my computer rebooted), so I did it through the terminal and that worked fine. I've read about Term Frequency and Cosine Similarity more to gain a better understanding of these concepts.

The largest difficulty today was to get my foundation working: installing the packages that I need, getting NLTK working in the notebook, and trying to understand basic chatbot code. I figured out all my problems today, so we're set!

My plan tomorrow is to further understand how this chatbot works, and continue to dive into example code.

**January 9th**

Now that I have NLTK installed, I've been starting to work with it more to understand how it works. So far, this is my understanding of how the chatbot process flows:

***Pre-prossing The Text***
To preprocess, the entire text needs to be scanned and a number of different transformations and edits need to be made.
- Convert to one case
I choose to convert the entire text to lower-case so that the algorithm doesn't see "Hello" and "hello" as different strings, for example.
- Tokenization
The text also needs to be tokenized, where the text is converted to a list of tokens, i.e. the words that will actually make a difference. Assign a number to each unique word, as computers talk in terms of numbers, not text.
- Remove noise and stop words
We don't want to process punctuation, odd characters, or stop words (such as "the," "these," "is," etc...).
- Stemming
This is where we match words to their stems. E.g. we would match "running" and "ran" to "run."
- Lemmatization
Lemmatization is a lot like stemming, but even more broad. It will match "better," "good," and "superior" to the same lemma, which means that they are essentially considered the same.

***Term Frequency-Inverse Document Frequency***
By using the following calculations, we can train the algorithm to match existing documents with the given input text.
- Term Frequency (frequency of a word in the current document) = (Number of times term t appears in a document)/(Number of terms in the document)
- Inverse Document Frequency (how rare a word is across documents) = IDF = 1+log(N/n), where, N is the number of documents and n is the number of documents a term t has appeared in.

***Cosine Similarity***
When we apply TF-IDF to two texts we get two vectors. We can determine how similar they are by using the cosine similarity equation: Cosine Similarity (d1, d2) =  Dot product(d1, d2) / ||d1|| * ||d2||. Once we find the most similar vector, we know that that is our output!

**January 10th**

Now that I understand chatbots more, I spent today getting my data ready to put into my algorithm. I want to be able to make a chatbot that responds to texts for me, so I needed to parse my text database. Using an open-source Jupyter Notebook, I converted the SQL database to a rough CSV file. I then wrote my own code to take in the CSV table with all the data, extract just the messages with one specific person, organize them into a NLTK-readable format, and saves the output to a TXT file.

I was sick today, so I spent as much time as possible getting caught up.

**January 13th**



**January 15th**

**January 16th**

**January 17th**
