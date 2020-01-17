# Cursed Chests
Cursed Chests are a new treasure type chests that are spawned randomly on the map. They can't be just opened like any other, first, player will have to defend against waves of powerful monsters. After surviving and killing every spawned monster, chest will open with rewards inside.

# Installation
* Open `data/actions/actions.xml`
* Add
```xml
<action actionid="9000" script="custom/cursed_chest.lua" />
```
* Open `data/creaturescripts/creaturescripts.xml`
* Add
```xml
<event type="death" name="CursedChestsDeath" script="cursed_chests.lua" />
```
* Open `data/globalevents/globalevents.xml`
* Add (300000 = 5 minutes, spawn new chest every 5 minutes)
```xml
<globalevent name="CursedChestSpawner" interval="300000" script="cursed_chest_spawner.lua"/>
```
* Open `data/events/events.xml`
* Enable `onMoveItem`, set `enabled="1"` here
```xml
<event class="Player" method="onMoveItem" enabled="1" />
```
* Open `data/events/scripts/player.lua`
* Find function `Player:onMoveItem` and add as first line in that function
```lua
if item:getActionId() == CURSED_CHESTS_AID then return false end
```
* Open `data/globalevents/scripts/startup.lua`
* At the end of the function `onStartup` (before last `end`) add this `CursedChestsLoad()`
* Open `data/lib/core/position.lua`
* Add if you don't have this function
```lua
function Position.sendAnimatedText(self, message)
    local specs = Game.getSpectators(self, false, true, 9, 9, 8, 8)
    if #specs > 0 then
        for i = 1, #specs do
            local player = specs[i]
            player:say(message, TALKTYPE_MONSTER_SAY, false, player, self)
        end
    end
end
```
* Download latest version from [here](https://github.com/Oen44/TFS-Cursed-Chests/releases/latest)
* Add files to your server `data` by following folder structure

# Configuration
Basic chests configuration - `data/actions/scripts/custom/cursed_chest.lua`
Here you can set chance to spawn given tier and text to be shown on spawned chest.
Change `item` to ids of your chests if you want different chest look for each tier.
```lua
CURSED_CHESTS_TIERS = {
    {
        tier = CURSED_CHESTS_TIER_COMMON,
        chance = 70,
        text = "Common Cursed Chest",
        item = 1750
    },
    {
        tier = CURSED_CHESTS_TIER_RARE,
        chance = 50,
        text = "Rare Cursed Chest",
        item = 1750
    },
    {
        tier = CURSED_CHESTS_TIER_EPIC,
        chance = 30,
        text = "Epic Cursed Chest",
        item = 1750
    },
    {
        tier = CURSED_CHESTS_TIER_LEGENDARY,
        chance = 10,
        text = "Legendary Cursed Chest",
        item = 1750
    }
}
```

Rewards and waves are now configured separately for each tier. Example chest inside. Hope you get it.
`CURSED_CHESTS_SKULL_DEFAULT` - skull type to set to wave monsters.
`CURSED_CHESTS_SKULL_BOSS` - skull type to set to boss monster.

`CURSED_CHESTS_CONFIG` - here you can add, remove and adjust chests.
`[1], [2]` - these are Chests IDs, make sure they are unique to each other.
`message` - text to show when player activates the chest.

`rewards` - list of items that can be added to the chest after every monster dies.
`chance` - value in % indicates chance for that item to be added. If set to 100, then will be added only ONCE
`item` - ID of the item
`amount` - how many items to add
`random` - set to true if you want to randomize amount of that item (from 1 to amount set)

`waves` - list of monsters that are randomly chosen to spawn
`"Monster Name"` - name of the monster to spawn

`boss` - optional if you want to spawn Boss at the end
`name` - name of the monster to be Boss.
`fightDuration` - in seconds, if this time passes and boss is alive, chest and boss will be removed from the map, player failed.
`message` - text to show on the player when boss is about to be spawned

Chests spawn configuration - `data/globalevents/scripts/cursed_chest_spawner.lua`.
`CURSED_CHESTS_AID` - action id set to the chest, change if already in use, change in `actions.xml` too.
`CURSED_CHESTS_SPAWNS` - list of centers of the spawn area where chests can appear.
`[1]` - Spawn ID, make sure they are unique to each other.
`pos` - Center of the spawn area.
`size` - Size of the spawn area.
`chests` - list of Chests IDs that can be spawned in that area, eg. `{ 1, 2, 6, 10 }`.