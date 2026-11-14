AnchorData 1.0.0
Made by: Loner Realms




AnchorData.GetData(player, mode)
With this function you can get the player's current data.

Arguments:

player — a numeric UserId or a Player object.
mode — a boolean value (optional parameter):
├─ true — the function will wait for the data to load for up to 10 seconds. If the data still has not appeared, or the player left, it will return nil.
└─ false / nil — returns the result immediately, without waiting (default).
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local data = AnchorData.GetData(player, true)
if data then
	data.Coins += 100
	print(data.Coins)
end
```

⚠️ This is a live table: everything you change in it will be saved. To read without risk, use GetDataCopy(). The Meta service information is not returned by this function.
The function will return the player's data table (Data). It will return nil if the player is not loaded yet, has already left, or an invalid argument was passed.

AnchorData.GetOldData(player, mode)
With this function you can get the player's data as it was at the moment of joining.

Arguments:

player — a numeric UserId or a Player object.
mode — a boolean value (optional parameter):
├─ true — the function will wait for the data to load for up to 10 seconds. If the data still has not appeared, it will return nil.
└─ false / nil — returns the result immediately, without waiting (default).
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local before = AnchorData.GetOldData(player, true)
local now = AnchorData.GetData(player)
if before and now then
	print("Earned this session:", now.Coins - before.Coins)
end
```

⚠️ This is a snapshot: changes in it are not saved and do not affect the current data. The Meta service information is not returned by this function.
The function will return a table with the player's data as of joining, or nil if there is no data.

AnchorData.GetFullData(player, signature)
With this function you can get all of the player's data, including the Meta service information.

Arguments:
player — a numeric UserId or a Player object.
signature — the signature "I-KNOW-WHAT-I'M-DOING", otherwise the function will return nil.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local record = AnchorData.GetFullData(player, "I-KNOW-WHAT-I'M-DOING")
if record then
	print(record.Data)
	print(record.Meta)
end
```

⚠️ The signature is required. Enter it consciously: changing Meta directly can break the module (the session, autosave, the "new player" detection). This is a live table, not a copy.
The function will return the player's whole table { Data = ..., Meta = ... } or nil (no signature or the player is not loaded).

AnchorData.GetDataCopy(player)
With this function you can get a copy of the player's data.

Argument:

player — a numeric UserId or a Player object.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local copy = AnchorData.GetDataCopy(player)
if copy then
	copy.Coins = 0 -- the real data did not change
	print(copy)
end
```

⚠️ The copy is independent: changes in it are not saved. The Meta service information is not returned.
The function will return a copy of the player's data table, or nil if the player is not loaded.

AnchorData.GetOldDataCopy(player)
With this function you can get a copy of the player's data as it was at the moment of joining.

Argument:
player — a numeric UserId or a Player object.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local copy = AnchorData.GetOldDataCopy(player)
print(copy)
```

⚠️ The copy is independent. The Meta service information is not returned.
The function will return a copy of the data table as of joining, or nil if there is no data.

AnchorData.GetAccountCreated(player)
With this function you can find out when the player first joined the game (when their record was created).

Argument:
player — a numeric UserId or a Player object.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local created = AnchorData.GetAccountCreated(player)
if created then
	print(created.Date.year, created.Date.month, created.Date.day)
	local days = (os.time() - created.Time) / 86400
	print("Days playing:", math.floor(days))
end
```

More details about what Date and Time contain:

Date — a ready-made table with the date and time components, built with os.date("*t").
├─ Convenient for reading directly and showing in the UI without calculations.
└─ Fields: year, month, day, hour, min, sec, wday, yday, isdst.
Time — a numeric value (Unix timestamp in seconds) obtained with os.time().
└─ Suitable for calculations: how much time has passed, comparing two dates, timers.
⚠️ This is a copy, not a live table. The value is written once, on the first join, and does not change afterwards.
The function will return the table { Date = ..., Time = ... } or nil if the player is not loaded.

AnchorData.GetOldServerId(player)
With this function you can find out the identifier (JobId) of the server the player was on before the current session.

Argument:
player — a numeric UserId or a Player object.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local lastServer = AnchorData.GetOldServerId(player)
if lastServer == game.JobId then
	print("The player returned to the same server")
end
```

⚠️ For a player joining for the first time, an empty string is returned. In Studio game.JobId is empty, while the module uses the name "STUDIO_SESSION", so comparing with game.JobId does not work in Studio.
The function will return the JobId of the previous server (a string) or nil if the player is not loaded.

AnchorData.IsOnline(player)
With this function you can find out whether the player is playing right now, including on another server.

Argument:
player — a numeric UserId or a Player object.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local result = AnchorData.IsOnline(userId)

if result == true then
	print("Online")
elseif result == false then
	print("Offline")
else
	print("Could not check")
end
```

