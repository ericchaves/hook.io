


     __    __    ______     ______    __  ___         __    ______   
    |  |  |  |  /  __  \   /  __  \  |  |/  /        |  |  /  __  \  
    |  |__|  | |  |  |  | |  |  |  | |  '  /         |  | |  |  |  | 
    |   __   | |  |  |  | |  |  |  | |    <          |  | |  |  |  | 
    |  |  |  | |  `--'  | |  `--'  | |  .  \    __   |  | |  `--'  | 
    |__|  |__|  \______/   \______/  |__|\__\  (__)  |__|  \______/  

    a full featured i/o framework for node.js
    
# v0.4.0

## Choose from an extensive library of tiny, independent, autonomous "hooks" that emit and receive events to seamlessly compose robust i/o scenarios

# Features :

- Designed to build: large, decoupled, distributed, and fault tolerant I/O heavy applications
- Every hook is an isolated and separate node.js process ( that can be running anywhere )
- Every hook can have multiple upstreams ( outgoing client connections ) and multiple downstreams ( listening servers ) to other hooks
- Bi-direction communication on both upstream and downstream connections
- Interprocess Message Publishing and Subscribing done through [EventEmitter2](https://github.com/hij1nx/EventEmitter2) and [dnode](http://github.com/SubStack/dnode)
- Messaging API inherits and mimics Node's native EventEmitter API ( with the help of EventEmitter2 )
- Spawning and Daemonizing of processes handled with [Forever](https://github.com/indexzero/forever)
- Easily connect / disconnect hooks "hot" without affecting other services
- Core library currently checking in at about ~120 lines of code :-)

**TODO**

 - Create .start() method with auto role detection
 - Add ability to dynamically increment and assign ports for servers
 - Think about sibling event namespacing
   - "in" and "out" terminology is confusing
   -  is allows duplicate event names on in/out is smart?
 - Create better demos
 - Release more hooks
 - Create Flow Chart explaining architecture
 - Add a config.json per hook using nconf
 - Add ability to pass in argv data
 - Create better system for automatically loading upstreams via forever
 - Refactor [kohai](http://github.com/nodejitsu/kohai) bot to use hook.io
 
# Available Hooks ( more coming soon )

**Core** 

  - [hook.io-asyncify](http://github.com/marak/hook.io-asyncify)
  - [hook.io-repl](http://github.com/marak/hook.io-repl)
  - [hook.io-logger](http://github.com/marak/hook.io-logger)
  - [hook.io-webhook](http://github.com/marak/hook.io-webhook)
  - [hook.io-webserver](http://github.com/marak/hook.io-webserver)

**Web**
  
  - [hook.io-irc](http://github.com/marak/hook.io-irc)
  - [hook.io-twitter](http://github.com/marak/hook.io-twitter)

**Utility**

  - [hook.io-jsdom](http://github.com/tmpvar/hook.io-jsdom)
  - [hook.io-levenshtein](https://github.com/AvianFlu/hook.io-levenshtein)

  
**Humor**

  - [hook.io-insult](http://github.com/marak/hook.io-insult)


## Basic Hook Syntax

 ``` js
     var Hook = require('hook.io').Hook;
     var myhook = new Hook( { name: "myhook"} );
     
     myhook.start();
     
     myhook.on('ready', function(){
       
       // listen for messages on myhook's "upstream"
       // these are messages which are sent to the hook from its parent
       // "upstream" messages are always rebroadcasted to all siblings
       myhook.on('up.*', function(name, event, data){

         // whenever we get a message from a hook, let's echo back withs its name and event
         myhook.emit('up.echo', name + event);

       });


       // we can also listen for a specific message on myhook's "upstream"
       // we will chose to re-broadcast this specific message to myhook's children
       myhook.on('up.*', function(name, event, data){

         // emit to myhook's children ( if there happen to be any )
         myhook.emit('down.echo', name + event);

       });

       // listen for messages on myhook's "downstream"
       // these are messages which are sent to the hook's children
       myhook.on('down.*', function(name, event, data){
           console.log(name, ' ', event, ' ', data);
       });
       
     });

 
## Try out a contrived demo 

We will create a "battle" ( an i/o hook with a downstream ) that can have several actors ( i/o hooks with upstreams ). These actors will then work together in the battle with hilarious results. This demo begins to showcase the power of an input output hook. More involved demos will be coming shortly. 

## Install hook.io

    git clone git://github.com/Marak/hook.io.git
    cd hook.io
    [sudo] npm install
    
## Start up the battle

    node hooks/battle
    
*Starts up a hook process with a downstream server. Now the battle has started and is waiting for some actors.*


## Connect actors to the battle

    node hooks/scout
    node hooks/gunner
    node hooks/ammo_guy

*Starts up three hook processes with upstream connections to the battle. Each actor is responible for a specific task in the battle.*

## Introduce an enemy to the battle!

    node hooks/enemy
   
*Starts up an enemy hook process which causes a chain reaction of events from other actors causing the enemy to be shot to death.*


## Create a sound effects hook

    node hooks/sfx
   
*Starts up a sound effects hook which listens for messages from all the actors and plays funny sounds effects!*



    
## Contributors ( through code and advice )

AvianFlu, Chapel, Substack, Dominic Tarr, Charlie Robbins, h1jinx, Tim Smart