local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualUser = game:GetService("VirtualUser")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")

local Player = Players.LocalPlayer

local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local Options = Fluent.Options

local Window = Fluent:CreateWindow({
    Title = "Batman Hub",
    SubTitle = "Blox Fruits",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 400),
    Acrylic = false,
    Theme = "Dark",
    MinimizeKey = Enum.KeyCode.LeftControl,
})

local Main = Window:AddTab({ Title = "Main", Icon = "home" })
local Stack = Window:AddTab({ Title = "Stack Farming", Icon = "layers" })
local StatusTab = Window:AddTab({ Title = "Status", Icon = "activity" })
local RaidTab = Window:AddTab({ Title = "Raid", Icon = "users" })
local LocalTab = Window:AddTab({ Title = "Local Player", Icon = "user" })
local Travel = Window:AddTab({ Title = "Travel", Icon = "plane" })
local Misc = Window:AddTab({ Title = "Misc", Icon = "wrench" })
local Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })

local CAKE_MOBS = { "Cookie Crafter", "Cake Guard", "Baking Staff", "Head Baker" }
local BOSS_NAMES = { "Cake Prince", "Katakuri" }
local MOB_AREAS = {
    CFrame.new(-2374, 37, -12125),
    CFrame.new(-1598, 43, -12244),
    CFrame.new(-1887, 77, -12900),
    CFrame.new(-2216, 52, -12853),
}
local CAKE_ENTRANCE = CFrame.new(-2124, 69, -12401)
local CAKE_TOTAL = 500
local SEA3_IDS = { [7449423635] = true, [100117331123089] = true }

local METHODS = {
    ["Katakuri"] = {
        cake = true,
        bosses = {},
        giver = CFrame.new(-1927.9, 37.8, -12842.5),
        mobs = {
            { name = "Cookie Crafter", lvl = 2200, quest = "CakeQuest1", idx = 1 },
            { name = "Cake Guard", lvl = 2225, quest = "CakeQuest1", idx = 2 },
            { name = "Baking Staff", lvl = 2250, quest = "CakeQuest2", idx = 1 },
            { name = "Head Baker", lvl = 2275, quest = "CakeQuest2", idx = 2 },
        },
    },
    ["Bones"] = {
        bosses = {},
        giver = CFrame.new(-9516.9, 178.0, 6078.9),
        mobs = {
            { name = "Reborn Skeleton", lvl = 1975, quest = "HauntedQuest1", idx = 1 },
            { name = "Living Zombie", lvl = 2000, quest = "HauntedQuest1", idx = 2 },
            { name = "Demonic Soul", lvl = 2025, quest = "HauntedQuest2", idx = 1 },
            { name = "Posessed Mummy", lvl = 2050, quest = "HauntedQuest2", idx = 2 },
        },
    },
    ["Tyrant Of The Skies"] = {
        bosses = { "Tyrant of the Skies" },
        giver = CFrame.new(-16541.0215, 54.770813, 1051.46118),
        mobs = {
            { name = "Sun-kissed Warrior", lvl = 1575, quest = "TikiQuest2", idx = 1 },
            { name = "Isle Champion", lvl = 1600, quest = "TikiQuest2", idx = 2 },
            { name = "Serpent Hunter", lvl = 1625, quest = "TikiQuest3", idx = 1, giver = CFrame.new(-16665.1914, 104.596405, 1579.69434) },
            { name = "Skull Slayer", lvl = 1650, quest = "TikiQuest3", idx = 2, giver = CFrame.new(-16665.1914, 104.596405, 1579.69434) },
        },
    },
}

local ISLAND_NAMES = {
    "Haunted Castle", "Tiki Outpost", "Castle On The Sea", "Mansion", "Hydra Island",
    "Port Town", "Great Tree", "Cake Land", "Chocolate Land",
}
local ISLANDS = {
    ["Haunted Castle"] = Vector3.new(-9517, 142.1348876953125, 5528),
    ["Tiki Outpost"] = Vector3.new(-16224, 9.090619087219238, 439),
    ["Castle On The Sea"] = Vector3.new(-4980, 314.5460205078125, -3018),
    ["Mansion"] = Vector3.new(-12472.18359375, 374.9445495605469, -7554.11865234375),
    ["Hydra Island"] = Vector3.new(5294, 1005.3837890625, 391),
    ["Port Town"] = Vector3.new(-340, 20.641170501708984, 5524),
    ["Great Tree"] = Vector3.new(2205, 21.746213912963867, -6766),
    ["Cake Land"] = Vector3.new(-1897, 13.819925308227539, -11576),
    ["Chocolate Land"] = Vector3.new(219, 126.62200927734375, -12604),
}

local BRING_RANGE = 300
local HOVER_HEIGHT = 20
local STUCK_TIME = 3
local SKIP_TIME = 15
local BUG_RADIUS = 60
local BUG_TIME = 30

local ELITE_BOSSES = { "Diablo", "Deandre", "Urban" }
local DOUGH_BOSS = { "Dough King" }
local RIP_BOSS = { "rip_indra True Form", "rip_indra" }
local ELITE_CALL_COOLDOWN = 5

local CASTLE_POS = Vector3.new(-5496, 313, -2841)
local CASTLE_RADIUS = 700
local RAID_WAIT = 240
local RAID_END_IDLE = 15

local farming = false
local traveling = false
local travelId = 0
local target = nil
local attackFocus = nil
local BringGroup = {}
local BringPoint = nil
local engaged = setmetatable({}, { __mode = "k" })
local skipUntil = setmetatable({}, { __mode = "k" })
local memberInfo = setmetatable({}, { __mode = "k" })
local bugSpots = {}
local questId, kills, needed, questSeen, hiddenSince = nil, 0, 0, false, nil
local questMobName = nil
local InSea3 = false

local lastMode = "farm"
local lastEliteCall = 0
local lastHaki = 0
local lastSim = 0
local raidUntil, raidLastSeen, raidSawEnemies = 0, 0, false

local Stats = { State = "Idle", PauseStore = false, RaidMsg = "-", RaidsMulti = 0, RaidAuraKills = 0 }

local RaidCfg = {
    Multi = false,
    Pay = false,
    Secondary = false,
    Targets = {},
    Chip = "Ice",
    KillAura = true,
    MaxFruitPrice = 1000000,
    HostCFrame = CFrame.new(-5033.24805, 310.821991, -2949.90894, 0.927278221, 0, 0.374372959, 0, 1, 0, -0.374372959, 0, 0.927278221),
    GuestCFrames = {
        CFrame.new(-5018.04883, 318.082001, -2912.26807, 0.927278221, 0, 0.374372959, 0, 1, 0, -0.374372959, 0, 0.927278221),
        CFrame.new(-5003.35498, 318.082001, -2875.87695, 0.927278221, 0, 0.374372959, 0, 1, 0, -0.374372959, 0, 0.927278221),
        CFrame.new(-4988.50293, 318.082001, -2839.09302, 0.927278221, 0, 0.374372959, 0, 1, 0, -0.374372959, 0, 0.927278221),
    },
    GuestSlot = 1,
    SlotRadius = 8,
}

local Kat = { lastIdleQuery = 0, remaining = nil, needCheck = true, blockUntil = 0, mode = nil, lastN = nil, bossSeen = false }

local CommF = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("CommF_")
local Net = ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net")
local RegisterAttack = Net:WaitForChild("RE/RegisterAttack")
local RegisterHit = Net:WaitForChild("RE/RegisterHit")

local cloneref = cloneref or function(x) return x end
local okNet, netModule = pcall(require, Net)
local seedRemote = Net:WaitForChild("seed", 5)
local targetRemote, targetId = nil, nil

local function checkRemote(obj)
    if obj:IsA("RemoteEvent") and obj:GetAttribute("Id") then
        targetId = obj:GetAttribute("Id")
        targetRemote = obj
    end
end

task.spawn(function()
    for _, name in ipairs({ "Util", "Common", "Remotes", "Assets", "FX" }) do
        local folder = ReplicatedStorage:WaitForChild(name, 5)
        if folder then
            for _, child in ipairs(folder:GetChildren()) do checkRemote(child) end
            folder.ChildAdded:Connect(checkRemote)
        end
    end
end)

local function root()
    local char = Player.Character
    return char and char:FindFirstChild("HumanoidRootPart")
end

local function commF(...)
    return pcall(CommF.InvokeServer, CommF, ...)
end

local function alive(enemy)
    if not enemy or not enemy.Parent then return false end
    local humanoid = enemy:FindFirstChildOfClass("Humanoid")
    local hrp = enemy:FindFirstChild("HumanoidRootPart")
    return humanoid ~= nil and hrp ~= nil and humanoid.Health > 0
end

local function clearGroup()
    BringGroup = {}
    BringPoint = nil
end

local function waitFor(fn, timeout)
    local t0 = os.clock()
    while farming and os.clock() - t0 < timeout do
        local result = fn()
        if result then return result end
        task.wait(0.2)
    end
