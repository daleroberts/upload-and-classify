# Upload and classify

Simple [Flask](http://flask.pocoo.org/) server to take an uploaded PNG file and
classify it using a pre-trained [Keras](https://keras.io/) model based on the
specification:
```
POST /mnist/classify
    Returns the class of the image. Invalid input should return a 404.
```

This should work with Python 3 and (legacy) Python 2.

### Dependencies

First, make sure you have all the dependencies.

```
pip install -r requirements.txt
```

### Server

The server takes PNG images, converts them to the correctly shaped numpy array,
and passes them to a pre-trained [Keras](https://keras.io/) model. The shape is
determined automatically from the [Keras](https://keras.io/) model which means this server code
shouldn't need to be changed if you change models. The only thing that is MNIST
specific is the `/mnist/classify` route.

The server returns JSON with the predicted class (i.e., "number" for the MNIST
case) and the name of the PNG file that was uploaded (so that client-side
javascript could potentially send multiple requests and then associate the
correct reponse to the correct PNG). The names in the JSON were made
deliberately short to hint that one should think about network bandwidth
when returning JSON.

The server returns error code 404 when something bad happens to make it easy to
handle this error from client-side javascript.

To run the server, do:
```
./server
```

or alternatively:
```
flask run --host=0.0.0.0
```

Navigate [here](http://127.0.0.1:5000) and upload a PNG file. I've placed some
examples images under the `imgs` directory for convenience.

### Tester

Uploading test data by hand is pretty painful so if you want to hammer the
server with test data automatically then you can run the test tool. The test
tool grabs the MNIST test data, converts it to PNG on-the-fly, submits it to
the server, and prints out the JSON response received.

The PNG's are named in the format 'index-class.png' so that it is easy to
observe what the correct class should be.

Make sure the server is running (in another terminal session?) and then run:
```
./test
```

### Training

If you really want to retrain the model, you can do:
```
./train
```
and the model will be saved to `model.h5`.

Since the brief was "do not get hung up on scaling or model accuracy issues"
the model is stock-standard, nothing really to see here. The model was trained
on a Tesla K80 using the `tensorflow-gpu` backend.

### Predict

If you want to test the model from the command line (without touching the server) 
you can do:
```
./predict img1.png [img2.png img3.png ...]
```
