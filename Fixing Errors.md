# Fixing Errors 
This section will go over some basics on how to fix or find errors

# Checking Output 
Usually if your code doesn't run then it might be because be because the code has errored.

To open output you go to View -> Output
![Alt text](https://media.discordapp.net/attachments/1090554268492247103/1144376169463042178/image.png?width=767&height=127)

!!!info 
    Another useful tool is the Script Analysis, what is does is show warnings/errors (mainly [syntax](https://www.sciencedirect.com/topics/engineering/syntax-error) related). It is directly below Output

To see if there is an error you will see a message that are red like this one
![error](https://media.discordapp.net/attachments/1090554268492247103/1144377810933260418/image.png?width=767&height=104)

You can click on the error and it will bring you to the line and script that it errored on 

If clicking on it doesn't lead you to any where and you see this 
![a](https://media.discordapp.net/attachments/1090554268492247103/1144378615115546724/image.png?width=767&height=103)
when you hover over it it means that the script it errored in was deleted.
What you can do is click on the error while in game instead of after stopping and it should lead you to the right script.

## Reading errors messages
![smtidk](https://media.discordapp.net/attachments/1090554268492247103/1144383368163774474/image.png?width=767&height=182)

<span style="color:Orange">Time of message: </span> When did it error
<span style="color:Lime">Script Location: </span> What script did it error on and where is the script
<span style="color:magenta">Line the error is on: </span> What line did it error on
<span style="color:yellow">Error Message: </span> What is the error
<span style="color:White">Environment: </span> Did is error on the server, client, or studio

You mainly only need to focus on the Script Location, error line, and the error it self. If its an error you don't understand you can use google to search for the error message. There is a a section below explaining how to do that

## Common Error Messages
### <span style="color:red"> attempt to perform arithmetic(arithmetic) on number and [type] </span>
This means you are trying to do and arithmetic on a number an a type you can not do arithmetic on. 
The only objects you can do arithmetic on is numbers , strings, objects that has arithmetic metamethods (__mul,__add...)

!!!Warning 
    This also applies to <span style="color:red"> attempt to compare something < number </span> and <span style="color:red"> attempt to concatenate string with something </span>. They either must be types that be compared on concatenated or have the [metatmethods](https://create.roblox.com/docs/luau/metatables#metamethods) that allow them to do it
###  <span style="color:red"> Argument 1 missing or nil </span>
This means that one of the argument you sent is nil or empty. arguments are the data you are sending ex.
```lua
local function f(x) -- x is a parameter
    ...
end
f(y) -- y is an argument
```
to fix this make sure that the 1st argument (or what ever the error says) is being defined correctly
###  <span style="color:red"> Unable to cast value to Object </span>
this means that the value you are trying to send cannot bet convert into the object the function wants. To fix this make sure that the date type is the correct one.

# Print Debugging 
Print debugging mainly consists of adding print statements to find out what wrong this is mainly used if there are no errors showing up in output

## use case

lets say we have a script set up like this
```lua
local x = getX()
local y = getY()
if x >= 3 then
    if y == 0 then
        doSomething()
    end
end
```
and for some reason doSomething() is not being invoked, instead of going to scripting help and asking why the doSomething isn't working here is what you can do.

1. Print before an if statement 
```lua
local x = getX()
local y = getY()
print("script is running")
if x >= 3 then
   print("x check passed")
    if y == 0 then
       print("y check passed")
        doSomething()
    end
end
```
When you run the script and you only see 
```
script is running
x passed
```
Then you will know that y is not equal to 0

!!! 
    You don't really need to add all those prints to figure out what wrong for example if you know your code is running you can probably remove the print("script is running").
    You also can just print("X") (or anything that will help) instead of print("x check passed")
2. printing the variables

```lua
local x = getX()
local y = getY()
print(x)
if x >= 3 then
    print(y)
    if y == 0 then
        doSomething()
    end
end
```
```
4
4
```
From this you can tell that y is 4 not 0 so from this you would most likely look into how y is being assigned which in this case can tell us that something must be wrong with the getY function 

# visualize debugging
This type of debugging can be using parts or other methods to visualize the problem this can be used if using prints doesn't really tell you whats wrong 

## use case
Lets say we are doing some raycasting but the ray isn't hitting anything 
```lua
local green = workspace.PartA
local red = workspace.PartB
local direction = (green.Position-red.Position).Unit*20

local ray = workspace:Raycast(green.Position,direction)
print(ray)
print(direction)
```
![placeholder](https://media.discordapp.net/attachments/1097115140924645376/1143013121468940299/image.png?width=517&height=410)
```
nil
600.8695068359375, -531.7534790039062, -596.8199462890625
```

For beginners you might not see anything wrong with the script and looking at what direction is doesn't really help so what you can do here is create a part that goes in that ray direction like this

```lua
local green = workspace.PartA
local red = workspace.PartB
local direction = (green.Position-red.Position).Unit*20

local ray = workspace:Raycast(green.Position,direction)
print(ray)

local dir = Instance.new('Part')
dir.Size = Vector3.new(.2,.2,20)
dir.CFrame = CFrame.lookAt(green.Position+direction/2,green.Position + direction)
dir.Anchored = true
dir.Parent = workspace
```
![placeholder](https://media.discordapp.net/attachments/1097115140924645376/1143015453329989662/image.png?width=767&height=465)
From this we can see that direction is indeed wrong and when we look at how direction is defined we can see that we did (start-goal) when the proper way is (goal - start)

So fixing that and running the code will now give the correct results
```lua
local green = workspace.PartA
local red = workspace.PartB
local direction = (red.Position-green.Position).Unit*20

local ray = workspace:Raycast(green.Position,direction)
print(ray)

local dir = Instance.new('Part')
dir.Size = Vector3.new(.2,.2,20)
dir.CFrame = CFrame.lookAt(green.Position+direction/2,green.Position + direction)
dir.Anchored = true
dir.Parent = workspace
```
```
RaycastResult{PartB @ -4.82407379, 11.2030563, 18.3839722; normal = 0, 0, -1; material = Plastic} 
```
![placeholder](https://media.discordapp.net/attachments/1097115140924645376/1143015906289655878/image.png?width=621&height=435)

# Using google 
When you get an error message that you don't know or understand, what you can do is go to google.com and search for the error message. Usually you will find an devform post that has the same error and the a solution. Another way to help enhance the search is by using "" this tells google to look for that key word. Sometimes you might not get the results you're look for, for example this error
![placeholder](https://media.discordapp.net/attachments/1097115140924645376/1143422139744137257/image.png?width=552&height=24)
and when we do a google search we don't get any thing that relates to that
![hi - hao](https://media.discordapp.net/attachments/1097115140924645376/1143422833259724882/image.png?width=713&height=800)
so what we can do here is maybe change the wording or adding roblox into the search to help google search better
![asaada](https://media.discordapp.net/attachments/1097115140924645376/1143423095567298610/image.png?width=767&height=668)
and there we go.
# Using roblox debugger
Another way of debugging is using tools roblox provided such as break points, watch, call stack. But most of the times you won't really be needing this. but if you want you can find out how to use them [here](https://create.roblox.com/docs/studio/debugging).

# Common mistakes 
This section is just a list of common mistakes people tend to do

## 1.Using starter Gui instead of playergui
### StarterGui
StarterGui is a container that is under Game. Its purpose is to act like a container to hold your guis before they are cloned into the PlayerGui. When a player joins roblox will take everything that is currently in StarterGui and clone them into the players PlayerGui.
https://create.roblox.com/docs/reference/engine/classes/StarterGui

so given a script like the one below, it will **not** make ScreenGui visible because it is modifying a ScreenGui in StarterGui
 ```lua
 --LocalScript
local StarterGui = game.StarterGui 
local ScreenGui = StarterGui:WaitForChild("ScreenGui")
ScreenGui.Enabled = true --!! THIS WILL NOT WORK !!
 ```
### PlayerGui

PlayerGui is a container that is under the player that joined. Each player will have their own PlayerGui which other players will **not** be able to see. The server will be able to see it but its is **not recommended** to let the server modify anything in PlayerGui. PlayerGui is what the client actually sees.
https://create.roblox.com/docs/reference/engine/classes/PlayerGui

![hi](https://media.discordapp.net/attachments/1097115140924645376/1143426421381922907/image.png?width=210&height=122)

so to fix the given script above we can do this

 ```lua
 --LocalScript
local player = game.Players
local LocalPlayer = player.LocalPlayer
local PlayerGui = LocalPlayer.PlayerGui
local ScreenGui = PlayerGui:WaitForChild("ScreenGui")
ScreenGui.Enabled = true 
 ```

 ## 2. LocalScripts in the wrong places
another common mistake is having LocalScripts in the wrong place such as having a LocalScript in ServerScriptService. LocalScripts run in a client environment such as StarterPlayerScripts, ServerScriptService is on the server

![a](https://media.discordapp.net/attachments/1097115140924645376/1143428730899935252/image.png?width=767&height=251)
https://create.roblox.com/docs/reference/engine/classes/LocalScript

## 3. Trying to access ServerStorage from the client
ServerStorage is a Storage for the server to see not the client. If you want to store something store it in ReplicatedStorage

## 4. Indirect Change
Indirect Change is when you are changing the variable not the actual value, for example:

```lua
local t = {}
t.Value = 1
local value = t.Value
value = 5
print(t.Value) --> 1
print(value) -->5

--another example:

local part = workspace.Part
local position = part.Position

position = Vector3.new(1,2,3)
-- this is wrong
```
The example above are wrong because you are not changing the values but instead the variable.

the proper way to do this is do to:
```lua
local t = {}
t.Value = 1
t.Value = 5
print(t.Value) --> 5

--another example:

local part = workspace.Part
part.Position = Vector3.new(1,2,3)
```


## 5. Not having a BindToClose in their DataStoreSaving script
A very common mistake people tend to make is not having an BindToClose in their Datastore script

Here is an example script:
```lua
local dss =game:GetService("DataStoreService")
local MyDs = dss:GetDataStore("MyStore")

local function save(player)
    MyDs:SetAsync(player.UserId,"your data")
end

game.Players.PlayerRemoving(save)
```
This will work, but will only work sometimes, this is because when the last person leaves roblox doesn't care if a script is still running or not. To fix this you can use a [BindToClose](https://create.roblox.com/docs/reference/engine/classes/DataModel#BindToClose). What a BindToClose does is it forces roblox's server or studio to wait until the function inside of it is done running (30 seconds max) then tell the server to close.

Heres how we can implement it:
```lua
local dss =game:GetService("DataStoreService")
local MyDs = dss:GetDataStore("MyStore")

local function save(player)
    MyDs:SetAsync(player.UserId,"your data")
end

game.Players.PlayerRemoving(save)

game:BindToClose(function()
    for _,Player in game.Players:GetPlayers() do
        save(Player)
    end
end)
```

!!! warning 
    Another mistake people tend to do is using spawn/task.spawn inside of the BindToClose
    ```lua
    game:BindToClose(function()
        for _,Player in game.Players:GetPlayers() do
            task.spawn(save,Player)
        end
    end)
    ```
    this is wrong because task.spawn makes it so that the the saving process doesn't yield, but we need it to yield. If you want to do it correctly you can do it like this
    ```lua
    game:BindToClose(function()
        local players = game.Players:GetPlayers()
        local amt = #players -- how many players needs to be saved
        local count = 0 -- how many players has been saved
        for _,Player in players do
            task.spawn(function() 
            -- makes it so that everyone save at the same time instead of 1 by 1
                save(Player)
                count += 1
                -- when player is done saving increase
            end)
        end
        repeat task.wait() until count == amt -- yields until everything is done
    end)
    ```
    or if you want to be fancy
    ```lua
    game:BindToClose(function()
        local players = game.Players:GetPlayers()
        local thread = coroutine.running() --gets current thread
        local amt = #players -- how many players needs to be saved
        local count = 0 -- how many players has been saved
        for _,Player in players do
            task.spawn(function()
            -- makes it so that everyone save at the same time instead of 1 by 1
                save(Player)
                count += 1
                -- when player is done saving increase
                if count == amt then
                    coroutine.resume(thread)
                    -- if its the last player, resume the thread
                end
            end)
        end
        if count == amt then return end -- if everyone's data has already been saved there is no need to yield
        coroutine.yield() -- yields the thread
    end)
    ```
## 6. Animations working in Studio but not In game
Animations only work if the game the animation is owned is the same to fix it you will have to upload the animation to the same game owner or group 

![ani](https://media.discordapp.net/attachments/1097115140924645376/1133659449123410000/togif.gif?width=767&height=367)

# Conclusion  
Hope this helps if you have any more question you can ask in scripting-help. Anyways Good luck out there and don't get joe tagged.

![joe](https://media.discordapp.net/attachments/895151830345084949/1129887795138277496/togif.gif?width=360&height=96)