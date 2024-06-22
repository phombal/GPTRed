# GPTRed
🏆 This project won the honorable mention at the Stanford CS 109 Probability Challenge in Fall 2023. 🏆

# Abstract:
With the advent and enhancement of large language models like ChatGPT, the concern for student plagiarism with the assistance of these tools is rapidly growing. Plagiarism can significantly hamper students’ learning trajectories and undermine academic integrity, which can lead to serious long term consequences. In this model, I propose GPTRed, an algorithm utilizing machine learning infrastructure to assess whether long-form student-created content is plagiarized. GPTRed uses Bayes Rule to determine the frequency and probability of words and conditional probability to elucidate the perplexity of sentences within past student-generated content, as well as ChatGPT generated content. Within our model, which has a 80/20 training and test split on our initial dataset, after using Maximum Likelihood Estimate (MLE) to determine our parameters, we use LASSO to regularize the parameters. Then, the logistic regression model is tested on the newly-provided content that the user wants to check for plagiarism, which returns whether the user or ChatGPT wrote the paper. Upon testing the model, we determined that it displayed 86% accuracy on our test set. GPTRed differs from preexisting GPT plagiarism models due to its focus on long-form content like essays, as well as its training on past individual student data, which can help improve accuracy of detectors in the long run
# Background:
A growing issue in the world of education is the use of large language models (LLMs) to generate written content. While these models offer unparalleled support to instructors for creating course materials and to students in synthesizing course content, a massive issue looms in the use of these models to bypass the intellectual stimulation of critically thinking about issues and ideas to instead pass off the work of these models as one's own. Not only does this harm students' educational trajectories, it undermines the academic integrity that forms the trust between educational institutions and students. Therefore, an accurate, student-based GPT plagiarism detector for educational content can improve student learning outcomes and help mend the trust between instructors and students.
# Methods:
Two arbitrary past writing samples and a main evaluatory sample can be solicited from the user, given that the user is the one who wrote each of these samples. 10 sample passages, each with a size of around 1000 words, are generated on GPT 3.5 on random educational topics and broken into five parts each, and were evaluated for their perplexity and probability of unigrams, bigrams, and trigrams in one of the past writing samples being found in the passages. The past writing sample is divided into 10 portions, and is evaluated for the same metrics. The perplexity score, unigram score, bigram score, and trigram score, as well as their output value, comprise the dataset. Because this dataset is dependent on the user's inputted writing samples, we must recreate it for every input. On this dataset, we randomly split 80% of the data for training and 20% for testing. Because our data is relatively sparse and the perplexity parameter has too much influence over the output, we regularize the parameters using LASSO. After training our model and determining our parameter coefficient values, which we optimize by tuning hyperparameters, we test our model on our test set and determine the accuracy. With our model in place, we calculate the perplexity and the probability of unigrams, bigrams, and trigrams for our evaluatory sample, which we tested on our model. In the case that the predicted label for our sample was greater than 0.5, then we classify that sample as written by ChatGPT and if it was less, then we classify it as written by the user, which we return to the user.
# Results:
We consistently achieved at least 0.86 accuracy for our model when tested on our data. However, this accuracy may be skewed because our variables are not I.I.D. due to the lack of prior user data in our model and because of the small sample size. Due to a lack of compute power, I could not conduct any large scale simulations to determine the accuracy of the model with a different input. However, on a small sample of five articles from a prominent journalist that were trained upon, I found that all five were properly classified by our model. Most prominently, the perplexity parameter had a great deal of influence in determining whether a sample was written by a human or ChatGPT, which even after regularization, was consistently trained to have 20% greater weight than any of the n-gram parameters. I also found that setting the LASSO regularization strength to 1 yielded the best results, which was reflective of the influence of the perplexity parameter and the sparsity of our data.
# Statistical Analysis:
I used statistics in various parts throughout this project, but due to space limitations, I'll be focusing on perplexity and logistic regression.
# Perplexity
Perplexity is calculated using conditional probability—specifically, by taking the probability of the next word i occurring given every word that has occurred thus far in the sentence; formally, this is P(wi | w1w2...wi - 1). The product of all of these probabilities, including the ending punctuation is what comprises the likelihood that this sentence would be stated by the model. To understand this concept more formally, let's consider the following example: P("I love cats.") = P("I") * P("love" |"I") * P("cats" | "I love") * P("." | "I love cats").
It is apparent that longer sentences tend to have larger probabilities than shorter ones with this model. However, perplexity accounts for that by normalizing for size. We multiply each probability by 1n where n is the number of words in the sentence. Also, perplexity takes the inverse of this measure, so thus if the conditional probability of a sentence is equal to W, then the perplexity of the sentence is equal to some value proportionate to 11nlog2P(W) (there. We know that sentences generated by ChatGPT tends to have lower perplexity than humans due to the fact that it is adding the most likely next word onto a sentence given what it has added so far. On the other hand, humans tend to write more spuriously, often leading their perplexities to be higher.
# Logistic Regression
After we have calculated the perplexity of each of the examples and the probability of the n-gram sequence appearing in the user's past writing sample, we generate a dataset, which we use to train a logistic regression model. In our model, we used Maximum Likelihood Estimate (MLE) to estimate our parameters. We know that given the n data points in our dataset—let's call each xi, we can define the logistic regression function for each parameter to be F(x) = 11 + e-(0 + 1x). We can derive this function by considering that a logistic regression, is simply a function of z = 0 + 1x1 + ... + pxp , where i describes our parameter values, which we can input into a sigmoid function. We know that to find our MLE, we must choose the parameters  that maximize the likelihood function, given by L() = f(Xi | ). Using our logistic regression function, we can take the log likelihood, which we can simplify to be LL() =i = 0nyii + log(11 + exi) . Now, we must find the value of  that maximizes 11 + exi . To do so, we can take argmax(L | ), from which we find our parameters. In our model, we use stochastic gradient descent to approach these maximizing parameter values, but any convex optimization method could be used.
# Limitations:
One limitation of this tool is that it does not rely on I.I.D. data inputs, which weakens the accuracy of our model on newly inputted data. Another challenge with this program is that due to financial constraints, unless the OpenAI API is utilized, user inputs cannot be compared to a similar LLM-generated model, meaning that in this case, specific domain-based language used in each of the sample passages generated on ChatGPT may have influenced the similarity for the generation of the n-gram data depending on the user's input. Finally, one practical limitation of this program is that despite its high accuracy, it requires significant training and testing before it can be implemented in schools. To account for this, the model sets a classification threshold that favors the student.
# Conclusion:
An aspect of this project that I found particularly challenging was generating data with ChatGPT. The reason that ChatGPT plagiarism detection is much more difficult than conventional plagiarism detection is that the knowledge base of GPT 3.5 is highly dependent on the context of the response and the parameters that it generates new responses based on. Both of these metrics are highly obscure, and we can only emulate them using GPT 2 probabilities, which are available, but computationally expensive. This prevented me from using a Bayesian model to assign content similarity, and many other models that could have been used in a conventional plagiarism detection software. However, it forced me to get creative with what I could use to generate data on every run. Although I ended up using perplexity and n-gram values, a few other options included sentence length and Jaccarian similarity.
A model like GPTRed could have massive implications on preserving positive educational trajectories and maintaining academic integrity in the light of large language models. Specifically, GPTRed has a student-centric approach, due to its reliance on past content generated by the student. Current GPT plagiarism detection models only rely on the current assignment that a student is working on, which is convenient for the user, but lacks the accuracy necessary in the educational world. Comparing a student's work to past samples of their own work is key because with a lack of clear indication of GPT responses, achieving higher similarity accuracy to one's own work can help improve models. Therefore, while GPTRed is by no means the most accurate plagiarism model, through more meticulous training, it can boost detection accuracy significantly.
