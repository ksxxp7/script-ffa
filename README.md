local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "Kronix scripts",
   Icon = 0, -- Icon in Topbar. Can use Lucide Icons (string) or Roblox Image (number). 0 to use no icon (default).
   LoadingTitle = "Welcome to Kronix scripts",
   LoadingSubtitle = "by Kronix",
   Theme = "Ocean", -- Check https://docs.sirius.menu/rayfield/configuration/themes

   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false, -- Prevents Rayfield from warning when the script has a version mismatch with the interface

   ConfigurationSaving = {
      Enabled = true,
      FolderName = nil, -- Create a custom folder for your hub/game
      FileName = "Portal hub"
   },

   telegram = {
      Enabled = true, -- Prompt the user to join your Discord server if their executor supports it
      Invite = "invitelink", -- The telegram invite code, do not include t.me/Portal_hub_bugs E.g. t.me/Portal_hub_bugs would be ABCD
      RememberJoins = true -- Set this to false to make them join the telegram every time they load it up
   },

   KeySystem = false, -- Set this to true to use our key system
   KeySettings = {
      Title = "Untitled",
      Subtitle = "Key System",
      Note = "No method of obtaining the key is provided", -- Use this to tell the user how to get a key
      FileName = "Key", -- It is recommended to use something unique as other scripts using Rayfield may overwrite your key file
      SaveKey = true, -- The user's key will be saved, but if you change the key, they will be unable to use your script
      GrabKeyFromSite = false, -- If this is true, set Key below to the RAW site you would like Rayfield to get the key from
      Key = {"Hello"} -- List of keys that will be accepted by the system, can be RAW file links (pastebin, github etc) or simple strings ("hello","key22")
   }
})

local MainTab = Window:CreateTab("Main", 4483362458) -- Title, Image

local Button = MainTab:CreateButton({
   Name = "AimBot",
   Callback = function()
-- Variables
local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local range = 100 -- Maximum range for aimlock
local targetPart = "Head" -- Part of the character to lock onto

-- Function to get the closest visible target
local function getClosestTarget()
    local closestTarget = nil
    local shortestDistance = range

    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer ~= player and otherPlayer.Character and otherPlayer.Character:FindFirstChild(targetPart) then
            local target = otherPlayer.Character[targetPart]
            local distance = (player.Character.HumanoidRootPart.Position - target.Position).Magnitude
            if distance < shortestDistance then
                closestTarget = target
                shortestDistance = distance
            end
        end
    end

    return closestTarget
end

-- Function to lock the camera onto the target
local function lockCamera(target)
    if target then
        camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
    end
end

-- Update the camera every frame
game:GetService("RunService").RenderStepped:Connect(function()
    local target = getClosestTarget()
    if target then
        lockCamera(target)
    end
end)

-- Output feedback
print("Camera Aimlock script is active.")   
   end,
})

local Button = MainTab:CreateButton({
   Name = "safe mod",
   Callback = function()
-- Safe Moderation Script
local Players = game:GetService("Players")
local badWords = {"badword1", "badword2", "exampleword"} -- Add bad words here

-- Function to check chat for bad words
local function checkChat(player, message)
    for _, badWord in ipairs(badWords) do
        if string.find(string.lower(message), string.lower(badWord)) then
            -- Warn the player
            warn(player.Name .. " used inappropriate language!")
            
            -- Optionally kick the player for bad language
            player:Kick("You have been kicked for using inappropriate language.")
            break
        end
    end
end

-- Connect chat detection
Players.PlayerAdded:Connect(function(player)
    player.Chatted:Connect(function(message)
        checkChat(player, message)
    end)
end)

-- Function to moderate player actions (example: detecting speed hacks)
local function monitorPlayer(player)
    player.CharacterAdded:Connect(function(character)
        local humanoid = character:WaitForChild("Humanoid")
        
        while humanoid do
            if humanoid.WalkSpeed > 16 then -- Default WalkSpeed in Roblox is 16
                warn(player.Name .. " is suspected of speed hacking!")
                
                -- Optionally kick the player for cheating
                player:Kick("You have been kicked for using unauthorized modifications.")
                break
            end
            wait(1) -- Check every second
        end
    end)
end

-- Connect player monitoring
Players.PlayerAdded:Connect(function(player)
    monitorPlayer(player)
end)   
   end,
})

