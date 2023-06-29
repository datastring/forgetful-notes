---
title: Lua - First Love2D Game
---

## 1. Getting Setup

I'm assuming no knowledge of programming or Love. If you're familiar with either or have spent some time in other tutorials feel free to skip to section 2 or even 3.

[Love2d](http://love2d.org/) is a great, completely free, simple 2D game engine. I cannot recommend it enough for beginners. The community is great and no one is trying to sell you anything (compare that to GameMaker or Unity!) Go ahead and download the proper version of Love for your system. I'll be covering usage in Windows here and I'll link some Mac setup tips. If you're using Linux or if you need more help you can check out the [Love wiki page on getting started](http://www.love2d.org/wiki/Getting_Started).

For windows just unzip the Love archive into whichever directory you'd like. I place my in C:\GameDev so that I have a very short path to the file, but you can put it anywhere. I'll also create a folder named "ScrollingShooter" in C:\GameDev. If you prefer to have your Love executable in a different directory you can create a new shortcut in the folder above ScrollingShooter. When we want to launch the game all we do is drag the ScrollingShooter folder onto the Love executable or shortcut.

## 2. Configuration

Inside ScrollingShooter I'm going to create 3 files and 2 folders. The folders are `assets` (where we will be storing our graphics) and `bin` (for our .love file and executables. Go ahead and create these folders.

For text editing I'm using [Atom](http://atom.io) with [language-lua](https://atom.io/packages/language-lua) installed. You can use any code editor you'd like. Some good editors include [TextMate](http://macromates.com/), [Notepad++](http://notepad-plus-plus.org/), and [Sublime](http://sublimetext.com). Some of these options cost but Atom is completely free.

I'm going to open the ScrollingShooter directory in Atom then create two new files. The first file is `conf.lua`. This is a special file. Unlike other source files, the contents of `conf.lua` are parsed before Love finishes initializing. This means we can set things like the window size and other variables that may be locked once the game has started.

```lua
-- Configuration
function love.conf(t)
	t.title = "Scrolling Shooter Tutorial" -- The title of the window the game is in (string)
	t.version = "0.9.1"         -- The LÖVE version this game was made for (string)
	t.window.width = 480        -- we want our game to be long and thin.
	t.window.height = 800

	-- For Windows debugging
	t.console = true
end
```

`love.conf(t)` is a special function that is executed before any other Love modules are loaded. We're going to keep it simple. It takes a single argument usually called `t`. This argument will store the game configuration. We're just going to set the title of our window -- `t.title` -- and the version of Love we're building this for -- `t.version`. Then we'll set the window width and height. Finally, we set `t.console` to `true` so that we can see errors written in a console window when debugging on Windows. You'll want to change this to false before releasing your game.

A complete list of the attributes you can pass to `t` can be found on the [Config files wiki](http://www.love2d.org/wiki/Config_Files).

Next create a file next to `conf.lua` called `main.lua`. This file will store all of our game logic. These are the only two code files we will need. We'll start by filling `main.lua` with the basic Love bits. It'll look like this:

```lua
debug = true
function love.load(arg)
end
function love.update(dt)
end
function love.draw(dt)
end
```

The first line just sets a variable called "debug" to true. You'll want to flip this to false before release. I use this variable later for determining whether or not to write things like the frames-per-second to the screen. I'll demonstrate in a little bit.

Next we have three functions. These are called by the Love engine. `love.load` is called when the game first starts. We'll load our assets -- images, sounds, etc -- here. `love.update` and `love.draw` are called on every frame. Every action here will occur several times a second. Both of these function take `dt` or `deltaTime` as an argument. This is a measure of how much time has passed since the last call. We'll multiply our timers by these values to ensure the game runs the same on your friends old laptop that gets 30 fps or your brand new gaming rig that gets 700 fps.

## 3. Drawing to the Screen

Before we go any farther, lets just draw something to the screen. I'm not much of an artist, but I know where to find people that are. For this project I'll be using some pixel airplanes created by [chabull](http://opengameart.org/users/chabull) and distributed for free on [OpenGameArt.org](http://opengameart.org/). You can download them [here](http://opengameart.org/content/aircrafts).

I'm going to take *Airplane_3.png* from that graphics kit, drop it in our assets folder, and rename it *plane.png*. To get this showing up in Love2d we have two steps.

1. Load the image into memory
2. Draw the image

If you remember, we have both a load and a draw function in our `main.lua`. Let's use them.

First, load the image into memory.

```lua
playerImg = nil -- this is just for storage

function love.load(arg)
    playerImg = love.graphics.newImage('assets/plane.png')
    --we now have an asset ready to be used inside Love
end
```

Next we add a draw command inside our draw function. Please note that draw commands *must* be called inside the draw function. They need to be executed every frame and the draw function is called every frame.

```lua
function love.draw(dt)
    love.graphics.draw(playerImg, 100, 100)
end
```

Our application is ready to run! Just drag the entire ScrollingShooter folder onto love.exe or a shortcut to love.exe and the application will start. The complete source for this example can be found on [GitHub](https://github.com/DawsonG/Love2d-Tutorial-Scrolling-Shooter/tree/master/Lesson1/Part1).

- [Image Placeholder]

It doesn't look like much, but it's a start! You'll note that `love.graphics.draw` has a second and third argument. For this example I've passed in 100 and 100. These are the x and y coordinates (respectively) where the image will be drawn. We'll talk more about those in [part 2](http://www.osmstudios.com/page/your-first-love2d-game-in-200-lines-part-2-of-3).

---

In [part 1](http://www.osmstudios.com/page/your-first-love2d-game-in-200-lines-part-1-of-3) we drew the player object to the screen. You may have noticed something a tad bit counterintuitive -- the origin point (0,0) isn't in the bottom left like we were taught in algebra. Instead it is in the top left. Each individual image also has an origin point in the top left. So when we're picking a position for a image what we're really measuring is the distance from the top-left of the screen to the top-left of the image in pixels.

## 4. Creating A Player Object

In the last tutorial we placed the player at 100,100 but that value isn't going to be static. Instead, the player will be moving. We need to create variables to store our current player location. While we could just add an `x` and `y` variable to our file, I prefer to store related variables together. Let's change our `player` variable to be a "table" or "object."

Let's remove the `playerImg` variable. This will cause an error in the draw command, but we'll fix that in a second. Then lets add the following declaration where `playerImg` used to be.

```lua
player = { x = 200, y = 710, speed = 150, img = nil }
```

Now we have a player table that contains an `x` and `y` coordinate, a `speed` we'll be using later, and an `img`. Note that I've left the img value blank. That's because we need to fill it in at runtime. Where you currently have `playerImg = love.graphics.newImage('assets/plane.png')` change `playerImg` to `player.img`. Do the same to the `playerImg` used in the `love.draw` function.

If you run the application now -- and you should -- you'll see exactly the same thing as you did before.

Let's make use of our new player variables. Change our draw command to read:

```lua
love.graphics.draw(player.img, player.x, player.y)
```

## 5. Adding User Input

You should now see our player image centered in the bottom of the screen, right where it should be. If we change `player.x` or `player.y` the player will move accordingly. Let's add some user input. Replace your current empty `love.update` with.

```lua
-- Updating
function love.update(dt)
	-- I always start with an easy way to exit the game
	if love.keyboard.isDown('escape') then
		love.event.push('quit')
	end

	if love.keyboard.isDown('left','a') then
		player.x = player.x - (player.speed*dt)
	elseif love.keyboard.isDown('right','d') then
		player.x = player.x + (player.speed*dt)
	end
end
```

When you run our game again you'll be able to move the player back and forth and exit out just by hitting the escape key. If you feel your player should be faster or slower you can change player.speed to a higher or lower number. We multiple by `dt` standing for delta-time or the change in time since the last update call to account for framerate differences between machines.

We do have a few issues though. The main one is concerning bounding. Our player can just fly right off screen. We'll have to check his current position before we move him. Change our movement key block in the update function to the following:

```lua
if love.keyboard.isDown('left','a') then
	if player.x > 0 then -- binds us to the map
		player.x = player.x - (player.speed*dt)
	end
elseif love.keyboard.isDown('right','d') then
	if player.x < (love.graphics.getWidth() - player.img:getWidth()) then
		player.x = player.x + (player.speed*dt)
	end
end
```

All we're doing is making sure our `player.x` variable is within our game world. For our right bound we get the width of the screen (`love.graphics.getWidth()`) and subtract the width of the player image (`player.img:getWidth()`). This gives us the correct value for the top-left corner position of our player.

## 6. Creating Bullets

Are you ready for the tricky part? We need to allow our player to fire. This means creating and tracking an entire table of bullet objects, drawing them, updating them, etc., *and* using timers to track when our player is allowed to fire. Let's start by declaring some variables at the top of the file.

```lua
-- Timers
-- We declare these here so we don't have to edit them multiple places
canShoot = true
canShootTimerMax = 0.2 
canShootTimer = canShootTimerMax

-- Image Storage
bulletImg = nil

-- Entity Storage
bullets = {} -- array of current bullets being drawn and updated
```

While we're here let's load our bullet image (stored in bulletImg) in our load function. Our load function needs to contain the following line:

```lua
bulletImg = love.graphics.newImage('assets/bullet.png')
```

You're probably wondering why we're not using an object for bullet like we did with player. Bullet *will* be an object but we're going to declare that object with each bullet created. To say ourselves some loading time we're going to load the image ahead of time, store it by itself, and just reference it later when we create the bullet itself.

Those timers aren't going to take care of themselves. We'll have to deal with them in our update loop. We'll be manually subtracting values each time the frame updates. Here is the code:

```lua
-- Time out how far apart our shots can be.
canShootTimer = canShootTimer - (1 * dt)
if canShootTimer < 0 then
  canShoot = true
end
```

You'll note that we're not just subtracting 1 on each update. Instead we're subtracting 1 × dt. Like in our other calculations we want to account for varying framerates between computers.

After we do our subtraction we check our timer value and if it is under 0 we set our canShoot variable to true. Later in our update function we'll check this value and our keypresses. That code reads as follows:

```lua
if love.keyboard.isDown('space', 'rctrl', 'lctrl') and canShoot then
	-- Create some bullets
	newBullet = { x = player.x + (player.img:getWidth()/2), y = player.y, img = bulletImg }
	table.insert(bullets, newBullet)
	canShoot = false
	canShootTimer = canShootTimerMax
end
```

We're giving the player a lot of options here. They can use space or either control button as their fire key. However, we can't just create a new bullet if one of those keys is down. If we do that the player will be able to create a constant stream of bullets, literally hosing down enemies. You can see this by removing `and canShoot` from our code above.

The next line creates a new object called `newBullet`. Like the player object, we give bullets an x and y position as well as an image. Our y position is just the player's y position. This will put the bullet at the top of our player image. For our x position we need to do a little math. We take the player's x position and move over by half the width of the player's image. This gives us the center x position of the player, and means the bullet will be "fired" from the top center of our player image.

Finally we get to our `table.insert` call. This is one of lua's built in functions for dealing with tables. Earlier we declared an empty table called `bullets`. Now we'll use `table.insert` to add our entire newBullet object to the bullets table. Later we'll loop through this table and update each bullet object.

Actually, we're going to loop through those twice. First, in our draw function:

```lua
for i, bullet in ipairs(bullets) do
  love.graphics.draw(bullet.img, bullet.x, bullet.y)
end
```

Then in our update function:

```lua
-- update the positions of bullets
for i, bullet in ipairs(bullets) do
	bullet.y = bullet.y - (250 * dt)

  	if bullet.y < 0 then -- remove bullets when they pass off the screen
		table.remove(bullets, i)
	end
end
```

Both of these little code snippets are loops. Here we take a table full of bullet objects and perform the same operations on each one. The loop is just saying `for each bullet in bullets do x`. However, we're going to use a curious lua function called `ipairs`. In our loop we have both a bullet object, `bullet`, and the index (location) of that object, `i`. These values are returned by `ipairs`. We use that index in our `table.remove` function to ensure we're not wasting time drawing or updating bullets that have left the screen.

That concludes part 2. In part 3 we'll create enemies, handle collisions, and make a real game out of this.

## 8. Creating Enemies

The logic for our enemies is going to look a lot like the logic we're using for our bullets. We'll have a timer that tells us when to create enemies, a table of enemy objects, and a couple of loops to update and draw our enemies. Let's add declarations for our timers and enemy image at the top of out `main.lua`.

```lua
--More timers
createEnemyTimerMax = 0.4
createEnemyTimer = createEnemyTimerMax
  
-- More images
enemyImg = nil -- Like other images we'll pull this in during out love.load function
  
-- More storage
enemies = {} -- array of current enemies on screen
```

Then we fill in our `enemyImg` variable in our `love.load` function like so:

```lua
enemyImg = love.graphics.newImage('assets/enemy.png')
```

Here is where we differ from our bullet code. While bullets are created on a keypress from the player, enemies are handled independently. Let's use our timers to create new enemies every so often. Add this code to our `love.update` function.

```lua
-- Time out enemy creation
createEnemyTimer = createEnemyTimer - (1 * dt)
if createEnemyTimer < 0 then
	createEnemyTimer = createEnemyTimerMax

	-- Create an enemy
	randomNumber = math.random(10, love.graphics.getWidth() - 10)
	newEnemy = { x = randomNumber, y = -10, img = enemyImg }
	table.insert(enemies, newEnemy)
end
```

Still looks pretty similar to our bullet code, right? There are still a few new concepts so bear with me. First, we're using lua's built in `math.random` function to generate a number between 10 and the width of the screen minus ten. This gives us a good area to create an enemy in. We want to make sure that incoming enemies are spread out. The we start the enemies partially off-screen with a y coordinate of -10. This ensures that there is no weird "pop-ins" when enemies are created.

As with bullets we need to loop through and update their positions.

```lua
-- update the positions of enemies
for i, enemy in ipairs(enemies) do
	enemy.y = enemy.y + (200 * dt)

	if enemy.y > 850 then -- remove enemies when they pass off the screen
		table.remove(enemies, i)
	end
end
```

And draw them.

```lua
for i, enemy in ipairs(enemies) do
	love.graphics.draw(enemy.img, enemy.x, enemy.y)
end
```

If everything goes according to plan you can fire up the game and see enemies streaming down from above.

## 9. Handling Collisions

That just leaves us with the connundrum of collisions. Bullets collide with enemies. Enemies collide with the player. We need to know when and what to do. For most Love projects, users will roll out a library like [HardonCollider](http://vrld.github.io/HardonCollider/) or [bump.lua](https://github.com/kikito/bump.lua). These are overkill for our project. Instead, I'm going to lift a little collision function from the Love2d wiki and place it at the top of our `main.lua`.

```lua
-- Collision detection taken function from http://love2d.org/wiki/BoundingBox.lua
-- Returns true if two boxes overlap, false if they don't
-- x1,y1 are the left-top coords of the first box, while w1,h1 are its width and height
-- x2,y2,w2 & h2 are the same, but for the second box
function CheckCollision(x1,y1,w1,h1, x2,y2,w2,h2)
  return x1 < x2+w2 and
         x2 < x1+w1 and
         y1 < y2+h2 and
         y2 < y1+h1
end
```

While we're up here lets also add a real quick check to the status of our player and a score variable.

```lua
isAlive = true
score = 0
```

Now things get really tricky. We have two arrays of objects that we need to check for collisions and a separate entity (the player) that we also need to check. In our game, the player cannot collide with his or her own bullets, so that simplifies things slightly. Let's loop through our list of enemies then through our bullets then finally our player. It's going to look like this jumbled mess:

```lua
-- run our collision detection
-- Since there will be fewer enemies on screen than bullets we'll loop them first
-- Also, we need to see if the enemies hit our player
for i, enemy in ipairs(enemies) do
	for j, bullet in ipairs(bullets) do
		if CheckCollision(enemy.x, enemy.y, enemy.img:getWidth(), enemy.img:getHeight(), bullet.x, bullet.y, bullet.img:getWidth(), bullet.img:getHeight()) then
			table.remove(bullets, j)
			table.remove(enemies, i)
			score = score + 1
		end
	end

	if CheckCollision(enemy.x, enemy.y, enemy.img:getWidth(), enemy.img:getHeight(), player.x, player.y, player.img:getWidth(), player.img:getHeight()) 
	and isAlive then
		table.remove(enemies, i)
		isAlive = false
	end
end
```

Looks pretty scary, but it's not so bad. For each enemy, check each bullet and the player for coordinate overlap. If there is overlap take the appropriate action -- usually destroying one or both of the entities and incrementing the score.

## 10. The Game Part

*Shewww*, we're almost done, but it's not quite a game yet. We're marking the player as dead, but failing to actually kill him or her. After that the player needs the ability to restart the game. Fortunately, this is really easy.

First, let's wrap our `.draw(player.img, …` in an if block. We only need to draw the player when he or she is alive. If the player isn't alive we'll tell them how to restart the game.

```lua
if isAlive then
	love.graphics.draw(player.img, player.x, player.y)
else
	love.graphics.print("Press 'R' to restart", love.graphics:getWidth()/2-50, love.graphics:getHeight()/2-10)
end
```

Then we handle this button press at the bottom of our update function.

```lua
if not isAlive and love.keyboard.isDown('r') then
	-- remove all our bullets and enemies from screen
	bullets = {}
	enemies = {}

	-- reset timers
	canShootTimer = canShootTimerMax
	createEnemyTimer = createEnemyTimerMax

	-- move player back to default position
	player.x = 50
	player.y = 710

	-- reset our game state
	score = 0
	isAlive = true
end
```

There you go. That's everything. You have just completed a simple game in under 200 lines, and that's a fantastic first step.

If you want to continue learning go ahead and check out the exercises in the next section by [clicking here](http://www.osmstudios.com/tutorials/your-first-love2d-game-in-200-lines-exercises).

## Exercises

### Adding Sound Effects

The game is kind of quiet, don't you think? I've included a gunshot sound in the assets zip. Go ahead and add it to the game. You can get some help here: [https://www.love2d.org/wiki/Tutorial:Audio](https://www.love2d.org/wiki/Tutorial:Audio)

Like images, sound effects need to be loaded inside our `love.load` function.

```lua
gunSound = love.audio.newSource("assets/gun-sound.wav", "static")
```

We then go down into our update, find where the bullets are created and add our play command.

```lua
if love.keyboard.isDown(' ', 'rctrl', 'lctrl', 'ctrl') and canShoot then
	-- Create some bullets
	newBullet = { x = player.x + (player.img:getWidth()/2), y = player.y, img = bulletImg }
	table.insert(bullets, newBullet)
	--NEW LINE
    gunSound:play()
    --END NEW
    canShoot = false
	canShootTimer = canShootTimerMax
end
```

You might notice that we're not using `gunSound.play()` but instead we use `gunSound:play()`. This is a Lua language convention. Saying `:play` is the same as saying `.play(gunSound)`

### Player Vertical Movement

Moving side-to-side is fine, but this isn't the 80s anymore. Long gone are the days of Space Invaders. Let's add vertical movement to our game just like we added horizontal movement. Make sure that we keep our player from flying off-screen.

Add to `love.update`:

```lua
-- Vertical movement
if love.keyboard.isDown('up', 'w') then
	if player.y > (love.graphics.getHeight() / 2) then
		player.y = player.y - (player.speed*dt)
	end
elseif love.keyboard.isDown('down', 's') then
	if player.y < (love.graphics.getHeight() - 55) then
		player.y = player.y + (player.speed*dt)
	end
end
```

### Display the Score

We're tracking the score, but I seem to have forgotten to display it anywhere. Go ahead and write it out. Remember, we're already writing some text when the game is restarted.

Add to `love.draw`:

```lua
love.graphics.setColor(255, 255, 255)
love.graphics.print("SCORE: " .. tostring(score), 400, 10)
```
