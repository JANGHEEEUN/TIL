# keras11_hamsu

#### 1. 데이터

```python
x = np.array([range(1,101), range(101,201), range(301,401)])
y = np.array([range(101,201)])

x= np.transpose(x)
y= np.transpose(y)

from sklearn.model_selection import train_test_split
x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.4,shuffle=False) 
x_test, x_val, y_test, y_val = train_test_split(x_test, y_test, test_size=0.5, shuffle=False)
```



#### 2. 모델 구성

함수형 모델 사용

- `keras.models`에서 `Model` import
- `keras.layers`에서 `Input` import

```python
from keras.models import Sequential, Model
from keras.layers import Dense, Input

input1 = Input(shape=(3,))
x = Dense(5, activation='relu')(input1)
x = Dense(2)(x)
x = Dense(3)(x)
output1 = Dense(1)(x)

model = Model(input = input1, output = output1)
```

- Dense 맨 뒤 괄호에 앞 layer의 이름을 넣어주면서 layer 늘림

- 맨 처음에 모델을 선언한 `Sequential`과 달리 맨 마지막에 함수형 모델임을 명시

  

#### 3. 훈련

```python
model.compile(loss='mse', optimizer='Adam', metrics=['mse'])
model.fit(x_train, y_train, epochs=200, batch_size=1, validation_data=(x_val, y_val))
```



#### 4. 평가 예측

```python
loss, mse = model.evaluate(x_test,y_test, batch_size=1) #3.test
print('mse:' , mse)
print('loss:' , loss)

x_prd = np.array([[501,502,503]])
aaa = model.predict(x_prd, batch_size=1)
print(aaa)

y_predict = model.predict(x_test, batch_size=1)
```



#### plus) 함수형 API

> - 참고: https://wikidocs.net/38861

- `sequential API`는 여러층을 공유하거나 다양한 입출력을 사용하는 등 복잡한 모델을 만들기엔 한계가 있음
- `functional API`: 각 층을 일종의 함수로 정의
  - 특징
    1. Input 함수에 입력 크기 정의
    2. 이전층을 다음층의 입력으로 사용하고 변수에 할당
    3. Model() 함수에 입력과 출력 정의

######  

##### 1. Fully-Connected FFNN 

```python
from tensorflow.keras.layers import Input, Dense
from tensorflow.keras.models import Model

#input 정의
inputs = Input(shape=(10,))

#dense로 layer 설정
hidden1 = Dense(64, activation='relu')(inputs)
hidden2 = Dense(64, activation='relu')(hidden1)

#output 정의
output = Dense(1, activation='sigmoid')(hidden2)

#model 정의
model = Model(inputs=inputs, outputs=output)

model.compile(optimizer='rmsprop', loss='categorical_crossentropy', metrics=['accuracy'])
model.fit(data, labels)
```



##### 2. Linear Regression

```python
from tensorflow.layers import Input, Dense
from tensorflow.models import Model

#input 정의
inputs = Input(shape=(10,))

#output 정의
output = Dense(1, activation='linear')(inputs)

#model 정의
model = Model(inputs=inputs, outputs=output)

linear_model.compile(optimizer='sgd', loss='mse')
linear_model.fit(x=dat_test, y=y_cts_test, epochs=50, verbose=0)
linear_model.fit(x=dat_test, y=y_cts_test, epochs=1, verbose=1)
```



##### 3. Logistic Regression

```python
from tensorflow.keras.layers import Input, Dense
from tensorflow.keras.models import Model

#input 정의
inputs = Input(shape=(3,))

#output 정의
output = Dense(1, activation='sigmoid')(inputs)

#model 정의
logistic_model = Model(inputs, output)

logistic_model.compile(optimizer='sgd', loss = 'binary_crossentropy', metrics=['accuracy'])
logistic_model.optimizer.lr = 0.001
logistic_model.fit(x=dat_train, y=y_classifier_train, epochs = 5, validation_data = (dat_test, y_classifier_test))
```