local Button = MainTab:CreateButton({
   Name = "Esp",
   Callback = function()
-- Variables
local players = game:GetService("Players")
local localPlayer = players.LocalPlayer
local camera = workspace.CurrentCamera

-- Table to store ESP data
local espData = {}

-- Function to create ESP elements
function createESP(player)
    local billboardGui = Instance.new("BillboardGui")
    billboardGui.Name = "ESP"
    billboardGui.AlwaysOnTop = true
    billboardGui.Size = UDim2.new(0, 200, 0, 50)
    billboardGui.StudsOffset = Vector3.new(0, 3, 0)

    local frame = Instance.new("Frame", billboardGui)
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.BackgroundTransparency = 1
    frame.BorderSizePixel = 0

    local textLabel = Instance.new("TextLabel", frame)
    textLabel.Size = UDim2.new(1, 0, 0.5, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    textLabel.TextStrokeTransparency = 0.5
    textLabel.Text = "Loading..."

    local healthBar = Instance.new("Frame", frame)
    healthBar.Size = UDim2.new(0.5, 0, 0.1, 0)
    healthBar.Position = UDim2.new(0.25, 0, 0.6, 0)
    healthBar.BackgroundColor3 = Color3.fromRGB(0, 255, 0)

    local line = Drawing.new("Line")
    line.Thickness = 1
    line.Color = Color3.fromRGB(255, 255, 255)
    line.Visible = true

    local box = Drawing.new("Square")
    box.Thickness = 2
    box.Color = Color3.fromRGB(255, 0, 0)
    box.Filled = false
    box.Visible = true

    return billboardGui, textLabel, healthBar, line, box
end

-- Update ESP elements
function updateESP(player, esp, textLabel, healthBar, line, box)
    local character = player.Character
    if character and character:FindFirstChild("HumanoidRootPart") then
        local humanoid = character:FindFirstChild("Humanoid")
        local rootPart = character:FindFirstChild("HumanoidRootPart")
        local distance = (localPlayer.Character.HumanoidRootPart.Position - rootPart.Position).Magnitude

        esp.Adornee = rootPart
        textLabel.Text = string.format("Name: %s\nDistance: %.0f", player.Name, distance)

        if humanoid then
            healthBar.Size = UDim2.new(humanoid.Health / humanoid.MaxHealth, 0, 0.1, 0)
        end

        -- Update line
        local screenPos, onScreen = camera:WorldToViewportPoint(rootPart.Position)
        local localScreenPos, localOnScreen = camera:WorldToViewportPoint(localPlayer.Character.HumanoidRootPart.Position)
        if onScreen and localOnScreen then
            line.From = Vector2.new(localScreenPos.X, localScreenPos.Y)
            line.To = Vector2.new(screenPos.X, screenPos.Y)
            line.Visible = true
        else
            line.Visible = false
        end

        -- Update box
        local minX, minY, maxX, maxY = math.huge, math.huge, -math.huge, -math.huge
        for _, part in pairs(character:GetChildren()) do
            if part:IsA("BasePart") then
                local partPos, partVisible = camera:WorldToViewportPoint(part.Position)
                if partVisible then
                    minX = math.min(minX, partPos.X)
                    minY = math.min(minY, partPos.Y)
                    maxX = math.max(maxX, partPos.X)
                    maxY = math.max(maxY, partPos.Y)
                end
            end
        end

        if minX < math.huge then
            box.Position = Vector2.new(minX, minY)
            box.Size = Vector2.new(maxX - minX, maxY - minY)
            box.Visible = true
        else
            box.Visible = false
        end
    else
        esp:Destroy()
        line.Visible = false
        box.Visible = false
    end
end

-- Main
for _, player in ipairs(players:GetPlayers()) do
    if player ~= localPlayer then
        local esp, textLabel, healthBar, line, box = createESP(player)
        esp.Parent = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
        
        espData[player] = {esp, textLabel, healthBar, line, box}

        game:GetService("RunService").RenderStepped:Connect(function()
            updateESP(player, esp, textLabel, healthBar, line, box)
        end)
    end
end

players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        local esp, textLabel, healthBar, line, box = createESP(player)
        esp.Parent = player.Character:FindFirstChild("HumanoidRootPart")
        
        espData[player] = {esp, textLabel, healthBar, line, box}

        game:GetService("RunService").RenderStepped:Connect(function()
            updateESP(player, esp, textLabel, healthBar, line, box)
        end)
    end)
end)

players.PlayerRemoving:Connect(function(player)
    if espData[player] then
        espData[player][4]:Remove() -- Remove the line
        espData[player][5]:Remove() -- Remove the box
        espData[player] = nil
    end
end)   
   end,
})

