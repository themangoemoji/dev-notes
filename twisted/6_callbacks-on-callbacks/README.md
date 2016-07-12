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

## The Program Life Cycle

#### 1. `poetry_main():`

> starting the program

1. Get the addresses from our arguments

2. Import the reactor

3. Set up data structures to collect our poems & or erros

4. For each of our addresses, we call (get_poetry) with callbacks to deal with having got a poem and having a poem fail (got_poem, poem_failed)

#### 2. `get_poetry(host, port, got_poem, poem_failed)`

1. **import our reactor**

> `from twisted.internet import reactor`

2. **create the factory** and hand it the **callback** and **errbacks** (got_poem, poem_failed)

> `factory = PoetryClientFactory(callback, errback)`

    * In our loop of addresses, we create factories which we give functions to deal with finishing a poem and failing on a poem

    * protocol is created (and is used for all factories)

    * *NOTE: Our factroy `__init__` sets the callback and errback as member vars*

    > ```python
    def __init__(self, callback, errback):
        self.callback = callback
        self.errback = errback
      ```

3. Our reactor connects to TCP with host and port (passed from connectTCP)

> `reactor.connectTCP(host, port, factory)`

4. the reactor returns IConnector which calls callbacks on the factory when the connection is made, failed or lost. We need to provide `factory` with those callbacks

#### `PoetryProtocol(protocol)`

1. creates `poem` data member

2. Receives data, adds it to `poem`, does this until `connectionLost` is called

3. Connection Lost calls poemRecieved

4. Poem Received passes the poem to the factory, which places the poem into the `got_poem` callback

5. Callback takes the poem and checks if it has all of the poems (that ammount to the number of addresses) or not. If it does, the reactor stops, if it doesnt, the reactor continues