# keras12_ensemble

#### 1. 데이터

```python
x1 = np.array([range(1,101), range(101,201), range(301,401)])
x2 = np.array([range(1001,1101), range(1101,1201), range(1301,1401)])

y1= np.array([range(101,201)])


x1= np.transpose(x1)
x2= np.transpose(x2)
y1= np.transpose(y1)

from sklearn.model_selection import train_test_split

x1_train, x1_test, x2_train, x2_test, y1_train, y1_test = train_test_split(x1, x2, y1, test_size=0.4, shuffle=False)
x1_test, x1_val, x2_test, x2_val, y1_test, y1_val = train_test_split(x1_test, x2_test, y1_test, test_size=0.5, shuffle=False)

print(x1_train.shape) #(50,3)
print(x2_train.shape) #(60,3)
print(y1_train.shape) #(60,1)
```



#### 2. 모델 구성

```python
from keras.models import Sequential, Model
from keras.layers import Dense, Input

input1 = Input(shape=(3,))
dense1 = Dense(5)(input1)
dense2 = Dense(2)(dense1)
dense3 = Dense(3)(dense2)
output1 = Dense(1)(dense3) 

input2 = Input(shape=(3,))
dense21 = Dense(5)(input2)
dense22 = Dense(2)(dense21)
dense23 = Dense(3)(dense22)
output2 = Dense(1)(dense23)

# from keras.layers.merge import concatenate
# merge1 = concatenate([output1, output2]) 

from keras.layers.merge import Concatenate #Concatenate Class
merge1 = Concatenate()([output1, output2])

middle1 = Dense(4)(merge1)
middle2 = Dense(7)(middle1)
output = Dense(1)(middle2)

model = Model(inputs = [input1, input2], outputs=output)
```

- `Concatenate`: 여러 모델을 엮는데 이용

  Concatenate를 사용할거라면, 모델 output을 input col과 맞출 필요 없음

  - 함수 사용

    ```python
    from keras.layers.merge import concatenate
    merge1 = concatenate([output1, output2])
    ```

  - 클래스 사용

    ```python
    from keras.layers.merge import Concatenate
    merge1 = Concatenate()([output1, output2])
    ```

- 입력이 여러개일 경우 `list`에 넣어줌

  ```python
  #ex1)
  merge1 = Concatenate()([output1, output2])
  
  #ex2)
  model = Model(inputs = [input1, input2], outputs=output)
  ```

  

#### 3. 훈련

들어가는 데이터는 2개인데(x1, x2) 결과 데이터는 하나이므로(y1) `validation_data`에 `x1_val`과 `x2_val`을 `list`로 넣어줌

```python
model.compile(loss='mse', optimizer='Adam', metrics=['mse'])
model.fit([x1_train, x2_train], y1_train,epochs=200, batch_size=10, validation_data=([x1_val, x2_val], y1_val)) 
```



#### 4. 평가 예측

```python
loss, mse = model.evaluate([x1_test, x2_test], y1_test, batch_size=1) #3.test
print('mse:' , mse)
print('loss:' , loss)


x1_prd = np.array([[501,502,503],[504,505,506],[507,508,509]])
x2_prd = np.array([[501,502,503],[504,505,506],[507,508,509]])
aaa = model.predict([x1_prd, x2_prd], batch_size=1)
print(aaa)

y_predict = model.predict([x1_test, x2_test], batch_size=1)
```



#### 5. RMSE/R2 구하기

```python
#RMSE
from sklearn.metrics import mean_squared_error
def RMSE(y_test, y_predict):
    return np.sqrt(mean_squared_error(y_test, y_predict))
    #np.sqrt: 루트
print("RMSE : ", RMSE(y1_test, y_predict))

#R2
from sklearn.metrics import r2_score
r2_y_predict = r2_score(y1_test, y_predict)
print("R2: ", r2_y_predict)
```