local Button = MainTab:CreateButton({
   Name = "Inf ammo not working",
   Callback = function()
-- Infinite Ammo Script
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()

-- Function to give infinite ammo to all tools
local function enableInfiniteAmmo(tool)
    if tool:IsA("Tool") and tool:FindFirstChild("Ammo") then
        -- Override the ammo property
        tool.Ammo.Changed:Connect(function()
            tool.Ammo.Value = math.huge -- Set ammo to infinite
        end)

        -- Ensure ammo is always max
        tool.Ammo.Value = math.huge
    end
end

-- Monitor the character's tools
local function monitorTools()
    for _, tool in ipairs(Character:GetChildren()) do
        enableInfiniteAmmo(tool)
    end

    -- Detect new tools added
    Character.ChildAdded:Connect(function(child)
        if child:IsA("Tool") then
            enableInfiniteAmmo(child)
        end
    end)
end

-- Ensure the script runs when the character spawns
if Character then
    monitorTools()
end

LocalPlayer.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    monitorTools()
end)

print("[Infinite Ammo Script] Loaded. All tools have infinite ammo.")   
   end,
})

local Button = MainTab:CreateButton({
   Name = "Auto Fire",
   Callback = function()
-- Variables
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local camera = workspace.CurrentCamera
local fireRate = 0.2 -- Seconds between each shot
local range = 50 -- Firing range in studs
local tool = player.Backpack:FindFirstChild("Gun") or player.Character:FindFirstChild("Gun") -- Replace "Gun" with the name of your tool
local canShoot = true

-- Function to find the closest target
function getClosestTarget()
    local closestTarget = nil
    local shortestDistance = range

    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer ~= player and otherPlayer.Character and otherPlayer.Character:FindFirstChild("Humanoid") then
            local humanoidRootPart = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                local distance = (humanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
                if distance < shortestDistance then
                    shortestDistance = distance
                    closestTarget = humanoidRootPart
                end
            end
        end
    end

    return closestTarget
end

-- Function to fire the weapon
function fireAtTarget(target)
    if tool and canShoot then
        canShoot = false

        -- Aim at the target (optional, adjust depending on your game's aiming system)
        camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)

        -- Fire the weapon (adjust depending on your game's firing system)
        if tool:FindFirstChild("Fire") then
            tool.Fire:FireServer(target.Position) -- Example for RemoteEvent firing
        elseif tool:FindFirstChild("Activate") then
            tool:Activate() -- Activates the tool (default behavior for Tools)
        end

        -- Reset firing cooldown
        task.wait(fireRate)
        canShoot = true
    end
end

-- Main loop
game:GetService("RunService").RenderStepped:Connect(function()
    local target = getClosestTarget()
    if target then
        fireAtTarget(target)
    end
end)   
   end,
})
