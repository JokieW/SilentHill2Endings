# Silent Hill 2 Endings guide

When reaching the end, the game will calculate points for 4 endings:
`Ritual`, `Leave`, `Maria`, and `In water`.

These points are calculated from these flags and conditions:

## Ritual
`Ritual` starts at 0 points. These add one point each:
- Have [Book: "Lost Memories"] in inventory.
- Have [Book: "Crimson Ceremony"] in inventory.
- Have [White Chrism] in inventory.
- Have [Obsidian Goblet] in inventory.

## Leave
`Leave` starts at 5 points. These add one point each:
- Listened to the whole sickbed conversation.
- Healed in excess of 200% HP. [1]
- Never got the *Leave* ending but did get the *Maria* ending.
- Never got the *Leave* ending but did get the *In water* ending.

These will remove one point each:
- Went east after meeting Maria and had her remind you the way to the hotel.

## Maria
`Maria` starts at 3 points. These add one point each:
- Spent 10 minutes or more near Maria. [2]
- Bumped in Maria less than three times. [4]
- Took no damage in the entire game OR Maria took less than 3% the damage you got from monsters. [8]
- Checked back on Maria in Hospital S3. [5]
- Tried to go back to see dead Maria in the labyrith twice. (White door)
- Never got the *Maria* ending but did get the *Leave* ending.
- Never got the *Maria* ending but did get the *In water* ending.

These will remove one point each:
- Went on Nathan Avenue instead of chasing Laura and had Maria remind you.
- Examined the [Photo of Mary] and/or [Mary's Letter] at least twice.
- Spent 5 minutes or more far from Maria. [3]
- Did more than 32 damage to Maria. [6]
- Did more than 80 damage to Maria on beginner/easy, or more than 40 damage on normal/hard. [6]
- Bumped in Maria 30 times or more. [4]

## In Water
`In water` starts at 2 points. These add one point each:
- Read the diary on the Hospital Rooftop
- Listened to the whole sickbed conversation.
- Read the second message in Neely's Bar. (The one on the wall, not the window)
- Examined [Angela's Knife] at least once.
- Have the "bad health score" above 60. [7]
- Have the "bad health score" above 240. [7]
- Never got the *In water* ending but did get the *Leave* ending.
- Never got the *In water* ending but did get the *Maria* ending.

---
After all these points are tallied up, it will look for the good ending through these conditions [9], in order:

1. Are there 4 `Ritual` points? **Get ending *Ritual***.

2. Is `Leave` larger than both `Maria` and `In water`? **Get ending *Leave***.
3. Did you never get the *Leave* ending, and `Leave` is larger or equal to both `Maria` and `In water`? **Get ending *Leave***. 

4. Is `Maria` larger than both `Leave` and `In water`? **Get ending *Maria***.
5. Did you never get the *Maria* ending, and `Maria` is larger or equal to both `Leave` and `In water`? **Get ending *Maria***.

6. Is `In water` larger than both `Leave` and `Maria`? **Get ending *In water***.
7. Did you never get the *In water* ending? then:
   1. Is `In water` larger or equal to both `Leave` and `Maria`? **Get ending *In water***.
   2. Is `Leave` larger or equal to both `Maria` and `In water`? **Get ending *Leave***.
   3. Is `Maria` larger or equal to `In water`? **Get ending *Maria***.

8. If none of the above apply, **Get ending *In water***.

Your ending is determined the moment you go through the very last door, after the hallway with the sickbed conversation.
It will not change after that.

---
That's it!
Note this is from the PC version. In theory it should apply to Restless Dreams on consoles, but I haven't tested.

If you have a question or a query, don't hesitate to send them!
You can reach me at these places:
- Discord: Jokie#9392
- GitHub: <https://github.com/JokieW>
- Twitter: <https://twitter.com/Jokie_W>

If you use this information on a website I would appreciate a mention!

---
## Notes
[1] Say when you are at 85/100HP and heal 25HP, the excess is 10 HP, or 10%.
The point is given when `excessHealth > 2.0`, and is calculated like this every time you heal:
```c
health += heal
if(health > maxHealth)
{
	excessHealth += (health - maxHealth) / maxHealth;
	health = maxHealth;
}
```
[2] "Near" is within 1000 units from Maria, or about 2 meters. When she isn't walking/running towards you, she's within that distance.
Note that, at least on the PC version, this is bugged. This number will _always_ count up when she is not with you (So most of the game).

[3] "Far" is further than 5000 units from Maria. You can mostly only get it when running outside.

[4] You can confirm the bump if Maria vocalizes "agh".

[5] Bugged, at least on the PC version. It's given to you the first time you enter the room with her, and so is unmissable.

[6] This only counts damage done by you, not by monsters.

[7] This number goes up when you have less than 50% health.
It's calculated like this, every frame:
```c
//sin is in radians
//deltaTime: time in milliseconds of each frame. 0.03333 for 30FPS, 0.016666 for 60 FPS
float hpPercent = health / maxHealth;
if(hpPercent  < 0.50)
{
	badHealth += (1 - sin(hpPercent * 3.141592741) ) * deltaTime;
}
```
[8] If you take 0 damage in the whole game, then this point is just given to you, no matter how much Maria got hurt.
Technically, it is if you took less than 1 damage, not 0. But that's pretty much impossible.
It is calculated like this:
```c
if(damageTaken > 1.0)
{
  if(monsterDmgDoneOnMaria / damageTakenByJames < 0.03) 
  {
    maria++;
  }
}
else
{
  maria++;
}
```

[9] Here's the whole thing in pseudocode:
```c
if(ritual == 4)
{
	return RITUAL;
}

if(leave > maria && leave > water) 
{
	return LEAVE;
}
if(neverGotLeave)
{
	if(leave >= maria && leave >= water)
	{
		return LEAVE;
	}
}

if(maria > water && maria > leave) 
{
	return MARIA;
}
if(neverGotMaria)
{
	if(maria >= water && maria >= leave)
	{
		return MARIA;
	}
}

if(water > leave && water > maria) 
{
	return WATER;
}
if(neverGotInWater)
{
	if(water >= leave && water >= maria)
	{
		return WATER;
	}
	if(leave >= maria && leave >= water)
	{
		return LEAVE;
	}
	if(maria >= water)
	{
		return MARIA;
	}
}
return WATER;
```
