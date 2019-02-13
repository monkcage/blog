---
layout: post
title: A step forward to Time Series Forecasting
category: ai
tags: [ai, cnn, lstm]
---

  Time series forecasting is challenging, escpecially when working with long sequences, noisy data, multi-step forecasts and 
  multiple input and output variables.
      
  Deep learning methods offer a lot of promise for time series forecasting, such as the automatic learning of temporal dependence 
  and the automatic handling of structures like trends and seasonnality.
  
  In this crash course, you will discover how youo can get started and confidently develop deep learning models for time series
  forecasting problems usig Python.
  
  This is a big and important post. you might want to bookmark it.
  
  Let's get started.
  
### Overview

  This tutorial is broken down into 7 parts.
  
  Below are 7 parts that you will get started and productive with deep learning for time series forecasting in Python:
- Promise of Deep Learning
- How to Transform Data for Time Series
- MLP for Time Series Forecasting
- CNN for Time Series Forecasting
- LSTM for Time Series Forecasting 
- CNN-LSTM for Time Series Forecasting
- Encoder-Decoder LSTM Multi-step Forecasting

### Part 1: Promise of Deep Learning
  
  In this part, you will discover the promise of deep learning methods for time series forecasting.
  
  Generally, neural networks like Multilayer Perceptrons or MLPs provide capabilities that are offered by few algorithms, such as:
  
  - Robust to Noise. Neural netowrks are robust to noise in input data and in the mapping function and can even support learning 
  and prediction in presence of missing values.
  - Nonlinear. Neural networks do not make strong assumptions about the mapping function and readily learn linear and nonlinear 
  relationships.
  - Multivariate Inputs. An arbitray number of input features can be specified, providing direct support for multivariate 
  forecasting.
  - Multi-step Forecasts. An arbitray number of output values can be specified, providing direct support for multi-step and event 
  multivariate forecasting.
  
  For these capabilities alone, feedforword neural networks may be useful for time series forecasting.
  
### Part 02: How to Transform Data for time Series

  In this part, you will discover how to tranform your time series data into a supervised learning format.
  
  The majority of practical machine learnning uses supervised learning.
  
  Supervised learning is where you have input variables(X) and output variable(Y) and you use an algorithm to learn the mapping
  function from the input to the output. The goal is to approximate the real underlying mapping so well that when you have new 
  input data, you can predict the output variables for that data.
  
  Time series data can be phrased as supervised learning.
  
  Given a sequence of numbers for a time series dataset, we can restructure the data to look like a supervised learning problem.
  We can do this by using previous time steps as input variables and use the next time step as the output variable.
  
  For example, the series:
  ```python
  1, 2, 3, 4, 5, ...
  ```
  Can be transformed into samples with input and output components that can be used as part of a training set to train a supervised
  learning model like a deep learning neural network.
  ```python
  X                  Y
  [1, 2, 3]          4
  [2, 3, 4]          5
  ...
  ```
  This is called a sliding window tranformation as it is just like sliding a window across prior observations that are used as inouts
  to the model in order to predict the next value in the series.
  In this case the window width is 3 time steps.
  
### Part 03: MLP for Time Series Forecasting
  
  In this part, you will discover how to develop a Multilayer Perceptron model or MLP for univariable time series forecasting.
  
  We can define a simple univariable problem as a sequence of intergers, fit the model on this sequence and have the model predict 
  the next value in the sequence. We will frame the problem to have 3 inputs and 1 output, for example: [10, 20, 30] as input and 
  [40]  as output.
  
  First, we can define the model. We will define the number of input time steps as 3 via the input_dim argument on the first hidden
  layer. In this case we will use the efficient Adam version of stochastic gradient descent and optimizes the mean sequared error
  ('mse') loss function.
  
  Once the model is difined, it can be fit on the training data and the fit model can be used  to make a prediction.
  
  The complete examples is listed below.
  ```python
  from numpy import array
  from keras.models import Sequential
  from keras.layers import Dense
  
  x = array([[10, 20, 30], [20, 30, 40], [30, 40, 50], [40, 50, 60]])
  y = array([40, 50, 60, 70])
  
  model = Sequential()
  model.add(Dense(100, activation='relu', input_dim=3))
  model.add(Dense(1))
  model.compile(optimizer='adam', loss='mse')
  
  model.fit(x, y, epochs=2000, verbose=0)
  
  x_input = array([50, 60, 70])
  x_input = x_input.reshape((1,3))
  pred = model.predict(x_input, verbose=0)
  print(pred)
  ```
  Running the example will fit the model on the data then predict the next-of-sample value.
  
