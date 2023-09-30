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
## Thread Safety
Thread Safety is the avoidance of data races or race conditions, Which happens when multiple threads try to read and write to a shared resource causing unwanted behaviors. When using Parallel lua with roblox's [DataModel](https://create.roblox.com/docs/en-us/reference/engine/classes/DataModel). Roblox already has Thread Safety implemented into its Instances. Roblox's has 4 Safety Level: Unsafe, Read Parallel, Local Safe, Safe. You can tell if a function/property is has one of these tags by looking at the roblox's API. If it has no tags then it defaults to UnSafe.

!!!info Safety Tags
    ##### UnSafe
    Functions cannot be called and Properties cannot be read or write(modified) to

    ##### Read Parallel
    Properties can be read but not write to 

    ##### Local Safe
    If the instance was created was created under an actor then that actor can Read and Write to. Other actors can Read but not write. Functions can be called only in that actor.

    ##### Safe
    Functions can be called and Properties can be read and write to

### Data Races
Data Races is when a thread read or writes data just as another thread is also writing to that shared Variable, Causing unexpected output. 
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
print(SharedTable.Value) --> This won't always print 200 as both Parallel are writing to The Value at the same time which can over lap
```
!!!info More Explanation
    The Reason why its happening can be shown with this diagram. If you look at Value, each time a thread read and writes it is reading and writing at the same time (this will not always happen) so Value will only update
    ![Data Races](https://raw.githubusercontent.com/haotian2006/HelperDocs-contributions/master/Images/ThreadSafety.png)