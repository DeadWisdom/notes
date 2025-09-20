# Beyond All Reason In the Browser

## Workers

Can we put workers in different threads to effect the game?
If so, we need a *super* efficient way to send messages.

Ways to partition workers:
- Map area
- Team
- Player
- System

Notes
- Physics doesn't have to effect the game
- Most units are at full health and have no cooldown and have no resource delta
- A lot of units go from full to zero health instantly
- Quantize everything

Unit Composition:
- Type
- Team
- Position (2D)
- Position (3D)
- Rotation
- Health
- Cooldown
- Resource Delta

Commands:
- Units
- Type
- Target Unit
- Target Location
- Queue Type

Resource Composition:
- Type
- Player
- Value

## Math

Number of Units
5000 * 32 Players = 160000

Tight unit:
ID (24) + Type (10) + Player (8) + Position (64) + Rotation (8) = 114 ~ 128

160000 * 128 = 20 MB!!!!!

### Data Oriented

Unit Table Per Player:
ID (16) + Type(10) + Position (64) + Rotation(8) = 98

32 * 5000 * 98 = 1.96 MB!!!!

That's nothing

Now add healths
ID (16) + Health (16) = 32
32 * 1000 * 32 = 128 KB

Now add resource usage
ID (16) + Energy (16) = 32
32 * 200 * 32 = 25 KB

So 25 KB per resource type (2 = 50KB)

Now add cooldown or for each one?
ID (16) + Cooldown Type (4) + Delta Time (12 ???) = 32
32 * 1000 * 8 = 128 KB

Now add explosions / effects
ID (16) + Player (8) + Time Triggered (10) + Type (8) + Magnitude (8)
...

Now commands per Player
Unit ID (16) + Position (64) + Type(8) + Delta Time (16) + Index(8)
Jeeeeze 2.24 MB .... Hrm

Quantize Position More!
Unit ID (16) + Position (16) + Type(8) + Delta Time (16) + Index(8)
1.28 MB ... Well better

Unit Groups Per Player
Group ID (16) + Unit ID (16)
How many? I dunno maybe like 1000 per player tops?
128 KB

Then you would do:
Group ID (16) + Position (16) + Type(8) + Delta Time (16) + Index(8)
Then only 32 (Per player) * (Number of queued / in progress commands ~ 32???)
8.192 KB ... Daaaaaamn

So if game state gets out of sync, a full update is only a few MBs. Also you could hash each dataset and update only the ones that are actually out of sync. Meanwhile no one else really has to stop at all. Fuck your pausing.

## Systems
Cooldown System
Command System
Position System
Effect System
AI System (Per Player / AI)
Renderer (Main Thread)

## Functional

Renderer Needs Current:
  Each Unit Type + Player
  Each Unit Position/Rotation
  Each Unit Health
  Each Unit Resources
  Each Cooldown for Each Unit
  Each Effect
  Known Commands

## Calculating current state
The effect system hast to be able to play, rewind, and replay a full history of events.

Can we use OP here?

We assume the units are moving into this range and then firing, killing our unit, exploding it and killing a bunch of other units.
But wait, actually (like 30 ticks ago) they went the other way. We need to rewind our various states, insert the command, and then replay. And the renderer needs to update in a beleivable way (it's not dead and the explosion didn't happen!)

Figuring out the position of units as a function across commands seems impossible. But maybe not.
Unit is traveling from here to there, five effects happen along the way, the unit is dead by here.

Do we simulate each step? I can't get away from that. I guess that's why we do lockstep.

## GGPO style deterministic lockstep
(https://mas-bandwidth.com/choosing-the-right-network-model-for-your-multiplayer-game/)

So one possibility is to build up a delta of the state before we get an update, and then resimulate entirely once we get the real version. I guess the difference between OP here is that if the simulation function can go forwards AND backwards, we rewind rather than throw it away. 
I'm not sure what's more efficient, I guess it depends on how often we get updates. If it's often, then resimulation is easy enough. If it's not than rewinding might be better. It would also be a factor of cost to simulate, obviously.

## Networking
I guess the difference in gameplay experience would be that if you don't have a strong connection to the primary server, then your simulation would continually stutter. I just have no idea how consistent the network is for whatever we choose here. I mean I guess if your connection sucks it just sucks so whatever. 
Plus we're sending so little data this way. Apparently among WebRTC, Websockets, and WebTransport the latter is probably the best option. HTTP/3 is super fast.

This site https://github.com/Sh3b0/realtime-web?tab=readme-ov-file#demos shows websockets being bursty which would be bad. WebTransport on the other hand is very consistent even with lots of packet loss.

So we have to figure out how hard it is to simulate the game, and if it's easy enough we go GGPO and if it's not we need to do OP style.

## Hiding Units / Unknown Info

Server could obfuscate or withhold commands and rather re-interpret them as events for clients
So instead of everyone getting the command "this group of units goes here", the server could send that to teammates, but only tell them the unit exists at a certain time.
Huh, maybe the units movement is transfered, but it's encrypted, and only after you have vision do you get its key. That's kinda wild.

Seems like over-engineering.
Still it would be really easy to cheat here, so I'm wondering what we can do.

If we withold units, flying over an area would have to move so much data around... But I guess we only have to send new state.

## Quasi - State vs Command

Could we transmit events rather commands, and those events are near real-time?

Events:
  Group moved from here to here
  Group targeted this

Unit group moving from here to there.
Group ID (16) + Origin (32) + Destination (16) + Type(8) + Delta Start (16)
11.264 KB

Unit Reveal Data Per Group
- Group ID (16) + Player ID (8)
  - Unit ID (16) + Position (32)
 
So we don't send commands (the player level), rather we send actions (at the group level)

You could still cheat and see where the units are going / targeting, but you couldn't see the full history of them and you wouldn't be able to see all of them.

As you discover units, they would be added to groups.
