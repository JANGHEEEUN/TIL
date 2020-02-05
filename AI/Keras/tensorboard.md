## tensorboard

### 1. C:/Study/Keras/graph 폴더 만들기



### 2. import tensorboard

```python
from keras.callbacks import EarlyStopping, TensorBoard
tb_hist = TensorBoard(log_dir='./graph',
                      histogram_freq=0,
                      write_graph=True,
                      write_images=True )

#early stopping
early_stopping = EarlyStopping(monitor='loss', patience=100, mode='auto') 

#3. 훈련- matrics: mse
model.compile(loss='mae', optimizer='Adam', metrics=['mae'])
model.fit(x_train,y_train,epochs=100, batch_size=60, validation_data=(x_val,y_val), callbacks=[early_stopping, tb_hist]) 
```



### 3. Run



### 4. cmd

```bash
cd c:/study/Keras
tensorboard --logdir=./graph
```



![image-20200131102123032](C:\Users\student\Desktop\TIL\AI\Keras\image\image-20200131102123032.png) 



### 5. chrome

localhost:6006으로 이동

![image-20200131102257389](C:\Users\student\Desktop\TIL\AI\Keras\image\image-20200131102257389.png)