end

local function detectSea3()
    if SEA3_IDS[game.PlaceId] then return true end
    local origin = workspace:FindFirstChild("_WorldOrigin")
    local spawns = origin and origin:FindFirstChild("EnemySpawns")
    for _, s in ipairs(spawns and spawns:GetChildren() or {}) do
        if table.find(CAKE_MOBS, s.Name) then return true end
    end
    for _, e in ipairs(workspace.Enemies:GetChildren()) do
        if table.find(CAKE_MOBS, e.Name) then return true end
    end
    return false
end

task.spawn(function()
    while true do
        local ok, result = pcall(detectSea3)
        InSea3 = ok and result == true
        task.wait(2)
    end
end)

local function skipped(enemy)
    local t = skipUntil[enemy]
    if t and os.clock() < t then return true end
    skipUntil[enemy] = nil
    return false
end

local function nearBug(pos)
    local now = os.clock()
    for i = #bugSpots, 1, -1 do
        local spot = bugSpots[i]
        if now > spot.untilT then
            table.remove(bugSpots, i)
        elseif (spot.pos - pos).Magnitude <= BUG_RADIUS then
            return true
        end
    end
    return false
end

local function usable(enemy)
    return alive(enemy) and not skipped(enemy) and not nearBug(enemy.HumanoidRootPart.Position)
end

local function watchdog()
    if not BringPoint then return end
    for _, enemy in ipairs(BringGroup) do
        if alive(enemy) then
            local humanoid = enemy:FindFirstChildOfClass("Humanoid")
            local rec = memberInfo[enemy]
            if not rec or humanoid.Health ~= rec.h then
                memberInfo[enemy] = { h = humanoid.Health, t = os.clock() }
            elseif os.clock() - rec.t > STUCK_TIME then
                skipUntil[enemy] = os.clock() + SKIP_TIME
                table.insert(bugSpots, { pos = BringPoint, untilT = os.clock() + BUG_TIME })
                memberInfo[enemy] = nil
                clearGroup()
                attackFocus = nil
                return
            end
        end
    end
end

local function pickMob(method)
    local level = Player.Data.Level.Value
    local picked = method.mobs[1]
    for _, mob in ipairs(method.mobs) do
        if level >= mob.lvl then
            picked = mob
        end
    end
    return picked
end

local function allNames(method, first)
    local names = { first.name }
    for _, mob in ipairs(method.mobs) do
        if mob.name ~= first.name then
            table.insert(names, mob.name)
        end
    end
    return names
end

local function questVisible()
    local ok, visible = pcall(function()
        return Player.PlayerGui.Main.Quest.Visible
    end)
    return ok and visible
end

local function questTitle()
    local ok, text = pcall(function()
        return Player.PlayerGui.Main.Quest.Container.QuestTitle.Title.Text
    end)
    return ok and text or ""
end

local function questNeeded(mob)
    local ok, count = pcall(function()
        return require(ReplicatedStorage.Quests)[mob.quest][mob.idx].Task[mob.name]
    end)
    return ok and tonumber(count) or 10
end

local function questDone()
    if kills >= needed then return true end
    if questVisible() then
        questSeen, hiddenSince = true, nil
    elseif questSeen then
        hiddenSince = hiddenSince or os.clock()
        return os.clock() - hiddenSince > 2
    end
    return false
end

local function track(enemy)
    if engaged[enemy] then return end
    engaged[enemy] = true
    local humanoid = enemy:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.Died:Connect(function()
            if enemy.Name == questMobName then
                kills += 1
            end
            if Kat.remaining and Kat.remaining > 0 and table.find(CAKE_MOBS, enemy.Name) then
                Kat.remaining -= 1
            end
        end)
    end
end

local function findEnemy(names, isBoss)
    local hrp = root()
    local nearest, best = nil, math.huge
    for _, enemy in ipairs(workspace.Enemies:GetChildren()) do
        if table.find(names, enemy.Name) and (isBoss and alive(enemy) or usable(enemy)) then
            local part = enemy.HumanoidRootPart
            local distance = hrp and (part.Position - hrp.Position).Magnitude or 0
            if distance < best then
                nearest, best = enemy, distance
            end
        end
    end
    return nearest
end

local function findUnloaded(names)
    for _, v in ipairs(ReplicatedStorage:GetChildren()) do
        if table.find(names, v.Name) and alive(v) then return v end
    end
end

local function findSpawn(names, fallback)
    local hrp = root()
    local list = {}
    local origin = workspace:FindFirstChild("_WorldOrigin")
    local spawns = origin and origin:FindFirstChild("EnemySpawns")
    for _, part in ipairs(spawns and spawns:GetChildren() or {}) do
        if part:IsA("BasePart") and not nearBug(part.Position) then
            for _, name in ipairs(names) do
                if part.Name:find(name, 1, true) then
                    table.insert(list, part.Position)
                    break
                end
            end
        end
    end
    if #list == 0 and fallback then
        for _, cf in ipairs(fallback) do table.insert(list, cf.Position) end
    end
    if hrp then
        table.sort(list, function(a, b)
            return (a - hrp.Position).Magnitude < (b - hrp.Position).Magnitude
        end)
    end
    for _, pos in ipairs(list) do
        if not hrp or (pos - hrp.Position).Magnitude > 25 then return pos end
    end
    return list[1]
end

local function selectedWeapon()
    return Options.SelectWeapon and Options.SelectWeapon.Value or "Melee"
end

local function isFruit(tool)
    return tool:IsA("Tool") and (tool.ToolTip == "Blox Fruit" or tool.Name:find("Fruit") ~= nil)
end

local function isSelectedWeapon(tool)
    local weapon = selectedWeapon()
    if weapon == "Fruit(M1)" then return isFruit(tool) end
    return tool:IsA("Tool") and tool.ToolTip == weapon
end

local function equipWeapon()
    local char = Player.Character
    local humanoid = char and char:FindFirstChildOfClass("Humanoid")
    if not humanoid then return end
    local held = char:FindFirstChildOfClass("Tool")
    if held and isSelectedWeapon(held) then return end
    for _, tool in ipairs(Player.Backpack:GetChildren()) do
        if isSelectedWeapon(tool) then
            humanoid:EquipTool(tool)
            return
        end
    end
end

local function enableHaki()
    local char = Player.Character
    if not char or os.clock() - lastHaki < 2 then return end
    if char:GetAttribute("BusoEnabled") == true or char:FindFirstChild("HasBuso") then return end
    lastHaki = os.clock()
    commF("Buso")
end

local function hover(enabled)
    local hrp = root()
    if not hrp then return end
    local velocity = hrp:FindFirstChild("BatmanHover")
    if enabled and not velocity then
        velocity = Instance.new("BodyVelocity")
        velocity.Name = "BatmanHover"
        velocity.Velocity = Vector3.zero
        velocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        velocity.Parent = hrp
    elseif not enabled and velocity then
        velocity:Destroy()
    end
end

local function click()
    local char = Player.Character
    local tool = char and char:FindFirstChildOfClass("Tool")
    if tool then tool:Activate() end
    VirtualUser:CaptureController()
    VirtualUser:Button1Down(Vector2.new(1280, 672))
    VirtualUser:Button1Up(Vector2.new(1280, 672))
end

local function fastAttack()
    local char = Player.Character
    local hrp = root()
    if not (char and hrp) then return end

    equipWeapon()

    local targets = {}
    for _, enemy in ipairs(Workspace.Enemies:GetChildren()) do
        local eRoot = enemy:FindFirstChild("HumanoidRootPart")
        local eHum = enemy:FindFirstChildOfClass("Humanoid")
        if eRoot and eHum and eHum.Health > 0 and (eRoot.Position - hrp.Position).Magnitude <= 60 then
            for _, part in ipairs(enemy:GetChildren()) do
                if part:IsA("BasePart") then
                    table.insert(targets, { enemy, part })
                end
            end
        end
    end
    if #targets == 0 then return end

    local tool = char:FindFirstChildOfClass("Tool")
    if not tool then return end
    local weapon = selectedWeapon()

    if weapon == "Fruit(M1)" then
        local remote = tool:FindFirstChild("LeftClickRemote")
        if not isFruit(tool) or not remote then return end
        local focus = alive(attackFocus) and attackFocus or targets[1][1]
        local direction = focus.HumanoidRootPart.Position - hrp.Position
        remote:FireServer(direction.Magnitude > 0 and direction.Unit or hrp.CFrame.LookVector, 1)
    elseif tool:GetAttribute("WeaponType") ~= weapon then
        return
    end

    if okNet then netModule:RemoteEvent("RegisterHit", true) end
    RegisterAttack:FireServer()

    local head = targets[1][1]:FindFirstChild("Head")
    if not head then return end

    local uidStr = tostring(Player.UserId):sub(2, 4)
    local coStr = tostring(coroutine.running()):sub(11, 15)
    RegisterHit:FireServer(head, targets, {}, uidStr .. coStr)

    if targetRemote and targetId and seedRemote then
        local remoteName = string.gsub("RE/RegisterHit", ".", function(c)
            return string.char(bit32.bxor(string.byte(c), math.floor((Workspace:GetServerTimeNow() / 10) % 10) + 1))
        end)
        local seedValue = seedRemote:InvokeServer() * 2
        local xorValue = bit32.bxor(targetId + 909090, seedValue)
        cloneref(targetRemote):FireServer(remoteName, xorValue, head, targets)
    end
