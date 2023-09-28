#Parallel luau
Parallel Luau is a way of achieving real multithreading in roblox using the Actor instance.

!!!info What is Multithreading
    Multithreading is a way of running multiple threads at the same time. Base lua does not have this features and when you use task.spawn or coroutines it is not really multithreading. The function is still only running one a single thread but lua makes it so it looks like it is running at the same time


## Thread Safety
Thread Safety is the avoidance of data races, Which happens when multiple threads try to read and write to a shared resource causing unwanted behaviors. When using Parallel lua with roblox's [DataModel](https://create.roblox.com/docs/en-us/reference/engine/classes/DataModel). Roblox already has Thread Safety implemented into its Instances. You can tell if a methods/property is safe using roblox's Safety Level: Unsafe, Read Parallel, Local Safe, Safe.

### UnSafe
**Functions**
Cannot be called in parallel 
**Properties**
Cannot be read or written in Parallel

### Read Parallel
**Functions**
N/A - function will never have this
**Properties**
Can be read but not modifiable in Parallel 

### Local Safe
**Functions**
If the 
**Properties**

### Safe
**Functions**
Can be called
**Properties**
Can be read and written

```{r echo = False, results = ''asis}
libary(kniter)
```