### Part 04: CNN for Time Series Forecasting
  
  In this part, you will discover how to develop a Convolutional Neural Network model or CNN for univariate time series forecasting
  
  We can define a simple univariate problem as a sequence of integers, fit the model on this sequence and have the model predict the
  next value in the sequence. We will frame the problem to have 3 inputs and 1 output.
  
  An importamt difference from the MLP model is that the cnn model expects three-dimensional input with the shape[samples, timestps,
  features].  We will define the data in the form [samples, timesteps] and reshape it accordingly.
  
  We will define the number of input time steps as 3 and  the number of features as 1 via the input_shape argument on the first hidden
  layer.
  
  We will use one convolutional hidden layer followd by a max pooling layer. The filter maps are then flattened before being 
  interpreted by a Dense layer and outputting a prediction. The model uses the efficient Adam version of stochastic gradient 
  descent and optimizes the mean squared error loss function.
  
  Once the model is defined, it can be fit in the training data and the fit model can be used to make a prediction
  
  The complete example is listed below.
  ```python
  from numpy import array
  from keras.models import Sequential
  from keras.layers import Dense
  from keras.layers import Flatten
  from keras.layers import Conv1D
  from keras.layers import MaxPooling1D
  
  x = array([[10, 20, 30], [20, 30, 40], [30, 40, 50], [40, 50, 60]])
  y = array([40, 50, 60, 70])
  
  x = x.reshape((x.shape[0], x.shape[1], 1))
  
  model = Sequential()
  model.add(Conv1D(filters=64, kernel_size=2, activation='relu', input_size=(3,1)))
  model.add(MaxPooling1D(pool_size=2))
  model.add(Flatten())
  model.add(Dense(50), activation='relu')
  model.add(Dense(1))
  model.compile(optimizer='adam', loss='mse')
  print(model.summary())
  
  model.fit(x, y, epochs=1000, verbose=0)x_input = array([50, 60, 70])
  
  x_input = x_input.reshape((1, 3, 1))
  yhat = model.predict(x_input, verbose=0)
  print(yhat)
  ```
  Running the example will fit the model on the data then predict the next-of-sample value.
  
  
### Part 05: LSTM for Time Series Forecasting

  In this part, you will discover how to develop a long short-term memory neural network model or LSTM for univariate time series
  forecasting.
  
  We can define a simple univariate problem as a sequence of integers, fit the model on this sequence and have the model predict
  the next value in the sequence. We will frame the problem to have 3 inputs and 1 output.
  
  An important difference from the MLP model, and like the CNN model, is that the LSTM model expects three-dimensional input with
  the shape[samples, timesteps, features]. We will define the data in the form [samples, timesteps] and reshape it accordingly.
  
  We will define the number of input time steps as 3 and the number of features as 1 via the input_shape argument on the first
  hidden layer.
  
  We will use one LSTM layer to process each input sub-sequence of 3 time steps, followed by a  Dense layer to interpret the 
  summary of the input sequence. The model uses the efficient Adam version of stochastic gradient descent and optimizes the mean
  squared error loss function.
  
  Once the model is defined, it can be fit on the training data and the fit model can be used to make a prediction.
  
  The complete example is listed below.
  ```python
  from numpy import array
  from keras.models import Sequential
  form keras.layers import LSTM
  form keras.layers import Dense
  
  x = array([[10, 20, 30], [20, 30, 40], [30, 40, 50], [40, 50, 60]])
  y = array([40, 50, 60, 70])
  # reshape from [samples, timesteps] into [samples, timesteps, features]
  x = x.reshape((x.shape[0], x.shape[1], 1))
  
  model = Sequential()
  model.add(LSTM(50, activation='relu', input_shape=(3, 1)))
  model.add(Dense(1))
  model.compile(optimizer='adam', loss='mse')
  
  model.fit(x, y, epochs=1000, verbose=0)
  x_input = array([50, 60, 70])
  x_input = x_input.reshape((1, 3, 1))
  yhat = model.predict(x_input, verbose=0)
  print(yhat)
  ```
