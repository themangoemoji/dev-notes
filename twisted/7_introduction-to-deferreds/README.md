# A Deferred, deferred

```python
from twisted.internet.defer import Deferred

def got_poem(res):
    print 'Your poem is served:'
    print res

def poem_failed(err):
    print 'No poetry for you.'

d = Deferred()

# add a callback/errback pair to the chain
d.addCallbacks(got_poem, poem_failed)

# fire the chain with a normal result
d.callback('This poem is short.')

print "Finished"
```

**RESULT:**

```
Your poem is served:
This poem is short.
Finished
```

Important notes:

1. This code makes a new deferred, adds a callback/errback pair with the `addCallbacks` method, and then fires the “normal result” chain with the `callback` method. Of course, it’s not much of a chain since it only has a single callback

2. the callbacks we add to this deferred each take one argument, either a normal result or an error result. It turns out that deferreds support callbacks and errbacks with multiple arguments, but they always have at least one, and the first argument is always either a normal result or an error result.

3. the callback method fires the deferred with a normal result, the method’s only argument.

4. Looking at the order of the `print` output, we can see that firing the deferred invokes the callbacks immediately. There’s nothing asynchronous going on at all. There can’t be, since no reactor is running. It really boils down to an ordinary Python function call.

## A `Deferred` can't serve more than one callback

This won't work:

```python
from twisted.internet.defer import Deferred
def out(s): print s
d = Deferred()
d.addCallbacks(out, out)
d.callback('First result')
d.errback(Exception('I have failed you my son'))
print 'Finished'
```
It will error with:

```bash
:!python deffered_demo.py

First result
Traceback (most recent call last):
  File "deffered_demo.py", line 6, in <module>
    d.errback(Exception('I have failed you my son'))
  File "/Library/Python/2.7/site-packages/twisted/internet/defer.py", line 434, in errback
    self._startRunCallbacks(fail)
  File "/Library/Python/2.7/site-packages/twisted/internet/defer.py", line 494, in _startRunCallbacks
    raise AlreadyCalledError
twisted.internet.defer.AlreadyCalledError
shell returned 1
```
**A deferred can only be fired once.**
