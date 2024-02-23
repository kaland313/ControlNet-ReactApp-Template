# Scribble to ControlNet React App  :camera::arrow_right:​:pencil::arrow_right::framed_picture:

## Copyright

<PRE>
Jelen GitHub repozitórium a Budapesti Műszaki és Gazdaságtudományi Egyetemen tartott "UX laboratórium" tantárgy segédanyagaként készült.
A tantárgy honlapja: https://smartlab.tmit.bme.hu/UX

Az itt található forráskód bármely részének újra felhasználása, publikálása csak a szerzők írásos beleegyezése esetén megegengedett.

2024 (c) Kalapos András (kalapos pont andras kukac edu pont bme pont hu), Zainkó Csaba (zainko kukac tmit pont bme pont hu), Csapó Tamás
</PRE>

## Measurement instructions

In this lab you'll learn how to make the diffusion model running inside a colab notebook available on the web via an API that you'll define. You'll also implement the client side code that connects to this API, sends requests to it and displays the results on a user interface running in a browser as a React.js app. The web app'll record a scribble that you draw by hand using your webcam and send it to the model. 

### Create an API that exposes the scribble-to-image diffusion model

1. If you're not familiar with the FastAPI package, read the following blogpost: https://www.geeksforgeeks.org/deploying-ml-models-as-api-using-fastapi/

2. To send and receive images through via an API we'll convert them to base64 strings. If you're not familiar with base64 encoding we recommend this blogpost: https://www.geeksforgeeks.org/how-to-display-base64-images-in-html/

3. To convert between numpy arrays and base64/png images we provide two helper functions. (Note: `numpy_to_base64` takes numpy arrays not PIL images! Consider what are the inputs and outputs of the diffusion model's `process_scribble` function and adjust these functions accordingly!)

   ```python
   import numpy as np
   import io
   import base64
   from PIL import Image
   
   def base64_to_numpy(base64_string):
       # Decode base64 string
       image_bytes = base64.b64decode(base64_string.split(',')[1])
       # Convert bytes to PIL Image
       image = Image.open(io.BytesIO(image_bytes))
       # Convert PIL Image to NumPy array
       image_array = np.array(image)
       return image_array
   
   def numpy_to_base64(image_array):
       # Convert NumPy array to PIL Image
       image = Image.fromarray(image_array)
       # Create an in-memory binary stream
       image_stream = io.BytesIO()
       # Save PIL Image to the stream as PNG
       image.save(image_stream, format='PNG')
       # Encode stream contents as base64
       base64_string = base64.b64encode(image_stream.getvalue()).decode('utf-8')
       return 'data:image/jpeg;base64,' + base64_string
   ```

4. Wrap the `process_scribble` function in a function that takes the image input as base64 string and returns a base64 string. Test it with a dummy base64 image (you can convert any small image using various online tools)!

5. Define the body of the requests the API will accept 

6. Set up the API

7. Make the app available publicly from a colab instance

   1. To make the available publicly when running on a colab instance we'll use ngrok. Ngrok requires an account and a token to function, so register to get one. 
   2. Add the following middleware configuration to your API: 

   ```py
   from fastapi import FastAPI
   from fastapi.middleware.cors import CORSMiddleware
   app = FastAPI()
   app.add_middleware(
       CORSMiddleware,
       allow_origins=['*'],
       allow_credentials=True,
       allow_methods=['*'],
       allow_headers=['*'],
   )
   ```

   3. Run the API using uvicorn and ngrok

   ```py
   import nest_asyncio
   from pyngrok import ngrok
   import uvicorn
   
   ngrok_tunnel = ngrok.connect(8000)
   print('Public URL:', ngrok_tunnel.public_url)
   nest_asyncio.apply()
   uvicorn.run(app, port=8000)
   ```

8. Verify the API and display its documentation by calling the `/docs` endpoint. Include a screenshot of the API description as displayed by the `/docs` endpoint.

> Note to teachers: Using ngrok might not be necessary in the future, or other method might be necessary to expose the API running on colab to the web. 

### Generate images via the API

1. Clone this repository and start a codespace in it

2. Examine the ![App.jsx](src/App.jsx) file. Search for how we define the prompt variable in the React code and how it is displayed on the UI.

3. Try running the UI (in a separate tab, the webcam access doesn't work in the built-in browser. Close the tab before editing the jsx code!)

4. Search for the `callImageGenAPI` function header and implement the API call (if you're not familiar with calling an API, ask ChatGPT by describing your API to it.)

   This is what I got from ChatGPT:

   ``` javascript
   // Define the data to be sent to the API
   const requestData = {
     scribble: "Your scribble data here",
     prompt: "Your prompt data here"
       ...
   };
   
   // Make a POST request to the API endpoint
   fetch('https://your-api-url.com/predict', {
     method: 'POST',
     headers: {
       'Content-Type': 'application/json'
     },
     body: JSON.stringify(requestData)
   })
   .then(response => {
     // Check if the response is successful
     if (!response.ok) {
       throw new Error('Network response was not ok');
     }
     // Parse the JSON response
     return response.json();
   })
   .then(data => {
     // Handle the response data
     console.log('Response from API:', data);
   })
   .catch(error => {
     // Handle errors
     console.error('There was a problem with the fetch operation:', error);
   });
   ```

5. For debugging open the web app in a new tab and use the Console of your browser's Developer tools menu (`Ctrl`+`Shift`+`i` > Console)

6. To display a base64 image returned by the API call `setImageUrl(...)` with it

7. Modify the UI to allow for changing more inputs of the diffusion model code (e.g. image resolution or number of images, etc)! 

8. Record a short video of using the web app



## GitHub Codespaces ♥️ React

**This repository was created from the GitHub codespaces template for React. Below are a few details about the template that might be useful to you.**

Welcome to your shiny new Codespace running React! We've got everything fired up and running for you to explore React.

You've got a blank canvas to work on from a git perspective as well. There's a single initial commit with the what you're seeing right now - where you go from here is up to you!

Everything you do here is contained within this one codespace. There is no repository on GitHub yet. If and when you’re ready you can click "Publish Branch" and we’ll create your repository and push up your project. If you were just exploring then and have no further need for this code then you can simply delete your codespace and it's gone forever.

This project was bootstrapped for you with [Vite](https://vitejs.dev/).

### Running the app

We've already run this for you in the `Codespaces: server` terminal window below. If you need to stop the server for any reason you can just run `npm start` again to bring it back online.

Runs the app in the development mode.\
Open [http://localhost:3000/](http://localhost:3000/) in the built-in Simple Browser (`Cmd/Ctrl + Shift + P > Simple Browser: Show`) to view your running application.

The page will reload automatically when you make changes.\
You may also see any lint errors in the console.

### Learn More

You can learn more in the [Vite documentation](https://vitejs.dev/guide/).

To learn Vitest, a Vite-native testing framework, go to [Vitest documentation](https://vitest.dev/guide/)

To learn React, check out the [React documentation](https://reactjs.org/).


