# Space Invaders

**Unreal Engine Version**: 4.27

**YouTube Demo**: https://youtu.be/rigbhkFNx20

## Navigation

Here's a brief summary of how the content folders are structured within the project.

![Folders](https://user-images.githubusercontent.com/62073792/163354248-74fe5820-7d45-49da-b38f-21cab9ac50db.png)

**Blueprints** → All of the classes created for the game are stored in this folder. There exist a couple of blueprints that spawn the grid of aliens and walls respectively and one that spawns projectiles, i.e. the bullets used to defeat the player/enemy. Other than that, there's an alien blueprint that marches across rows and columns, a brick blueprint that could be destroyed when overlapped with by bullets or aliens, and the player blueprint.

**Levels** → In this folder, there are 3 levels that are used to render the "Start Game" and "End Game" options menu, and a level where the actual game occurs.

**Materials** → Here, I created a few simple materials for the aliens, bricks, and bullets

**Models** → 3D models (static meshes) that are used within the game are stored here, namely, the spacecraft and aliens.

**Widgets** → Widget blueprints for the Heads-Up Display (HUD) and menu options are placed here.

## Blueprints

In this section, I briefly go through a list of important Blueprints and the logic that has been implemented within them.

**Note**: Most of the rules of the game such as the horizontal boundaries, grid size, starting distance between each section (walls, aliens, and players) are defined in `SideScrollerCharacter` and they're passed on to the other blueprints when they're spawned.

### `SideScrollerCharacter`

- Spawning the army and walls
- Caclulating the points earned for each successful kill
- Determining whether the player has lost the game or a life depending on the number of lives they have remaining
- Opening the End Game levels
- Spawning bullets when the shooting input is pressed
- Calculate boundaries and the padding for the wall section

### `BP_AlienArmySpawner`

- Spawn a grid of aliens (5 rows & 11 columns). Before each alien is spawned, we add a struct element to an array to store information about it such as its ID, row, column, and status (dead or alive). The object reference to `BP_AlienArmySpawner` is passed down to every `BP_Alien` that is spawned.
- Create a recursive function that gets called every 1 second. This event checks if the army has to change its marching direction in case it has reached the boundary. It also selects the shooter for the next tick.
- We select the shooter for the next tick by looping through the array of structs for every alien's column-mates. We check if there are aliens in the same column and rows ahead of them that are alive. If that isn't the case, the alien is added to an array of prospective shooters. A random function is then used to select one of the prospective shooters to perform the deed (it is ensured that one of the prospects is definitely chosen by increasing the bool weightage to 1 by the time we loop to the last prospect).
- A custom event that updates the array of locations of the aliens whenever they march.
- A custom event that determines if the "Lose Game" screen should be rendered when the player has been invaded.
- A function that updates the alien's status when it is killed. We also have a counter variable that keeps track of the number of aliens that have been killed so far. When all of the aliens are killed, the "Win Game" screen is rendered.

### `BP_WallSpawner`

- Padding and spacing is applied between the walls.
- Most of the spawning logic is similar to `BP_AlienArmySpawner`. However, padding and spacing is taken into account based on the space occupied by the alien grid.

### `BP_Wall`

- The actor is destroyed if it is overlapped by a bullet or alien

### `BP_Bullet`

- The projectile speed and material is set depending on whether the person who spawned the bullet is the player or the alien.
- If another actor overlaps with it and it isn't the shooter, the overlapped actor is destroyed.

### `BP_Brick`

- This actor is used to build the walls.
- If it is overlapped with by a bullet or alien, it is destroyed.

## Reflections

In this section, I'll briefly go through what I think I could have done better:

- **Use Custom Events Wisely** → Custom Events are used to create events of your own and they should ideally be used when something important happens in the game. I usually don't follow this rule of thumb when creating Custom Events and use them interchangeably with functions. I need to fix this habit.
- **Use a more appropriate starter template** → I used the side-scroller template for this project but I feel that that might not have been the best choice because:
  - It is hard to use a common unit of measurement for static meshes in a 3D template. Although the `getComponentBounds` function exists, it is still difficult to set the boundaries of game. Thus, I had to use the player's location as a reference and not the camera frame's end locations. I had to hardcode the grid size (depending on the alien mesh size). I should explore Unreal's 2D features more.
- **Using the Game Mode Class** → I could have stored information such as the grid size, number of rows and columns etc. in the Game Mode class instead of `SideScrollerCharacter` because it makes more sense to store it there. Information/rules that persist throughout the game must be stored there.
