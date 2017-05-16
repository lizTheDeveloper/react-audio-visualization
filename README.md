# react-audio-visualization

This tutorial is for creating an audio visualization in React with the web audio API, and the canvas API.

##Editorial
You assume a bunch of stuff, put it in a bulleted list with hyperlinks.

### Let's get some stuff out of the way first,

In this tutorial will use Node.js and npm, if you already have that installed then that's great move on to the next step, otherwise [learn about // get npm here.](https://www.npmjs.com/get-npm)

This is *not* a tutorial on how to use React. If you want to learn more about how React works check out [Fullstack React](https://www.fullstackreact.com/)

The first thing we need to do is download create-react-app to get our react boilerplate. [Here are the docs if you want to learn more.](https://github.com/facebookincubator/create-react-app)
Type the following into your terminal:

```
npm install -g create-react-app
```

### Okay now let's get started!

We can create our app by typing this command followed by the name of your application. I called mine audio-visuals in this example. Type the following into your terminal:


```
create-react-app audio-visuals
```

This may take a moment...
After it's done you can go ahead and change directories into the newly created app. Type the following into your terminal.
```
cd audio-visuals
```

Then open up the directory in your favorite text editor. \(I use Atom\) You can open it up from the terminal by typing
```
atom .
```
##Editorial
Assume they have an editor installed by using the variable $CODE_EDITOR and programmers kinda know what that means

If you see an error that says atom is not a command then go to atom and look at the top menu bar. Click the drop down that says Atom and look for Install shell commands. Click that and you should now be able to use the atom command in your terminal.  


![Atom menu example](http://i68.tinypic.com/avhjz7.png)

##Editorial
Assume some constant level of knowledge, and if you're teaching React, it's generally safe to assume they know node or at least not to fuck with node modules.

 Now that you've opened up the directory with your text editor, take a look around! We will walk through this project step by step below.  

The file structure will look like this:  
![File structure example](http://i64.tinypic.com/keao0i.png)
<!-- ```
audio-visuals/
  README.md
  node_modules/
  package.json
  .gitignore
  public/
    favicon.ico
    index.html
  src/
    App.css
    App.js
    App.test.js
    index.css
    index.js
    logo.svg
``` -->


## audio-visuals/src/App.js

```
 import React, { Component } from 'react';
 import logo from './logo.svg';
 import './App.css';

 class App extends Component {
   render() {
     return (
       <div className="App">
         <div className="App-header">
           <img src={logo} className="App-logo" alt="logo" />
           <h2>Welcome to React</h2>
         </div>
         <p className="App-intro">
           To get started, edit <code>src/App.js</code> and save to reload.
         </p>
       </div>
     );
   }
 }

 export default App;
```
We can simplify and leave it looking like this to get started.

 ```
 import React, { Component } from 'react';
 import './App.css';

 class App extends Component {
   render() {
     return (
       <div className="App">
       </div>
     );
   }
 }

 export default App;
 ```
### Now comes the real coding :)

Inside the div with the className App, insert an audio tag with whatever song you'd like. I'm going to use Sublime! Also add an `h2` tag to say the name of the song and artist you choose.

Now the App Component should look like this:

```
class App extends Component {
  render() {
    return (
      <div className="App">
      <h2>Sublime - 40oz to Freedom</h2>
      <audio
          ref="audio"
          autoPlay={true}
          controls={true}
          src="https://p.scdn.co/mp3-preview/e4a8f30ca62b4d2a129cc4df76de66f43e12fa3f?cid=null"
          >
          </audio>
      </div>
    );
  }
}
```

We've set a few attributes on the audio tag, including the `ref` attribute. This is for referencing the tag in a method we will write on our component later on. [Learn more about the ref attribute here!](https://facebook.github.io/react/docs/refs-and-the-dom.html)

You can now run ```npm start``` in your terminal and see the audio tag rendered on your webpage running on `localhost:3000`. Mine looks like this:  


![First website test](http://i67.tinypic.com/11t5sv8.png)  

Go back to atom or your text editor and add a canvas tag underneath the audio tag. Add a `ref` attribute of `"analyzerCanvas"` and an `id` of `"analyzer"`. I've also added a couple `div`s with specific `id`s so we can reference them later. Your app component should look like this now:

```
class App extends Component {
    render() {
        return (
            <div className="App">
                <h2>Sublime - 40oz to Freedom</h2>
                <div id="mp3_player">
                    <div id="audio_box">
                        <audio
                            ref="audio"
                            autoPlay={true}
                            controls={true}
                            //this is the link to my song url feel free to use it or replace it with your own
                            src={"https://p.scdn.co/mp3-preview/e4a8f30ca62b4d2a129cc4df76de66f43e12fa3f?cid=null"}
                            >
                            </audio>
                        </div>
                            <canvas
                                ref="analyzerCanvas"
                                id="analyzer"
                                >
                                </canvas>
                    </div>
                </div>
            );
        }
    }
```

Now we're going to add a method to the App component called createVisualization.
inside that method we will create a new audio context with the Web Audio API. You do not need to download anything to have access to this API. An audio context represents an audio-processing graph, built from audio modules linked together, that are represented by AudioNodes. Just create a new instance by typing new AudioContext(); and save it to a variable. Later our audio context will be connected to our audio tag. So far our method looks like this

```
createVisualization(){
    let context = new AudioContext();
}
```
Now that we have the Audio context object saved to a variable we can create an analyser node from it with the create analyser method. According to [the MDN documentation](https://developer.mozilla.org/en-US/docs/Web/API/AnalyserNode) an analyser node "represents a node able to provide real-time frequency and time-domain analysis information. It is an AudioNode that passes the audio stream unchanged from the input to the output, but allows you to take the generated data, process it, and create audio visualizations." If that sounds confusing it's okay you will still be able to move forward with the tutorial.

```
let analyser = context.createAnalyser();
```
Then we create a reference to the canvas element and save it to a variable. We do this by accessing the refs object that React sets up for us by typing ```this.refs```. Then we want to get the context of the canvas so we can draw shapes on it. We do this with the methods that the canvas element has available for us to use.

```
let canvas = this.refs.analyserCanvas;
let ctx = canvas.getContext('2d');
```
 Now we want to grab the audio tag in the same way we did the canvas. Then we set the cross origin of the audio tag to anonymous

 ```
let audio = this.refs.audio;
audio.crossOrigin = "anonymous"
 ```
After that we pass the audio tag into the Audio context method called createMediaElementSource in order to create an audio source that the audio context can work with. Then we connect it to the audio context's analyser and then both the analyser and the audio source are connected to the context's destination.

```
let audioSrc = context.createMediaElementSource(audio);
audioSrc.connect(analyser);
audioSrc.connect(context.destination);
analyser.connect(context.destination);
```
#### Just to check in this is what App.js should look like so far:  

```
import React, { Component } from 'react';
import './App.css';

class App extends Component {

    createVisualization(){
        let context = new AudioContext();
        let analyser = context.createAnalyser();
        let canvas = this.refs.analyserCanvas;
        let ctx = canvas.getContext('2d');
        let audio = this.refs.audio;
        audio.crossOrigin = "anonymous";
        let audioSrc = context.createMediaElementSource(audio);
        audioSrc.connect(analyser);
        audioSrc.connect(context.destination);
        analyser.connect(context.destination);

    }

    render() {
        return (
            <div className="App">
                <h2>Sublime - 40oz to Freedom</h2>
                <div id="mp3_player">
                    <div id="audio_box">
                        <audio
                            ref="audio"
                            autoPlay={true}
                            controls={true}
                            //this is the link to my song url feel free to use it or replace it with your own
                            src={"https://p.scdn.co/mp3-preview/e4a8f30ca62b4d2a129cc4df76de66f43e12fa3f?cid=null"}
                            >
                            </audio>
                        </div>
                        <canvas
                            ref="analyzerCanvas"
                            id="analyzer"
                            >
                            </canvas>
                        </div>
                    </div>
                );
            }
        }

        export default App;
```

Now we will create a  recursive function called renderFrame within our createVisualization method and then we will invoke it.

```
function renderFrame(){
    let freqData = new Uint8Array(analyser.frequencyBinCount)
    requestAnimationFrame(renderFrame)
    analyser.getByteFrequencyData(freqData)
    ctx.clearRect(0, 0, canvas.width, canvas.height)
    console.log(freqData)
    ctx.fillStyle = '#9933ff';
    let bars = 100;
    for (var i = 0; i < bars; i++) {
        let bar_x = i * 3;
        let bar_width = 2;
        let bar_height = -(freqData[i] / 2);
        ctx.fillRect(bar_x, canvas.height, bar_width, bar_height)
    }
};
renderFrame()
```

Here we are using some methods from analyser nodes to collect frequency data from the music playing and repeatedly draw bars on the canvas. The bars height depend on the audio's frequency. I made my bars purple but feel free to change the value of ctx.fillStyle to whatever color you want.

### Almost done!

Now we need to make sure to invoke the createVisualization method after the app component loads. We will do this with a built in method called componentDidMount. This is called after the render function. Add this to your app component's methods.
```
componentDidMount(){
    this.createVisualization()
}
```

Now we just need to bind the context of this to the createVisualization method so that when we say ```this``` we mean the app component, not the createVisualization method. We will do this in the constructor function.

```
constructor(props){
    super(props)

    this.createVisualization = this.createVisualization.bind(this)
}
```

### Congratulations you're done! Run npm start in your terminal to see it working on localhost:3000  and if you have problems check your code against mine:

```
import React, { Component } from 'react';
import './App.css';

class App extends Component {
    constructor(props){
        super(props)

        this.createVisualization = this.createVisualization.bind(this)
    }

    componentDidMount(){
        this.createVisualization()
    }

    createVisualization(){
        let context = new AudioContext();
        let analyser = context.createAnalyser();
        let canvas = this.refs.analyzerCanvas;
        let ctx = canvas.getContext('2d');
        let audio = this.refs.audio;
        audio.crossOrigin = "anonymous";
        let audioSrc = context.createMediaElementSource(audio);
        audioSrc.connect(analyser);
        audioSrc.connect(context.destination);
        analyser.connect(context.destination);

        function renderFrame(){
            let freqData = new Uint8Array(analyser.frequencyBinCount)
            requestAnimationFrame(renderFrame)
            analyser.getByteFrequencyData(freqData)
            ctx.clearRect(0, 0, canvas.width, canvas.height)
            console.log(freqData)
            ctx.fillStyle = '#9933ff';
            let bars = 100;
            for (var i = 0; i < bars; i++) {
                let bar_x = i * 3;
                let bar_width = 2;
                let bar_height = -(freqData[i] / 2);
                ctx.fillRect(bar_x, canvas.height, bar_width, bar_height)
            }
        };
        renderFrame()
    }

    render() {
        return (
            <div className="App">
                <h2>Sublime - 40oz to Freedom</h2>
                <div id="mp3_player">
                    <div id="audio_box">
                        <audio
                            ref="audio"
                            autoPlay={true}
                            controls={true}
                            //this is the link to my song url feel free to use it or replace it with your own
                            src={"https://p.scdn.co/mp3-preview/e4a8f30ca62b4d2a129cc4df76de66f43e12fa3f?cid=null"}
                            >
                            </audio>
                        </div>
                        <canvas
                            ref="analyzerCanvas"
                            id="analyzer"
                            >
                            </canvas>
                        </div>
                    </div>
                );
            }
        }

        export default App;
```
