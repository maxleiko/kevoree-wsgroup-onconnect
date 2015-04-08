## Kevoree WSGroup "onConnect" example

### Architecture
This example is composed of 3 different runtimes.  

 - **main** starts a **JavascriptNode** composed of:
   - `printer: ConsolePrinter` to log incoming messages
   - `broker: WSMsgBroker` to give a message broker endpoint to clients
   - `sync: WSGroup` to broadcast models accross nodes and add a **Ticker** and a **WSChan** binding to each new client with `onConnect`
 - **jsClient** starts a **JavascriptNode** that connects to the **WSGroup** master server
 - **javaClient** starts a **JavaNode** that connects to the **WSGroup** master server

### onConnect content
```kevs
add {nodeName}.ticker: Ticker
bind {nodeName}.ticker.tick chan
```
Each time a new node will connect to `sync: WSGroup`, the **KevScript** content in **onConnect** will be interpreted on the master node and send back to the newly connected node as a **new model to deploy**.  

 - `{nodeName}` refers to the newly connected node name
 - `{groupName}` refers to the current WSGroup name

> So the content of **onConnect** will add a `ticker: Ticker` instance to the newly connected node, and it will bind its ticker output port to the current `main.kevs` channel instance `chan: WSChan`


### How to run it
First of all, you will need to start the `main.kevs` runtime
```sh
kevoreejs -p . -n mainNode --kevscript main.kevs
```

> If you do not have **kevoreejs** available, just run:  
> `npm i -g kevoree-nodejs-runtime`

Then, start wheter the Javascript or the Java client (order does not matter)
```sh
# Java client
java -Dnode.name=javaClient -Dnode.bootstrap=javaClient.kevs -jar kevoree.jar
```
```sh
# JavascriptClient
kevoreejs -p . -n jsClient --kevscript jsClient.kevs
```
