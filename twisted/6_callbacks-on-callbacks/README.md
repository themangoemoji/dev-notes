# Lesson 6 - Callback Inception

In #5, we used the `ProtocolFactory` to do more than just create a protocol.

It was also in charge of shutting down a program when it was finished.

That was bad. That was a very bad thing to do. So how can we fix it?

Well, twisted uses **callbacks** to solve problems, right? Seems like a pretty sweet thing so maybe we can try it too.

Maybe it'll look something like this:

```python

def get_poetry(host, port, callback):
    """
    Download a poem from the given host and port and invoke

      callback(poem)

    when the poem is complete.
    """

```

the `get_poetry()` definition might look like this:

```python

def get_poetry(host, port, callback):
    from twisted.internet import reactor
    factory = PoetryClientFactory(callback)
    reactor.connectTCP(host, port, factory)

```

`Protocol` defines:

```python

    def dataReceived(self, data):
        self.poem += data

    def connectionLost(self, reason):
        self.poemReceived(self.poem)

    def poemReceived(self, poem):
        self.factory.poem_finished(poem)


```

when connection is lost, run `poemRecieved` which runs the `factory.poem_finished` method and then calls the callback.

So the protocol figures out when the poem is finished, lets the factory know that the specific poem (passed as arg) is finished.

The factory then does a callback of `got_poem`.

This `got_poem` is defined in poetry_main and is passed into get_poetry 

You give the factory a callback and then give the reactor the factory
