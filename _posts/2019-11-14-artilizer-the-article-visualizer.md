---
title: Artilizer - The Article Visualizer
cover-img: /assets/img/artilizer-banner.png
share-img: /assets/img/artilizer-banner.png
readtime: true
subtitle: Got bored of reading boring article? Watch them instead 
tags: [Arttilizer, Webapp, Django, Machine Learning, Python]
---

Have you ever wondered while reading a newspaper article, that how could I read it without getting bored, or how can I learn more of these stuffs without reading them. So, I too had this problems a while ago when I started thinking about artilizer.

I firstly started this out by listing the tech stack that can be used for this kind of an applicaiton, So, i had Java's Spring Boot, PhP's laravel and then Python's Django, I listed out the pros and cons and found Django to be the most suitable tech for this project as its not a big software and more multimedia library options are available in Django.

# The Flow
I tried to divide the project into smaller tasks, so that the application can be created more efficiently. The project had the following parts,i.e
1. Getting the article from the URL
2. Summarizing the Article
3. Generation of the Sentiment of the Article
4. Getting Images for the Visualizer
5. Combining the parts and then getting a Video out of the article

**Getting the Article**

For this task I used beautiful soup to send request to the user given website and then extracting the article.

**Summarizing the Article**

For summarizing the article we generally have two approaches, the *extractive* and the *abstractive* way
* Extractive Summarizer - This Summarizer ranks the sentences in the article and then pickts the top n sentences.
* Abstractive Summarizer - This summarizer repharses(uses new words) the article,i.e it firsts understands a sentence and then tries to create a new sentence with similar meaning and less words.

So, for creating an abstractive summarizer, we found many datasets, but we failed to acheieve it due to some resources restictions, instead we went for an abstractive one. In the abstractive summarizer, we first remove the stopwords from the sentences and then generate a similarity matrix and then rank the sentences using the page algorithm.

**Sentiment Analysis**

One may say, why do we need sentiment for this application. But what's a video without sound effects. We use sound to add emotions to the video. Suppose its a new for a sports team about winning a trophy, so, in the video we'll get similar audio.

So, for achieveing this we need to create a Machine Learning model which can perform this task. So, we searched for many datasets and then finalized on a twitter dataset as it was small and we could perform modeling locally as we had some resource restrictions and also, it contains 14 sentiments(Not attaching the Dataset as it is nowhere to be found right now).

So, we first tokenize the dataset and built a vocabulary of known words using CountVectorizer. Next we tried multiple classifier on the model. I've attached some performance %.


| Classifier | Performance % |
| -------- | -------- | 
| Naive Bayes  | 60%(nearly)   |
| SGDClassifier  | 82%(nearly)   |
|K-Nearest Neighbor| 60%(nearly)|

So, we found that the SGDClassifier was working best for our scenario.

**Getting Images**

So, to get the images for the video I had two options, one was to scrape google's data and then get Images or to user django's google_images_download package. I went with the later one. So, I summarized the article in to n/3 sentences of the original article and the tried to get an image from the package using this sentence.

**Generating Video**

To create a video out of all the resources, I used python's moviepy package. Its a module for video editing, which can be used for basic operations (like cuts, concatenations, title insertions), and perform many such tasks. So, I firstly ordered the sentences according to their appearnce in the original article, then attach the respective images in the same pattern as that of the senctences. In the  sentences we added a subtitle using the moviepy library. And, then we added a sound track according to the sentiment recievevd from the sentiment analyser. Next, I added an atuomated voice using python's gTTS Library, which could be used for a visually disable person.


All these resources combined would generate a video which can be view and can be less boring than the original Article. Although there is a scope for improvement such as-
* Work on the Sentiment Analyser
* Article Summarizer Efficiency

You can find the github repo here [https://github.com/betafactory/Artilizer](https://github.com/betafactory/Artilizer)
