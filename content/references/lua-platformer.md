---
title: Lua - Platformer
---

## Part 1

If you're new to programming, I highly suggest checking out my first tutorial [Your First Love2d Game in 200 Lines](http://osmstudios.com/tutorials/your-first-love2d-game-in-200-lines-part-1-of-3). This tutorial will assume some programming knowledge. We'll start with moving that knowledge to game development in [Love2d](https://love2d.org/).

In part 1 we're going to make a small proof of concept. We'll build a little platformer with a player who can interact with platforms, move, and jump. In part 2 we'll abstract out that code and prepare to make our little protagonist into the big protagonist of a real game. We'll have plenty of time to talk about gamestates, building levels, animation, and more once our super simple platformer is done.

Let's start by making our basic Love engine functions:

```lua
function love.load()
  
end

function love.update(dt)

end

function love.keypressed(key)
  if key == "escape" then
    love.event.push("quit")
  end
end

function love.draw(dt)
  
end
```

One of the first things I do when starting a new Love project is to add that love.keypressed function that listens for "escape." I don't have time to be exiting out in other ways. When our game is done we'll change that to be a pause button.

Next, let's think about our minimal platformer game. What is the least amount of work we can do to get a working demo? We call this the minimal viable product in the more professional development industry. You might be thinking, "well we need bosses and levels and score keeping and bad guys." Think simpler! A platformer needs a player character and platforms. That's it. Everything else, bosses and enemies, is just an expansion of that. We'll make a plan for dealing with all of that in the next part.

Let's go ahead and define a stub player above those functions.

```lua
-- Setup a player object to hold an image and attach a collision object
player = {
  x = 16,
  y = 16,
  
  -- Here are some incidental storage areas
  img = nil -- store the sprite we'll be drawing
}
```

So far, we've given the player a location, but we know from playing platformers that there are certain ways the player character interacts with the world. The player should be able to move backwards and forwards. Unlike my side-scrolling tutorial, the player doesn't move in set intervals. The player character needs to accelerate in a direction up to a given maximum and slow-down when the player stops giving the command. In other words, the player needs a velocity and friction with the ground. Let's store these values. Replace the player code block with:

```lua
player = {
  x = 16,
  y = 16,
  
  -- The first set of values are for our rudimentary physics system
  xVelocity = 0, -- current velocity on x, y axes
  yVelocity = 0,
  acc = 100, -- the acceleration of our player
  maxSpeed = 600, -- the top speed
  friction = 20, -- slow our player down - we could toggle this situationally to create icy or slick platforms

  -- Here are some incidental storage areas
  img = nil -- store the sprite we'll be drawing
}
```

We'll also need to jump. This is a more complex version of above. We'll set a jump acceleration as well as some maximums on how our character jumps. Finally, if we're jumping, we'll need gravity.

```lua
-- The finished player object
player = {
  x = 16,
  y = 16,
  -- The first set of values are for our rudimentary physics system
  xVelocity = 0, -- current velocity on x, y axes
  yVelocity = 0,
  acc = 100, -- the acceleration of our player
  maxSpeed = 600, -- the top speed
  friction = 20, -- slow our player down - we could toggle this situationally to create icy or slick platforms
  gravity = 80, -- we will accelerate towards the bottom

  -- These are values applying specifically to jumping
  isJumping = false, -- are we in the process of jumping?
  isGrounded = false, -- are we on the ground?
  hasReachedMax = false, -- is this as high as we can go?
  jumpAcc = 500, -- how fast do we accelerate towards the top
  jumpMaxSpeed = 9.5, -- our speed limit while jumping

  -- Here are some incidental storage areas
  img = nil -- store the sprite we'll be drawing
}
```

Go ahead and finish our player out by adding…

```lua
player.img = love.graphics.newImage('assets/character_block.png')
```

…to the `love.load` function and…

```lua
love.graphics.draw(player.img, player.x, player.y)
```

…to `love.draw`.

If everything went as planned you should see a little rectangle on a black screen when you run the application.

The Player Character has a lot of variables to take in, but they'll make more sense as we use them. First, let's go ahead and write in our movement system. It's quite a bit more complex than the code in our scrollling shooter. We're rolling in some very rudimentary physics. The player will have a velocity and be acted on by gravity and friction.

Replace `love.update` with this code block:

```lua
function love.update(dt)
  player.x = player.x + player.xVelocity
  player.y = player.y + player.yVelocity

  -- Apply Friction
  player.xVelocity = player.xVelocity * (1 - math.min(dt * player.friction, 1))
  player.yVelocity = player.yVelocity * (1 - math.min(dt * player.friction, 1))

  -- Apply gravity
  player.yVelocity = player.yVelocity + player.gravity * dt

	if love.keyboard.isDown("left", "a") and player.xVelocity > -player.maxSpeed then
		player.xVelocity = player.xVelocity - player.acc * dt
	elseif love.keyboard.isDown("right", "d") and player.xVelocity < player.maxSpeed then
		player.xVelocity = player.xVelocity + player.acc * dt
	end

  -- The Jump code gets a lttle bit crazy.  Bare with me.
  if love.keyboard.isDown("up", "w") then
    if -player.yVelocity < player.jumpMaxSpeed and not player.hasReachedMax then
      player.yVelocity = player.yVelocity - player.jumpAcc * dt
    elseif math.abs(player.yVelocity) > player.jumpMaxSpeed then
      player.hasReachedMax = true
    end

    player.isGrounded = false -- we are no longer in contact with the ground
  end
end
```

Let's walk through the code. There is a lot to see here so brace yourself. First, we move the player x, y position to their old position plus their velocity on the given axis.

```lua
player.x = player.x + player.xVelocity
player.y = player.y + player.yVelocity
```

It's odd that we're doing this first. Normally we'd process the input first then add the velocity results. However, when we write the code for collision handling we'll want collisions handled immediately to avoid shudders.

Next we add a small amount of deceleration and call it friction.

```lua
player.xVelocity = player.xVelocity * (1 - math.min(dt * player.friction, 1))
player.yVelocity = player.yVelocity * (1 - math.min(dt * player.friction, 1))
```

`friction` was set in our player variables. All we are doing here is multiplying the current velocity by a number between 0 and 1. `math.min` just chooses the smaller of `dt * player.friction` and `1`. In the real-world the y axis doesn't have a whole lot of friction, but it ends up feeling better than turning the gravity down.

Gravity is just a constant downward acceleration. It will work the same way as our left / right movement code which looks like this:

```lua
if love.keyboard.isDown("left", "a") and player.xVelocity > -player.maxSpeed then
  player.xVelocity = player.xVelocity - player.acc * dt
elseif love.keyboard.isDown("right", "d") and player.xVelocity < player.maxSpeed then
  player.xVelocity = player.xVelocity + player.acc * dt
end
```

Pay attention to the signs here. For left we subtract and for right we add.

One of the fundamental elements of a platformer game is the player characters ability to interact with platforms. Obviously.

We have a few options here. We could use Love2d's built in Box2D physics engine. We could use a very simple rectangular collision check function like we did in the side scrolling tutorial. Or we could find a library mid-way inbetween.

Box2D feels like overkill. We don't need realistic physics. We need "game-y" physics. And a simple function isn't going to do enough when we have many collisions happening at once.

Instead, let's load up a library called [bump.lua](https://github.com/kikito/bump.lua). You'll want to download the entire project then unzip it into `/libs`. It should look like this:

[Image Placeholder]

We include the library by adding `bump = require 'libs.bump.bump'` to the very top of the file. Next, we'll stub out a world and a couple of ground objects just below the require. The top of our file should now look like this:

```lua
world = nil -- storage place for bump

ground_0 = {}
ground_1 = {}
```

In bump our collidables are stored in a "world" which we create in our load event. Objects that have collisions are then inserted into the world. When we want to move an object we move it relative to the world we created so that bump can process our collisions for us.

We declare this world right at the top of `love.load`. Our tiles will be 16 pixels wide and tall so we pass that into bump.newWorld which defaults to 64. After we load in our player object we'll add objects to our world so that our final love.load looks like this:

```lua
function love.load()
  -- Setup bump
  world = bump.newWorld(16)  -- 16 is our tile size

  -- Create our player.
  player.img = love.graphics.newImage('assets/character_block.png')

  world:add(player, player.x, player.y, player.img:getWidth(), player.img:getHeight())

  -- Draw a level
  world:add(ground_0, 120, 360, 640, 16)
  world:add(ground_1, 0, 448, 640, 32)
end
```

Let's add our ground to love.draw so we can see it. Our final love.draw function will look like this:

```lua
function love.draw(dt)
  love.graphics.draw(player.img, player.x, player.y)
  love.graphics.rectangle('fill', world:getRect(ground_0))
  love.graphics.rectangle('fill', world:getRect(ground_1))
end
```

We only need a couple more lines to start processing collisions. Our process will be to calculate where we want to go, a goalX and goalY, then attempt to move there. bump.lua will process our collisions and return the actual x and y of what was moved.

Let's change…

```lua
player.x = player.x + player.xVelocity
player.y = player.y + player.yVelocity
```

… to …

```lua
local goalX = player.x + player.xVelocity
local goalY = player.y + player.yVelocity
```

Then add our movement to the player object. `world:move` returns the actual x and y coordinates after stopping our moving objects and a list of collisions that occurred.

```lua
player.x, player.y, collisions = world:move(player, goalX, goalY)
```

If we run the code now we'll see our little player character fall from the sky and stop when it hits the white platform. It should look a little like this:

[image placeholder]

This is actually complete and ready to go, but we will eventually need some collision filtering. For instance, we want our character to pass through the bottom of platforms but get stopped by landing on them. So we'll add a filter that looks like this:

```lua
player.filter = function(item, other)
  local x, y, w, h = world:getRect(other)
  local px, py, pw, ph = world:getRect(item)
  local playerBottom = py + ph
  local otherBottom = y + h

  if playerBottom <= y then
    return 'slide'
  end
end
```

This function takes the item colliding, the player in this case, and what it is colliding with. From there we use a bump.lua world function `world:getRect(item)` to return the coordinates of the object at the time of collision. If the bottom of our player is above (less than) the top of the platform we are colliding with the platform from above. We return 'slide' as the type of collision we'd like to see. You can read about collision types in [bump.lua here](https://github.com/kikito/bump.lua#collision-resolution). If we aren't above the platform we return an implicit nil value that means no collision is being processed.

Next we run our `world:move` passing in player.filter as our last argument. `player.x, player.y, collisions, len = world:move(player, goalX, goalY, player.filter)`

Finally, we'll loop through the returned collisions and react appropriately:

```lua
for i, coll in ipairs(collisions) do
  if coll.touch.y > goalY then
    player.hasReachedMax = true
    player.isGrounded = false
  elseif coll.normal.y < 0 then
    player.hasReachedMax = false
    player.isGrounded = true
  end
end
```

All we're doing is allowing the player to jump again once the character touches the ground.

That's all there is for this bit. By now `main.lua` is looking a little cluttered. In part two we'll do some behind the scenes plumbing to make our game development quicker and cleaner.

## Part 2

The second part of this tutorial is going to be pretty heavy on general programming and a bit light on game making. If you're pretty familiar with OOP principles you'll probably just want to skim this tutorial and move onto part three.

This tutorial will seem a bit obtuse, but it's worth it to know. We're going to take what we did in the previous tutorial, add a few libraries, and split our code into several files.

Let's start by grabbing the libraries we need. I like to use git submodules to manage my dependencies, but downloading the source and adding it to your project manually will also work. We're going to use vlrd's fantastic[HUMP](https://github.com/vrld/hump) library for classes and gamestates. We'll eventually use it for our camera as well. We'll use [bump.lua](https://github.com/kikito/bump.lua) again for our collisions.

If you're doing things manually, go to the links above, download the files and unzip them into the libs directory. If you're using git for managing things:

```
git init #if you haven't already.
git submodule add git@github.com:vrld/hump libs/hump
git submodule add git@github.com:kikito/bump.lua libs/bump
```

The new structure of our game will have a simple main.lua that sets up our gamestates, an entities folder that contains every object in the game that is drawn or updated, and a gamestate folder that contains our menus, levels, etc. It will look something like this:

```
root
- entities
- gamestates
- libs
 - hump
 - bump
```

Let's talk about Object Oriented Programming -- OOP for short. It's a programming paradigm that says code should be based around objects and data rather than actions. So far we've been discussing our game in terms of action. We have a load action, draw action, update action. Now we're going to transition to think of our game in terms of *what* is performing an action. So from now on we can say, "player draws x" and "enemy draws x" instead of "draw x and y."

Objects also give us the ability to share traits in what programming textbooks call inheritance and polymorphism. The idea is that the more code you can reuse the less code you need to write. So rather than defining a dog and a cat, you can first define a mammal and have dog and cat *inherit* from mammal. We can also inherit from multiple sources. So we could have a bit of code describing traits of a house pet that we can bring in.

Now for the catch, Lua is not an object oriented language. However, we can expand Lua with the features we need. The HUMP library adds a class system -- roughly speaking.

Create a file in the entities folder called "Entity.lua" and add the following code:

```lua
-- Represents a single drawable object
local Class = require 'libs.hump.class'

local Entity = Class{}

function Entity:init(world, x, y, w, h)
  self.world = world
  self.x = x
  self.y = y
  self.w = w
  self.h = h
end

function Entity:getRect()
  return self.x, self.y, self.w, self.h
end

function Entity:draw()
  -- Do nothing by default, but we still have to have something to call
end

function Entity:update(dt)
  -- Do nothing by default, but we still have to have something to call
end

return Entity
```

That's the complete code for Entity.lua for now. We pull in Class from the HUMP library and use that to create Entity. Everything in our game is going to be an entity - from the ground to the background to our player character. If it can be drawn on the screen or needs to update per frame, it's an entity. Most of these objects only share a few properties - the world they inhabit for physics (potentially nil), an x and y coordinate, and a width and height. As the game expands we'll make note of other variables all Entities need and move the values into this file.

Next we have a simple `getRect` function. This is just a time saver for down the road. We'll need the shape and size of the object for use in our collision detector and this lets us fire it all back at once. It's just for convenience.

Finally, we have empty functions from draw and update. Each Entity will have both of these called from the gamestate they're a part of. Even if the functions do nothing, ground for instance does not have an update, it still needs a placeholder to avoid errors.

Let's use our new entity system to create our first entity. We'll start simple. Create a new file in the entities folder named ground.lua. The code is almost exactly the same as in Part 1 but we've abstracted a layer away.

```lua
local Class = require 'libs.hump.class'
local Entity = require 'entities.Entity'

local Ground = Class{
  __includes = Entity -- Ground class inherits our Entity class
}

function Ground:init(world, x, y, w, h)
  Entity.init(self, world, x, y, w, h)

  self.world:add(self, self:getRect())
end

function Ground:draw()
  love.graphics.rectangle('fill', self:getRect())
end

return Ground
```

We'll do exactly the same thing for the player. Create a new file called player.lua in the entities folder.

```lua
local Class = require 'libs.hump.class'
local Entity = require 'entities.Entity'

local player = Class{
  __includes = Entity -- Player class inherits our Entity class
}

function player:init(world, x, y)
  self.img = love.graphics.newImage('/assets/character_block.png')

  Entity.init(self, world, x, y, self.img:getWidth(), self.img:getHeight())

  -- Add our unique player values
  self.xVelocity = 0 -- current velocity on x, y axes
  self.yVelocity = 0
  self.acc = 100 -- the acceleration of our player
  self.maxSpeed = 600 -- the top speed
  self.friction = 20 -- slow our player down - we could toggle this situationally to create icy or slick platforms
  self.gravity = 80 -- we will accelerate towards the bottom

    -- These are values applying specifically to jumping
  self.isJumping = false -- are we in the process of jumping?
  self.isGrounded = false -- are we on the ground?
  self.hasReachedMax = false  -- is this as high as we can go?
  self.jumpAcc = 500 -- how fast do we accelerate towards the top
  self.jumpMaxSpeed = 11 -- our speed limit while jumping

  self.world:add(self, self:getRect())
end

function player:collisionFilter(other)
  local x, y, w, h = self.world:getRect(other)
  local playerBottom = self.y + self.h
  local otherBottom = y + h

  if playerBottom <= y then -- bottom of player collides with top of platform.
    return 'slide'
  end
end

function player:update(dt)
  local prevX, prevY = self.x, self.y

  -- Apply Friction
  self.xVelocity = self.xVelocity * (1 - math.min(dt * self.friction, 1))
  self.yVelocity = self.yVelocity * (1 - math.min(dt * self.friction, 1))

  -- Apply gravity
  self.yVelocity = self.yVelocity + self.gravity * dt

	if love.keyboard.isDown("left", "a") and self.xVelocity > -self.maxSpeed then
		self.xVelocity = self.xVelocity - self.acc * dt
	elseif love.keyboard.isDown("right", "d") and self.xVelocity < self.maxSpeed then
		self.xVelocity = self.xVelocity + self.acc * dt
	end

  -- The Jump code gets a lttle bit crazy.  Bare with me.
  if love.keyboard.isDown("up", "w") then
    if -self.yVelocity < self.jumpMaxSpeed and not self.hasReachedMax then
      self.yVelocity = self.yVelocity - self.jumpAcc * dt
    elseif math.abs(self.yVelocity) > self.jumpMaxSpeed then
      self.hasReachedMax = true
    end

    self.isGrounded = false -- we are no longer in contact with the ground
  end

  -- these store the location the player will arrive at should
  local goalX = self.x + self.xVelocity
  local goalY = self.y + self.yVelocity

  -- Move the player while testing for collisions
  self.x, self.y, collisions, len = self.world:move(self, goalX, goalY, self.collisionFilter)

  -- Loop through those collisions to see if anything important is happening
  for i, coll in ipairs(collisions) do
    if coll.touch.y > goalY then  -- We touched below (remember that higher locations have lower y values) our intended target.
      self.hasReachedMax = true -- this scenario does not occur in this demo
      self.isGrounded = false
    elseif coll.normal.y < 0 then
      self.hasReachedMax = false
      self.isGrounded = true
    end
  end
end

function player:draw()
  love.graphics.draw(self.img, self.x, self.y)
end

return player
```

Hopefully you're starting to see the advantage of this approach. If I need to make changes to the player I don't have to scroll through main.lua, I can just pop open player.lua and find what I need there. Big games become manageable. All we're missing now is the glue to hold it altogether.

For that we'll use Gamestate from the HUMP library. Each Gamestate is like a separate main.lua file. It has a load event, a draw event, and an update event. A Gamestate could represent a menu, a level, or a pause screen. We'll handle all three, but we'll build out the menu later.

The [Gamestate documentation](http://hump.readthedocs.io/en/latest/gamestate.html) has a few examples of putting together your game with this pattern. Our main.lua is going to be heavily simplified in favor of moving code into the Gamestate objects. Here is the final code for main.lua.

```lua
-- Pull in Gamestate from the HUMP library
Gamestate = require 'libs.hump.gamestate'

-- Pull in each of our game states
local mainMenu = require 'gamestates.mainmenu'
local gameLevel1 = require 'gamestates.gameLevel1'
local pause = require 'gamestates.pause'

function love.load()
  Gamestate.registerEvents()
  Gamestate.switch(gameLevel1)
end

function love.keypressed(key)
  if key == "escape" then
    love.event.push("quit")
  end
end
```

Quick breakdown: We load up the libraries we'll use, in this case just Gamestate from HUMP. Then we'll pull in three gamestate libraries. (We'll create these next.) Then we replace our `love.load()` function with two lines. You'll note that this file no longer has draw or update functions. Instead, you'll find those functions in the Gamestate files. What we do have is a `keypressed` function. This is just because I like to be able to get out of the game quickly while building. Eventually we'll tie this to a debug variable.

Before we build out our Gamestates we need to identify the code that will be common between them and we'll abstract that code out so that we don't repeat ourselves. Each Gamestate will be managing a list of entities and calling the individual draws and updates of those entities. I don't want to write those loops into each Gamestate so let's create a new file in our entities folder named -- appropriately -- Entities.lua.

```lua
-- Represents a collection of drawable entities.  Each gamestate holds one of these.

local Entities = {
  active = true,
  world = nil,
  entityList = {}
}

function Entities:enter(world)
  self:clear()

  self.world = world
end

function Entities:add(entity)
  table.insert(self.entityList, entity)
end

function Entities:addMany(entities)
  for k, entity in pairs(entities) do
    table.insert(self.entityList, entity)
  end
end

function Entities:remove(entity)
  for i, e in ipairs(self.entityList) do
    if e == entity then
      table.remove(self.entityList, i)
      return
    end
  end
end

function Entities:removeAt(index)
  table.remove(self.entityList, index)
end

function Entities:clear()
  self.world = nil
  self.entityList = {}
end

function Entities:draw()
  for i, e in ipairs(self.entityList) do
    e:draw(i)
  end
end

function Entities:update(dt)
  for i, e in ipairs(self.entityList) do
    e:update(dt, i)
  end
end

return Entities
```

This file wraps entityList -- an array of entities -- and provides us with handy methods for manipulation.

With DRY (don't repeat yourself) principles followed let's create our gameLevel1 Gamestate. Create a folder in the root (if you haven't already) named `gamestates` and add a file called gameLevel1.lua. Obviously we're naming it like this because we plan on having many levels.

```lua
-- Import our libraries.
bump = require 'libs.bump.bump'
Gamestate = require 'libs.hump.gamestate'

-- Import our Entity system.
local Entities = require 'entities.Entities'
local Entity = require 'entities.Entity'

-- Create our Gamestate
local gameLevel1 = Gamestate.new()

-- Import the Entities we build.
local Player = require 'entities.player'
local Ground = require 'entities.ground'

-- Declare a couple immportant variables
player = nil
world = nil

function gameLevel1:enter()
  -- Game Levels do need collisions.
  world = bump.newWorld(16) -- Create a world for bump to function in.

  -- Initialize our Entity System
  Entities:enter()
  player = Player(world, 16, 16)
  ground_0 = Ground(world, 120, 360, 640, 16)
  ground_1 = Ground(world, 0, 448, 640, 16)

  -- Add instances of our entities to the Entity List
  Entities:addMany({player, ground_0, ground_1})
end

function gameLevel1:update(dt)
  Entities:update(dt) -- this executes the update function for each individual Entity
end

function gameLevel1:draw()
  Entities:draw() -- this executes the draw function for each individual Entity
end

return gameLevel1
```

The code here is pretty self-explanatory. Setup our physics, add our entities, update, draw. You might also notice that it looks a lot like main.lua used to look. Each Gamestate is a different *main*, a different set of events.

I'm also going to create a mainMenu.lua Gamestate, but I'm leaving it blank for now. We'll fill it out in a future tutorial.

```lua
local mainMenu = {}

return mainMenu
```

Finally, we'll create one more Gamestate for a bit of magic. This is lifted directly from the HUMP libraries documentation, but I'll try to explain it here as well. This is our "pause" Gamestate. The HUMP library creates a list of Gamestates that we can manipulate by pushing (inserting a state) and popping (removing a state at the end). This means that as we switch Gamestates, we can still take advantage of code in previous Gamestates. In this case, we have a level Gamestate that we still want to draw but we don't want to update while paused.

```lua
pause = Gamestate.new()

function pause:enter(from)
  self.from = from -- record previous state
end

function pause:draw()
  local w, h = love.graphics.getWidth(), love.graphics.getHeight()
  -- draw previous screen
  self.from:draw()

  -- overlay with pause message
  love.graphics.setColor(0,0,0, 100)
  love.graphics.rectangle('fill', 0,0, w, h)
  love.graphics.setColor(255,255,255)
  love.graphics.printf('PAUSE', 0, h/2, w, 'center')
end

function pause:keypressed(key)
  if key == 'p' then
    return Gamestate.pop() -- return to previous state
  end
end

return pause
```

When we enter this Gamestate (you'll remember the code to enter this state is in our main.lua) we record the previous state in self.from. Later we call the draw function of our previous state then overlay a black film over it with the word "PAUSE." We have no update function because nothing is moving, but we do need to catch the player unpausing so we have a keypressed event that looks for the letter "p" and returns us to the previous state.

That's it for Part 2. It's a bit painful since our finally product looks exactly the same as it did at the end of Part 1, but we've layed a solid foundation for all the features a real platforming game needs.

In Part 3 we'll create and load levels in with [Tiled](http://www.mapeditor.org/).

## Part 3

This tutorial should be more fun than the last one. With our framework in place we can get down to the business of making a game. In this section I'll teach you how to use [Tiled](http://www.mapeditor.org/) to create levels for Love games. We'll have to write a little bit of code to use them.

**Important:** *In the process of making this tutorial I noticed some problems with the logic in part 2. We'll be correcting this, but not until Part 4. I considered going back and writing the code right the first time, but that would have left people already following this tutorial series in a lurch. I also thought it might be good to show you all how I debug code.*

With that out of the way, let's make our first level! Here are our steps.

1. Make the level using [Tiled](http://www.mapeditor.org/).
2. Create a class layer to load the level.

## 1. Make the Level

For this tutorial, I have decided to use [Open Pixel Project](http://www.openpixelproject.com/) for our assets. It's a fantastic project with some really cool art and because they follow a set of rules, you can choose whichever set of tiles you want for your level. I'm going to choose cave for my level.

You are free to choose any tileset you want, but be sure to move the tiles you'll be using to the `/assets/` folder of our project to avoid weird loading bugs.

[Tiled](http://www.mapeditor.org/) is very easy to use, but there are a few gotchas so I'm going to give you a rundown of exactly what I did to create the level.

First, open Tiled and choose New Map… You'll get a screen that looks a bit like this:

[image placeholder]

For this level I'm going to make a relatively small map. This makes debugging player behavior easier since you can transverse the entirety of the level quickly. The important values here are that is is an Orthagonal map with a height of 20 and a Tile size of 32 by 32. When you save, create a new folder in `/assets` called `/levels`. I saved mine as **level_1**.

Second, load the tileset by clicking the little New Tileset icon in the bottom right of the screen. You should get a popup. Fill out the settings so it looks like this:

[image placeholder]

Third, we need to add a custom property to some tiles so that we can handle our collisions. Choose the tileset you've just imported and click the document with a wrench on it. This will bring up the tileset editor. In this editor, select every tile you want to be impassable and create a new Custom Property on the left side called "collidable." Set the type to "bool" and the value to true. For the tiles you can pass through, just leave them alone. For me, the final product looks like this:

[image placeholder]

Note that I didn't make those little bits above the rocks collidable. The evel! For now, try a couple long platforms towards the bottom of the level. We need to fix our player in part 4 before we get too complicated.

Fifth, export your level. Go up to the File menu, choose Export As, make sure the file type is .lua and save the exported file into `/assets/levels`.

## 2. Coding Our Base Class.

At some point we'll want a lot of level in our game and since they'll all be the levels of a platformer we'll want a way to separate out common code -- such as loading our Tiled maps. Create an empty file in `/gamestates` called LevelBase.lua. We'll move whatever common code we can to this file. Let's revisit `gameLevel1.lua`. In that file we have a couple of entities, ground and player, plus our bump library. In this tutorial we'll be adding level loading and a camera. Entities will change but the Entity system will not. So our common code will include the Entity system, bump library (collision handling), level loading, and our camera. This frees up gameLevel# files to include only the logic specific to that particular level.

We'll be using [Simple Tiled Implementation](https://github.com/karai17/Simple-Tiled-Implementation) (or STI) by karai17. Add that to your libs folder by whichever method you prefer.

Now we're ready for to program our LevelBase. In that file put:

```lua
-- Each level will inherit from this class which itself inherits from Gamestate.
-- This class is Gamestate but with function for loading up Tiled maps.

local bump = require 'libs.bump.bump'
local Gamestate = require 'libs.hump.gamestate'
local Class = require 'libs.hump.class'
local sti = require 'libs.sti.sti' -- New addition here
local Entities = require 'entities.Entities'
local camera = require 'libs.camera' -- New addition here

local LevelBase = Class{
  __includes = Gamestate,
  init = function(self, mapFile)
    self.map = sti(mapFile, { 'bump' })
    self.world = bump.newWorld(32)
    self.map:resize(love.graphics.getWidth(), love.graphics.getHeight())

    self.map:bump_init(self.world)

    Entities:enter()
  end;
  Entities = Entities;
  camera = camera
}
```

In this new block we tell the engine that level base inherits from Gamestate then we define a function to be run on LevelBase initialization. This function takes the location of a map file and sets up the player's environment. Now for the breakdown by line number:

1. `self.map = sti(mapFile, { 'bump' })` - Use sti to open our mapfile. We then tell sti that we are making use of the bump plugin for our collision handling.
2. `self.world = bump.newWorld(32)` - Just as before, we declare a world for our collisions to occur in.
3. `self.map:resize(love.graphics.getWidth(), love.graphics.getHeight())` - Resize the map to fill our screen.
4. `self.map:bump_init(self.world)` - Initialize the bump library for our map.
5. `Entities:enter()` - Create the entities system.
6. `Entities = Entities;` - Makes Entities which we required above a class variable for easy access from anyone that inherits this LevelBase.

This will allow us to simplify gameLevel1.lua, but before we do that let's setup our camera. Camera's in Love are super simple. We actually have a class already included but I found that it overcomplicated the process and actually fought with our level loader. Instead we're going to grab the camera from this nova-fusion tutorial: [Cameras in Love2d Part 1](http://nova-fusion.com/2011/04/19/cameras-in-love2d-part-1-the-basics/) I recommend you read that tutorial as well. Understanding this bit of code isn't strictly *necessary* but it's always a good idea.

Take the code from that tutorial (which I have pared down and copied below) and add it to a file in `/libs` named camera.lua.

```lua
camera = {}
camera.x = 0
camera.y = 0

function camera:set()
  love.graphics.push()
  love.graphics.translate(-self.x, -self.y)
end

function camera:unset()
  love.graphics.pop()
end

function camera:move(dx, dy)
  self.x = self.x + (dx or 0)
  self.y = self.y + (dy or 0)
end

function camera:setPosition(x, y)
  self.x = x or self.x
  self.y = y or self.y
end
  
return camera
```

We "set" the camera prior to drawing our graphics. This performs a translate operation that ensures we're drawing things in the correct position relative to our viewport. We'll use the setPosition function to make sure our viewport is in the proper position relative to the player.

Returning to LevelBase.lua we'll add our common procedures. Right now that's just pausing, but you could use this same concept for informational popups or other actions that appear in multiple levels.

```lua
function LevelBase:keypressed(key)
  -- All levels will have a pause menu
  if Gamestate.current() ~= pause and key == 'p' then
    Gamestate.push(pause)
  end
end
```

Finally, let's write a little code to move the camera to match the player position. This is just a tiny bit tricky, but it boils down into very little code. First, we determine the width of the level by multiplying the number of tiles in the width by the width of the tiles (that was a bit of a mouthful), then we get the width of the screen and half that to center our player. Then we check if we're close to the starting edge or the ending edge and use `Math.min` and `Math.max` to set the position of the camera to the closest edge or to half a screen from our player character. The whole block looks like this:

```lua
function LevelBase:positionCamera(player, camera)
  local mapWidth = self.map.width * self.map.tilewidth -- get width in pixels
  local halfScreen =  love.graphics.getWidth() / 2

  if player.x < (mapWidth - halfScreen) then -- use this value until we're approaching the end.
    boundX = math.max(0, player.x - halfScreen) -- lock camera at the left side of the screen.
  else
    boundX = math.min(player.x - halfScreen, mapWidth - love.graphics.getWidth()) -- lock camera at the right side of the screen
  end

  camera:setPosition(boundX, 0)
end
```

To finish up this file add the return statement at the bottom.

```lua
return LevelBase
```

Returning to `gameLevel1.lua` we remove some redundant code and add the LevelBase dependency. There is nothing *new* in this file other than the mounting and positioning of the camera - a few simple function calls. The final gameLevel1 file looks like this:

```lua
-- Import our libraries.
local Gamestate = require 'libs.hump.gamestate'
local Class = require 'libs.hump.class'

-- Grab our base class
local LevelBase = require 'gamestates.LevelBase'

-- Import the Entities we will build.
local Player = require 'entities.player'
local camera = require 'libs.camera'

-- Declare a couple immportant variables
player = nil

local gameLevel1 = Class{
  __includes = LevelBase
}

function gameLevel1:init()
  LevelBase.init(self, 'assets/levels/level_1.lua')
end

function gameLevel1:enter()
  player = Player(self.world,  32, 64)
  LevelBase.Entities:add(player)
end

function gameLevel1:update(dt)
  self.map:update(dt) -- remember, we inherited map from LevelBase
  LevelBase.Entities:update(dt) -- this executes the update function for each individual Entity

  LevelBase.positionCamera(self, player, camera)
end

function gameLevel1:draw()
  -- Attach the camera before drawing the entities
  camera:set()

  self.map:draw(-camera.x, -camera.y) -- Remember that we inherited map from LevelBase
  LevelBase.Entities:draw() -- this executes the draw function for each individual Entity

  camera:unset()
  -- Be sure to detach after running to avoid weirdness
end

-- All levels will have a pause menu
function gameLevel1:keypressed(key)
  LevelBase:keypressed(key)
end

return gameLevel1
```

That wraps up part 3! When you run the game you should see something like this:

[image placeholder]

In part 4 we'll make a better, more responsive player!

(20230629 - Part 4 has yet to be posted. Don't think it will be.)
