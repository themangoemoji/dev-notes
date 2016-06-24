# Twisted

## Transports, Protocols, & Protocol Factories


### Transports

* `ITtransport`
  
  * `getPeer` - identifies which server the data is coming from

* Represents a single connection that can send/recieve bytes AND handles the details of asynchronous I/O

* They abstract TCP/Unix Sockets/UDP sockets

* They throw requests over a wall and say "get to this when you can!"

### Protocols

* what are these crazy protocols to which you refer?

  * a twisted `IProtocol` object implements a protocol (duh).

  * one protocol object implements one specific networking protocol (IMAP, FTP, etc.)

  * so each connection our program makes/accepts will require one instance of a protocol

  * we store the state of 'stateful' protocols & the accumulated data of partially recieved messages

    * this is b/c we recieve bytes in arbitrary-sized chunks with asynch I/O


* methods to note:

  * `makeConnection`: method lets a protocol know what connection it is responsible for

  * it is a callback

  * twisted code calls this with a `Transport` instance as its only argument

    * the transport instance is the connection that the protocol will use

### Protocol Factories

* what are these nifty factory things?

  * they make new protocol instances

  * twisted needs a way to create connections (protocols/transports) on demand whenever a new connection is made

  * the *Protocol Factory API* is defined by `IProtocolFactory`

* important methods

  * `ProtocolFactor.buildProtocol` --> returns new protocol instance when it is called

## All of these working together

* We tell the `reactor` to make connections like this:


```python
factory = PoetryClientFactory(len(addresses))

from twisted.internet import reactor

for address in addresses:
    host, port = address
    reactor.connectTCP(host, port, factory)
```

* Also, the `Factory` implements `buildProtocol` for us:

```python
def buildProtocol(self, address):
    proto = ClientFactory.buildProtocol(self, address)
    proto.task_num = self.task_num
    self.task_num += 1
    return proto
```

* The base class knows what protocol to build because we set it in our factory:

```python
class PoetryClientFactory(ClientFactory):

    task_num = 1

    protocol = PoetryProtocol # tell base class what proto to build
```

* protocol birth looks something like this (discrescion advised):

![Protocol Birth (ew)](http://i2.wp.com/krondo.com/blog/wp-content/uploads/2009/08/protocols-1.png)

* and a protocol connects to it's transport like this:

![a protocol connects to transport](http://i0.wp.com/krondo.com/blog/wp-content/uploads/2009/08/protocols-2.png)

* once the protocol is initialized and set up with its transport, it can start processing data:

```python
def dataReceived(self, data):
    self.poem += data
    msg = 'Task %d: got %d bytes of poetry from %s'
    print  msg % (self.task_num, len(data), self.transport.getPeer())
```
* once a poem has finished downloading, the protocol object notifies its factory with the calls:

```python
def connectionLost(self, reason):
    self.poemReceived(self.poem)

def poemReceived(self, poem):
    self.factory.poem_finished(self.task_num, poem)
``

* Handling errors is easier too:

```pytyhon
def clientConnectionFailed(self, connector, reason):
    print 'Failed to connect to:', connector.getDestination()
    self.poem_finished()
```

