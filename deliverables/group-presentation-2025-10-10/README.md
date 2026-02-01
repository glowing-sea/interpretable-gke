## Script

#### Slide 1: 

Hello, everyone. In today’s presentation, I will introduce my research proposal, “Toward an Interpretable Knowledge Tracing Model”. 


#### Slide 2: 

We will first look at the basic definition of knowledge tracing, and then look at some typical knowledge tracing model architectures, which represent different ways of doing knowledge tracing. From these existing model architectures, I will highlight the research gaps and proposal my research plan. Now, let’s get started.


#### Slide 3: 

So what is knowledge tracing? Knowledge tracing is a process of using machines to track the knowledge of students and tailor their learning experience.


#### Slide 4: 

Now, consider a list of question and result pairs extracted from the student’s homework, assignment or whatever, where each element represents a student’s attempt to a question, also known as interaction. Given this list of the student’s historical performance, our task is to train a model to predict whether the student can answer a specific question correctly at a future time.

So you may wonder how can this tailor learning experience. Well, imagine you are in the exam week, and you put all your previous homework questions and feedback into a so-called knowledge tracing machine. The output of this machine is then how likely you can correctly answer each question. Therefore, you can only pick those with low chance of success to practice.

So, before the day of having knowledge tracing. You may only pick the questions that you have really done wrong to practice. Let’s say question 8. However, a past question that you did it wrong before doesn’t necessary mean that you will do it wrong, and vice versa. This is because the knowledge tracing prediction is based on your mastery of the knowledge hidden in the questions, not the questions themselves. 

Let’s q8 is about how to print Hello World and you did it wrong in Week 1, but later in Week 3 you learned more advanced stuff like error handling that depends on the print function, and you did those question right, So when you revisit question 8, there is high chance of success instead.


#### Slide 5:

Usually, a knowledge tracing dataset may provide more than just question and result pairs. However, they are not guaranteed. And some information, like the knowledge components, require manual annotation, which may not be available in a real-life scenario. However, it is possible to do knowledge tracing with question and result pairs only, and we will mainly discuss how to do knowledge tracing when only these two types of information are available.


#### Slide 6:

Many knowledge tracing models, regardless of their types, are essentially doing two things logically: “Modelling the latent knowledge components and their relationship in the questions” and “Modelling the learning and forgetting of the knowledge components of a student over time”.

Since knowledge components are not given is the dataset, it is the job of the model to find them.

So why should we bother the learning of knowledge components instead of questions? Well, if you are a teacher, you will probably be care about if your students have truly learned or mastered the knowledge components, like addition, multiplication, in the question 1 + 3 * 4, instead of just the question itself.

Once we have built a knowledge state of a student in terms of the mastery of each knowledge component based on their historical performance, given a query question, by finding out its required knowledge components and corresponding mastery level, we can then know if the student can answer the query question correctly.


#### Slide 7: 

Now let’s go on and introduce different types of knowledge tracing models. Normally, I should start from an early model. However, I think later models can be more easily understood.


#### Slide 8:

Now let’s look at a type of knowledge tracing models that are based on graph neural network, also known as graph-based knowledge tracing.

Intuitively, given a question and result pair, which corresponds to a student’s past attempt, the model first maps the question to some latent knowledge components. These latent knowledge components are structured as a graph, connected by some edges, representing their relations. All the knowledge component nodes and the relational edges are trainable parameters in the model. After finding the corresponding knowledge component nodes, then based on the result of this attempt, the mastery of the knowledge components is updated. After the change in mastery, since nodes are connected, the network then goes through a message passing process, where other nodes, that are not directly relevant to the question are also updated. In addition, there are also forgetting functions to modify the mastery of knowledge components based on time.

After all the past attempts have been input into the model, the model now holds the knowledge state of the student. When a query question input, it is again mapped to some knowledge component. Then, based on the mastery of the relevant knowledge components, the model generates a query-conditioned graph embedding, representing the student’s knowledge state for answering the query question. The embedding is further passed down to an output layer to convert into the probability of answering the query question right.

Note that for simplicity, we use a single scalar to represent mastery in the RHS graph. However, in an actual graph-based knowledge tracing model, the mastery is represented as vectors. Why? Because mastery in the real-world is also multidimensional. When you say you have mastered Java, you may refer to the mastery of its theory or its application, or both. In addition, the mappings between question and knowledge components are not binary. A question can map to all knowledge components with some weights.

So how can we train the model? Well, we can train it in a semi-supervised manner. We can give the first interaction to the model to predict the result of the second interaction. Then use the first and the second interaction to predict the third interaction. Since we know the ground truth of them, we can compute the loss and do back propagation to update the model parameters, such as  the knowledge component node embeddings and the mapping between questions and nodes.