end

local function buildBringGroup(lead, names)
    local limit = math.max(1, tonumber(Options.BringCount.Value) or 2)
    BringGroup = { lead }
    track(lead)
    local leadPos = lead.HumanoidRootPart.Position
    local candidates = {}
    for _, e in ipairs(workspace.Enemies:GetChildren()) do
        if e ~= lead and table.find(names, e.Name) and usable(e) then
            local d = (e.HumanoidRootPart.Position - leadPos).Magnitude
            if d <= BRING_RANGE then
                table.insert(candidates, { e, d })
            end
        end
    end
    table.sort(candidates, function(a, b) return a[2] < b[2] end)
    for i = 1, math.min(limit - 1, #candidates) do
        table.insert(BringGroup, candidates[i][1])
        track(candidates[i][1])
    end
    BringPoint = leadPos

    for _, e in ipairs(BringGroup) do
        local humanoid = e:FindFirstChildOfClass("Humanoid")
        memberInfo[e] = { h = humanoid and humanoid.Health or 0, t = os.clock() }
    end
end

local function flyTo(cf, tolerance, timeout, abort)
    target = cf
    local t0 = os.clock()
    while farming and root() and (root().Position - cf.Position).Magnitude > tolerance
        and (not timeout or os.clock() - t0 < timeout) and not (abort and abort()) do
        task.wait()
    end
end

local function onText(text)
    if type(text) ~= "string" then return end
    local t = text:lower()
    if t:find("pirate", 1, true) and (t:find("castle", 1, true) or t:find("invad", 1, true)) then
        raidUntil = os.clock() + RAID_WAIT
        raidLastSeen = os.clock()
        raidSawEnemies = false
    end
end

pcall(function()
    Player:WaitForChild("PlayerGui").DescendantAdded:Connect(function(d)
        if d:IsA("TextLabel") then
            onText(d.Text)
            task.delay(0.3, function() onText(d.Text) end)
        end
    end)
end)

pcall(function()
    game:GetService("TextChatService").MessageReceived:Connect(function(msg)
        onText(msg.Text)
    end)
end)

local function castleEnemies()
    local hrp = root()
    local list = {}
    for _, enemy in ipairs(workspace.Enemies:GetChildren()) do
        if alive(enemy) then
            local pos = enemy.HumanoidRootPart.Position
            if (pos - CASTLE_POS).Magnitude <= CASTLE_RADIUS then
                table.insert(list, { enemy, hrp and (pos - hrp.Position).Magnitude or 0 })
            end
        end
    end
    table.sort(list, function(a, b) return a[2] < b[2] end)
    return list
end

local function raidActive()
    local now = os.clock()
    if #castleEnemies() > 0 then
        raidSawEnemies = true
        raidLastSeen = now
        return true
    end
    if raidSawEnemies then
        if now - raidLastSeen > RAID_END_IDLE then
            raidSawEnemies = false
            raidUntil = 0
            return false
        end
        return true
    end
    return now < raidUntil
end

local function handleRaid()
    clearGroup()
    local list = castleEnemies()
    if #list > 0 then
        local enemy = list[1][1]
        attackFocus = enemy
        target = CFrame.new(enemy.HumanoidRootPart.Position + Vector3.new(0, HOVER_HEIGHT, 0))
        click()
    else
        attackFocus = nil
        target = CFrame.new(CASTLE_POS + Vector3.new(0, 30, 0))
    end
end

local function findBossAny(names)
    local e = findEnemy(names, true)
    if e then return e, true end
    for _, name in ipairs(names) do
        local m = ReplicatedStorage:FindFirstChild(name)
        if m and m:FindFirstChild("HumanoidRootPart") then
            return m, false
        end
    end
end

local function eliteQuestActive()
    if not questVisible() then return false end
    local title = questTitle():lower()
    for _, name in ipairs(ELITE_BOSSES) do
        if title:find(name:lower(), 1, true) then return true end
    end
    return false
end

local function handleElite(boss, inWorld)
    clearGroup()
    if not eliteQuestActive() and os.clock() - lastEliteCall > ELITE_CALL_COOLDOWN then
        lastEliteCall = os.clock()
        if questVisible() and questTitle() ~= "" then
            commF("AbandonQuest")
            task.wait(0.3)
        end
        commF("EliteHunter")
        task.wait(0.5)
    end
    local hrp = boss:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    target = CFrame.new(hrp.Position + Vector3.new(0, HOVER_HEIGHT, 0))
    if inWorld then
        attackFocus = boss
        click()
    else
        attackFocus = nil
    end
end

local function handleStackBoss(boss, inWorld)
    clearGroup()
    local hrp = boss:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    target = CFrame.new(hrp.Position + Vector3.new(0, HOVER_HEIGHT, 0))
    if inWorld then
        attackFocus = boss
        click()
    else
        attackFocus = nil
    end
end

local function findBoss()
    local boss = findEnemy(BOSS_NAMES, true)
    if boss then return boss end
    for _, v in ipairs(ReplicatedStorage:GetChildren()) do
        if table.find(BOSS_NAMES, v.Name) and alive(v) then return v end
    end
end

local function applyCountText(res)
    local text = res ~= nil and tostring(res) or ""
    if text == "" or text == "false" or text == "nil" or text == "true" then return end

    local nums = {}
    for d in text:gmatch("%d+") do table.insert(nums, tonumber(d)) end

    if #nums == 0 then
        Kat.remaining = 0
        return
    end
    if #nums >= 2 and nums[1] <= nums[2] then
        Kat.remaining = nums[2] - nums[1]
        Kat.lastN = nums[1]
        return
    end

    local n = nums[1]
    local low = text:lower()
    local mode
    if low:find("more") or low:find("left") or low:find("remain") or low:find("to go") or low:find("faltam") then
        mode = "left"
    elseif low:find("defeated") or low:find("killed") or low:find("slain") then
        mode = "killed"
    else
        if not Kat.mode and Kat.lastN then
            if n < Kat.lastN then
                Kat.mode = "left"
            elseif n > Kat.lastN then
                Kat.mode = "killed"
            end
        end
        mode = Kat.mode
    end
    Kat.lastN = n

    if mode == "left" then
        Kat.remaining = n
    elseif mode == "killed" then
        Kat.remaining = math.max(CAKE_TOTAL - n, 0)
    else
        Kat.remaining = nil
    end
end

local function queryCount()
    local ok, res = commF("CakePrinceSpawner", true)
    if ok then pcall(applyCountText, res) end
end

task.spawn(function()
    while true do
        task.wait(2)
        if InSea3 and not traveling then
            local farmingKat = farming and Options.Farm.Value and Options.MethodFarm.Value == "Katakuri"
            if farmingKat or os.clock() - Kat.lastIdleQuery > 6 then
                Kat.lastIdleQuery = os.clock()
                queryCount()
            end
        end
    end
end)

local function reachBoss()
    attackFocus = nil
    local ok, mirror = pcall(function() return workspace.Map.CakeLoaf.BigMirror.Main end)
    if not (ok and mirror) then
        flyTo(CAKE_ENTRANCE, 8, 30)
        return
    end
    local function touch()
        pcall(function()
            local r = root()
            firetouchinterest(r, mirror, 0)
            task.wait(0.1)
            firetouchinterest(r, mirror, 1)
        end)
    end
    touch()
    task.wait(1)
    local boss, hrp = findBoss(), root()
    if boss and hrp and (boss.HumanoidRootPart.Position - hrp.Position).Magnitude > 1500 then
        flyTo(CFrame.new(mirror.Position), 8, 30)
        touch()
        task.wait(1)
    end
end

local function summonBoss()
    for _ = 1, 3 do
        if not farming then return false end
        if waitFor(findBoss, 2) then return true end
        commF("CakePrinceSpawner")
        if waitFor(findBoss, 2) then return true end
    end
    return false
end

local function katakuri()
    local hrp = root()
    if not hrp then return true end

    local boss = findBoss()
    if boss then
        Kat.bossSeen = true
        Stats.State = "Katakuri Boss"
        clearGroup()
        if (boss.HumanoidRootPart.Position - hrp.Position).Magnitude > 1500 then
            reachBoss()
        else
            attackFocus = boss
            target = CFrame.new(boss.HumanoidRootPart.Position + Vector3.new(0, HOVER_HEIGHT, 0))
            click()
        end
        return true
    end

    if Kat.bossSeen then
        Kat.bossSeen = false
        Kat.remaining = nil
        Kat.needCheck = true
    end

    if Kat.needCheck then
        clearGroup()
        attackFocus = nil
        queryCount()
        Kat.needCheck = false
        return true
    end

    if Kat.remaining == 0 and os.clock() >= Kat.blockUntil then
        clearGroup()
        attackFocus = nil
        Stats.State = "Summoning Katakuri"
        if not summonBoss() then
            Kat.blockUntil = os.clock() + 30
            Kat.remaining = nil
        end
        return true
    end

    return false
end

local fruitTry = setmetatable({}, { __mode = "k" })

local function nearestFruit()
    local hrp = root()
    local nearest, best = nil, math.huge
    for _, v in ipairs(workspace:GetChildren()) do
        if v:IsA("Tool") and v.Name:find("Fruit") and not skipped(v) then
            local handle = v:FindFirstChild("Handle")
            if handle then
                local distance = hrp and (handle.Position - hrp.Position).Magnitude or 0
                if distance < best then
                    nearest, best = v, distance
                end
            end
        end
    end
    return nearest
end

local function handleFruit(fruit)
    clearGroup()
    attackFocus = nil
    local handle = fruit:FindFirstChild("Handle")
    if not handle then return end
    local hrp = root()
    target = CFrame.new(handle.Position)
    if hrp and (hrp.Position - handle.Position).Magnitude < 15 then
        fruitTry[fruit] = fruitTry[fruit] or os.clock()
        pcall(function()
            firetouchinterest(hrp, handle, 0)
            firetouchinterest(hrp, handle, 1)
        end)
        if os.clock() - fruitTry[fruit] > 10 then
            skipUntil[fruit] = os.clock() + 60
            fruitTry[fruit] = nil
        end
    end
end

local function storeFruits()
    local fruits = {}
    for _, container in ipairs({ Player.Character, Player.Backpack }) do
        if container then
            for _, tool in ipairs(container:GetChildren()) do
                if isFruit(tool) then
                    table.insert(fruits, tool)
                end
            end
        end
    end
    local keepFirst = selectedWeapon() == "Fruit(M1)"
    for i, tool in ipairs(fruits) do
        if not (keepFirst and i == 1) then
            commF("StoreFruit", tool:GetAttribute("OriginalName") or tool.Name, tool)
            task.wait(0.3)
        end
    end
end

task.spawn(function()
    while true do
        task.wait(2)
        if Options.StoreFruits and Options.StoreFruits.Value and not Stats.PauseStore then
            pcall(storeFruits)
        end
    end
end)

local RaidTypes = { "Flame", "Ice", "Sand", "Dark", "Light", "Magma", "Quake", "Buddha", "Spider" }

local function multiRaidActive()
    return RaidCfg.Multi and (RaidCfg.Pay ~= RaidCfg.Secondary)
end

local function abortMulti()
    return not multiRaidActive()
end

local function notify(title, content, duration)
    Fluent:Notify({ Title = title, Content = content, Duration = duration or 4 })
end

local function short(err)
    return tostring(err):sub(1, 80)
end

local function findNPCByName(name)
    local npcs = workspace:FindFirstChild("NPCs")
    return (npcs and npcs:FindFirstChild(name)) or workspace:FindFirstChild(name, true)
end

local function flyAndWait(cf, timeout, abort)
    flyTo(cf, 8, timeout, abort)
end

local function enemyNames()
    local names, seen = {}, {}
    for _, e in ipairs(workspace.Enemies:GetChildren()) do
        if not seen[e.Name] then
            seen[e.Name] = true
            table.insert(names, e.Name)
        end
    end
    return names
end

-- mata o alvo puxando os outros inimigos; devolve (morreu, travou)
local function killTarget(enemy, abort, stuck)
    local lastHealth, since = nil, os.clock()
    while farming and alive(enemy) and not (abort and abort()) do
        local hrp = root()
        local humanoid = enemy:FindFirstChildOfClass("Humanoid")
        if not hrp then break end
        equipWeapon()
        if humanoid.Health ~= lastHealth then
            lastHealth, since = humanoid.Health, os.clock()
        elseif stuck and os.clock() - since > stuck then
            attackFocus = nil
            return false, true
        end
        local hoverPos = enemy.HumanoidRootPart.Position + Vector3.new(0, HOVER_HEIGHT, 0)
        attackFocus = enemy
        if (hrp.Position - hoverPos).Magnitude > 50 then
            clearGroup()
            target = CFrame.new(hoverPos)
        else
            if BringGroup[1] ~= enemy or not BringPoint then buildBringGroup(enemy, enemyNames()) end
            target = CFrame.new(BringPoint + Vector3.new(0, HOVER_HEIGHT, 0))
        end
        click()
        task.wait(0.03)
    end
    attackFocus = nil
    return farming and not alive(enemy), false
end

local lastRaidWarn = {}
local function raidWarn(msg)
    Stats.RaidMsg = msg
    if os.clock() - (lastRaidWarn[msg] or -100) > 30 then
        lastRaidWarn[msg] = os.clock()
        notify("Raid", msg, 6)
    end
end

local RaidArmed = false
local RaidRespawnBlockUntil = 0
local RaidIgnore = setmetatable({}, { __mode = "k" })

Player.CharacterAdded:Connect(function()
    RaidRespawnBlockUntil = os.clock() + 25
    RaidArmed = false
end)

local RaidBtnPart, RaidBtnDet, RaidBtnTry = nil, nil, 0
local function findRaidButton()
    if RaidBtnPart and RaidBtnPart.Parent and RaidBtnDet and RaidBtnDet.Parent then
        return RaidBtnPart, RaidBtnDet
    end
    if os.clock() - RaidBtnTry < 5 then return nil end
    RaidBtnTry = os.clock()
    local ok, main = pcall(function() return workspace.Map["Boat Castle"].RaidSummon2.Button.Main end)
    if ok and main then
        local det = main:FindFirstChildOfClass("ClickDetector")
        if det then
            RaidBtnPart, RaidBtnDet = main, det
            return main, det
        end
    end
    local map = workspace:FindFirstChild("Map")
    if map then
        for _, d in ipairs(map:GetDescendants()) do
            if d:IsA("ClickDetector") and d.Parent:IsA("BasePart") and d:GetFullName():find("RaidSummon") then
                RaidBtnPart, RaidBtnDet = d.Parent, d
                return d.Parent, d
            end
        end
    end
end

local function presentTargets()
    local list = {}
    for _, name in ipairs(RaidCfg.Targets) do
        local p = Players:FindFirstChild(name)
        if p then table.insert(list, p) end
    end
    return list
end

local function atAnyGuestSlot(p)
    local r = p and p.Character and p.Character:FindFirstChild("HumanoidRootPart")
    if not r then return false end
    for _, cf in ipairs(RaidCfg.GuestCFrames) do
        local d = r.Position - cf.Position
        if Vector3.new(d.X, 0, d.Z).Magnitude <= RaidCfg.SlotRadius and math.abs(d.Y) <= 12 then
            return true
        end
    end
    return false
end

local function allSecondariesReady(list)
    for _, p in ipairs(list) do
        if not atAnyGuestSlot(p) then return false, p.Name end
    end
    return true
end

local function lobbyPos()
    return RaidCfg.HostCFrame.Position
end

local function raidGui()
    local ok, v = pcall(function()
        local hud = Player.PlayerGui.Main:FindFirstChild("TopHUDList")
        local rt = hud and hud:FindFirstChild("RaidTimer")
        return rt ~= nil and rt.Visible
    end)
    return ok and v == true
end

local function farFromLobby()
    local hrp = root()
    return hrp ~= nil and (hrp.Position - lobbyPos()).Magnitude > 600
end

local function inRaidNow()
    if os.clock() < RaidRespawnBlockUntil then return false end
    return raidGui() or (RaidArmed and farFromLobby())
end

-- ilha mais perto (até 2500 studs), parte da ilha seguinte e parte da ilha atual
local function raidIslandInfo()
    local hrp = root()
    if not hrp then return nil, nil, nil end
    local all, cur, curD = {}, nil, 2500
    pcall(function()
        for _, v in ipairs(workspace._WorldOrigin.Locations:GetChildren()) do
            local n = tonumber(v.Name:match("^Island%s*(%d+)"))
            if n and v:IsA("BasePart") then
                all[n] = v
                local d = (v.Position - hrp.Position).Magnitude
                if d < curD then cur, curD = n, d end
            end
        end
    end)
    if not cur then return nil, nil, nil end
    return cur, all[cur + 1], all[cur]
end

local function goToSpot(cf)
    local hrp = root()
    if not hrp then return false end
    local dist = (hrp.Position - cf.Position).Magnitude
    if dist > 4 then
        if dist > 12 then
            flyAndWait(CFrame.new(cf.Position + Vector3.new(0, 6, 0)), dist / Options.FlySpeed.Value + 15, abortMulti)
        end
        hrp = root()
        if hrp and multiRaidActive() then
            target = cf
            hrp.CFrame = cf
        end
    end
    return true
end

-- ilhas 4 e 5: fica parado no meio da ilha enquanto a Kill Aura mata tudo
local auraIdleSince, auraEnemySince = nil, nil
local function raidAuraStand(hrp)
    if not RaidCfg.KillAura then auraIdleSince, auraEnemySince = nil, nil return false end
    local cur, nxt, curPart = raidIslandInfo()
    if not cur or cur < 4 or not curPart then auraIdleSince, auraEnemySince = nil, nil return false end
    local center = curPart.Position
    local hasEnemy = false
    for _, e in ipairs(workspace.Enemies:GetChildren()) do
        if alive(e) and (e.HumanoidRootPart.Position - center).Magnitude <= 600 then
            hasEnemy = true
            break
        end
    end
    if hasEnemy then
        auraIdleSince = nil
        auraEnemySince = auraEnemySince or os.clock()
        if os.clock() - auraEnemySince > 8 then return false end
    else
        auraEnemySince = nil
        auraIdleSince = auraIdleSince or os.clock()
    end
    local goalPart = curPart
    if not hasEnemy and nxt and os.clock() - auraIdleSince >= 1 then goalPart = nxt end
    attackFocus = nil
    clearGroup()
    target = CFrame.new(goalPart.Position + Vector3.new(0, 50, 0))
    return true
end

local function raidFight(graceSeconds)
    local startChar = Player.Character
    local auraAtStart = Stats.RaidAuraKills
    local graceUntil = os.clock() + (graceSeconds or 0)
    local lastInside = os.clock()
    local idleSince, sawEnemy = nil, false
    while farming and multiRaidActive() do
        local char = Player.Character
        local humanoid = char and char:FindFirstChildOfClass("Humanoid")
        if char ~= startChar or not humanoid or humanoid.Health <= 0 then
            raidWarn("personagem morreu durante o raid (volta ao slot)")
            break
        end

        if raidGui() or farFromLobby() then
            lastInside = os.clock()
            graceUntil = 0
            local hrp = root()
            if not hrp then
                task.wait(0.2)
            elseif raidAuraStand(hrp) then
                idleSince = nil
                task.wait(0.2)
            else
                local best, bestD = nil, 2500
                for _, e in ipairs(workspace.Enemies:GetChildren()) do
                    local ign = RaidIgnore[e]
                    if alive(e) and not (ign and ign > os.clock()) then
                        local d = (e.HumanoidRootPart.Position - hrp.Position).Magnitude
                        if d < bestD then best, bestD = e, d end
                    end
                end
                if best then
                    idleSince, sawEnemy = nil, true
                    local _, stuck = killTarget(best, abortMulti, 8)
                    if stuck then
                        RaidIgnore[best] = os.clock() + 10
                        clearGroup()
                    end
                else
                    idleSince = idleSince or os.clock()
                    local idleTime = os.clock() - idleSince
                    if idleTime < 1 then
                        task.wait(0.2)
                    else
                        local _, nxt, curPart = raidIslandInfo()
                        local goal = nxt or curPart
                        if goal then
                            target = CFrame.new(goal.Position + Vector3.new(0, 50, 0))
                        end
                        if idleTime > 600 then
                            Stats.RaidMsg = "raid sem inimigos nem ilhas ha 10 min"
                            break
                        end
                        task.wait(0.3)
                    end
                end
            end
        else
            if os.clock() < graceUntil then
                task.wait(0.3)
            elseif os.clock() - lastInside > 6 then
                break
            else
                task.wait(0.3)
            end
        end
    end
    attackFocus = nil
    clearGroup()
    RaidArmed = false
    if (sawEnemy or Stats.RaidAuraKills > auraAtStart) and multiRaidActive() then
        Stats.RaidsMulti += 1
        notify("Raid", "Raid concluido!", 5)
    end
end

-- Kill Aura do raid: mata na hora os inimigos a até 600 studs do centro das ilhas 4 e 5
local function raidKillAuraStep()
    local cur, _, curPart = raidIslandInfo()
    if not cur or cur < 4 or not curPart then return end
    pcall(sethiddenproperty, Player, "SimulationRadius", math.huge)
    local center = curPart.Position
    for _, e in ipairs(workspace.Enemies:GetChildren()) do
        if alive(e) and (e.HumanoidRootPart.Position - center).Magnitude <= 600 then
            local humanoid = e:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.Health = 0
                local head = e:FindFirstChild("Head")
                if head then head:Destroy() end
                Stats.RaidAuraKills += 1
            end
        end
    end
end

task.spawn(function()
    while true do
        task.wait(0.1)
        if RaidCfg.KillAura and farming and multiRaidActive() and (raidGui() or farFromLobby()) then
            pcall(raidKillAuraStep)
        end
    end
end)

local function hasChip()
    for _, c in ipairs({ Player.Backpack, Player.Character }) do
        if c and c:FindFirstChild("Special Microchip") then return true end
    end
    return false
end

local RaidPayFruit = nil
local lastFruitNotify = -100

local function otherFruitTools()
    local list = {}
    for _, c in ipairs({ Player.Backpack, Player.Character }) do
        if c then
            for _, t in ipairs(c:GetChildren()) do
                if t:IsA("Tool") and t.Name:find("Fruit") and t:GetAttribute("OriginalName") ~= RaidPayFruit then
                    table.insert(list, t)
                end
            end
        end
    end
    return list
end

local function anyFruitTool()
    for _, c in ipairs({ Player.Backpack, Player.Character }) do
        if c then
            for _, t in ipairs(c:GetChildren()) do
                if t:IsA("Tool") and t.Name:find("Fruit") then return true end
            end
        end
    end
    return false
end

local FruitPrices = {
    rocket = 5000, spin = 7500, blade = 30000, spring = 60000, bomb = 80000, smoke = 100000,
    spike = 180000, flame = 250000, ice = 350000, sand = 420000, dark = 500000, eagle = 550000,
    diamond = 600000, light = 650000, rubber = 750000, ghost = 940000, magma = 960000,
    quake = 1000000, buddha = 1200000, love = 1300000, creation = 1400000, spider = 1500000,
    sound = 1700000, phoenix = 1800000, portal = 1900000, rumble = 2100000, pain = 2300000,
    blizzard = 2400000, gravity = 2500000, mammoth = 2700000, trex = 2700000, dough = 2800000,
    shadow = 2900000, venom = 3000000, control = 3200000, spirit = 3400000, dragon = 3500000,
    leopard = 5000000, kitsune = 8000000,
}

local function fruitPriceByName(name)
    local low = tostring(name):lower():gsub("[%s%-_]", "")
    local bestKey
    for k in pairs(FruitPrices) do
        if low:find(k, 1, true) and (not bestKey or #k > #bestKey) then bestKey = k end
    end
    return bestKey and FruitPrices[bestKey] or nil
end

local function firstFruitTool()
    for _, c in ipairs({ Player.Backpack, Player.Character }) do
        if c then
            for _, t in ipairs(c:GetChildren()) do
                if t:IsA("Tool") and t.Name:find("Fruit") then return t end
            end
        end
    end
end

-- frutas guardadas, da mais barata para a mais cara
local function readStoredFruits()
    local list, seen = {}, {}
    local function add(name, price)
        if type(name) ~= "string" or name == "" or seen[name] then return end
        price = tonumber(price) or fruitPriceByName(name)
        if not price then return end
        seen[name] = true
        table.insert(list, { name = name, price = price })
    end

    local ok, inv = pcall(function() return CommF:InvokeServer("getInventoryFruits") end)
    if ok and type(inv) == "table" then
        for k, v in pairs(inv) do
            if type(v) == "table" then
                add(v.Name or v.name or v.FruitName or (type(k) == "string" and k or nil),
                    v.Price or v.price or v.Value)
            elseif type(v) == "string" then
                add(v, nil)
            elseif type(k) == "string" then
                add(k, nil)
            end
        end
    end

    if #list == 0 then
        pcall(function()
            local rep = require(ReplicatedStorage:WaitForChild("ItemReplicationService", 3))
            local cfg = require(ReplicatedStorage:WaitForChild("ItemConfig", 3))
            local cache = rep._UserCache[Player.UserId]
            for _, v in pairs(cache:GetItems("Quantity") or {}) do
                local ok2, c = pcall(function() return cfg.match(v.ItemId):unwrap() end)
                if ok2 and c and c.Index and c.Index.IdType == "PhysicalMoveset" then
                    local label = tostring(c.Index.DebugLabel or ""):gsub(" %[.-%]", "")
                    add(label, nil)
                end
            end
        end)
    end

    table.sort(list, function(x, y) return x.price < y.price end)
    return list
end

local function nameVariants(name)
    local out = { name }
    local base = name:gsub(" Fruit$", "")
    local b2 = base:match("^([^%-]+)%-") or base
    for _, v in ipairs({ b2 .. "-" .. b2, b2 .. " Fruit", base }) do
        if not table.find(out, v) then table.insert(out, v) end
    end
    return out
end

local function unstoreCheapFruit()
    local fruits = readStoredFruits()
    if #fruits == 0 then return false, "nenhuma fruta guardada lida do inventario" end
    local tried = 0
    for _, f in ipairs(fruits) do
        if f.price <= RaidCfg.MaxFruitPrice then
            tried += 1
            for _, nm in ipairs(nameVariants(f.name)) do
                pcall(function() CommF:InvokeServer("LoadFruit", nm) end)
                local t0 = os.clock()
                while os.clock() - t0 < 2 do
                    local tool = firstFruitTool()
                    if tool then
                        RaidPayFruit = tool:GetAttribute("OriginalName")
                        return true, f.name .. " (" .. f.price .. ")"
                    end
                    task.wait(0.2)
                end
            end
            if tried >= 3 then break end
        end
    end
    if tried == 0 then
        return false, "nenhuma fruta guardada ate o limite (a mais barata custa " .. fruits[1].price .. ")"
    end
    return false, "nao consegui desarmazenar (tentei " .. tried .. " fruta(s))"
end

local function buyChip()
    Stats.PauseStore = true
    local ok, err = pcall(function()
        local npc = findNPCByName("Mysterious Scientist")
        if npc then
            local okp, pivot = pcall(function() return npc:GetPivot() end)
            if okp then
                flyAndWait(CFrame.new(pivot.Position + Vector3.new(0, 3, 0)), 60, abortMulti)
            end
        end
        if not multiRaidActive() then return end

        for _, t in ipairs(otherFruitTools()) do
            pcall(function() CommF:InvokeServer("StoreFruit", t:GetAttribute("OriginalName") or t.Name, t) end)
        end
        task.wait(0.5)
        if #otherFruitTools() > 0 then
            raidWarn("guarde as frutas da mochila antes (uma fruta cara poderia ser usada no chip)")
            return
        end

        if not anyFruitTool() then
            local okU, info = unstoreCheapFruit()
            Stats.RaidMsg = okU and ("pagando com: " .. info) or info
            if not okU and os.clock() - lastFruitNotify > 30 then
                lastFruitNotify = os.clock()
                notify("Raid", "Fruta para o chip: " .. info, 8)
            end
            task.wait(0.6)
        end

        for _ = 1, 3 do
            if hasChip() or not multiRaidActive() then break end
            pcall(function() CommF:InvokeServer("RaidsNpc", "Select", RaidCfg.Chip) end)
            task.wait(1)
        end
        if hasChip() then RaidPayFruit = nil end
    end)
    Stats.PauseStore = false
    if not ok then Stats.RaidMsg = "erro: " .. short(err) end
end

local function pressRaidButton()
    local _, det = findRaidButton()
    if not det then
        raidWarn("botao do raid nao encontrado")
        return false
    end
    if not fireclickdetector then
        raidWarn("este executor nao tem fireclickdetector")
        return false
    end
    for _ = 1, 3 do
        local hadChip = hasChip()
        pcall(fireclickdetector, det)
        local t0 = os.clock()
        while os.clock() - t0 < 20 and multiRaidActive() do
            task.wait(0.3)
            if raidGui() or farFromLobby() then return true end
            if hadChip and not hasChip() then return true end
        end
    end
    return false
end

local function runRaidPay()
    if #RaidCfg.Targets == 0 then
        raidWarn("nenhuma conta secundaria selecionada na aba Raid")
        task.wait(1)
        return
    end
    local targets = presentTargets()
    if #targets == 0 then
        raidWarn("as contas selecionadas nao estao no servidor")
        task.wait(1)
        return
    end
    if inRaidNow() then
        raidFight(0)
        return
    end
    if not hasChip() then
        buyChip()
        if not hasChip() then task.wait(2) return end
    end
    if not goToSpot(RaidCfg.HostCFrame) then task.wait(1) return end
    RaidArmed = true
    local waitStart, warned = os.clock(), false
    while multiRaidActive() do
        targets = presentTargets()
        if #targets == 0 then return end
        if inRaidNow() then raidFight(0) return end
        local ready, missing = allSecondariesReady(targets)
        if ready then break end
        Stats.RaidMsg = "esperando " .. tostring(missing) .. " ir para um slot"
        if not warned and os.clock() - waitStart > 30 then
            warned = true
            notify("Raid", "Esperando " .. tostring(missing) .. " ir para um slot", 6)
        end
        goToSpot(RaidCfg.HostCFrame)
        task.wait(0.5)
    end
    if not multiRaidActive() then return end
    task.wait(1)
    if pressRaidButton() then
        raidFight(25)
    else
        RaidArmed = false
        task.wait(2)
    end
end

local function runRaidSecondary()
    if inRaidNow() then
        raidFight(0)
        return
    end
    local slot = math.clamp(RaidCfg.GuestSlot, 1, #RaidCfg.GuestCFrames)
    if goToSpot(RaidCfg.GuestCFrames[slot]) then
        RaidArmed = true
    else
        task.wait(1)
    end
    task.wait(0.5)
end

local function runMultiRaid()
    if RaidCfg.Pay then runRaidPay() else runRaidSecondary() end
end

RunService.Heartbeat:Connect(function(dt)
    local hrp = root()
    if not ((farming or traveling) and target and hrp) then return end
    local delta = target.Position - hrp.Position
    local distance = Options.FlySpeed.Value * dt
    if delta.Magnitude <= distance then
        hrp.CFrame = target
    else
        hrp.CFrame = CFrame.new(hrp.Position + delta.Unit * distance) * target.Rotation
    end
    hrp.AssemblyLinearVelocity = Vector3.zero
end)

RunService.Heartbeat:Connect(function()
    if not farming then return end
    if os.clock() - lastSim > 1 then
        lastSim = os.clock()
        pcall(sethiddenproperty, Player, "SimulationRadius", math.huge)
    end
    if not BringPoint then return end
    for i = #BringGroup, 1, -1 do
        local m = BringGroup[i]
        if alive(m) then
            local hrp = m.HumanoidRootPart
            if not hrp:FindFirstChild("BatmanLock") then
                for _, part in ipairs(m:GetDescendants()) do
                    if part:IsA("BasePart") then part.CanCollide = false end
                end
                local lock = Instance.new("BodyVelocity")
                lock.Name = "BatmanLock"
                lock.Velocity = Vector3.zero
                lock.MaxForce = Vector3.new(1e5, 1e5, 1e5)
                lock.Parent = hrp
            end
            hrp.CFrame = CFrame.new(BringPoint)
            hrp.AssemblyLinearVelocity = Vector3.zero
            hrp.CanCollide = false
            local hum = m:FindFirstChildOfClass("Humanoid")
            if hum then
                hum.WalkSpeed = 0
                hum.JumpPower = 0
            end
        else
            table.remove(BringGroup, i)
        end
    end
    if #BringGroup == 0 then BringPoint = nil end
end)

RunService.Stepped:Connect(function()
    if not ((farming or traveling) and target and Player.Character) then return end
    for _, part in ipairs(Player.Character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
end)

task.spawn(function()
    while true do
        task.wait(0.01)
        if farming and not traveling and attackFocus then
            local ok, err = pcall(fastAttack)
            if not ok then warn("[Batman Hub] fastAttack: " .. tostring(err)) end
        end
    end
end)

local function idle()
    clearGroup()
    attackFocus = nil
    target = nil
    hover(false)
    lastMode = "idle"
    Stats.State = "Idle"
end

local Tyrant = {
    vases = {
        Vector3.new(-16230.123046875, 153.8718719482422, 1449.07373046875),
        Vector3.new(-16309.607421875, 153.9078826904297, 1452.01708984375),
        Vector3.new(-16313.8388671875, 153.9078826904297, 1339.071533203125),
        Vector3.new(-16234.29296875, 153.79917907714844, 1336.2052001953125),
    },
    lastWarn = 0,
    needReset = false,
    hooked = false,
    aimPos = nil,
    aimPart = nil,
}

function Tyrant.eyes()
    local map = workspace:FindFirstChild("Map")
    local tiki = map and map:FindFirstChild("TikiOutpost")
    local island = tiki and tiki:FindFirstChild("IslandModel")
    if not island then return 0 end
    local count = 0
    for i = 1, 4 do
        local eye = island:FindFirstChild("Eye" .. i, true)
        if eye and eye:IsA("BasePart") and eye.Transparency == 0 then
            count += 1
        end
    end
    return count
end

function Tyrant.equipGuitar()
    local char = Player.Character
    local humanoid = char and char:FindFirstChildOfClass("Humanoid")
    if not humanoid then return false end
    local held = char:FindFirstChildOfClass("Tool")
    if held and held.Name == "Skull Guitar" then return true end
    local tool = Player.Backpack:FindFirstChild("Skull Guitar")
    if not tool then return false end
    humanoid:EquipTool(tool)
    task.wait(0.6)
    return true
end

-- silent aim: enquanto aimPos estiver definido, os scripts do jogo veem o mouse apontando para ele
function Tyrant.installHook()
    if Tyrant.hooked or not hookmetamethod then return end
    Tyrant.hooked = true
    local mouse = Player:GetMouse()
    local wrap = newcclosure or function(f) return f end
    local old
    local ok = pcall(function()
        old = hookmetamethod(game, "__index", wrap(function(self, key)
            if Tyrant.aimPos and self == mouse and not (checkcaller and checkcaller()) then
                if key == "Hit" then return CFrame.new(Tyrant.aimPos) end
                if key == "Target" then return Tyrant.aimPart end
            end
            return old(self, key)
        end))
    end)
    if not ok then Tyrant.hooked = false end
end

function Tyrant.shootDown(pos)
    local char = Player.Character
    local tool = char and char:FindFirstChildOfClass("Tool")
    if not tool then return end

    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = { char, workspace.Enemies }
    local hit = workspace:Raycast(pos, Vector3.new(0, -300, 0), params)
    local aimPos = hit and hit.Position or (pos - Vector3.new(0, 20, 0))
    local aimPart = hit and hit.Instance

    Tyrant.installHook()
    Tyrant.aimPos, Tyrant.aimPart = aimPos, aimPart

    local camera = workspace.CurrentCamera
    RunService:BindToRenderStep("BatmanAimDown", Enum.RenderPriority.Camera.Value + 1, function()
        local _, yaw = camera.CFrame:ToOrientation()
        camera.CFrame = CFrame.new(camera.CFrame.Position) * CFrame.fromOrientation(-math.rad(89), yaw, 0)
    end)
    task.wait(0.2)

    local shootEvent = Net:FindFirstChild("RE/ShootGunEvent")
    local shootFunction = tool:FindFirstChild("RemoteFunctionShoot")
    for _ = 1, 3 do
        pcall(click)
        if mouse1click then pcall(mouse1click) end
        if shootEvent then
            pcall(shootEvent.FireServer, shootEvent, aimPos, { aimPart })
        end
        if shootFunction then
            task.spawn(pcall, shootFunction.InvokeServer, shootFunction, aimPos, aimPart)
        end
        task.wait(0.3)
    end

    RunService:UnbindFromRenderStep("BatmanAimDown")
    Tyrant.aimPos, Tyrant.aimPart = nil, nil
end

function Tyrant.run(method)
    local function abort()
        return not farming or findEnemy(method.bosses, true) ~= nil
    end
    clearGroup()
    attackFocus = nil
    for _, pos in ipairs(Tyrant.vases) do
        if abort() then return end
        flyTo(CFrame.new(pos), 8, 60, abort)
        if abort() then return end
        if not Tyrant.equipGuitar() then
            if os.clock() - Tyrant.lastWarn > 30 then
                Tyrant.lastWarn = os.clock()
                Fluent:Notify({ Title = "Batman Hub", Content = "Skull Guitar nao encontrada", Duration = 5 })
            end
            task.wait(1)
            return
        end
        Tyrant.shootDown(pos)
        local t0 = os.clock()
        while os.clock() - t0 < 3 and not abort() do
            task.wait(0.2)
        end
    end
end

local function step()
    if traveling then return end

    local method = METHODS[Options.MethodFarm.Value]
    local questMob = pickMob(method)
    local names = method.cake and CAKE_MOBS or allNames(method, questMob)
    questMobName = questMob.name

    local function prep()
        hover(true)
        enableHaki()
        equipWeapon()
    end

    if multiRaidActive() then
        prep()
        lastMode = "multiraid"
        Stats.State = "Multi Raid"
        runMultiRaid()
        return
    end

    local stackBoss, stackInWorld
    if Options.AttackDough.Value then
        stackBoss, stackInWorld = findBossAny(DOUGH_BOSS)
    end
    if not stackBoss and Options.AttackRip.Value then
        stackBoss, stackInWorld = findBossAny(RIP_BOSS)
    end
    if stackBoss then
        prep()
        lastMode = "stack"
        Stats.State = table.find(DOUGH_BOSS, stackBoss.Name) and "Dough King" or "Rip Indra"
        handleStackBoss(stackBoss, stackInWorld)
        return
    end

    if Options.CastleRaid.Value and raidActive() then
        prep()
        lastMode = "raid"
        Stats.State = "Pirate Raid"
        handleRaid()
        return
    end

    if Options.CollectFruits.Value then
        local fruit = nearestFruit()
        if fruit then
            hover(true)
            lastMode = "fruit"
            Stats.State = "Collecting Fruit"
            handleFruit(fruit)
            return
        end
    end

    if Options.EliteHunter.Value then
        local eBoss, inWorld = findBossAny(ELITE_BOSSES)
        if eBoss then
            prep()
            lastMode = "elite"
            Stats.State = "Elite Hunter"
            handleElite(eBoss, inWorld)
            return
        end
    end

    if not Options.Farm.Value or (method.cake and not InSea3) then
        idle()
        return
    end

    if Options.MethodFarm.Value == "Tyrant Of The Skies" then
        if findEnemy(method.bosses, true) then
            Tyrant.needReset = true
        elseif Tyrant.eyes() < 4 then
            Tyrant.needReset = false
        elseif not Tyrant.needReset then
            hover(true)
            enableHaki()
            Stats.State = "Tyrant Vases"
            Tyrant.run(method)
            return
        end
    end

    prep()

    if lastMode ~= "farm" then
        lastMode = "farm"
        questId = nil
        Kat.needCheck, Kat.remaining = true, nil
        clearGroup()
        attackFocus = nil
    end

    Stats.State = "Farming " .. Options.MethodFarm.Value

    if method.cake then
        if katakuri() then return end
    else
        local boss = findEnemy(method.bosses, true)
        if boss then
            Stats.State = Options.MethodFarm.Value .. " Boss"
            clearGroup()
            attackFocus = boss
            target = CFrame.new(boss.HumanoidRootPart.Position + Vector3.new(0, HOVER_HEIGHT, 0))
            click()
            return
        end
    end

    if Options.GetQuest.Value then
        local id = questMob.quest .. questMob.idx
        if questId ~= id or questDone() then
            clearGroup()
            attackFocus = nil
            if questVisible() then commF("AbandonQuest") end
            flyTo((questMob.giver or method.giver) + Vector3.new(0, 3, 0), 8)
            if not farming then return end
            commF("StartQuest", questMob.quest, questMob.idx)
            questId, kills, needed, questSeen, hiddenSince = id, 0, questNeeded(questMob), false, nil
            task.wait(0.5)
        end
    end

    watchdog()

    local lead = BringGroup[1]
    if BringPoint and lead and alive(lead) then
        attackFocus = lead
        target = CFrame.new(BringPoint + Vector3.new(0, HOVER_HEIGHT, 0))
        click()
        return
    end

    local enemy = (Options.GetQuest.Value and findEnemy({ questMob.name })) or findEnemy(names)
    if enemy then
        local hrp = root()
        local hoverPos = enemy.HumanoidRootPart.Position + Vector3.new(0, HOVER_HEIGHT, 0)
        attackFocus = enemy
        target = CFrame.new(hoverPos)

        if hrp and (hrp.Position - hoverPos).Magnitude > 50 then
            click()
            return
        end

        buildBringGroup(enemy, names)
        target = CFrame.new(BringPoint + Vector3.new(0, HOVER_HEIGHT, 0))
        click()
        return
    end

    clearGroup()
    attackFocus = nil
    local unloaded = findUnloaded(names)
    local pos = unloaded and unloaded.HumanoidRootPart.Position or findSpawn(names, method.cake and MOB_AREAS)
    if pos then
        target = CFrame.new(pos + Vector3.new(0, 25, 0))
    end
end

local function farmLoop()
    while farming do
        local ok, err = pcall(step)
        if not ok then warn(err) end
        task.wait(0.1)
    end
    if not traveling then
        target = nil
        hover(false)
    end
    attackFocus = nil
    clearGroup()
    table.clear(bugSpots)
    lastMode = "farm"
end

local function startTraveling()
    local dest = ISLANDS[Options.TravelIsland.Value]
    if not dest then return end

    travelId += 1
    local id = travelId
    traveling = true
    clearGroup()
    attackFocus = nil

    task.spawn(function()
        hover(true)
        target = CFrame.new(dest)
        while traveling and travelId == id and root() and (root().Position - dest).Magnitude > 10 do
            task.wait()
        end
        if travelId == id then
            traveling = false
            target = nil
            if not farming then hover(false) end
            Fluent:Notify({
                Title = "Batman Hub",
                Content = "Travel: " .. tostring(Options.TravelIsland.Value) .. " concluído",
                Duration = 3,
            })
        end
    end)
end

Main:AddDropdown("MethodFarm", {
    Title = "Method Farm",
    Values = { "Katakuri", "Bones", "Tyrant Of The Skies" },
    Multi = false,
    Default = 1,
})

Main:AddToggle("Farm", { Title = "Farm", Default = false })
Main:AddToggle("GetQuest", { Title = "Get Quest", Default = false })
Main:AddToggle("EliteHunter", { Title = "Auto Elite Hunter", Default = false })
Main:AddToggle("CastleRaid", { Title = "Auto Castle Raid", Default = false })
Main:AddToggle("CollectFruits", { Title = "Auto Collect Fruits", Default = false })
Main:AddToggle("StoreFruits", { Title = "Auto Store Fruits", Default = false })

Stack:AddToggle("AttackRip", { Title = "Auto Attack Rip Indra", Default = false })
Stack:AddToggle("AttackDough", { Title = "Auto Attack Dough King", Default = false })

do
    local stateLabel = StatusTab:AddParagraph({ Title = "State:", Content = "Idle" })
    local katakuriLabel = StatusTab:AddParagraph({ Title = "Katakuri:", Content = "-" })
    local tyrantLabel = StatusTab:AddParagraph({ Title = "Tyrant Of The Skies:", Content = "0/4" })

    local function setText(label, text)
        pcall(function() label:SetDesc(text) end)
    end

    task.spawn(function()
        while true do
            task.wait(0.5)
            setText(stateLabel, traveling and "Traveling" or (farming and Stats.State or "Idle"))

            local okK, katText = pcall(function()
                if findBoss() then return "Spawned" end
                return Kat.remaining and tostring(Kat.remaining) or "-"
            end)
            setText(katakuriLabel, okK and katText or "-")

            local okT, tyrText = pcall(function()
                if findEnemy(METHODS["Tyrant Of The Skies"].bosses, true) then return "Spawned" end
                return Tyrant.eyes() .. "/4"
            end)
            setText(tyrantLabel, okT and tyrText or "0/4")
        end
    end)
end

LocalTab:AddToggle("AutoV3", { Title = "Auto Use V3", Default = false })
LocalTab:AddToggle("AutoV4", { Title = "Auto Use V4", Default = false })

do
    local VIM = game:GetService("VirtualInputManager")
    local remotes = ReplicatedStorage:FindFirstChild("Remotes")
    local commE = remotes and remotes:FindFirstChild("CommE")
    local lastV3 = 0
    task.spawn(function()
        while true do
            task.wait(1)
            local char = Player.Character
            if char then
                if Options.AutoV3.Value and commE and os.clock() - lastV3 > 3 then
                    lastV3 = os.clock()
                    pcall(commE.FireServer, commE, "ActivateAbility")
                end
                if Options.AutoV4.Value then
                    local energy = char:FindFirstChild("RaceEnergy")
                    local transformed = char:FindFirstChild("RaceTransformed")
                    if energy and energy.Value >= 1 and not (transformed and transformed.Value) then
                        pcall(function()
                            VIM:SendKeyEvent(true, Enum.KeyCode.Y, false, game)
                            task.wait(0.1)
                            VIM:SendKeyEvent(false, Enum.KeyCode.Y, false, game)
                        end)
                    end
                end
            end
        end
    end)
end

Travel:AddDropdown("TravelIsland", {
    Title = "Island",
    Values = ISLAND_NAMES,
    Multi = false,
    Default = 1,
})

Travel:AddButton({
    Title = "Start Traveling",
    Callback = startTraveling,
})

local fogHooked = false

local function clearFog()
    Lighting.FogStart = 1e6
    Lighting.FogEnd = 1e6
    for _, v in ipairs(Lighting:GetDescendants()) do
        if v:IsA("Atmosphere") then
            v.Density = 0
            v.Haze = 0
        end
    end
end

Misc:AddButton({
    Title = "Remove Fog",
    Callback = function()
        clearFog()
        if fogHooked then return end
        fogHooked = true
        Lighting:GetPropertyChangedSignal("FogEnd"):Connect(function()
            if Lighting.FogEnd < 1e6 then clearFog() end
        end)
        Lighting.DescendantAdded:Connect(function(d)
            if d:IsA("Atmosphere") then
                d.Density = 0
                d.Haze = 0
            end
        end)
    end,
})

Settings:AddSlider("FlySpeed", {
    Title = "Fly Speed",
    Default = 175,
    Min = 100,
    Max = 350,
    Rounding = 0,
})

Settings:AddDropdown("SelectWeapon", {
    Title = "Select Weapon",
    Values = { "Melee", "Sword", "Fruit(M1)" },
    Multi = false,
    Default = 1,
})

Settings:AddDropdown("BringCount", {
    Title = "Mobs In Bring Mob",
    Values = { "1", "2", "3", "4", "5" },
    Multi = false,
    Default = 2,
})

local function refresh()
    local want = Options.Farm.Value or Options.EliteHunter.Value or Options.CastleRaid.Value or Options.CollectFruits.Value
        or Options.AttackRip.Value or Options.AttackDough.Value or multiRaidActive()
    if want and not farming then
        farming = true
        task.spawn(farmLoop)
    elseif not want then
        farming = false
    end
end

Options.Farm:OnChanged(refresh)
Options.EliteHunter:OnChanged(refresh)
Options.CastleRaid:OnChanged(refresh)
Options.CollectFruits:OnChanged(refresh)
Options.AttackRip:OnChanged(refresh)
Options.AttackDough:OnChanged(refresh)

local function playerNames()
    local list = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= Player then table.insert(list, p.Name) end
    end
    table.sort(list)
    return list
end

RaidTab:AddDropdown("RaidChip", {
    Title = "Chip da Raid",
    Values = RaidTypes,
    Multi = false,
    Default = 2,
}):OnChanged(function(value)
    RaidCfg.Chip = value
end)

local RaidDropdown = RaidTab:AddDropdown("RaidPlayers", {
    Title = "Contas Secundarias",
    Values = playerNames(),
    Multi = true,
    Default = {},
})
RaidDropdown:OnChanged(function(value)
    local list = {}
    if type(value) == "table" then
        for name, on in pairs(value) do
            if on then table.insert(list, name) end
        end
    end
    table.sort(list)
    RaidCfg.Targets = list
end)

RaidTab:AddButton({
    Title = "Atualizar Lista",
    Callback = function()
        local names = playerNames()
        pcall(function() RaidDropdown:SetValues(names) end)
        local keep = {}
        for _, n in ipairs(RaidCfg.Targets) do
            if table.find(names, n) then keep[n] = true end
        end
        pcall(function() RaidDropdown:SetValue(keep) end)
        notify("Raid", #names .. " jogador(es) na lista", 3)
    end,
})

RaidTab:AddToggle("MultiRaid", {
    Title = "Multi Raid",
    Default = false,
}):OnChanged(function(value)
    RaidCfg.Multi = value
    RaidArmed = false
    refresh()
end)

RaidTab:AddToggle("RaidPay", {
    Title = "Pagar Raid",
    Default = false,
}):OnChanged(function(value)
    RaidCfg.Pay = value
    RaidArmed = false
    if value then
        pcall(function() Options.RaidSecondary:SetValue(false) end)
        pcall(function() Options.MultiRaid:SetValue(true) end)
    end
    refresh()
end)

RaidTab:AddToggle("RaidSecondary", {
    Title = "Conta Secundaria",
    Default = false,
}):OnChanged(function(value)
    RaidCfg.Secondary = value
    RaidArmed = false
    if value then
        pcall(function() Options.RaidPay:SetValue(false) end)
        pcall(function() Options.MultiRaid:SetValue(true) end)
    end
    refresh()
end)

RaidTab:AddDropdown("RaidGuestSlot", {
    Title = "Slot da Conta Secundaria",
    Values = { "1", "2", "3" },
    Multi = false,
    Default = 1,
}):OnChanged(function(value)
    RaidCfg.GuestSlot = tonumber(value) or 1
end)

Player.Idled:Connect(function()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new())
end)

local Gui = Instance.new("ScreenGui")
Gui.Name = "BatmanHubButton"
Gui.ResetOnSpawn = false
Gui.Parent = (gethui and gethui()) or game:GetService("CoreGui")

local Button = Instance.new("TextButton")
Button.Size = UDim2.fromOffset(46, 46)
Button.Position = UDim2.new(0, 12, 0.5, -23)
Button.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Button.Text = "B"
Button.TextColor3 = Color3.fromRGB(255, 255, 255)
Button.TextSize = 22
Button.Font = Enum.Font.GothamBold
Button.Active = true
Button.Draggable = true
Button.Parent = Gui

Instance.new("UICorner", Button).CornerRadius = UDim.new(1, 0)

Button.MouseButton1Click:Connect(function()
    Window:Minimize()
end)

Window:SelectTab(1)
