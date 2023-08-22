# Debugging 
Debugging is a process where you use prints or other methods to find out what is wrong with your code

# Print Debugging 
This type of debugging can be used in most languages with ways to log code. Print debugging mainly consists of adding print statements to find out what wrong

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
    print("x passed")
    if y == 0 then
        print("y passed")
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
    You don't really need to add all those prints to figure out what wrong for example if you know your code is running you can probably remove the print("script is running")
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
From this you can tell that y will is 4 not 0 so from this you would most likely look into how y is being assigned which in this case can tell that something must be wrong with the getY function 

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
From this we can infer that direction is indeed wrong and when we look at how direction is define we can see that we did (start-goal) when the proper way is (goal - start)

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
When you get an error message that you don't know or understand, what you can do is go to google.com and search for the error message. Usually you will find an devform post that has the same error and the same solution. Another way to help enhance the search is by using "" this tells google to look for that key word. Sometimes you might not get the results you're look for, for example this error
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

## 3. Trying to accuses ServerStorage from the client
ServerStorage is a Storage for the server to see not the client. If you want to store something store it in ReplicatedStorage