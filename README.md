local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualUser = game:GetService("VirtualUser")
local Lighting = game:GetService("Lighting")
local Workspace = game:GetService("Workspace")

local Player = Players.LocalPlayer
local K = {}
K.SEA2_IDS = { [79091703265657] = true, [4442272183] = true }
local InSea2 = K.SEA2_IDS[game.PlaceId] == true

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
local StatusTab = not InSea2 and Window:AddTab({ Title = "Status", Icon = "activity" }) or nil
local RaidTab = not InSea2 and Window:AddTab({ Title = "Raid", Icon = "users" }) or nil
local LocalTab = Window:AddTab({ Title = "Local Player", Icon = "user" })
local Travel = Window:AddTab({ Title = "Travel", Icon = "plane" })
local Misc = Window:AddTab({ Title = "Misc", Icon = "wrench" })
local Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })

K.CAKE_MOBS = { "Cookie Crafter", "Cake Guard", "Baking Staff", "Head Baker" }
K.BOSS_NAMES = { "Cake Prince", "Katakuri" }
K.MOB_AREAS = {
    CFrame.new(-2374, 37, -12125),
    CFrame.new(-1598, 43, -12244),
    CFrame.new(-1887, 77, -12900),
    CFrame.new(-2216, 52, -12853),
}
K.CAKE_ENTRANCE = CFrame.new(-2124, 69, -12401)
K.CAKE_TOTAL = 500
K.SEA3_IDS = { [7449423635] = true, [100117331123089] = true }

