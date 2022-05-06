
## Stage Objects

    Map - The stage for all activity
    Tile - A segregation of the map based on a grid
    Layer - The list of segregations of props
    Unit - A object that can move across the map
    Prop - An object that exists in a layer on a tile
    Item - A thing to pick up, can go into an inventory and stack
    Zone - A polygon used for logic and triggers and effects
    Effect - A temporary object that starts, proceeds, and ends

## Sprites
  
    Animation - A sequence of sprites / splines to tween through, start, proceeds, ends
    Parts - A set of sprites to layer on top of eachother

## Ensemble

    Chunk - Groups of tiles that get rendered at the same time
    Blueprint - A group of props that can be stamped onto an area

## Abstract Objects

    Action - Business/Event Logic, can have a start, processsion, end
    Command - An action enqueued for a unit
    Queue - A list of commands for one or many units
    Factions - A group for a unit that specify reactions
    Selection - A group of objects that a player is selecting
    Stats - A set of data for an object
    Config - A configuration for an object
    Preset - A configuration available for many objects that can be set as a whole
    Work - An action that is available to be enqueued by a unit

