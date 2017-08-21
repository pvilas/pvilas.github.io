
---           
layout: post
title: Introducción a Tensorflow
tags: machine-learning tensorflow
---

Siguiendo la guia de [Analitics Vidhya](https://www.analyticsvidhya.com/blog/2016/10/an-introduction-to-implementing-neural-networks-using-tensorflow/), tomo unos apuntes de uso de tensorflow.

En sklearn el flujo de trabajo es el siguiente:

1. Crear un objeto con el algoritmo deseado
2. Alimentar el objeto con datos para entrenarlo
3. Hacer predicciones

En código sera algo tal como esto:
```python

# Paso 1 - creamos un objeto para Support Vector C
clasificador = svm.SVC(gamma=0.01, C=100.)

# Paso 2 - Alimentamos el objeto con datos para entrenarlo
clasificador.fit(X_train, y)

# Paso 3 - Con el modelo entrenado efectuamos predicciones
clasificador.predict(X_test)
```

[TensorFlow](https://www.tensorflow.org/) por su parte está diseñado desde el principio para ser escalable, por lo cuál es un pcoo más complicado de usar.
En tf primero creamos un grafo con las operaciones que queremos realizar, un contexto con el que alimentarlo con datos, 
una sesión donde correrá el entrenamiento y finalmente arrancamos la sesión. Esto lo hace que la sesión pueda correr en un servidor
independiente o adaptarse al hardware disponible.

Por otra parte, hay cierta terminología que hay que resaltar:

placeholder: 
  Una forma de alimentar el grafo con datos

feed-dict:
  Un diccionario con el que pasar valores numéricos a un grafo que está corriendo


El flujo de trabajo quedara resumido como:

1. Crear el grafo de operaciones
2. Inicializar variables
3. Crear una sesión
4. Correr el grafo en la sesión
5. Cerrar la sesión

Y un ejemplo en python

```python
import tensorflow as tf

# Paso 1 - Crear el grafo de operaciones
a = tf.placeholder(tf.int16) # to feed data into the graph
b = tf.placeholder(tf.int16)

addition = tf.add(a,b)

# Paso 2 - inicializar variables
init = tf.global_variables_initializer()

# Paso 3 - crear una sesión
with tf.Session() as sess:
    # Paso 4 - Correr el grafo en la sesión
    sess.run(init) # initialize
    res=sess.run(addition, feed_dict={a: 2, b: 3}) # perform op
    print("Addition: {}".format(res))
    
# Paso 5 - Cerrar la sesión
sess.close()
```