#### Slide 9:

Now, let’s analyse the model. How does it model knowledge component? It should be pretty obvious. The knowledge components are explicitly modelled through nodes, and their relations are modelled through edges. For modelling learning, this is done explicitly by some update function to the mastery value of the knowledge components. Regarding forgetting of knowledge components, there are also dedicated forgetting functions. The whole knowledge state of the student are explicitly represented as graph.



#### Slide 10:

Here’s a summary. Note that when I  say, the model has explicitly modelled a component or process. I  mean, there exists clearly identifiable variable or functions dedicatedly representing the component or process. Otherwise, the model behaves implicitly.



#### Slide 11:

Now let’s look at another type of knowledge tracing model, key-value memory network, which can be considered as a simpler version of the graph-based knowledge tracing model we have just visited. Instead of having a graph, we now have a table of knowledge component and their corresponding mastery to represent a student’s knowledge state. Again, a  past attempt is mapped to some knowledge components in the table and triggers the mastery values to update. For a query question, the correctness is predicted based on the knowledge state of the student after all past attempts are loaded into the model’s memory. Note that the mastery is usually represented as a vector instead of a scalar.



#### Slide 12: 

Now let’s analyse the model. How are knowledge components modelled? They are explicitly modelled as key. However, because it is a table. The relations between knowledge components are typically missing. The learning of knowledge components are explicitly modelled by the erase-add update mechanism of the key-value memory network. However, in the version shown on the LHS, there is no explicit time-based forgetting function. Overall, the whole student’s knowledge state is represented as a table.


#### Slide 13:
Here is a summary of models visited so far.



#### Slide 14:

Now let’s look at the way of using Recurrent Neural Network to do knowledge tracing. Now given a student past attempt, represented as a question and answer pair. The model first transforms it into an embedding. Compared to simple one-hot encoding, the embedding contains richer information, and it should tell what knowledge components the question is composed of in an implicit way. This is because questions with similar knowledge components are expected to have high similarity if we compute the dot product of their embedding. Next, the embedding are sent into a recurrent neural network, the most famous one is LSTM. The recurrent neural network updates the student’s knowledge state based on the input embedding, and output an updated student’s hidden knowledge state as one or two vectors. The hidden knowledge state vector is input to the model again, along with the next historical attempt. After all past attempts are loaded, the student’s knowledge state is also built. For a query question, we can then use the knowledge state vector, along with the query question to compute the probability of answering the query question right, based on how well the query question aligns with the student’s knowledge state.


#### Slide 15:

Now, unlike graph neural network and key-value memory network. The knowledge components are modelled implicitly in question embeddings. The update and forgetting of knowledge components are naturally handled by the update and forgetting gate of some recurrent neural networks like LSTM. However, there aren’t dedicated update or forgetting functions. The relationship between knowledge components are also implicitly modelled as the recurrent weights in the model. Unlike having a well-organised knowledge state table or graph, the student’s knowledge state of a recurrent neural network is in the form of vector. All the knowledge components and their mastery are merged into this vector. However, I would say the knowledge state is still explicit, because it is identifiable as a vector. For the next model type that I will introduce, the student's global knowledge state does not even exist.



#### Slide 16:

So here is a summary of models visited so far.



#### Slide 17:

Now let’s introduce the last type of knowledge tracing model today. This type of knowledge tracing models are based on the transformer architecture. They are also known as attentive knowledge tracing models. Unlike all the previous models, the transformer model takes all the student’s past interactions as well as the query question at once in parallel. Like recurrent neural networks, the input tokens are first transformed into embedding. Since they are input in parallel, we also need to encode and embed the position or time for each interaction. After the embedding process, the embeddings go through the multi-head masked attention process. Intuitively, we want to compute the similarity between query question and past interactions from  different perspectives. This is because results of the past interaction can help us determine the result of the query question. After the comparison, we can obtain the student knowledge state embedding, as h4, shown in the graph. Note that this embedding tells only the student’s knowledge state for answering the query question, q4, instead of the global knowledge state of the student. With this embedding, we can then convert it into probability through the output layer. 

The forgetting mechanism can be added when we compute the similar between the past interactions and the query question through a bias function. For example, we may need to decrease the similarity score by some amount if the past interaction is too far away in time from the query question.



#### Slide 18:

Now let’s answer the five questions again. As in recurrent neural networks, the knowledge components are implicit modelled when we turn questions into embeddings. We can consider each embedding as a combination of some latent knowledge components. When measuring the similarity between the embeddings of past interactions and the query question, the KC-KC relationship is also taken into account. This is because questions of past interactions with hidden knowledge components relevant to the query questions are expected to be attended more. The learning of knowledge components are implicitly modelled through attention mechanism as well, by measuring how much information each past interactions contributes to the prediction of the query question.  The full static student’s knowledge state does not exist in this type of model, although we can obtain the knowledge state vector relative to the query question as a vector embedding. The forgetting mechanism, however, is modelled explicitly by bias functions in the computation of attention.



#### Slide 19:

Here is a summary of models visited so far.



#### Slide 20:

Now after covering the necessary background, we have moved onto the most interesting part. We have visited four types of knowledge tracing models, can you tell which type of knowledge tracing model provide the most interpretable student’s knowledge state. A graph, a table, a vector, or even nothing. I would choose graph neural network.



#### Slide 21:

So why should we bother interpretable knowledge states. To answer this, we should move forward to the application of knowledge tracing. If our goal is to let the model predict the probability of answering each question right. We don’t need to care about the interpretability of knowledge state, since it is just our intermediate result. However, what students and teachers also care about is if the students have truly leaned the knowledge in the question, instead of being accustomed to the questions.



#### Slide 22:

Now let’s focus on the knowledge state of graph-based knowledge tracing models. Here's a question. Even though all knowledge components have been organised in a graph, is it interpretable enough? Probably No. Especially, the knowledge components are some vector embedding which are not human-readable. However, these knowledge components should ultimately map to some real-world knowledge components, like the graph on the RHS. Since our current inputs to a knowledge tracing model are just question ID and result pairs, it is reasonable that the model cannot figure out the real-world knowledge component's name because the model does not even know the subject. However, what if the model can now access to the question text. In this case, the node embedding should have semantics and can we now map these implicit knowledge component embeddings back to some real-world knowledge components.



#### Slide 23:

Before talking about how we might do so. I would like to mention the  major of this research, that is the trade-off between explicitness and flexibility. Consider a function σ(q) which can convert an interaction into its latent knowledge state representation. What graph neural network or recurrent network does conceptually is to transforms both the student’s historical interactions and the query question into its latent knowledge state representation. h = σ(student’s history) and h_query = σ(query question). Then the model makes prediction based on how well h aligns with h_query, e.g., (h ⋅ h_query) in the latent space.

However, the kernel trick says that if we compute the dot product of two transformed latent vectors or matrix, it is equivalent to keep them in original form and put them into some kernel function. In this case, we don’t even need to compute the student’s latent knowledge state. This is basically how transformers do knowledge tracing.

One benefit of being implicit is that our knowledge state can now arbitrary complex. A classical machine learning example is the RBF kernel. If we expand this kernel, it is equivalent to the dot product of two latent variables of infinite number of dimensions, which is hard to be represented  explicitly.



#### Slide 24:

Our research question then becomes “How can we make Knowledge Tracing models, especially Graph-based KT, more explicit and interpretable — enabling human teachers to understand students’ knowledge states — without severely sacrificing flexibility?”



#### Slide 25:

To achieve the plan. I have proposal two stages or directions. The first stage is to use a language model to generate human-readable knowledge components to initialise and train a graph-based knowledge tracing model. The second is to, given a trained graph-based knowledge tracing model, map its implicit knowledge component nodes embedding to some meaningful and human-interpretable knowledge components or text.



#### Slide 26:

In terms of Stage 1, we plan to first fine-tune a language model to find out the  real-world knowledge components among the questions based on the question text. They are then  used to initialise the graph neural network. The knowledge component nodes, as well as the mapping between questions and knowledge components, are fixed during the training. In this case, we will obtain a knowledge state graph of student’s with inherently explicit and interpretable real-world knowledge components. However, since we freeze the knowledge components, we also prevent the model to automatically find out more complex knowledge components, especially those with different granularity.



#### Slide 27:

To achieve both interpretability and flexibility, we can keep our knowledge state implicit, but have a separate output layer to decode the implicit knowledge state into some human-readable form, like text. We plan to use a Large Language Model encoder to do the mapping between the question and the knowledge component node embeddings. In this case, the node embedding should contain semantics, that is, they should encode the name of some real-world knowledge components, even though they are not understandable to humans for now. The output layer is based on a Large Language Model decoder. It is expected to take input queries in natural language and answer it based on the knowledge state of the student.

In this method, both interpretability and flexibility may be achieved through implicitness maintained.

So, this is our final ambition, but I think it is not impossible.



#### Slide 28:

This graph is a simple analogy to our goals.



#### Slide 39:

All right. Thank you for listening to the end. There are also a couple of bonus slides and you are welcome to read them. And I hope you can support my research.