K.METHODS = {
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

K.METHODS["Cocoa"] = {
    bosses = {},
    noQuest = true,
    giver = CFrame.new(219, 126, -12604),
    area = { CFrame.new(219, 126, -12604) },
    mobs = {
        { name = "Chocolate Bar Battler", lvl = 2300, quest = "ChocQuest1", idx = 1 },
        { name = "Cocoa Warrior", lvl = 2325, quest = "ChocQuest1", idx = 2 },
    },
}
K.COCOA_NEEDED = 10

-- Sea 2: navio mal assombrado (Ectoplasma), fabrica (Core) e Darkbeard
K.SHIP_POS = Vector3.new(923.21252441406, 126.9760055542, 32852.83203125)
K.METHODS["Ectoplasm"] = {
    sea2 = true,
    ship = true,
    bosses = {},
    giver = CFrame.new(1037.80127, 125.092171, 32911.6016),
    area = {
        CFrame.new(1212.0111, 150.7920, 33059.2461),
        CFrame.new(919.4786, 43.5440, 32779.9687),
        CFrame.new(919.4786, 129.5560, 33436.0352),
    },
    mobs = {
        { name = "Ship Deckhand", lvl = 1250, quest = "ShipQuest1", idx = 1 },
        { name = "Ship Engineer", lvl = 1275, quest = "ShipQuest1", idx = 2 },
        { name = "Ship Steward", lvl = 1300, quest = "ShipQuest2", idx = 1, giver = CFrame.new(968.80957, 125.092171, 33244.125) },
        { name = "Ship Officer", lvl = 1325, quest = "ShipQuest2", idx = 2, giver = CFrame.new(968.80957, 125.092171, 33244.125) },
    },
}
K.FACTORY_BOSS = { "Core" }
K.FACTORY_CF = CFrame.new(448.46756, 199.356781, -441.389252)
K.DARKBEARD = { "Darkbeard" }
K.CURSED_CAPTAIN = { "Cursed Captain" }
K.GHOUL_FILE = "BatmanHub_Ghoul.json"
K.GHOUL_ECTO = 100
K.GHOUL_LEVEL = 1000

K.ISLAND_NAMES = {
    "Haunted Castle", "Tiki Outpost", "Castle On The Sea", "Mansion", "Hydra Island",
    "Port Town", "Great Tree", "Cake Land", "Chocolate Land",
}
K.ISLANDS = {
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

if InSea2 then
    K.ISLAND_NAMES = {
        "Café", "First Spot", "Dark Arena", "Don Swan Mansion", "Green Zone", "Graveyard",
        "Snow Mountain", "Hot and Cold", "Cursed Ship", "Ice Castle", "Forgotten Island", "Remote Island",
    }
    K.ISLANDS = {
        ["Café"] = Vector3.new(-11, 29, 2771),
        ["First Spot"] = Vector3.new(-11, 29, 2771),
        ["Dark Arena"] = Vector3.new(3494, 13, -3259),
        ["Don Swan Mansion"] = Vector3.new(-317, 331, 597),
        ["Green Zone"] = Vector3.new(-2258, 73, -2696),
        ["Graveyard"] = Vector3.new(-5552, 194, -776),
        ["Snow Mountain"] = Vector3.new(752, 408, -5277),
        ["Hot and Cold"] = Vector3.new(-6008, 29, -5018),
        ["Cursed Ship"] = K.SHIP_POS,
        ["Ice Castle"] = Vector3.new(5505, 40, -6178),
        ["Forgotten Island"] = Vector3.new(-3050, 240, -10178),
        ["Remote Island"] = Vector3.new(4816, 8, 2863),
    }
end

K.BRING_RANGE = 300
K.HOVER_HEIGHT = 20
K.STUCK_TIME = 3
K.SKIP_TIME = 15
K.BUG_RADIUS = 60
K.BUG_TIME = 30

K.ELITE_BOSSES = { "Diablo", "Deandre", "Urban" }
K.DOUGH_BOSS = { "Dough King" }
K.RIP_BOSS = { "rip_indra True Form", "rip_indra" }
K.SOUL_BOSS = { "Soul Reaper" }
K.SOUL_ALTAR = CFrame.new(-8932.09863, 143.755417, 6058.58154, -0.996918082, 3.84372747e-08, -0.0784496292, 4.57218867e-08, 1, -9.1060997e-08, 0.0784496292, -9.43672234e-08, -0.996918082)
K.ELITE_CALL_COOLDOWN = 5

K.CASTLE_POS = Vector3.new(-5496, 313, -2841)
K.CASTLE_RADIUS = 700
K.RAID_WAIT = 240
K.RAID_END_IDLE = 2

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
local lastShipEnter = 0
local ShipPortalPart = nil
local lastSim = 0
local raidUntil, raidLastSeen, raidSawEnemies = 0, 0, false

local Stats = { State = "Idle", PauseStore = false, RaidMsg = "-", RaidsMulti = 0, RaidAuraKills = 0 }

local RaidCfg = {
    Multi = false,
    Auto = false,
    AutoBuyChip = false,
    AutoUnstore = false,
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

local SeaCfg = {
    Auto = false,
    Boat = "Guardian",
    Farm = {},
    Level = 1,
    Levels = {
        [1] = CFrame.new(-21998.375, 30.0006, -682.309),
        [2] = CFrame.new(-26779.5215, 30.0005, -822.858),
        [3] = CFrame.new(-31171.957, 30.0001, -2256.9377),
        [4] = CFrame.new(-34054.6875, 30.2187, -2560.1201),
        [5] = CFrame.new(-38887.5547, 30.0004, -2162.9902),
        [6] = CFrame.new(-44541.7617, 30.0003, -1244.8584),
    },
    SkillHold = false,
    DealerCFrame = CFrame.new(-16923.447265625, 9.391397476196289, 434.3277282714844),
    Range = 1500,
    MaxBoatDist = 2500,
    needNewBoat = false,
    hadBoat = false,
    atSea = false,
    lastBoatPos = nil,
    dodge = nil,
    terrorNear = false,
    beastPart = nil,
    beastDodging = false,
    -- altura minima do personagem acima da superficie do mar enquanto ataca o Sea Beast
    -- (o Sea Beast mergulha, entao a posicao dele + altura pode ficar debaixo da agua)
    BeastMinAbove = 150,
    surfaceY = 30,
    extra = {},
    extraT = 0,
    lastBuy = 0,
    lastSkill = 0,
    lastSit = 0,
    -- referencia: barco anda pelo VehicleSeat numa altura fixa, e flutua alto durante o combate
    BoatPosY = 31,
    BoatSpeed = 230,
    ProtectBoat = true,
    ProtectY = 450,
    boatGoal = nil,
    boatGoalT = 0,
    wantSit = false,
}

-- CORRIGIDO: campo "text" guarda a ultima resposta do CakePrinceSpawner (usado na fase Dough King)
local Kat = { lastIdleQuery = 0, remaining = nil, needCheck = true, blockUntil = 0, mode = nil, lastN = nil, bossSeen = false, text = nil }

local Dough = { phase = nil, cocoa = 0, cocoaKnown = false, invGod = false, invSweet = false, lastInv = -100, lastCraft = -100, probeAt = 0, lastNotify = -100 }

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
    if K.SEA3_IDS[game.PlaceId] then return true end
    local origin = workspace:FindFirstChild("_WorldOrigin")
    local spawns = origin and origin:FindFirstChild("EnemySpawns")
    for _, s in ipairs(spawns and spawns:GetChildren() or {}) do
        if table.find(K.CAKE_MOBS, s.Name) then return true end
    end
    for _, e in ipairs(workspace.Enemies:GetChildren()) do
        if table.find(K.CAKE_MOBS, e.Name) then return true end
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
        elseif (spot.pos - pos).Magnitude <= K.BUG_RADIUS then
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
            elseif os.clock() - rec.t > K.STUCK_TIME then
                skipUntil[enemy] = os.clock() + K.SKIP_TIME
                table.insert(bugSpots, { pos = BringPoint, untilT = os.clock() + K.BUG_TIME })
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
            if Kat.remaining and Kat.remaining > 0 and table.find(K.CAKE_MOBS, enemy.Name) then
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

local Aim = { hooked = false, pos = nil, part = nil, fruitPos = nil, fruitPart = nil }

function Aim.install()
    if Aim.hooked or not hookmetamethod then return end
    Aim.hooked = true
    local mouse = Player:GetMouse()
    local wrap = newcclosure or function(f) return f end
    local old
    local ok = pcall(function()
        old = hookmetamethod(game, "__index", wrap(function(self, key)
            if self == mouse and not (checkcaller and checkcaller()) then
                local pos = Aim.pos or Aim.fruitPos
                if pos then
                    if key == "Hit" then return CFrame.new(pos) end
                    if key == "Target" then return Aim.part or Aim.fruitPart end
                end
            end
            return old(self, key)
        end))
    end)
    if not ok then Aim.hooked = false end
end

local function fastAttack()
    if SeaCfg.SkillHold then return end
    local char = Player.Character
    local hrp = root()
    if not (char and hrp) then return end

    Aim.fruitPos, Aim.fruitPart = nil, nil
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
    local focusModel = attackFocus
    if SeaCfg.Auto and focusModel and focusModel.Parent ~= Workspace.Enemies then
        local fRoot = focusModel:FindFirstChild("HumanoidRootPart") or focusModel.PrimaryPart
        local fHum = focusModel:FindFirstChildOfClass("Humanoid")
        if fRoot and (not fHum or fHum.Health > 0) and (fRoot.Position - hrp.Position).Magnitude <= 60 then
            for _, part in ipairs(focusModel:GetChildren()) do
                if part:IsA("BasePart") then
                    table.insert(targets, { focusModel, part })
                end
            end
        end
    end
    if #targets == 0 then return end

    local tool = char:FindFirstChildOfClass("Tool")
    if not tool then return end
    local weapon = selectedWeapon()

    if weapon == "Fruit(M1)" then
        local focus = alive(attackFocus) and attackFocus or targets[1][1]
        local focusRoot = focus.HumanoidRootPart
        Aim.install()
        Aim.fruitPos, Aim.fruitPart = focusRoot.Position, focusRoot
        local remote = tool:FindFirstChild("LeftClickRemote")
        if not isFruit(tool) or not remote then return end
        local direction = focusRoot.Position - hrp.Position
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
            if d <= K.BRING_RANGE then
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
        raidUntil = os.clock() + K.RAID_WAIT
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
            if (pos - K.CASTLE_POS).Magnitude <= K.CASTLE_RADIUS then
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
        if now - raidLastSeen > K.RAID_END_IDLE then
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
        target = CFrame.new(enemy.HumanoidRootPart.Position + Vector3.new(0, K.HOVER_HEIGHT, 0))
        click()
    else
        attackFocus = nil
        target = CFrame.new(K.CASTLE_POS + Vector3.new(0, 30, 0))
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
    for _, name in ipairs(K.ELITE_BOSSES) do
        if title:find(name:lower(), 1, true) then return true end
    end
    return false
end

local function handleElite(boss, inWorld)
    clearGroup()
    if not eliteQuestActive() and os.clock() - lastEliteCall > K.ELITE_CALL_COOLDOWN then
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
    target = CFrame.new(hrp.Position + Vector3.new(0, K.HOVER_HEIGHT, 0))
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
    target = CFrame.new(hrp.Position + Vector3.new(0, K.HOVER_HEIGHT, 0))
    if inWorld then
        attackFocus = boss
        click()
    else
        attackFocus = nil
    end
end

local lastSummonUse = 0

local function findEssence()
    for _, container in ipairs({ Player.Character, Player.Backpack }) do
        if container then
            for _, tool in ipairs(container:GetChildren()) do
                if tool:IsA("Tool") and tool.Name:find("Essence") then return tool end
            end
        end
    end
end

local function handleSummon(essence)
    clearGroup()
    attackFocus = nil
    target = K.SOUL_ALTAR
    local char = Player.Character
    local hrp = root()
    local humanoid = char and char:FindFirstChildOfClass("Humanoid")
    if not (hrp and humanoid) then return end
    if essence.Parent ~= char then
        humanoid:EquipTool(essence)
    end
    if (hrp.Position - K.SOUL_ALTAR.Position).Magnitude <= 5 and os.clock() - lastSummonUse > 2 then
        lastSummonUse = os.clock()
        essence:Activate()
    end
end

function Dough.hasChalice(kind)
    for _, container in ipairs({ Player.Backpack, Player.Character }) do
        if container then
            for _, tool in ipairs(container:GetChildren()) do
                local name = tool.Name:lower()
                if tool:IsA("Tool") and name:find("chalice", 1, true) and name:find(kind, 1, true) then
                    return true
                end
            end
        end
    end
    return false
end

-- le o inventario (conjured cocoa e calices) a cada 3 segundos
function Dough.readInventory()
    if os.clock() - Dough.lastInv < 3 then return end
    Dough.lastInv = os.clock()
    local ok, inv = commF("getInventory")
    if not (ok and type(inv) == "table") then return end
    local cocoa, known, god, sweet = 0, false, false, false
    for k, v in pairs(inv) do
        local name, count
        if type(v) == "table" then
            name = tostring(v.Name or v.name or v.Item or ""):lower()
            count = tonumber(v.Count or v.count or v.Amount or v.amount or v.Quantity or v.quantity) or 1
        elseif type(v) == "number" and type(k) == "string" then
            name, count = k:lower(), v
        end
        if name then
            if name:find("cocoa", 1, true) then
                cocoa += count
                known = true
            elseif name:find("chalice", 1, true) and count > 0 then
                if name:find("god", 1, true) then god = true end
                if name:find("sweet", 1, true) then sweet = true end
            end
        end
    end
    Dough.cocoa, Dough.cocoaKnown, Dough.invGod, Dough.invSweet = cocoa, known, god, sweet
end

-- fases: cocoa (farmar 10 conjured cocoa) -> craft (Sweet Crafter) -> kill (500 npcs + invocar Dough King)
function Dough.update()
    local phase
    if Options.AutoDough and Options.AutoDough.Value and InSea3 then
        pcall(Dough.readInventory)
        local sweet = Dough.invSweet or Dough.hasChalice("sweet")
        local god = Dough.invGod or Dough.hasChalice("god")
        if sweet then
            phase = "kill"
        elseif god then
            -- se o inventario nao mostra cocoa nenhum, tenta craftar mesmo assim de tempos em tempos
            if Dough.cocoa >= K.COCOA_NEEDED or (not Dough.cocoaKnown and os.clock() >= Dough.probeAt) then
                phase = "craft"
            else
                phase = "cocoa"
            end
        end
    end
    if phase ~= Dough.phase then
        Dough.phase = phase
        questId = nil
        Kat.needCheck, Kat.remaining = true, nil
        Kat.text = nil -- CORRIGIDO: zera o texto ao trocar de fase
        clearGroup()
        attackFocus = nil
    end
end

function Dough.sweetCrafter()
    local npcs = workspace:FindFirstChild("NPCs")
    for _, npc in ipairs(npcs and npcs:GetChildren() or {}) do
        if npc.Name:lower():find("sweet crafter", 1, true) then return npc end
    end
    return workspace:FindFirstChild("Sweet Crafter", true)
end

-- vai ate o Sweet Crafter (Chocolate Land) e troca God's Chalice + 10 Conjured Cocoa pelo Sweet Chalice
function Dough.craft()
    if os.clock() - Dough.lastCraft < 5 then return end
    Dough.lastCraft = os.clock()

    local npc = Dough.sweetCrafter()
    if not npc then
        flyTo(CFrame.new(K.ISLANDS["Chocolate Land"] + Vector3.new(0, 20, 0)), 10, 90)
        local t0 = os.clock()
        npc = Dough.sweetCrafter()
        while not npc and farming and os.clock() - t0 < 5 do
            task.wait(0.5)
            npc = Dough.sweetCrafter()
        end
    end
    if npc then
        local okP, pivot = pcall(function() return npc:GetPivot() end)
        if okP then flyTo(CFrame.new(pivot.Position + Vector3.new(0, 3, 0)), 8, 60) end
    end
    if not farming then return end

    local _, res = commF("SweetChaliceNpc")
    task.wait(1.5)
    Dough.lastInv = -100
    pcall(Dough.readInventory)

    if not (Dough.invSweet or Dough.hasChalice("sweet")) then
        Dough.probeAt = os.clock() + 90
        if os.clock() - Dough.lastNotify > 20 then
            Dough.lastNotify = os.clock()
            Fluent:Notify({
                Title = "Dough King",
                Content = (npc and "Sweet Crafter achado" or "Sweet Crafter nao achado")
                    .. " | cocoa: " .. Dough.cocoa .. " | resposta: " .. tostring(res):sub(1, 80),
                Duration = 8,
            })
        end
    end
end

-- CORRIGIDO: na fase kill so procura o Dough King (ignora Katakuri / Cake Prince)
local function findBoss()
    local names = K.BOSS_NAMES
    if Dough.phase == "kill" then names = { "Dough King" } end
    local boss = findEnemy(names, true)
    if boss then return boss end
    for _, v in ipairs(ReplicatedStorage:GetChildren()) do
        if table.find(names, v.Name) and alive(v) then return v end
    end
end

local function applyCountText(res)
    local text = res ~= nil and tostring(res) or ""
    if text == "" or text == "false" or text == "nil" or text == "true" then return end

    Kat.text = text:lower() -- CORRIGIDO: guarda a mensagem do servidor

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
        Kat.remaining = math.max(K.CAKE_TOTAL - n, 0)
    else
        Kat.remaining = nil
    end
end

-- CORRIGIDO: na fase kill (Dough King) consulta sem o argumento true, como no codigo de referencia
local function queryCount()
    local ok, res
    if Dough.phase == "kill" then
        ok, res = commF("CakePrinceSpawner")
    else
        ok, res = commF("CakePrinceSpawner", true)
    end
    if ok then pcall(applyCountText, res) end
end

task.spawn(function()
    while true do
        task.wait(2)
        if InSea3 and not traveling then
            local farmingKat = farming and ((Options.Farm.Value and Options.MethodFarm.Value == "Katakuri") or Dough.phase == "kill")
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
        flyTo(K.CAKE_ENTRANCE, 8, 30)
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

-- CORRIGIDO: fase Dough King so invoca quando o servidor disser "open the portal now"
local function katakuri()
    local hrp = root()
    if not hrp then return true end

    local boss = findBoss()
    if boss then
        Kat.bossSeen = true
        Stats.State = Dough.phase == "kill" and "Dough King Boss" or "Katakuri Boss"
        clearGroup()
        if (boss.HumanoidRootPart.Position - hrp.Position).Magnitude > 1500 then
            reachBoss()
        else
            attackFocus = boss
            target = CFrame.new(boss.HumanoidRootPart.Position + Vector3.new(0, K.HOVER_HEIGHT, 0))
            click()
        end
        return true
    end

    if Kat.bossSeen then
        Kat.bossSeen = false
        Kat.remaining = nil
        Kat.text = nil
        Kat.needCheck = true
    end

    -- FASE DOUGH KING
    if Dough.phase == "kill" then
        if Kat.text and Kat.text:find("open the portal now", 1, true) and os.clock() >= Kat.blockUntil then
            clearGroup()
            attackFocus = nil
            Stats.State = "Summoning Dough King"
            commF("CakePrinceSpawner")
            task.wait(1)
            Kat.text = nil
            Kat.blockUntil = os.clock() + 5
            return true
        end
        return false -- continua atacando os mobs de bolo ate liberar o portal
    end

    -- FASE KATAKURI NORMAL (V1), inalterada
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

task.spawn(function()
    while true do
        if Options.RandomBone and Options.RandomBone.Value then
            commF("Bones", "Buy", 1, 1)
        end
        task.wait(0.5)
    end
end)

task.spawn(function()
    local function spin()
        local gacha = Net:FindFirstChild("RF/GachaNetworkRF")
        if not gacha then return end
        pcall(function()
            gacha:InvokeServer({
                Context = "Purchase",
                BoxName = "ZiolesGacha",
            })
        end)
    end

    while true do
        local delay = 1
        if Options.RandomFruit and Options.RandomFruit.Value then
            -- so gira quando o jogo diz que esta liberado; em cooldown espera e checa de novo
            local okCheck, ready = commF("Cousin", "CheckTime", "DLCBoxData")
            if okCheck and ready == true then
                spin()
                delay = 3
            elseif okCheck then
                delay = 10
            else
                -- nao deu para checar o tempo: tenta girar, mas devagar
                spin()
                delay = 30
            end

            -- fecha o menu do spinner
            local spinnerWindow = Player.PlayerGui:FindFirstChild("SpinnerWindow")
            if spinnerWindow then
                local above = spinnerWindow:FindFirstChild("AboveSpinner")
                local nav = above and above:FindFirstChild("Navigation")
                local closeButton = nav and nav:FindFirstChild("CloseButton")
                if closeButton and closeButton.Visible and firesignal then
                    pcall(function()
                        firesignal(closeButton.Activated)
                    end)
                end
            end
        end
        task.wait(delay)
    end
end)

local RaidTypes = { "Flame", "Ice", "Sand", "Dark", "Light", "Magma", "Quake", "Buddha", "Spider" }

local function multiRaidActive()
    return RaidCfg.Auto or (RaidCfg.Multi and (RaidCfg.Pay ~= RaidCfg.Secondary))
end

local function abortMulti()
    return not multiRaidActive()
end

local function chipWanted()
    return multiRaidActive() or RaidCfg.AutoBuyChip
end

local function abortChip()
    return not chipWanted()
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
        local hoverPos = enemy.HumanoidRootPart.Position + Vector3.new(0, K.HOVER_HEIGHT, 0)
        attackFocus = enemy
        if (hrp.Position - hoverPos).Magnitude > 50 then
            clearGroup()
            target = CFrame.new(hoverPos)
        else
            if BringGroup[1] ~= enemy or not BringPoint then buildBringGroup(enemy, enemyNames()) end
            target = CFrame.new(BringPoint + Vector3.new(0, K.HOVER_HEIGHT, 0))
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
    target = nil
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
                flyAndWait(CFrame.new(pivot.Position + Vector3.new(0, 3, 0)), 60, abortChip)
            end
        end
        if not chipWanted() then return end

        for _, t in ipairs(otherFruitTools()) do
            pcall(function() CommF:InvokeServer("StoreFruit", t:GetAttribute("OriginalName") or t.Name, t) end)
        end
        task.wait(0.5)
        if #otherFruitTools() > 0 then
            raidWarn("guarde as frutas da mochila antes (uma fruta cara poderia ser usada no chip)")
            return
        end

        if not anyFruitTool() then
            if RaidCfg.AutoUnstore or RaidCfg.AutoBuyChip or (RaidCfg.Multi and RaidCfg.Pay) then
                local okU, info = unstoreCheapFruit()
                Stats.RaidMsg = okU and ("pagando com: " .. info) or info
                if not okU and os.clock() - lastFruitNotify > 30 then
                    lastFruitNotify = os.clock()
                    notify("Raid", "Fruta para o chip: " .. info, 8)
                end
                task.wait(0.6)
            else
                raidWarn("sem fruta na mochila para o chip (ative Auto Unstore Fruit Under 1M)")
                return
            end
        end

        for _ = 1, 3 do
            if hasChip() or not chipWanted() then break end
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
    if raidGui() or farFromLobby() then return true end
    target = nil
    attackFocus = nil
    clearGroup()
    for _ = 1, 2 do
        if raidGui() or farFromLobby() then return true end
        local hadChip = hasChip()
        pcall(fireclickdetector, det)
        local t0 = os.clock()
        while os.clock() - t0 < 30 and multiRaidActive() do
            task.wait(0.3)
            if raidGui() or farFromLobby() then return true end
            if hadChip and not hasChip() then
                task.wait(3)
                return true
            end
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

local lastChipTry = -100

local function runAutoRaid()
    if not InSea3 then
        raidWarn("Auto Raid so funciona no Sea 3")
        task.wait(2)
        return
    end
    if inRaidNow() then
        raidFight(0)
        return
    end
    if not hasChip() then
        raidWarn("sem chip para iniciar a raid")
        target = nil
        attackFocus = nil
        clearGroup()
        task.wait(2)
        return
    end
    if not goToSpot(RaidCfg.HostCFrame) then task.wait(1) return end
    RaidArmed = true
    task.wait(1)
    if pressRaidButton() then
        raidFight(25)
    else
        RaidArmed = false
        task.wait(2)
    end
end

local function runMultiRaid()
    if RaidCfg.Multi and (RaidCfg.Pay ~= RaidCfg.Secondary) then
        if RaidCfg.Pay then runRaidPay() else runRaidSecondary() end
    else
        runAutoRaid()
    end
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
    if not ((farming or traveling) and target and Player.Character) or SeaCfg.wantSit then return end
    for _, part in ipairs(Player.Character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
        end
    end
end)

task.spawn(function()
    while true do
        task.wait(0.01)
        local autoClick = Options.AutoClick and Options.AutoClick.Value and selectedWeapon() ~= "Fruit(M1)"
        if (farming and not traveling and attackFocus) or autoClick then
            local ok, err = pcall(fastAttack)
            if not ok then warn("[Batman Hub] fastAttack: " .. tostring(err)) end
        else
            Aim.fruitPos, Aim.fruitPart = nil, nil
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

do
local VirtualInputManager = game:GetService("VirtualInputManager")

local function seaFarmSelected(name)
    return SeaCfg.Farm[name] == true
end

local function ownedBoat(ignoreFar)
    local boats = Workspace:FindFirstChild("Boats")
    local playerRoot = root()
    for _, boat in ipairs(boats and boats:GetChildren() or {}) do
        if boat.Name == SeaCfg.Boat then
            local owner = boat:FindFirstChild("Owner")
            local value = owner and owner.Value
            if value == Player or tostring(value) == Player.Name then
                local seat = boat:FindFirstChild("VehicleSeat") or boat:FindFirstChildWhichIsA("VehicleSeat", true)
                local hp = boat:FindFirstChild("Humanoid")
                local dead = (hp ~= nil and hp:IsA("ValueBase") and hp.Value == 0)
                    or (hp ~= nil and hp:IsA("Humanoid") and hp.Health <= 0)
                    or boat:GetAttribute("Dead") == true
                    or boat:GetAttribute("Sunk") == true
                    or (seat ~= nil and seat.Position.Y < -15)
                if seat and not dead then
                    local tooFar = ignoreFar and playerRoot
                        and (seat.Position - playerRoot.Position).Magnitude > SeaCfg.MaxBoatDist
                    if not tooFar then return boat, seat end
                end
            end
        end
    end
end

local function buyBoat()
    if os.clock() - SeaCfg.lastBuy < 5 then return end
    SeaCfg.lastBuy = os.clock()
    Stats.State = "Buy Boat"
    flyAndWait(SeaCfg.DealerCFrame, 60, function() return not SeaCfg.Auto end)
    if not SeaCfg.Auto then return end
    target = nil
    commF("BuyBoat", SeaCfg.Boat)
    waitFor(function() return ownedBoat(SeaCfg.needNewBoat) end, 6)
end

local function sitOnBoat(seat)
    local char = Player.Character
    local hrp = root()
    local humanoid = char and char:FindFirstChildOfClass("Humanoid")
    if not (hrp and humanoid) then return false end
    if humanoid.SeatPart == seat then return true end
    if os.clock() - SeaCfg.lastSit < 1.5 then return false end
    SeaCfg.lastSit = os.clock()
    target = nil
    hrp.CFrame = seat.CFrame * CFrame.new(0, 2, 0)
    task.wait(0.4)
    return humanoid.SeatPart == seat
end

local function seaKind(name, fromBeasts)
    local lower = name:lower()
    if lower:find("terror", 1, true) then return "Terror Shark" end
    if lower:find("piranha", 1, true) then return "Piranha" end
    if name == "Shark" then return "Shark" end
    if fromBeasts then return "Sea Beast" end
end

local function modelPart(model)
    return model:FindFirstChild("HumanoidRootPart") or model.PrimaryPart or model:FindFirstChildWhichIsA("BasePart", true)
end

-- Terror Shark / Shark / Piranha may live outside workspace.Enemies, so also scan top-level folders
local function extraScan()
    if os.clock() - SeaCfg.extraT < 1.5 then return SeaCfg.extra end
    SeaCfg.extraT = os.clock()
    local found = {}
    local function check(model)
        if model:IsA("Model") and seaKind(model.Name, false) then table.insert(found, model) end
    end
    for _, child in ipairs(Workspace:GetChildren()) do
        if child:IsA("Model") then
            check(child)
        elseif child:IsA("Folder") and child.Name ~= "Map" and child.Name ~= "Boats" then
            for _, inner in ipairs(child:GetChildren()) do check(inner) end
        end
    end
    SeaCfg.extra = found
    return found
end

local function seaMobs()
    local list, seen = {}, {}
    local hrp = root()
    if not hrp then return list end

    local function consider(model, fromBeasts)
        if seen[model] or not model:IsA("Model") then return end
        local kind = seaKind(model.Name, fromBeasts)
        if not kind or not seaFarmSelected(kind) then return end
        local part = modelPart(model)
        if not part then return end
        local humanoid = model:FindFirstChildOfClass("Humanoid")
        if humanoid and humanoid.Health <= 0 then return end
        local d = (part.Position - hrp.Position).Magnitude
        if d > SeaCfg.Range then return end
        seen[model] = true
        table.insert(list, { model = model, part = part, dist = d, beast = kind == "Sea Beast", terror = kind == "Terror Shark" })
    end

    for _, model in ipairs(Workspace.Enemies:GetChildren()) do consider(model, false) end
    local beasts = Workspace:FindFirstChild("SeaBeasts")
    for _, model in ipairs(beasts and beasts:GetChildren() or {}) do consider(model, true) end
    for _, model in ipairs(extraScan()) do consider(model, false) end

    table.sort(list, function(x, y) return x.dist < y.dist end)
    return list
end

local function terrorDodge()
    local origin = Workspace:FindFirstChild("_WorldOrigin")
    for _, child in ipairs(origin and origin:GetChildren() or {}) do
        local name = child.Name:lower()
        if name:find("sharkslam", 1, true) or name:find("spin", 1, true) or name:find("chargemodel", 1, true) then
            return true
        end
    end
    return false
end

local function unselectedSea()
    local hrp = root()
    if not hrp then return false end

    local function near(part)
        local dx = part.Position.X - hrp.Position.X
        local dz = part.Position.Z - hrp.Position.Z
        return math.sqrt(dx * dx + dz * dz) <= SeaCfg.Range
    end

    local function check(model, fromBeasts)
        if not model:IsA("Model") then return false end
        local kind = seaKind(model.Name, fromBeasts)
        if not kind or seaFarmSelected(kind) then return false end
        local part = modelPart(model)
        local humanoid = model:FindFirstChildOfClass("Humanoid")
        return part ~= nil and (not humanoid or humanoid.Health > 0) and near(part)
    end

    for _, model in ipairs(Workspace.Enemies:GetChildren()) do
        if check(model, false) then return true end
    end
    local beasts = Workspace:FindFirstChild("SeaBeasts")
    for _, model in ipairs(beasts and beasts:GetChildren() or {}) do
        if check(model, true) then return true end
    end
    for _, model in ipairs(extraScan()) do
        if check(model, false) then return true end
    end

    -- boats that do not belong to any player (event ships)
    local boats = Workspace:FindFirstChild("Boats")
    for _, boat in ipairs(boats and boats:GetChildren() or {}) do
        local owner = boat:FindFirstChild("Owner")
        if not (owner and owner.Value) then
            local part = modelPart(boat)
            if part and near(part) then return true end
        end
    end
    return false
end

RunService.Heartbeat:Connect(function()
    if not SeaCfg.Auto then return end
    local hrp = root()
    if not hrp then return end

    local dodge = SeaCfg.dodge
    if dodge then
        local now = os.clock()
        if now - dodge.t0 >= 1 or now >= dodge.maxT then
            hrp.CFrame = dodge.back
            target = dodge.back
            SeaCfg.dodge = nil
        end
        return
    end

    if not SeaCfg.terrorNear then return end
    local humanoid = Player.Character and Player.Character:FindFirstChildOfClass("Humanoid")
    if not humanoid or humanoid.SeatPart then return end
    if terrorDodge() then
        Stats.State = "Dodging Terror Shark"
        local now = os.clock()
        SeaCfg.dodge = { back = hrp.CFrame, t0 = now, maxT = now + 10 }
        attackFocus = nil
        hrp.CFrame = hrp.CFrame + Vector3.new(0, 250, 0)
        target = hrp.CFrame
    end
end)

-- approximation of IsEntityAttacking: any non-looping animation playing on the entity
function SeaCfg.isAttacking(model)
    local ok, result = pcall(function()
        local humanoid = model:FindFirstChildOfClass("Humanoid")
        local animator = (humanoid and humanoid:FindFirstChildOfClass("Animator"))
            or model:FindFirstChildWhichIsA("Animator", true)
        if animator then
            for _, track in ipairs(animator:GetPlayingAnimationTracks()) do
                if not track.Looped then return true end
            end
        end
        return false
    end)
    return ok and result == true
end

function SeaCfg.dodgeBeast()
    local beasts = Workspace:FindFirstChild("SeaBeasts")
    for _, child in ipairs(beasts and beasts:GetChildren() or {}) do
        if child:FindFirstChild("HumanoidRootPart") and SeaCfg.isAttacking(child) then
            return true
        end
    end

    local origin = Workspace:FindFirstChild("_WorldOrigin")
    for _, child in ipairs(origin and origin:GetChildren() or {}) do
        if child:IsA("BasePart") then
            local name = child.Name:lower()
            local flag = name:find("lightbullet", 1, true) or name:find("waterbeam", 1, true) or name:find("beastroar", 1, true)
            if not flag then
                flag = child.BrickColor == BrickColor.new("Toothpaste") and child.Size.Magnitude > 15
            end
            if flag then return true end
        end
    end

    for _, child in ipairs(Workspace:GetChildren()) do
        if child:IsA("BasePart") then
            local name = child.Name:lower()
            if name:find("waterbeam", 1, true) or name:find("lightbullet", 1, true) then
                return true
            end
        end
    end
    return false
end

RunService.Heartbeat:Connect(function()
    local part = SeaCfg.beastPart
    if not (SeaCfg.Auto and part and part.Parent) then
        SeaCfg.beastDodging = false
        return
    end
    local hrp = root()
    local humanoid = Player.Character and Player.Character:FindFirstChildOfClass("Humanoid")
    if not hrp or not humanoid or humanoid.SeatPart then return end

    -- nunca deixa o personagem ficar debaixo da agua enquanto ataca o Sea Beast
    local minY = SeaCfg.surfaceY + SeaCfg.BeastMinAbove
    if hrp.Position.Y < SeaCfg.surfaceY - 5 then
        hrp.CFrame = CFrame.new(hrp.Position.X, minY, hrp.Position.Z) * hrp.CFrame.Rotation
        hrp.AssemblyLinearVelocity = Vector3.zero
        target = hrp.CFrame
    end

    local dodging = SeaCfg.dodgeBeast()
    SeaCfg.beastDodging = dodging
    if dodging then
        local p = part.Position - part.CFrame.LookVector * 20
        hrp.CFrame = CFrame.new(p.X, math.max(p.Y + 400, minY), p.Z) * hrp.CFrame.Rotation
        target = hrp.CFrame
    end
end)

K.SKILL_KEYS = { Enum.KeyCode.Z, Enum.KeyCode.X, Enum.KeyCode.C, Enum.KeyCode.V, Enum.KeyCode.F }
K.TRANSFORM_FRUITS = { "dragon", "leopard", "kitsune", "t-rex", "trex", "yeti", "tiger", "mammoth", "venom", "gas" }
K.SKILL_TOOLS = { "Melee", "Sword", "Blox Fruit", "Gun" }

local function findToolByTip(tip)
    local char = Player.Character
    for _, container in ipairs({ char, Player.Backpack }) do
        for _, tool in ipairs(container and container:GetChildren() or {}) do
            if tool:IsA("Tool") and tool.ToolTip == tip then return tool end
        end
    end
end

local function releaseSkills(part)
    if os.clock() - SeaCfg.lastSkill < 0.3 then return end
    SeaCfg.lastSkill = os.clock()
    local humanoid = Player.Character and Player.Character:FindFirstChildOfClass("Humanoid")
    if not humanoid then return end
    SeaCfg.SkillHold = true
    Aim.install()
    for _, tip in ipairs(K.SKILL_TOOLS) do
        if not (SeaCfg.Auto and part and part.Parent) then break end
        local tool = findToolByTip(tip)
        if tool then
            humanoid:EquipTool(tool)
            task.wait(0.15)
            Aim.pos, Aim.part = part.Position, part
            local skipV = false
            if tip == "Blox Fruit" then
                local lower = tool.Name:lower()
                for _, word in ipairs(K.TRANSFORM_FRUITS) do
                    if lower:find(word, 1, true) then skipV = true break end
                end
            end
            for _, key in ipairs(K.SKILL_KEYS) do
                if not (skipV and key == Enum.KeyCode.V) then
                    VirtualInputManager:SendKeyEvent(true, key, false, game)
                    task.wait(0.05)
                    VirtualInputManager:SendKeyEvent(false, key, false, game)
                    task.wait(0.05)
                end
            end
        end
    end
    Aim.pos, Aim.part = nil, nil
    SeaCfg.SkillHold = false
end

local function resetCharacter(reason)
    Stats.State = reason
    SeaCfg.needNewBoat = true
    SeaCfg.hadBoat = false
    target = nil
    attackFocus = nil
    local humanoid = Player.Character and Player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then humanoid.Health = 0 end
    task.wait(1)
    waitFor(function()
        local h = Player.Character and Player.Character:FindFirstChildOfClass("Humanoid")
        return h and h.Health > 0 and root()
    end, 15)
end

local function nearerToDealer(boatPos)
    local hrp = root()
    return hrp ~= nil and boatPos ~= nil
        and (hrp.Position - SeaCfg.DealerCFrame.Position).Magnitude < (hrp.Position - boatPos).Magnitude
end

local function runSeaEvents()
    local levelCF = SeaCfg.Levels[SeaCfg.Level]
    if not levelCF then
        Stats.State = "Sea: set Danger Level " .. SeaCfg.Level
        target = nil
        attackFocus = nil
        return
    end
    SeaCfg.surfaceY = levelCF.Y

    local hrp = root()
    local humanoid = Player.Character and Player.Character:FindFirstChildOfClass("Humanoid")
    if not (hrp and humanoid) then return end

    clearGroup()
    SeaCfg.beastPart = nil
    SeaCfg.boatGoal = nil
    SeaCfg.wantSit = false

    if SeaCfg.dodge then
        hover(true)
        attackFocus = nil
        target = hrp.CFrame
        return
    end

    local boatLost = false
    local boat, seat = ownedBoat(SeaCfg.needNewBoat)
    if boat then
        SeaCfg.needNewBoat = false
        SeaCfg.hadBoat = true
        SeaCfg.lastBoatPos = seat.Position
        if (seat.Position - hrp.Position).Magnitude > SeaCfg.MaxBoatDist then
            if nearerToDealer(seat.Position) then
                SeaCfg.needNewBoat = true
                SeaCfg.hadBoat = false
                attackFocus = nil
                target = nil
                buyBoat()
            else
                resetCharacter("Boat too far: resetting")
            end
            return
        end
    else
        if not SeaCfg.hadBoat then
            attackFocus = nil
            buyBoat()
            return
        end
        -- o barco morreu: ainda mata o que estiver por perto e so depois reseta
        boatLost = true
    end

    local mobs = seaMobs()
    local mob = mobs[1]
    local hasTerror = false
    for _, m in ipairs(mobs) do
        if m.terror then hasTerror = true break end
    end
    SeaCfg.terrorNear = hasTerror

    if mob then
        -- Protect Boat: o barco flutua alto enquanto voce luta, para nao tomar dano
        if boat and seat and SeaCfg.ProtectBoat then
            local pivot = boat:GetPivot()
            local py = SeaCfg.ProtectY
            if pivot.Position.Y < py - 10 then
                pcall(function()
                    boat:PivotTo(CFrame.new(pivot.Position.X, py, pivot.Position.Z) * pivot.Rotation)
                end)
            end
            local holdPos = Vector3.new(pivot.Position.X, py, pivot.Position.Z)
            local bp = seat:FindFirstChild("BoatProtectBP")
            if not bp then
                bp = Instance.new("BodyPosition")
                bp.Name = "BoatProtectBP"
                bp.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bp.P = 100000
                bp.D = 1000
                bp.Parent = seat
            end
            bp.Position = holdPos
            local bv = seat:FindFirstChild("BoatProtectBV")
            if not bv then
                bv = Instance.new("BodyVelocity")
                bv.Name = "BoatProtectBV"
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.P = 100000
                bv.Parent = seat
            end
            bv.Velocity = Vector3.zero
        end

        if humanoid.SeatPart then
            Stats.State = "Leaving boat"
            attackFocus = nil
            target = nil
            humanoid.Sit = false
            humanoid:ChangeState(Enum.HumanoidStateType.Jump)
            task.wait(0.3)
            return
        end

        hover(true)
        Stats.State = "Sea: " .. mob.model.Name
        local pos = mob.part.Position
        if mob.beast then
            attackFocus = nil
            SeaCfg.beastPart = mob.part
            local height = SeaCfg.beastDodging and 400 or 250
            local behind = pos - mob.part.CFrame.LookVector * (SeaCfg.beastDodging and 20 or 2)
            -- o Sea Beast mergulha: nunca deixa o alvo ficar abaixo da altura minima sobre a agua
            local goalY = math.max(pos.Y + height, levelCF.Y + SeaCfg.BeastMinAbove)
            target = CFrame.new(behind.X, goalY, behind.Z) * hrp.CFrame.Rotation
            if mob.dist <= 600 then releaseSkills(mob.part) end
        elseif mob.terror then
            -- alturas da referencia: na superficie 250, atacando (slam/spin) 150 e afastado 30, normal 40
            local offset
            if pos.Y > -3 then
                offset = CFrame.new(0, 250, 0)
            elseif terrorDodge() then
                offset = CFrame.new(0, 150, 30)
            else
                offset = CFrame.new(0, 40, 0)
            end
            target = CFrame.new((mob.part.CFrame * offset).Position) * hrp.CFrame.Rotation
            attackFocus = mob.model
        else
            target = CFrame.new(pos + Vector3.new(0, K.HOVER_HEIGHT, 0)) * hrp.CFrame.Rotation
            attackFocus = mob.model
        end
        return
    end

    attackFocus = nil

    -- sem alvos: remove a protecao do barco (RemoveProtect)
    if seat then
        local bp = seat:FindFirstChild("BoatProtectBP")
        local bv = seat:FindFirstChild("BoatProtectBV")
        if bp or bv then
            -- desce o barco ate a agua antes de soltar, para ele nao cair com inercia e sair andando
            local pivot = boat:GetPivot()
            pcall(function()
                boat:PivotTo(CFrame.new(pivot.Position.X, SeaCfg.BoatPosY, pivot.Position.Z) * pivot.Rotation)
            end)
            seat.AssemblyLinearVelocity = Vector3.zero
            seat.AssemblyAngularVelocity = Vector3.zero
            if bp then bp:Destroy() end
            if bv then bv:Destroy() end
        end
    end

    if boatLost then
        if nearerToDealer(SeaCfg.lastBoatPos) then
            SeaCfg.needNewBoat = true
            SeaCfg.hadBoat = false
            buyBoat()
        else
            resetCharacter("Boat destroyed: resetting")
        end
        return
    end

    if humanoid.SeatPart ~= seat then
        Stats.State = "Returning to boat"
        hover(true)
        local seatCF = seat.CFrame * CFrame.new(0, 3, 0)
        if (hrp.Position - seatCF.Position).Magnitude > 12 then
            target = seatCF
            return
        end
        SeaCfg.wantSit = true
        target = nil
        hover(false)
        sitOnBoat(seat)
        return
    end

    hover(false)
    local flat = Vector3.new(levelCF.X - hrp.Position.X, 0, levelCF.Z - hrp.Position.Z).Magnitude
    if SeaCfg.atSea and flat > 150 then SeaCfg.atSea = false end
    if not SeaCfg.atSea and flat <= 30 then SeaCfg.atSea = true end

    if SeaCfg.atSea then
        if unselectedSea() then
            Stats.State = "Avoiding unselected event"
            target = CFrame.new(hrp.Position.X, levelCF.Y + 2000, hrp.Position.Z) * hrp.CFrame.Rotation
        else
            Stats.State = "Waiting Sea Events"
            target = nil
            SeaCfg.boatGoal = levelCF.Position
            SeaCfg.boatGoalT = os.clock()
        end
    else
        Stats.State = "Sailing to Danger Level " .. SeaCfg.Level
        target = nil
        SeaCfg.boatGoal = levelCF.Position
        SeaCfg.boatGoalT = os.clock()
    end
end

RunService.Stepped:Connect(function(_, dt)
    if not SeaCfg.Auto then return end
    local boat, seat = ownedBoat()
    if not boat then return end
    -- o assento precisa colidir, senao o personagem nao consegue sentar
    for _, part in ipairs(boat:GetDescendants()) do
        if part:IsA("BasePart") then part.CanCollide = (part == seat) end
    end

    local goal = SeaCfg.boatGoal
    local humanoid = Player.Character and Player.Character:FindFirstChildOfClass("Humanoid")
    local seated = seat ~= nil and humanoid ~= nil and humanoid.SeatPart == seat
    -- destino vence em 0.5s: nunca anda com destino velho
    if goal and os.clock() - SeaCfg.boatGoalT > 0.5 then goal = nil end
    if goal and seat and seated then
        local from = Vector3.new(seat.Position.X, SeaCfg.BoatPosY, seat.Position.Z)
        local delta = Vector3.new(goal.X, SeaCfg.BoatPosY, goal.Z) - from
        local dist = delta.Magnitude
        if dist > 25 then
            local step = math.min(SeaCfg.BoatSpeed * dt, dist)
            seat.CFrame = CFrame.new(from + delta.Unit * step) * seat.CFrame.Rotation
        end
        seat.AssemblyLinearVelocity = Vector3.zero
    elseif seat and not seated and not seat:FindFirstChild("BoatProtectBP") then
        -- ninguem no assento e sem protecao: impede o barco de sair andando sozinho
        seat.AssemblyLinearVelocity = Vector3.zero
        seat.AssemblyAngularVelocity = Vector3.zero
    end
end)

SeaCfg.run = runSeaEvents
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

function Tyrant.aimDown(pos, action)
    local char = Player.Character
    if not char then return end

    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = { char, workspace.Enemies }
    local hit = workspace:Raycast(pos, Vector3.new(0, -300, 0), params)
    local aimPos = hit and hit.Position or (pos - Vector3.new(0, 20, 0))
    local aimPart = hit and hit.Instance

    Aim.install()
    Aim.pos, Aim.part = aimPos, aimPart

    local camera = workspace.CurrentCamera
    RunService:BindToRenderStep("BatmanAimDown", Enum.RenderPriority.Camera.Value + 1, function()
        local _, yaw = camera.CFrame:ToOrientation()
        camera.CFrame = CFrame.new(camera.CFrame.Position) * CFrame.fromOrientation(-math.rad(89), yaw, 0)
    end)
    task.wait(0.2)
    pcall(action, aimPos, aimPart)

    RunService:UnbindFromRenderStep("BatmanAimDown")
    Aim.pos, Aim.part = nil, nil
end

function Tyrant.shootDown(pos)
    Tyrant.aimDown(pos, function(aimPos, aimPart)
        local tool = Player.Character and Player.Character:FindFirstChildOfClass("Tool")
        if not tool then return end
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
    end)
end

-- sem Skull Guitar: usa as skills de fruta, melee, sword e gun (nessa ordem)
function Tyrant.useSkills(abort)
    local char = Player.Character
    local humanoid = char and char:FindFirstChildOfClass("Humanoid")
    if not humanoid then return end
    local vim = game:GetService("VirtualInputManager")
    local rank = { ["Blox Fruit"] = 1, Melee = 2, Sword = 3, Gun = 4 }
    local list = {}
    for _, container in ipairs({ Player.Backpack, char }) do
        for _, tool in ipairs(container:GetChildren()) do
            if tool:IsA("Tool") then
                local kind = isFruit(tool) and "Blox Fruit" or tool.ToolTip
                if rank[kind] then table.insert(list, { tool, rank[kind] }) end
            end
        end
    end
    table.sort(list, function(a, b) return a[2] < b[2] end)
    for _, entry in ipairs(list) do
        if abort() then return end
        humanoid:EquipTool(entry[1])
        task.wait(0.3)
        for _, key in ipairs({ Enum.KeyCode.Z, Enum.KeyCode.X, Enum.KeyCode.C, Enum.KeyCode.V, Enum.KeyCode.F }) do
            if abort() then return end
            vim:SendKeyEvent(true, key, false, game)
            task.wait(0.1)
            vim:SendKeyEvent(false, key, false, game)
            task.wait(0.2)
        end
    end
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
        if Tyrant.equipGuitar() then
            Tyrant.shootDown(pos)
        else
            if os.clock() - Tyrant.lastWarn > 30 then
                Tyrant.lastWarn = os.clock()
                Fluent:Notify({ Title = "Batman Hub", Content = "Skull Guitar nao encontrada, usando skills", Duration = 5 })
            end
            Tyrant.aimDown(pos, function() Tyrant.useSkills(abort) end)
        end
        local t0 = os.clock()
        while os.clock() - t0 < 2 and not abort() do
            task.wait(0.2)
        end
    end
end

-- procura o portal do navio no mapa (uma vez; fica guardado)
local function findShipPortal()
    if ShipPortalPart and ShipPortalPart.Parent then return ShipPortalPart end
    ShipPortalPart = nil
    local map = workspace:FindFirstChild("Map")
    for _, d in ipairs(map and map:GetDescendants() or {}) do
        if d:IsA("BasePart") then
            local n = (d.Name .. " " .. (d.Parent and d.Parent.Name or "")):lower()
            if n:find("ship", 1, true)
                and (n:find("portal", 1, true) or n:find("entrance", 1, true) or n:find("teleport", 1, true)) then
                ShipPortalPart = d
                break
            end
        end
    end
    return ShipPortalPart
end

-- entra no navio pelo portal (se nao achar ou nao funcionar, pede a entrada ao jogo); true = estava agindo
local function enterShip()
    local hrp = root()
    if not hrp or (hrp.Position - K.SHIP_POS).Magnitude <= 2500 then return false end
    if os.clock() - lastShipEnter <= 8 then return true end
    lastShipEnter = os.clock()
    clearGroup()
    attackFocus = nil
    Stats.State = "Entering Cursed Ship"

    local portal = findShipPortal()
    if portal then
        flyTo(CFrame.new(portal.Position), 6, 90)
        if not farming then return true end
        pcall(function()
            local r = root()
            firetouchinterest(r, portal, 0)
            task.wait(0.1)
            firetouchinterest(r, portal, 1)
        end)
        waitFor(function()
            local r = root()
            return r and (r.Position - K.SHIP_POS).Magnitude <= 2500
        end, 6)
    end

    local after = root()
    if after and (after.Position - K.SHIP_POS).Magnitude > 2500 then
        commF("requestEntrance", K.SHIP_POS)
        task.wait(1.5)
    end
    lastShipEnter = os.clock()
    return true
end

-- Auto Get Ghoul Race Fully: capitao -> (tocha) -> ectoplasma -> Experimic
local Ghoul = { phase = nil, ecto = 0, ectoKnown = false, lastInv = -100, probeAt = 0, lastBuy = -100,
    lastHop = -100, lastCaptainSeen = 0, since = os.clock(), warnedLevel = false }

function Ghoul.hasTorch()
    for _, container in ipairs({ Player.Backpack, Player.Character }) do
        if container then
            for _, tool in ipairs(container:GetChildren()) do
                if tool:IsA("Tool") and tool.Name:lower():find("hellfire", 1, true) then return true end
            end
        end
    end
    return false
end

function Ghoul.race()
    local ok, v = pcall(function() return Player.Data.Race.Value end)
    return ok and v or nil
end

function Ghoul.readEcto()
    if os.clock() - Ghoul.lastInv < 3 then return end
    Ghoul.lastInv = os.clock()
    local ok, inv = commF("getInventory")
    if not (ok and type(inv) == "table") then return end
    local total, known = 0, false
    for k, v in pairs(inv) do
        local name, count
        if type(v) == "table" then
            name = tostring(v.Name or v.name or v.Item or ""):lower()
            count = tonumber(v.Count or v.count or v.Amount or v.amount or v.Quantity or v.quantity) or 1
        elseif type(v) == "number" and type(k) == "string" then
            name, count = k:lower(), v
        end
        if name and name:find("ectoplasm", 1, true) then
            total += count
            known = true
        end
    end
    Ghoul.ecto, Ghoul.ectoKnown = total, known
end

function Ghoul.finish(reason)
    notify("Ghoul Race", reason, 8)
    pcall(function() Options.AutoGhoul:SetValue(false) end)
end

function Ghoul.update()
    local phase
    if Options.AutoGhoul and Options.AutoGhoul.Value and InSea2 then
        local lvl = 0
        pcall(function() lvl = Player.Data.Level.Value end)
        if Ghoul.race() == "Ghoul" then
            Ghoul.finish("Voce ja e Ghoul!")
        elseif lvl < K.GHOUL_LEVEL then
            Ghoul.finish("Precisa do level " .. K.GHOUL_LEVEL .. " (a tocha nao dropa antes)")
        else
            pcall(Ghoul.readEcto)
            if Ghoul.hasTorch() then
                if Ghoul.ecto >= K.GHOUL_ECTO or (not Ghoul.ectoKnown and os.clock() >= Ghoul.probeAt) then
                    phase = "buy"
                else
                    phase = "ecto"
                end
            else
                phase = "captain"
            end
        end
    end
    if phase ~= Ghoul.phase then
        Ghoul.phase = phase
        questId = nil
        clearGroup()
        attackFocus = nil
    end
end

-- compra a raca com o Experimic (100 Ectoplasma + Hellfire Torch)
function Ghoul.buy()
    if os.clock() - Ghoul.lastBuy < 6 then return end
    Ghoul.lastBuy = os.clock()
    if enterShip() then return end

    local npc = findNPCByName("Experimic")
    if npc then
        local okP, pivot = pcall(function() return npc:GetPivot() end)
        if okP then flyTo(CFrame.new(pivot.Position + Vector3.new(0, 3, 0)), 8, 60) end
    end
    if not farming then return end

    local _, checkRes = commF("Ectoplasm", "BuyCheck", 4)
    task.wait(0.5)
    local _, changeRes = commF("Ectoplasm", "Change", 4)
    task.wait(1.5)

    if Ghoul.race() == "Ghoul" then
        Ghoul.finish("Raca Ghoul comprada!")
    else
        Ghoul.probeAt = os.clock() + 90
        notify("Ghoul Race", (npc and "Experimic achado" or "Experimic nao achado")
            .. " | ectoplasma: " .. Ghoul.ecto .. " | " .. tostring(checkRes):sub(1, 40)
            .. " / " .. tostring(changeRes):sub(1, 40), 8)
    end
end

-- troca de server pelo __ServerBrowser do jogo: consulta as paginas 1 a 100 e entra no primeiro com vaga
function Ghoul.hop()
    if Ghoul.hopping or os.clock() - Ghoul.lastHop < 15 then return end
    Ghoul.hopping = true
    Ghoul.lastHop = os.clock()

    -- guarda que o Auto Ghoul estava ligado, para continuar no proximo server
    pcall(function()
        if writefile then writefile(K.GHOUL_FILE, "{\"active\":true}") end
    end)

    task.spawn(function()
        local remotes = ReplicatedStorage:FindFirstChild("Remotes")
        local browser = (remotes and remotes:FindFirstChild("__ServerBrowser"))
            or ReplicatedStorage:FindFirstChild("__ServerBrowser", true)
        if not browser then
            notify("Ghoul Race", "__ServerBrowser nao encontrado", 6)
            Ghoul.hopping = false
            return
        end

        -- cancela se desligou o Auto Ghoul ou se a tocha dropou (ela se perde ao sair do server)
        local function cancelled()
            return not (Options.AutoGhoul and Options.AutoGhoul.Value)
                or Ghoul.phase ~= "captain"
                or Ghoul.hasTorch()
        end

        local found = false
        for i = 1, 100 do
            if found or cancelled() then break end
            task.spawn(function()
                if found or cancelled() then return end
                local ok, result = pcall(function()
                    return browser:InvokeServer(i)
                end)
                if ok and type(result) == "table" then
                    for k, v in pairs(result) do
                        if not found and not cancelled()
                            and type(v) == "table" and v.Count and v.Count < 12 and k ~= game.JobId then
                            found = true
                            notify("Server Hop", "Server found! Teleporting...", 5)
                            pcall(function()
                                if not cancelled() then
                                    browser:InvokeServer("teleport", k)
                                end
                            end)
                        end
                    end
                end
            end)
        end

        task.wait(10)
        Ghoul.hopping = false
    end)
end

local function step()
    if traveling then return end

    Dough.update()
    Ghoul.update()
    local methodName = Ghoul.phase == "ecto" and "Ectoplasm"
        or Dough.phase == "cocoa" and "Cocoa"
        or Dough.phase == "kill" and "Katakuri"
        or Options.MethodFarm.Value
    local method = K.METHODS[methodName]
    local questMob = pickMob(method)
    local names = method.cake and K.CAKE_MOBS or allNames(method, questMob)
    questMobName = questMob.name

    local function prep()
        hover(true)
        enableHaki()
        equipWeapon()
    end

    if RaidCfg.AutoBuyChip and not (RaidCfg.Multi and (RaidCfg.Pay ~= RaidCfg.Secondary))
        and not hasChip() and not inRaidNow() and os.clock() - lastChipTry > 5 then
        lastChipTry = os.clock()
        prep()
        lastMode = "chip"
        Stats.State = "Buy Chip"
        buyChip()
        return
    end

    if SeaCfg.Auto and not multiRaidActive() then
        enableHaki()
        lastMode = "sea"
        Stats.State = "Sea Events"
        SeaCfg.run()
        return
    end

    if multiRaidActive() then
        prep()
        lastMode = "multiraid"
        Stats.State = "Multi Raid"
        runMultiRaid()
        return
    end

    local stackBoss, stackInWorld
    if Options.AttackDough.Value or (Options.AutoDough and Options.AutoDough.Value) then
        stackBoss, stackInWorld = findBossAny(K.DOUGH_BOSS)
    end
    if not stackBoss and Options.AttackRip.Value then
        stackBoss, stackInWorld = findBossAny(K.RIP_BOSS)
    end
    if not stackBoss and Options.KillSoulReaper.Value then
        stackBoss, stackInWorld = findBossAny(K.SOUL_BOSS)
    end
    if not stackBoss and Options.AttackDarkbeard and Options.AttackDarkbeard.Value then
        stackBoss, stackInWorld = findBossAny(K.DARKBEARD)
    end
    if stackBoss then
        prep()
        lastMode = "stack"
        Stats.State = table.find(K.DOUGH_BOSS, stackBoss.Name) and "Dough King"
            or table.find(K.SOUL_BOSS, stackBoss.Name) and "Soul Reaper"
            or table.find(K.DARKBEARD, stackBoss.Name) and "Darkbeard" or "Rip Indra"
        -- Dough King fica dentro do portal (igual ao Katakuri v1): longe demais, entra pelo espelho
        if table.find(K.DOUGH_BOSS, stackBoss.Name) then
            local hrp, bossRoot = root(), stackBoss:FindFirstChild("HumanoidRootPart")
            if hrp and bossRoot and (bossRoot.Position - hrp.Position).Magnitude > 1500 then
                clearGroup()
                reachBoss()
                return
            end
        end
        handleStackBoss(stackBoss, stackInWorld)
        return
    end

    if Options.SummonSoulReaper.Value and not findEnemy(K.SOUL_BOSS, true) then
        local essence = findEssence()
        if essence then
            hover(true)
            lastMode = "summon"
            Stats.State = "Summon Soul Reaper"
            handleSummon(essence)
            return
        end
    end

    if Options.CastleRaid.Value and raidActive() then
        prep()
        lastMode = "raid"
        Stats.State = "Pirate Raid"
        handleRaid()
        return
    end

    if Options.AutoFactory and Options.AutoFactory.Value then
        local core, coreInWorld = findBossAny(K.FACTORY_BOSS)
        if core then
            prep()
            lastMode = "factory"
            Stats.State = "Factory Core"
            handleStackBoss(core, coreInWorld)
            return
        end
    end

    if Ghoul.phase == "buy" then
        hover(true)
        lastMode = "ghoul"
        Stats.State = "Ghoul: comprando a raca"
        clearGroup()
        attackFocus = nil
        Ghoul.buy()
        return
    end

    if Ghoul.phase == "captain" then
        lastMode = "ghoul"
        if enterShip() then return end
        local captain, captainInWorld = findBossAny(K.CURSED_CAPTAIN)
        if captain then
            Ghoul.lastCaptainSeen = os.clock()
            prep()
            Stats.State = "Ghoul: Cursed Captain"
            handleStackBoss(captain, captainInWorld)
            return
        end
        -- sem capitao: espera 3s (carregar / dropar) e troca de server
        hover(true)
        clearGroup()
        attackFocus = nil
        target = nil
        if os.clock() - math.max(Ghoul.since, Ghoul.lastCaptainSeen, lastShipEnter) > 3 then
            Stats.State = "Ghoul: trocando de server"
            Ghoul.hop()
        else
            Stats.State = "Ghoul: procurando Cursed Captain"
        end
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

    -- com o Auto Dough King ligado e algum calice (de Deus ou doce), nao farma elite boss
    if Options.EliteHunter.Value and not Dough.phase then
        local eBoss, inWorld = findBossAny(K.ELITE_BOSSES)
        if eBoss then
            prep()
            lastMode = "elite"
            Stats.State = "Elite Hunter"
            handleElite(eBoss, inWorld)
            return
        end
    end

    if Dough.phase == "craft" then
        hover(true)
        lastMode = "dough"
        Stats.State = "Crafting Sweet Chalice"
        clearGroup()
        attackFocus = nil
        Dough.craft()
        return
    end

    if not (Options.Farm.Value or Dough.phase or Ghoul.phase == "ecto") or (method.cake and not InSea3) or (method.sea2 and not InSea2) then
        idle()
        if Options.AutoDough and Options.AutoDough.Value then
            Stats.State = InSea3 and "Dough King: sem calice detectado" or "Dough King: so funciona no Sea 3"
        end
        return
    end

    if methodName == "Tyrant Of The Skies" then
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

    if method.ship and enterShip() then
        return
    end

    Stats.State = "Farming " .. methodName
    if Dough.phase == "cocoa" then
        Stats.State = "Dough King: Cocoa " .. Dough.cocoa .. "/" .. K.COCOA_NEEDED .. (Dough.cocoaKnown and "" or " (inventario sem cocoa)")
    elseif Dough.phase == "kill" then
        Stats.State = "Dough King: Katakuri npcs"
    end
    if Ghoul.phase == "ecto" then
        Stats.State = "Ghoul: Ectoplasm " .. Ghoul.ecto .. "/" .. K.GHOUL_ECTO
    end

    if method.cake then
        if katakuri() then return end
    else
        local boss = findEnemy(method.bosses, true)
        if boss then
            Stats.State = methodName .. " Boss"
            clearGroup()
            attackFocus = boss
            target = CFrame.new(boss.HumanoidRootPart.Position + Vector3.new(0, K.HOVER_HEIGHT, 0))
            click()
            return
        end
    end

    if Options.GetQuest.Value and not method.noQuest then
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
        target = CFrame.new(BringPoint + Vector3.new(0, K.HOVER_HEIGHT, 0))
        click()
        return
    end

    local enemy = (Options.GetQuest.Value and not method.noQuest and findEnemy({ questMob.name })) or findEnemy(names)
    if enemy then
        local hrp = root()
        local hoverPos = enemy.HumanoidRootPart.Position + Vector3.new(0, K.HOVER_HEIGHT, 0)
        attackFocus = enemy
        target = CFrame.new(hoverPos)

        if hrp and (hrp.Position - hoverPos).Magnitude > 50 then
            click()
            return
        end

        buildBringGroup(enemy, names)
        target = CFrame.new(BringPoint + Vector3.new(0, K.HOVER_HEIGHT, 0))
        click()
        return
    end

    clearGroup()
    attackFocus = nil
    local unloaded = findUnloaded(names)
    local pos = unloaded and unloaded.HumanoidRootPart.Position or findSpawn(names, method.cake and K.MOB_AREAS or method.area)
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
    local dest = K.ISLANDS[Options.TravelIsland.Value]
    if not dest then return end

    travelId += 1
    local id = travelId
    traveling = true
    clearGroup()
    attackFocus = nil

    task.spawn(function()
        hover(true)

        -- Cursed Ship: vai ate o portal e entra por ele (se falhar, pede a entrada direto ao jogo)
        if InSea2 and Options.TravelIsland.Value == "Cursed Ship" then
            local portal = findShipPortal()
            if portal then
                target = CFrame.new(portal.Position)
                local t0 = os.clock()
                while traveling and travelId == id and root()
                    and (root().Position - portal.Position).Magnitude > 6 and os.clock() - t0 < 90 do
                    task.wait()
                end
                if traveling and travelId == id then
                    pcall(function()
                        local r = root()
                        firetouchinterest(r, portal, 0)
                        task.wait(0.1)
                        firetouchinterest(r, portal, 1)
                    end)
                    local t1 = os.clock()
                    while traveling and travelId == id and os.clock() - t1 < 6 do
                        local r = root()
                        if r and (r.Position - K.SHIP_POS).Magnitude <= 2500 then break end
                        task.wait(0.2)
                    end
                end
            end
            local r = root()
            if traveling and travelId == id and r and (r.Position - K.SHIP_POS).Magnitude > 2500 then
                commF("requestEntrance", K.SHIP_POS)
                task.wait(1.5)
            end
        end

        if not (traveling and travelId == id) then return end
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
    Values = InSea2 and { "Ectoplasm" } or { "Katakuri", "Bones", "Tyrant Of The Skies" },
    Multi = false,
    Default = 1,
})

Main:AddToggle("Farm", { Title = "Farm", Default = false })
Main:AddToggle("GetQuest", { Title = "Get Quest", Default = false })
if not InSea2 then
    Main:AddToggle("RandomBone", { Title = "Auto Random Bone", Default = false })
end
Main:AddToggle("RandomFruit", { Title = "Auto Random Fruit", Default = false })
if InSea2 then
    Main:AddToggle("CollectFruits", { Title = "Auto Get Fruit", Default = false })
    Main:AddToggle("AutoFactory", { Title = "Auto Factory", Default = false })
else
    Main:AddToggle("EliteHunter", { Title = "Auto Elite Hunter", Default = false })
    Main:AddToggle("CastleRaid", { Title = "Auto Castle Raid", Default = false })
    Main:AddToggle("CollectFruits", { Title = "Auto Collect Fruits", Default = false })
end
Main:AddToggle("StoreFruits", { Title = "Auto Store Fruits", Default = false })

if InSea2 then
    Stack:AddToggle("AttackDarkbeard", { Title = "Attack Darkbeard", Default = false })
    Stack:AddToggle("AutoGhoul", { Title = "Auto Get Ghoul Race Fully", Default = false })
else
    Stack:AddToggle("AttackRip", { Title = "Auto Attack Rip Indra", Default = false })
    Stack:AddToggle("AttackDough", { Title = "Auto Attack Dough King", Default = false })
    Stack:AddToggle("AutoDough", { Title = "Auto Dough King", Default = false })
    Stack:AddToggle("SummonSoulReaper", { Title = "Auto Summon Soul Reaper", Default = false })
    Stack:AddToggle("KillSoulReaper", { Title = "Auto Kill Soul Reaper", Default = false })
end

-- no Sea 2 as opcoes do Sea 3 nao existem; o resto do codigo le Options.X, entao entram valores desligados
if InSea2 then
    for _, name in ipairs({
        "RandomBone", "EliteHunter", "CastleRaid",
        "AttackRip", "AttackDough", "AutoDough", "SummonSoulReaper", "KillSoulReaper",
    }) do
        Options[name] = { Value = false, Stub = true, OnChanged = function() end, SetValue = function() end }
    end
end

if not InSea2 then
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
                if findEnemy(K.METHODS["Tyrant Of The Skies"].bosses, true) then return "Spawned" end
                return Tyrant.eyes() .. "/4"
            end)
            setText(tyrantLabel, okT and tyrText or "0/4")
        end
    end)
end

LocalTab:AddDropdown("Team", {
    Title = "Team",
    Values = { "Marine", "Pirate" },
    Multi = false,
    Default = 1,
})

do
    local TEAMS = { Marine = "Marines", Pirate = "Pirates" }

    local function joinTeam()
        for _ = 1, 5 do
            if Player.Team then return end
            commF("SetTeam", TEAMS[Options.Team.Value])
            task.wait(2)
        end
    end

    task.spawn(function()
        task.wait(2)
        joinTeam()
    end)
    Options.Team:OnChanged(function()
        task.spawn(joinTeam)
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
    Values = K.ISLAND_NAMES,
    Multi = false,
    Default = 1,
})

Travel:AddButton({
    Title = "Start Traveling",
    Callback = startTraveling,
})

Travel:AddButton({
    Title = "Stop Tween",
    Callback = function()
        travelId += 1
        traveling = false
        target = nil
        if not farming then hover(false) end
    end,
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

Settings:AddToggle("AutoClick", { Title = "Auto Click", Default = false })

Settings:AddDropdown("SelectWeapon", {
    Title = "Select Weapon",
    Values = { "Melee", "Sword", "Fruit(M1)" },
    Multi = false,
    Default = 1,
}):OnChanged(function(value)
    K.HOVER_HEIGHT = value == "Fruit(M1)" and 15 or 20
end)

Settings:AddDropdown("BringCount", {
    Title = "Mobs In Bring Mob",
    Values = { "1", "2", "3", "4", "5" },
    Multi = false,
    Default = 2,
})

local function refresh()
    local want = Options.Farm.Value or Options.EliteHunter.Value or Options.CastleRaid.Value or Options.CollectFruits.Value
        or Options.AttackRip.Value or Options.AttackDough.Value or Options.SummonSoulReaper.Value
        or Options.KillSoulReaper.Value or Options.AutoDough.Value
        or (Options.AutoFactory and Options.AutoFactory.Value) or (Options.AttackDarkbeard and Options.AttackDarkbeard.Value)
        or (Options.AutoGhoul and Options.AutoGhoul.Value)
        or multiRaidActive() or RaidCfg.AutoBuyChip or SeaCfg.Auto
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
Options.SummonSoulReaper:OnChanged(refresh)
Options.KillSoulReaper:OnChanged(refresh)
Options.AutoDough:OnChanged(refresh)
if Options.AutoFactory then Options.AutoFactory:OnChanged(refresh) end
if Options.AttackDarkbeard then Options.AttackDarkbeard:OnChanged(refresh) end
if Options.AutoGhoul then
    Options.AutoGhoul:OnChanged(function(value)
        if not value then
            pcall(function()
                if isfile and delfile and isfile(K.GHOUL_FILE) then delfile(K.GHOUL_FILE) end
            end)
        end
        refresh()
    end)
end

local function playerNames()
    local list = {}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= Player then table.insert(list, p.Name) end
    end
    table.sort(list)
    return list
end

if not InSea2 then
RaidTab:AddDropdown("RaidChip", {
    Title = "Chip da Raid",
    Values = RaidTypes,
    Multi = false,
    Default = 2,
}):OnChanged(function(value)
    RaidCfg.Chip = value
end)

RaidTab:AddToggle("AutoRaid", {
    Title = "Auto Raid",
    Default = false,
}):OnChanged(function(value)
    RaidCfg.Auto = value
    RaidArmed = false
    refresh()
end)

RaidTab:AddToggle("AutoBuyChip", {
    Title = "Auto Buy Chip",
    Default = false,
}):OnChanged(function(value)
    RaidCfg.AutoBuyChip = value
    refresh()
end)

RaidTab:AddToggle("AutoUnstoreFruit", {
    Title = "Auto Unstore Fruit Under 1M",
    Default = false,
}):OnChanged(function(value)
    RaidCfg.AutoUnstore = value
end)

local MultiRaidSection = RaidTab:AddSection("Multi Raid")

local RaidDropdown = MultiRaidSection:AddDropdown("RaidPlayers", {
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

MultiRaidSection:AddButton({
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

MultiRaidSection:AddToggle("MultiRaid", {
    Title = "Multi Raid",
    Default = false,
}):OnChanged(function(value)
    RaidCfg.Multi = value
    RaidArmed = false
    refresh()
end)

MultiRaidSection:AddToggle("RaidPay", {
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

MultiRaidSection:AddToggle("RaidSecondary", {
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

MultiRaidSection:AddDropdown("RaidGuestSlot", {
    Title = "Slot da Conta Secundaria",
    Values = { "1", "2", "3" },
    Multi = false,
    Default = 1,
}):OnChanged(function(value)
    RaidCfg.GuestSlot = tonumber(value) or 1
end)
end

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

-- Save Settings / Reset Settings
do
    local HttpService = game:GetService("HttpService")
    local SAVE_FILE = "BatmanHub_Settings.json"
    local SKIP = { RaidPlayers = true } -- lista de jogadores muda a cada servidor

    local function copyValue(v)
        if type(v) ~= "table" then return v end
        local c = {}
        for k, x in pairs(v) do c[k] = x end
        return c
    end

    -- valores de fabrica = o que cada opcao tem agora, antes de carregar qualquer save
    local Defaults = {}
    for name, opt in pairs(Options) do
        if not SKIP[name] and not opt.Stub and opt.Value ~= nil then
            Defaults[name] = copyValue(opt.Value)
        end
    end

    local function applyValues(values)
        for name, v in pairs(values) do
            local opt = Options[name]
            local valid = opt ~= nil and Defaults[name] ~= nil
            if valid and type(v) == "string" and type(opt.Values) == "table" and not table.find(opt.Values, v) then
                valid = false
            end
            if valid then
                pcall(function() opt:SetValue(copyValue(v)) end)
            end
        end
    end

    local function saveSettings()
        if not writefile then
            notify("Settings", "Este executor nao suporta salvar arquivos", 5)
            return
        end
        local data = {}
        if isfile and readfile and isfile(SAVE_FILE) then
            local okOld, old = pcall(function()
                return HttpService:JSONDecode(readfile(SAVE_FILE))
            end)
            if okOld and type(old) == "table" then data = old end
        end
        for name in pairs(Defaults) do
            data[name] = copyValue(Options[name].Value)
        end
        local ok, err = pcall(function()
            writefile(SAVE_FILE, HttpService:JSONEncode(data))
        end)
        if ok then
            notify("Settings", "Configuracoes salvas!", 3)
        else
            notify("Settings", "Erro ao salvar: " .. short(err), 5)
        end
    end

    local function loadSettings()
        if not (isfile and readfile and isfile(SAVE_FILE)) then return end
        local ok, data = pcall(function()
            return HttpService:JSONDecode(readfile(SAVE_FILE))
        end)
        if ok and type(data) == "table" then
            applyValues(data)
        end
    end

    local function resetSettings()
        applyValues(Defaults)
        if isfile and delfile then
            pcall(function()
                if isfile(SAVE_FILE) then delfile(SAVE_FILE) end
            end)
        end
        notify("Settings", "Configuracoes de fabrica restauradas!", 3)
    end

    Settings:AddButton({
        Title = "Save Settings",
        Callback = saveSettings,
    })

    Settings:AddButton({
        Title = "Reset Settings",
        Callback = resetSettings,
    })

    loadSettings()
end

-- depois de trocar de server, continua o Auto Ghoul se ele estava ligado
if InSea2 and Options.AutoGhoul and isfile and isfile(K.GHOUL_FILE) then
    pcall(function() Options.AutoGhoul:SetValue(true) end)
end

Window:SelectTab(1)