⚠️ The function calls the DataStore: it uses a request and waits for the response. Roblox may return a response cached for a few seconds. "Online" is determined by the session: the flag is set and it was updated no longer than LockTimeout seconds ago. If the server crashed, the player will stop counting as online once that time passes.
The function will return true if the player is online, and false if they are offline or there is no record for them. It will return nil if the check could not be done (invalid argument or DataStore error).

AnchorData.ForceSave(player)
With this function you can force-save the player's data right now, without waiting for the autosave.

Argument:
player — a numeric UserId or a Player object.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

AnchorData.ForceSave(player) -- for example, after an important purchase
```

⚠️ The player stays in the game and keeps the session. The function waits for the DataStore response, so the script that called it will wait. If the player is already being saved (autosave or leaving), the command will be ignored. It works only for players loaded on this server. If another server has already taken the player's session, the player will be kicked.
The function returns nothing.

AnchorData.DeleteData(player, signature)
With this function you can delete the player's saved data.

Arguments:
player — a numeric UserId or a Player object.
signature — the signature "I-KNOW-WHAT-I'M-DOING", otherwise the function will return false.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local result = AnchorData.DeleteData(player, "I-KNOW-WHAT-I'M-DOING")
if result then
	print("Deleted!")
else
	print("Error!")
end
```

⚠️ Enter the signature consciously: the player's record is deleted from the DataStore. If the player is currently being saved or loaded, the function will wait for that to finish and only then delete. It works only for players loaded on this server. If the player is on this server, starting data is loaded for them right away, and PlayerLoaded and NewPlayer fire again. Field objects obtained earlier will stop affecting the data: get them again. The copy of the deleted data is kept only in memory (GetDeletedBackup) and disappears when the player leaves.
The function will return true on a successful deletion and false on an error (no signature, the player is not loaded, or the DataStore returned an error).

AnchorData.GetDeletedBackup(player, signature)
With this function you can get the data that was deleted by the DeleteData function.

Argument:
player — a numeric UserId or a Player object.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local backup = AnchorData.GetDeletedBackup(player)
local data = AnchorData.GetData(player)

if backup and data then
	for key, value in pairs(backup) do
		data[key] = value -- restore the deleted values
	end
end
```

⚠️ The backup exists only while the player is on the server. After the player leaves it disappears, and the data can no longer be restored. Restore carefully: the example above overwrites the top-level values of the current data with the values from the backup.
The function will return a copy of the deleted data (a table), or nil if there is no backup.

AnchorData.GetRawData(signature)
With this function you can get the internal table of all loaded players (PlayersData). Intended for debugging.

Argument:
nil — the signature "I-KNOW-WHAT-I'M-DOING", otherwise the function will return false
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local all = AnchorData.GetRawData("I-KNOW-WHAT-I'M-DOING")
if all then
	for userId, record in pairs(all) do
		print(userId, record.Data, record.Meta)
	end
end
```

⚠️ The signature is required. Enter it consciously: these are internal live tables, and changes in them can break the module.
The function will return a table where the key is the UserId and the value is { Data = ..., Meta = ... }. Without the signature, false is returned.

AnchorData.GetRawOldData(signature)
With this function you can get the internal table of data snapshots taken at the moment of joining (OldPlayersData). Intended for debugging.

Argument:
nil — the signature "I-KNOW-WHAT-I'M-DOING", otherwise the function will return false
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local snapshots = AnchorData.GetRawOldData("I-KNOW-WHAT-I'M-DOING")
if snapshots then
	print(snapshots)
end
```

⚠️ The signature is required. Enter it consciously.
The function will return a table of snapshots (key — UserId, value { Data = ..., Meta = ... }). Without the signature, false is returned.

AnchorData.Field(player, target, mode)
With this function you get an object for working with one field of the player's data. Through it you can read, change and delete the value, and also subscribe to changes.

Arguments:
player — a numeric UserId or a Player object.
target — a string used to find the field:
├─ "Coins" — a key name. The first key with this name at any depth will be found.
└─ "Info.Coins" — a path through dots. It points to exactly one field.
mode — a boolean value (optional parameter):
├─ true — the function will wait for the data to load for up to 10 seconds. If the data still has not appeared, it will return nil.
└─ false / nil — returns the result immediately, without waiting.
Example code:

```lua
local AnchorData = require(game.ServerScriptService.AnchorData.Module)

local coins = AnchorData.Field(player, "Coins", true)
if coins then
	coins:Add(100)
	print(coins:Get())