### Part 06: CNN-LSTM for Time Series Forecasting
  
  In this part, you will discover how to develop a hybrid CNN-LSTM model for univariate time series forecasting.
  
  The benefit of this model is that the model can support very long input sequences that can be read as blocks or subsequences
  by the CNN model, then pieced together by the LSTM model.
  
  We can define a simple univariate problem as a sequence of integers, fit the model on this sequence and have the model predict 
  the next value in the sequence. We will frame the problem to have 4 inputs 1 output.
  
  When using a hybrid CNN-LSTM model, we will further divide each sample into further subsequences. The CNN model will interpret 
  each sub-sequence and the LSTM will piece together the interpretations form the subsequences. As such, we will split each sample
  into 2 subsequences of 2 times per subsequence.
  
  The CNN will be defined to expect 2 time steps per subsequence with one feature. The entire CNN model is then wrapped in 
  TimeDistributed wrapper layers so that it can be applied to each subsequence in the sample. The results are then interpreted
  by the LSTM layer before the model outputs a prediction.
  
  The model uses the efficient Adam version of stochastic gradient descent and  optimizes the mean sequared error loss function.
  
  Once the model is defined, it can be fit on the training data and the fit model can be used to make a prediction.
  
  The complete example is listed belowl
  ```python
  from numpy import array
  from keras.models import Sequential
  from keras.layers import LSTM
  from keras.layers import Dense
  from keras.layers import Flatten
  from keras.layers import TimeDistributed
  from keras.layers import Conv1D
  from keras.layers import MaxPooling1D
  
  x = array([[10, 20, 30, 40], [20, 30, 40, 50], [30, 40, 50, 60], [40, 50, 60, 70]])
  y = array([50, 60, 70, 80])
  # reshape from [samples, timesteps] into [samples, subsequences, timesteps, features]
  x = x.reshape((x.shape[0], 2, 2, 1))
  
  model = Sequential()
  model.add(TimeDistributed(Conv1D(filters=64, kernel_size=1, activation='relu'), input_shape=(None, 2, 1)))
  model.add(TimeDistributed(MaxPooling1D(pool_size=2)))
  model.add(TimeDistributed(Flatten()))
  model.add(LSTM(50, activation='relu'))
  model.add(Dense(1))
  model.compile(optimizer='adam', loss='mse')
  
  model.fit(x, y, epochs=500, verbose=0)
  
  x = array([50, 60, 70, 80])
  x = x.reshape(1, 2, 2, 1)
  pred = model.predict(x, verbose=0)
  print(pred)
  ```
  
### Part 07: Encoder-Decoder LSTM Multi-step Forecasting

```python
# multi-step encoder-decoder lstm example
from numpy import array
from keras.models import Sequential
from keras.layers import LSTM
from keras.layers import Dense
from keras.layers import RepeatVector
from keras.layers import TimeDistributed
# define dataset
X = array([[10, 20, 30], [20, 30, 40], [30, 40, 50], [40, 50, 60]])
y = array([[40,50],[50,60],[60,70],[70,80]])
# reshape from [samples, timesteps] into [samples, timesteps, features]
X = X.reshape((X.shape[0], X.shape[1], 1))
y = y.reshape((y.shape[0], y.shape[1], 1))
# define model
model = Sequential()
model.add(LSTM(100, activation='relu', input_shape=(3, 1)))
model.add(RepeatVector(2))
model.add(LSTM(100, activation='relu', return_sequences=True))
model.add(TimeDistributed(Dense(1)))
model.compile(optimizer='adam', loss='mse')
# fit model
model.fit(X, y, epochs=100, verbose=0)
# demonstrate prediction
x_input = array([50, 60, 70])
x_input = x_input.reshape((1, 3, 1))
yhat = model.predict(x_input, verbose=0)
print(yhat)

```



