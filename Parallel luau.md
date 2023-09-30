#Parallel luau
Parallel Luau is a way of achieving real multithreading in roblox using the Actor instance.

!!!info What is Multithreading
    Multithreading is a way of running multiple threads at the same time. Base lua does not have this feature and when you use task.spawn or coroutines it is not really multithreading. The function is still only running one a single thread but lua makes it so it looks like it is running at the same time

## Actors
Actors is a Instance that allows scripts under that Actor to run in Parallel. 

!!!warning Actors and Modules
    The Memory under Actors will not be the same so when you require a module a different table will be returned (if your module returns a table) then in the main Thread. This means you can't use modules to transfer data between Actors. 
    ```lua
    --Script1
    local module = require(...)
    module.X = 1
    print(module.X) --> 1

    --Script2 
    local module = require(...)
    task.wait(1)
    print(module.X) --> 1

    --Script Under Actor
    local module = require(...)
    task.wait(1)
    print(module.X) --> nil 
    ```
## Communicating Between Threads
There are current 4 ways to efficiently communicate between threads: Actor Messaging API, Shared Tables, Bindables, or Direct Data Model Communication.

###Actor Messaging API
The Actor Messaging Api allows a script to receive data from other actors or the main thread. It currently Consists of 3 methods: SendMessage(), BindToMessage(), BindToMessageParallel(). 

##### SendMessage
Used to Send Messages between threads. First argument would be the key while the rest will be the data to send
```lua
local Actor = ...
workerActor:SendMessage("myKey", "hello world")
```
##### BindToMessage(Parallel)
Used to receive data. If BindToMessageParallel was used then run the code in Parallel. First Parameter is the key while second Parameter is a callback.
```lua
local Actor = script:GetActor()
Actor:BindToMessage("myKey", function(data)
    print(data) 
end)

Actor:BindToMessageParallel("myKey", function(data)
    print(data)
end)
```
###Shared Tables
[Shared Table](https://create.roblox.com/docs/reference/engine/datatypes/SharedTable) is like a normal table except it's contents is shared between threads. Meaning that if you update a value in one thread, it will also update for other threads. 
```lua
local SharedTableRegistry = game:GetService("SharedTableRegistry") 
local SharedTable = SharedTableRegistry:GetSharedTable("mySharedTable")
--//Parallel Thread
SharedTable.X = 1
--//Another Parallel Thread
print(SharedTable.X) --> 1
``` 
!!!Warning Thread Safety
    When using SharedTable you could have race conditions which can cause unwanted behaviors. If you want to know more about Thread Safety you can scroll down
###Bindables
One of the best ways right now (9/29/2023) to send data between threads is using Bindables as they are almost 4x more efficient then SharedTables. 

```lua
--//main Thread
local Actor = ...
local BindableFunction = Actor.Bindable -- assuming you have an event/function parented to the Actor
local data = BindableFunction:Invoke("DoSomething")

--//Actor Script
local Actor = script:GetActor()
local BindableFunction = Actor.Bindable
BindableFunction.OnInvoke(data)
    task.desynchronized() -- runs in parallel 
    return doSomething(data)
end
```
!!!info Tables and Bindables
    When Sending/Returning Tables, avoid large dictionaries if you can. If possible try to convert dictionaries into arrays. 

### Direct Data Model Communication
This type of communication uses the Instances under game and modifying/reading their properties. However, to maintain thread-safety roblox has systems that disallows threads that are desynchronized to read/write to the Instances.  
## Thread Safety
Thread Safety is the avoidance of race conditions, Which happens when multiple threads try to read and write to a shared resource causing unwanted behaviors. When using Parallel lua with roblox's [DataModel](https://create.roblox.com/docs/en-us/reference/engine/classes/DataModel). Roblox already has Thread Safety implemented into its Instances. Roblox's has 4 Safety Level: Unsafe, Read Parallel, Local Safe, Safe. You can tell if a function/property is has one of these tags by looking at the roblox's API. If it has no tags then it defaults to UnSafe.

!!!info Safety Tags
    ##### UnSafe
    Functions cannot be called and Properties cannot be read or write(modified) to

    ##### Read Parallel
    Properties can be read but not write to 

    ##### Local Safe
    If the instance was created was created under an actor then that actor can Read and Write to. Other actors can Read but not write. Functions can be called only in that actor.

    ##### Safe
    Functions can be called and Properties can be read and write to

### Race Conditions
Race Conditions is when a thread read or writes data when another thread is not finish computing that data, causing data to overlap or create unexpected behaviors. 
```lua
--Example
local SharedTable = ...
SharedTable.Value = 0
--Lets say we are trying to add to a key called Value in the SharedTable
--Each Thread will add 100 to the Value Key

--//Parallel Thread
for i = 1,100 do
    SharedTable.Value = SharedTable.Value + 1
end

--//Another Parallel Thread
for i = 1,100 do
    SharedTable.Value = SharedTable.Value + 1
end

--//Outside of Parallel (After Parallel Threads ran)
print(SharedTable.Value) --> This won't always print 200 as both Parallel are writing to The Value at the same time which can overlapping
```
!!!info More Explanation
    The Reason why its happening can be shown with this diagram.
    ![Race Conditions](https://raw.githubusercontent.com/haotian2006/HelperDocs-contributions/master/Images/ThreadSafety_.png)
    If you look at Value, each time a thread read and writes it is reading and writing at the same time (this will not always happen). So it means that if both Threads run a for loop increase Value by 100, Value will not be 200 most of the time. 
### Avoiding Race Conditions
When working with the DataModel roblox already implemented Thread Safety as shown above. Otherwise what you can do is combine the data in a Synchronized state so there is a lesser chance of data overlapping with each other or if you are working with shared table use the functions [increment()](https://create.roblox.com/docs/reference/engine/datatypes/SharedTable#increment) or [update()](https://create.roblox.com/docs/reference/engine/datatypes/SharedTable#update) to update data as it does an atomic update to the values.

## Using Microprofiler 
To open microprofiler on the client press ctrl-shift-f6. To pause press ctrl-P, If it opens and object viewer press ctrl-shift-f6 twice to reopen it and press ctrl-P again.
### Using the Debug Library
You can use profilebegin to help find how long your tasks takes take this function:
```lua
function tasks.calculatePrimesFrom(from,to)
    debug.profilebegin("primes")
    local primes = {}
    for i = from,to do
        table.insert(primes,is_prime(i))
    end
    debug.profileend()
    return primes
end
```
This will allow you to see how long primes take![Alt text](https://raw.githubusercontent.com/haotian2006/HelperDocs-contributions/master/Images/primes.png)