end
```

⚠️ If the name appears in the data several times, it is not known in advance which of the fields will be found. In that case use a path through dots.
⚠️ The object is tied to the table where the field was found. If that table is deleted or the player's data is unloaded (the player left), the object stops affecting the saved data.
⚠️ If a path is given and only the last key does not exist (for example, "Info.Gems"), you still get an object: Get() will return nil, and Set() will create the field. All intermediate tables in the path must exist.
The function will return a field object, or nil if the player is not loaded or the field was not found by name.

handle:Get()
With this method you get the field's current value.
Example code:

```lua
local level = AnchorData.Field(player, "Level")
print(level:Get())
```

The method will return the field's value, or nil if there is no field.

handle:Type()
With this method you find out the type of the value in the field.
Example code:

```lua
local coins = AnchorData.Field(player, "Coins")
print(coins:Type()) -- "number"
```

The method will return a string: "number", "string", "boolean", "table". If the field no longer exists, the string "nil" is returned.

handle:Set(value)
With this method you write a new value into the field. The type of the value is not checked.

Arguments:
value — the new value of the field.
Example code:

```lua
local level = AnchorData.Field(player, "Level")
level:Set(25)
```

The method will return the written value. If someone is subscribed to the field, a Set event will arrive.

handle:Add(value) and handle:Take(value)
With these methods you increase (Add) or decrease (Take) a numeric field.

Arguments:
value — the number to change the field by.
Example code:

```lua
local coins = AnchorData.Field(player, "Coins")
coins:Add(100)
coins:Take(30)
print(coins:Get())
```

⚠️ The methods work only with numbers. If the field or the argument is not a number, a warning will appear in the Output and the value will not change.
The methods will return the new value, or nothing if the change failed. If someone is subscribed to the field, an Add or Take event will arrive.

handle:True() and handle:False()
With these methods you write true or false into a boolean field.
Example code:

```lua
local tutorial = AnchorData.Field(player, "TutorialDone")
tutorial:True()
```

⚠️ The methods work only with boolean fields. For other types a warning will appear in the Output.
The methods will return true if the value was written, and false if not. If someone is subscribed to the field, a True or False event will arrive.

handle:Delete()
With this method you delete the field, whatever its type, including a table with all of its contents.
Example code:

```lua
local temp = AnchorData.Field(player, "TempBoost")
temp:Delete()
```

⚠️ The method deletes tables without confirmation. If you need protection, use DeleteValue or DeleteTable.
The method will return true if the field was deleted, and false if there was no field. A Delete event will arrive, where the old value is in the old argument.

handle:DeleteValue()
With this method you delete the field only if it is not a table.
Example code:

```lua
local coins = AnchorData.Field(player, "Coins")
coins:DeleteValue()
```

⚠️ If the field holds a table, the method will delete nothing and will write a warning. For tables use DeleteTable.
The method will return true on a deletion and false on a refusal.

handle:DeleteTable()
With this method you delete the field only if it is a table.
Example code:

```lua
local inventory = AnchorData.Field(player, "Inventory")
inventory:DeleteTable()
```

⚠️ If the field does not hold a table, the method will delete nothing and will write a warning. For simple values use DeleteValue.
⚠️ After a table is deleted, the objects you obtained earlier for fields inside it stop affecting the saved data.
The method will return true on a deletion and false on a refusal.

handle:CreateEvent()
With this method you get an event that fires on every change of this field made through an object. If the event does not exist yet, it is created.
Example code:

```lua
local coins = AnchorData.Field(player, "Coins")

coins:CreateEvent():Connect(function(action, playerId, key, old, new)
	print(action, key, old, "->", new)
end)

AnchorData.Field(player, "Coins"):Add(100) -- Add Coins 1000 -> 1100
```

Event arguments:

action — Set, Add, Take, True, False or Delete.
playerId — the player's UserId.
key — the last key only ("Coins", not "Info.Coins").
old — the value before the change.
new — the value after the change.
⚠️ The event is shared by all objects of the same field: you subscribed through one object and changed through another, and the signal still arrives.
⚠️ Events fire only for changes made through an object. If you write data.Coins += 1 directly, there will be no event.
The method will return .Event, which you connect to with :Connect.

handle:GetEvent(create)
With this method you get the field's event, if it already exists.

Arguments:
create — a boolean value (optional parameter). If true and the event does not exist yet, it will be created.
Example code:

```lua
local coins = AnchorData.Field(player, "Coins")
local event = coins:GetEvent() -- nil if nobody created it
```

The method will return .Event or nil.

handle:DestroyEvent()
With this method you destroy the field's event for all objects of this field.
Example code:

```lua
AnchorData.Field(player, "Coins"):DestroyEvent()
```

If there was no event, a warning will appear in the Output.

The Detachment event
When a player's data is unloaded (the player left), all of the events of their fields receive the signal ('Detachment', playerId), and a frame later the events are destroyed. This means the field objects you are still holding no longer work.

```lua
coins:CreateEvent():Connect(function(action, playerId)
	if action == "Detachment" then
		print("Player data", playerId, "was unloaded")
	end
end)
```