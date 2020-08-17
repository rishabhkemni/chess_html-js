# chess_html-js
Tried building chess engine in javascript feel free to contribute and fork :)

## Primary Objective
* Create a Node.js web app, run, and debug it locally
* Create a basic a Socket.io server
* make it multiplayer and host somewhere

## Prerequisite software
Following pre-requisites are mandatory

* Windows 10
* [Visual Studio Code](http://code.visualstudio.com)
* [Node.js](https://nodejs.org)

## Firstly
````
  > npm install
 ````
 
 ## Objective
We will create a basic Node.js web app that serves up some HTML content using the Express framework and run it locally.

## Create your project folder

1. Open the **Node.js command prompt** from the task bar or by searching for it in Start.

2. Make a new folder named **chess** that will contain our source code and navigate to it

 ````
  > mkdir chess_html
  > cd chess_html
 ````
 
3. After you create the folder, now you need to create the package metadata for your node app. This file will include all of the project decencies. 
 
 ````
  > npm init
 ````
4. This will walk through a tutorial, keep the defaults except name your file **app.js**

5. List the folders contents and open the package file

   ````
   > dir
   ````
6. Open the current folder in Visual Studio Code

7. Observe your project metadata displayed in Visual Studio Code by clicking on the **package.json** file.


## Create a simple web server

1. Click on the new file button and create a new file named **app.js**

2. In your **app.js** file paste in the following code. This is the code for your web server.

    ````javascript
    var express = require('express');
    var app = express();
    app.use(express.static('public')); 
    var http = require('http').Server(app);
    var port = process.env.PORT || 3000;
    
    app.get('/', function(req, res) {
        res.sendFile(__dirname + '/public/default.html');
    });
    
    http.listen(port, function() {
        console.log('listening on *: ' + port);
    });
    ```` 

3. Create a new folder called **public** and create another file called **default.html** in the same folder and type hello world into it. Go ahead and save it.

4. Switch back to the Node.js command prompt and type **npm install express --save**. This will add the express web framework to your node app so you can host web content. 

    <sub>Note: The **--save** parameter adds a package dependency automatically for you in the **package.json** file.</sub>
    
    ```` 
    > npm install express --save
    ````
     
5. Switch back to Visual Studio Code, click the Debug button on the left and hit the play button to start your project. 

6. You can see in the Debug Console that the “listening on *: 3000” console message was printed out so we know our code is working. Let’s see if it’s hosting the page.

7. Open your browser and navigate to **http://localhost:3000** and you’ll see your webpage 
   
   <sub>Note: In case debug is not working simply open folder in cmd and run **node app.js** </sub>
   
## Add the chess game

### What we need
 1.	[Chessboard.js](http://www.chessboardjs.com/) – This is a full interactive touch ready chessboard that let’s you programmatically place chess pieces, respond to player movements through event callbacks, and customize piece theme. This is the UI and visual behavior.
 2.	[Chess.js](https://github.com/jhlywa/chess.js/) – This is the chess logic. It will know if something is a valid chess move and maintain the game logic and state. What’s cool is that it’s compatible with Chessboard.js by using a standard FEN notation for board layout and game moves.
 
### Get started
1. Add the chess markup to your **default.html** in your **/public/** file.

    ````html 
    <!doctype html>
    <html>
      <head>
        <title>Chess</title>
        <link rel="stylesheet" href="lib/chessboard-0.3.0.min.css">
        <link rel="stylesheet" href="/default.css" />
      </head>
      <body>
        <div id='gameBoard'></div>       
        <script src="lib/chess.min.js"></script> 
        <script src="lib/chessboard-0.3.0.min.js"></script> 
        <script src="lib/jquery-1.11.1.js"></script> 
        <script src="/default.js"></script>
      </body>
    </html>
    ````
     
2. Create a new file named **default.js** in your **/public/** folder and paste in the following source code.

    ````javascript
    var board;
    var game;

    window.onload = function () {
        initGame();
    };
    
    var initGame = function() {
       var cfg = {
           draggable: true,
           position: 'start',
           onDrop: handleMove,
       };
       
       board = new ChessBoard('gameBoard', cfg);
       game = new Chess();
    };

    var handleMove = function(source, target ) {
        var move = game.move({from: source, to: target});
        
        if (move === null)  return 'snapback';
    };
    ```` 
    
3. Create a new file named **default.css** in the **/public** folder. Copy the below styles to center the board on the page and give it a width.
   
    ````css
    body {
    	overflow-y: auto;
    	display: flex;
    	align-items: center;
            justify-content: center;
    } 
    
    #gameBoard {
        width: 400px;
    }

   ```` 
5. Finally, we need to go run the full game that we created. Click the play button to start debugging the Chess game. You’ll see that it’s running!

# Make it real-time with Socket.io

## Objective
What’s going to make our chess game special is that it’s real-time and superfast to make, and respond to chess moves. WebSockets and the Socket.io JavaScript framework are the special sauce that is going to make this work.
Respond to a simple Socket.io message

## Respond to a simple Socket.io message

### What we need
* [Socket.io](http://socket.io) - A client and server Javascript framework for doing real-time stuff with WebSockets

### Get started
1. Open the ‘Node.js command prompt’ and type ‘npm install socket.io’ This adds the project dependencies to our project.
  
  ```` 
    > npm install socket.io --save
  ````
  
2. Add an additional reference in your **public/default.html** to include the Socket.io client-side library above your other script references.

    ````html 
    <script src="/socket.io/socket.io.js"></script>
    ````
   <sub>Note: Libraries that your default.js code depend on, need to be declared before default.js if your loading them at the end of the body like this sample does.</sub>
   
3. Update **/public/default.js** to send a message to the server when someone clicks on the page.
  
  ````javascript
   // setup my socket client
   var socket = io();
   
    window.onclick = function(e) {
        socket.emit('message', 'hello world!');
    };
  ```` 
  
4. Now update **/app.js** to respond to that message by outputting to the log.

    ````javascript
    // setup my socket server
    var io = require('socket.io')(http);
     
    io.on('connection', function(socket) {
        console.log('new connection');
    
        socket.on('message', function(msg) {
            console.log('Got message from client: ' + msg);     
        });
    });
    ```` 

Now when you run it, observe in the console that your message made it across when you click on the page! 

## Connect our game to Socket.io
In our basic Socket.io server above, we just sent a message to the client every time someone clicked the button in their browser. For the chess game, all we need to do is send the chess move data to the Socket.io server every time there’s a move and send it to the other player. We’re going to use the same socket.emit() function to send a message, and the socket.on() handlers to respond to them.

1. Update your **/public/default.js** client to send a move with socket.emit when they make a move on the chess board.

    ````javascript
    // called when a player makes a move on the board UI
    var handleMove = function(source, target) {
        var move = game.move({from: source, to: target});
        
        if (move === null)  return 'snapback';
        else socket.emit('move', move);
        
    };
    ```` 

2. On the server in **/app.js** replace our simple onmessage handler with a handler for chess move.

    ````javascript
    io.on('connection', function(socket) {
        console.log('new connection');
    
        // Called when the client calls socket.emit('move')
        socket.on('move', function(msg) {
           socket.broadcast.emit('move', msg); 
        });
    });
    ````

## Handle moves you get from the server
Now that the server is broadcasting the move to the opponent all we need to do is handle that on the client to actually display the move. We just do that with another socket.on() callback, this time on the client. I think it’s super cool that Socket.io has the same programming model whether you’re on the client or server.

1. Update your **/public/default.js** client to handle a move with a socket.on handler.
    
    ````javascript
    // called when the server calls socket.broadcast('move')
    socket.on('move', function (msg) {
        game.move(msg);
        board.position(game.fen()); // fen is the board layout
    });
    ````

2. Now run your server and try out your game. When you make a move on when browser you should see it change on the other tab.


## Ideas for what's next
* Firstly currently if a player is trapped, no message is displayed. Need to ad that
* Need to make it more responsive
* Create a menu and a virtual space to find opponents
* Wrap the UI in a better framework like Ionic
* Consider building a 3D interface in Unity
* Need to deploy on AWS instance or any other hosting platform

# Credits

https://www.chess.com/article/view/how-to-set-up-a-chess-game

https://howtomakeachessengine.wordpress.com/2016/07/20/how-to-make-a-chess-engine/

https://decodechess.com/how-to-use-a-chess-engine-guide/

https://www.bing.com/videos/search?q=how+to+make+a+chess+engine+in+html&&view=detail&mid=017919B839060FF98741017919B839060FF98741&&FORM=VRDGAR&ru=%2Fvideos%2Fsearch%3Fq%3Dhow%2520to%2520make%2520a%2520chess%2520engine%2520in%2520html%26qs%3Dn%26form%3DQBVR%26sp%3D-1%26pq%3Dhow%2520to%2520make%2520a%2520chess%2520engine%2520in%2520html%26sc%3D0-34%26sk%3D%26cvid%3DFCACB425F1264564A512C9B5713017F2
