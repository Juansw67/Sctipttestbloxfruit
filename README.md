local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()
 
local Window = Fluent:CreateWindow({
    Title = "Syphon Hub Blox Fruits",
    SubTitle = "by nameless_.5",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true, -- The blur may be detectable, setting this to false disables blur entirely
    Theme = "Dark",
    MinimizeKey = Enum.KeyCode.LeftControl -- Used when theres no MinimizeKeybind
})
 
--Fluent provides Lucide Icons https://lucide.dev/icons/ for the tabs, icons are optional
local Tabs = {
	Discord = Window:AddTab({ Title = "Discord", Icon = "message-circle" }),
    Main = Window:AddTab({ Title = "Main", Icon = "signal" }),
    Game = Window:AddTab({ Title = "Game", Icon = "gamepad" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}
 
local Options = Fluent.Options
 
do
    Fluent:Notify({
        Title = "Syphon Hub",
        Content = "Thank you for using Syphon Hub!",
        SubContent = "Join our Discord for support", -- Optional
        Duration = 5 -- Set to nil to make the notification not disappear
    })
end
 
 
    Tabs.Main:AddParagraph({
        Title = "Syphon Hub",
        Content = "Thank you for using Syphon Hub. I hope you have a great experience."
    })
 
 
 
-- DISCORD TAB
 
    Tabs.Discord:AddButton({
        Title = "Discord",
        Description = "Copy our Discord's server link to your clipboard",
        Callback = function()
		local link = "discord.gg/C9Qwf6UxG5"
    if setclipboard then
        setclipboard(link)
        print("Link copied to clipboard!")
    else
        warn("Clipboard function not available in this executor.")
    end
        end
    })
 
 
 
-- MAIN TAB
 
 
 
    local Slider = Tabs.Main:AddSlider("Walkspeed", {
        Title = "Walkspeed",
        Description = "Change your walkspeed",
        Default = 16,
        Min = 16,
        Max = 500,
        Rounding = 1,
        Callback = function(Value)
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value
        end
    })
 
    local Slider = Tabs.Main:AddSlider("JumpPower", {
        Title = "Jump Power",
        Description = "Change your jump power",
        Default = 50,
        Min = 50,
        Max = 500,
        Rounding = 1,
        Callback = function(Value)
            game.Players.LocalPlayer.Character.Humanoid.JumpPower = Value
        end
    })
 
 
 
    local Slider = Tabs.Main:AddSlider("Quick TP", {
        Title = "Quick TP",
        Description = "Quickly teleport using the F key",
        Default = 0,
        Min = 0,
        Max = 5,
        Rounding = 1,
        Callback = function(Value)
            local player = game.Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")
local teleportDistance = Value -- Adjust the teleport distance (Lower values for precise movement)
 
-- Function to teleport player forward
local function teleportForward()
    local character = player.Character
    if character then
        local rootPart = character:FindFirstChild("HumanoidRootPart")
        if rootPart then
            -- Move forward but keep it gradual
            local newPosition = rootPart.Position + (rootPart.CFrame.LookVector * teleportDistance)
            rootPart.CFrame = CFrame.new(newPosition, newPosition + rootPart.CFrame.LookVector) -- Keeps rotation natural
        end
    end
end
 
-- Detect key press (F key)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.F then
        teleportForward()
    end
end)
        end
    })
 
 
 
    Tabs.Main:AddButton({
        Title = "ESP",
        Description = "You can toggle it with T",
        Callback = function()
            local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
 
local ESP_ENABLED = true
local TOGGLE_KEY = Enum.KeyCode.T
local ESP_COLOR = Color3.fromRGB(255, 255, 255)
local ESP_THICKNESS = 2
local ESP_OPACITY = 0.8
local BOX_FILLED = false
local SHOW_NAMES = false
local SHOW_HEALTH = false
local SHOW_DISTANCE = false
local NAME_SIZE = 16
local NAME_FONT = "Monospace"
local NAME_OUTLINE_COLOR = Color3.fromRGB(0, 0, 0)
 
local espObjects = {}
local connections = {}
 
local function calculateDistance(position1, position2)
    return (position1 - position2).Magnitude
end
 
local function abbreviateNumber(number)
    local abbreviations = {
        ["K"] = 10^3,
        ["M"] = 10^6,
        ["B"] = 10^9,
        ["T"] = 10^12
    }
 
    for suffix, value in pairs(abbreviations) do
        if number >= value then
            return string.format("%.1f%s", number/value, suffix)
        end
    end
    return tostring(math.floor(number))
end
 
local function createESP(player)
    if not player or not player.Character then return end
 
    local character = player.Character
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    local head = character:FindFirstChild("Head")
    local rootPart = character:FindFirstChild("HumanoidRootPart")
 
    if not humanoid or not head or not rootPart then return end
 
    local esp = {
        box = Drawing.new("Square"),
        name = Drawing.new("Text"),
        health = Drawing.new("Text"),
        distance = Drawing.new("Text")
    }
 
    esp.box.Visible = false
    esp.box.Color = ESP_COLOR
    esp.box.Thickness = ESP_THICKNESS
    esp.box.Filled = BOX_FILLED
    esp.box.Transparency = ESP_OPACITY
 
    esp.name.Visible = SHOW_NAMES
    esp.name.Color = ESP_COLOR
    esp.name.Outline = true
    esp.name.OutlineColor = NAME_OUTLINE_COLOR
    esp.name.Size = NAME_SIZE
    esp.name.Font = Drawing.Fonts[NAME_FONT]
    esp.name.Text = "@"..player.Name
 
    esp.health.Visible = SHOW_HEALTH
    esp.health.Color = ESP_COLOR
    esp.health.Outline = true
    esp.health.OutlineColor = NAME_OUTLINE_COLOR
    esp.health.Size = NAME_SIZE
    esp.health.Font = Drawing.Fonts[NAME_FONT]
 
    esp.distance.Visible = SHOW_DISTANCE
    esp.distance.Color = ESP_COLOR
    esp.distance.Outline = true
    esp.distance.OutlineColor = NAME_OUTLINE_COLOR
    esp.distance.Size = NAME_SIZE
    esp.distance.Font = Drawing.Fonts[NAME_FONT]
 
    espObjects[player] = esp
 
    connections[player] = character.AncestryChanged:Connect(function(_, parent)
        if not parent then
            if espObjects[player] then
                for _, obj in pairs(espObjects[player]) do
                    if obj then obj:Remove() end
                end
                espObjects[player] = nil
            end
            if connections[player] then
                connections[player]:Disconnect()
                connections[player] = nil
            end
        end
    end)
end
 
local function updateESP()
    for player, esp in pairs(espObjects) do
        if not player or not player.Character then
            for _, obj in pairs(esp) do
                if obj then obj:Remove() end
            end
            espObjects[player] = nil
            if connections[player] then
                connections[player]:Disconnect()
                connections[player] = nil
            end
        else
            local character = player.Character
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            local head = character:FindFirstChild("Head")
            local rootPart = character:FindFirstChild("HumanoidRootPart")
 
            if humanoid and head and rootPart then
                local headPos, headVisible = Workspace.CurrentCamera:WorldToViewportPoint(head.Position)
                local rootPos = Workspace.CurrentCamera:WorldToViewportPoint(rootPart.Position)
 
                if headVisible then
 
                    local topPos = Workspace.CurrentCamera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))
                    local bottomPos = Workspace.CurrentCamera:WorldToViewportPoint(rootPart.Position - Vector3.new(0, 3, 0))
 
                    local boxSize = Vector2.new(2350 / rootPos.Z, topPos.Y - bottomPos.Y)
                    local boxPosition = Vector2.new(rootPos.X - boxSize.X / 2, rootPos.Y - boxSize.Y / 2)
 
                    esp.box.Size = boxSize
                    esp.box.Position = boxPosition
                    esp.box.Visible = ESP_ENABLED
 
                    esp.name.Position = Vector2.new(rootPos.X, rootPos.Y + boxSize.Y / 2 - 25)
                    esp.name.Visible = ESP_ENABLED and SHOW_NAMES
 
                    esp.health.Text = string.format("[%s%%]", math.floor(humanoid.Health))
                    esp.health.Position = Vector2.new(rootPos.X, headPos.Y)
                    esp.health.Visible = ESP_ENABLED and SHOW_HEALTH
 
                    local localPlayer = Players.LocalPlayer
                    if localPlayer and localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart") then
                        local distance = calculateDistance(
                            rootPart.Position,
                            localPlayer.Character.HumanoidRootPart.Position
                        )
                        esp.distance.Text = string.format("[%sm]", abbreviateNumber(distance))
                        esp.distance.Position = Vector2.new(rootPos.X, rootPos.Y)
                        esp.distance.Visible = ESP_ENABLED and SHOW_DISTANCE
                    end
                else
 
                    esp.box.Visible = false
                    esp.name.Visible = false
                    esp.health.Visible = false
                    esp.distance.Visible = false
                end
            end
        end
    end
end
 
local function initializeESP()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= Players.LocalPlayer then
            createESP(player)
        end
    end
end
 
local function setupConnections()
    Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function(character)
            createESP(player)
        end)
    end)
 
    Players.PlayerRemoving:Connect(function(player)
        if espObjects[player] then
            for _, obj in pairs(espObjects[player]) do
                if obj then obj:Remove() end
            end
            espObjects[player] = nil
        end
        if connections[player] then
            connections[player]:Disconnect()
            connections[player] = nil
        end
    end)
end
 
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == TOGGLE_KEY then
        ESP_ENABLED = not ESP_ENABLED
        for _, esp in pairs(espObjects) do
            esp.box.Visible = ESP_ENABLED
            esp.name.Visible = ESP_ENABLED and SHOW_NAMES
            esp.health.Visible = ESP_ENABLED and SHOW_HEALTH
            esp.distance.Visible = ESP_ENABLED and SHOW_DISTANCE
        end
    end
end)
 
initializeESP()
setupConnections()
 
RunService.Heartbeat:Connect(function()
    if ESP_ENABLED then
        updateESP()
    end
end)
        end
    })
 
 
 
    Tabs.Main:AddButton({
        Title = "Open Flight GUI",
        Description = "Open the flight GUI",
        Callback = function()
            local main = Instance.new("ScreenGui")
 
local Frame = Instance.new("Frame")
 
local up = Instance.new("TextButton")
 
local down = Instance.new("TextButton")
 
local onof = Instance.new("TextButton")
 
local TextLabel = Instance.new("TextLabel")
 
local plus = Instance.new("TextButton")
 
local speed = Instance.new("TextLabel")
 
local mine = Instance.new("TextButton")
 
local closebutton = Instance.new("TextButton")
 
local mini = Instance.new("TextButton")
 
local mini2 = Instance.new("TextButton")
 
 
 
main.Name = "main"
 
main.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
 
main.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
 
main.ResetOnSpawn = false
 
 
 
Frame.Parent = main
 
Frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
 
Frame.BorderColor3 = Color3.fromRGB(0, 0, 0)
 
Frame.Position = UDim2.new(0.100320168, 0, 0.379746825, 0)
 
Frame.Size = UDim2.new(0, 190, 0, 57)
 
 
 
up.Name = "up"
 
up.Parent = Frame
 
up.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
 
up.Size = UDim2.new(0, 44, 0, 28)
 
up.Font = Enum.Font.SourceSans
 
up.Text = "↑"
 
up.TextColor3 = Color3.fromRGB(0, 0, 0)
 
up.TextSize = 14.000
 
 
 
down.Name = "down"
 
down.Parent = Frame
 
down.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
 
down.Position = UDim2.new(0, 0, 0.491228074, 0)
 
down.Size = UDim2.new(0, 44, 0, 28)
 
down.Font = Enum.Font.SourceSans
 
down.Text = "↓"
 
down.TextColor3 = Color3.fromRGB(0, 0, 0)
 
down.TextSize = 14.000
 
 
 
onof.Name = "onof"
 
onof.Parent = Frame
 
onof.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
 
onof.Position = UDim2.new(0.702823281, 0, 0.491228074, 0)
 
onof.Size = UDim2.new(0, 56, 0, 28)
 
onof.Font = Enum.Font.Michroma
 
onof.Text = "FLY"
 
onof.TextColor3 = Color3.fromRGB(0, 0, 0)
 
onof.TextSize = 14.000
 
 
 
TextLabel.Parent = Frame
 
TextLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
 
TextLabel.Position = UDim2.new(0.469327301, 0, 0, 0)
 
TextLabel.Size = UDim2.new(0, 100, 0, 28)
 
TextLabel.Font = Enum.Font.Michroma
 
TextLabel.Text = "Fly gui modded"
 
TextLabel.TextColor3 = Color3.fromRGB(0, 0, 0)
 
TextLabel.TextScaled = true
 
TextLabel.TextSize = 14.000
 
TextLabel.TextWrapped = true
 
 
 
plus.Name = "plus"
 
plus.Parent = Frame
 
plus.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
 
plus.Position = UDim2.new(0.231578946, 0, 0, 0)
 
plus.Size = UDim2.new(0, 45, 0, 28)
 
plus.Font = Enum.Font.SourceSans
 
plus.Text = "+"
 
plus.TextColor3 = Color3.fromRGB(0, 0, 0)
 
plus.TextScaled = true
 
plus.TextSize = 14.000
 
plus.TextWrapped = true
 
 
 
speed.Name = "speed"
 
speed.Parent = Frame
 
speed.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
 
speed.Position = UDim2.new(0.468421042, 0, 0.491228074, 0)
 
speed.Size = UDim2.new(0, 44, 0, 28)
 
speed.Font = Enum.Font.SourceSans
 
speed.Text = "1"
 
speed.TextColor3 = Color3.fromRGB(0, 0, 0)
 
speed.TextScaled = true
 
speed.TextSize = 14.000
 
speed.TextWrapped = true
 
 
 
mine.Name = "mine"
 
mine.Parent = Frame
 
mine.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
 
mine.Position = UDim2.new(0.231578946, 0, 0.491228074, 0)
 
mine.Size = UDim2.new(0, 45, 0, 29)
 
mine.Font = Enum.Font.SourceSans
 
mine.Text = "-"
 
mine.TextColor3 = Color3.fromRGB(0, 0, 0)
 
mine.TextScaled = true
 
mine.TextSize = 14.000
 
mine.TextWrapped = true
 
 
 
closebutton.Name = "Close"
 
closebutton.Parent = main.Frame
 
closebutton.BackgroundColor3 = Color3.fromRGB(255, 5, 5)
 
closebutton.Font = "Michroma"
 
closebutton.Size = UDim2.new(0, 45, 0, 28)
 
closebutton.Text = "x"
 
closebutton.TextSize = 30
 
closebutton.Position = UDim2.new(0, 0, -1, 27)
 
 
 
mini.Name = "minimize"
 
mini.Parent = main.Frame
 
mini.BackgroundColor3 = Color3.fromRGB(117, 117, 117)
 
mini.Font = "Michroma"
 
mini.Size = UDim2.new(0, 45, 0, 28)
 
mini.Text = "-"
 
mini.TextSize = 40
 
mini.Position = UDim2.new(0, 44, -1, 27)
 
 
 
mini2.Name = "minimize2"
 
mini2.Parent = main.Frame
 
mini2.BackgroundColor3 = Color3.fromRGB(117, 117, 117)
 
mini2.Font = "SourceSans"
 
mini2.Size = UDim2.new(0, 45, 0, 28)
 
mini2.Text = "+"
 
mini2.TextSize = 40
 
mini2.Position = UDim2.new(0, 44, -1, 57)
 
mini2.Visible = false
 
 
 
speeds = 1
 
 
 
local speaker = game:GetService("Players").LocalPlayer
 
 
 
local chr = game.Players.LocalPlayer.Character
 
local hum = chr and chr:FindFirstChildWhichIsA("Humanoid")
 
 
 
nowe = false
 
 
 
game:GetService("StarterGui"):SetCore("SendNotification", { 
 
 Title = "FLY GUI";
 
 Text = "Fly gui";
 
 Icon = "rbxthumb://type=Asset&id=5107182114&w=150&h=150"})
 
Duration = 5;
 
 
 
Frame.Active = true -- main = gui
 
Frame.Draggable = true
 
 
 
onof.MouseButton1Down:connect(function()
 
 
 
 if nowe == true then
 
  nowe = false
 
 
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Climbing,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.FallingDown,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Flying,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Freefall,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Landed,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Physics,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.PlatformStanding,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Running,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.RunningNoPhysics,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Seated,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.StrafingNoPhysics,true)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Swimming,true)
 
  speaker.Character.Humanoid:ChangeState(Enum.HumanoidStateType.RunningNoPhysics)
 
 else 
 
  nowe = true
 
 
 
 
 
 
 
  for i = 1, speeds do
 
   spawn(function()
 
 
 
    local hb = game:GetService("RunService").Heartbeat 
 
 
 
 
 
    tpwalking = true
 
    local chr = game.Players.LocalPlayer.Character
 
    local hum = chr and chr:FindFirstChildWhichIsA("Humanoid")
 
    while tpwalking and hb:Wait() and chr and hum and hum.Parent do
 
     if hum.MoveDirection.Magnitude > 0 then
 
      chr:TranslateBy(hum.MoveDirection)
 
     end
 
    end
 
 
 
   end)
 
  end
 
  game.Players.LocalPlayer.Character.Animate.Disabled = true
 
  local Char = game.Players.LocalPlayer.Character
 
  local Hum = Char:FindFirstChildOfClass("Humanoid") or Char:FindFirstChildOfClass("AnimationController")
 
 
 
  for i,v in next, Hum:GetPlayingAnimationTracks() do
 
   v:AdjustSpeed(0)
 
  end
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Climbing,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.FallingDown,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Flying,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Freefall,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Landed,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Physics,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.PlatformStanding,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Running,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.RunningNoPhysics,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Seated,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.StrafingNoPhysics,false)
 
  speaker.Character.Humanoid:SetStateEnabled(Enum.HumanoidStateType.Swimming,false)
 
  speaker.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Swimming)
 
 end
 
 
 
 
 
 
 
 
 
 if game:GetService("Players").LocalPlayer.Character:FindFirstChildOfClass("Humanoid").RigType == Enum.HumanoidRigType.R6 then
 
 
 
 
 
 
 
  local plr = game.Players.LocalPlayer
 
  local torso = plr.Character.Torso
 
  local flying = true
 
  local deb = true
 
  local ctrl = {f = 0, b = 0, l = 0, r = 0}
 
  local lastctrl = {f = 0, b = 0, l = 0, r = 0}
 
  local maxspeed = 50
 
  local speed = 0
 
 
 
 
 
  local bg = Instance.new("BodyGyro", torso)
 
  bg.P = 9e4
 
  bg.maxTorque = Vector3.new(9e9, 9e9, 9e9)
 
  bg.cframe = torso.CFrame
 
  local bv = Instance.new("BodyVelocity", torso)
 
  bv.velocity = Vector3.new(0,0.1,0)
 
  bv.maxForce = Vector3.new(9e9, 9e9, 9e9)
 
  if nowe == true then
 
   plr.Character.Humanoid.PlatformStand = true
 
  end
 
  while nowe == true or game:GetService("Players").LocalPlayer.Character.Humanoid.Health == 0 do
 
   game:GetService("RunService").RenderStepped:Wait()
 
 
 
   if ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0 then
 
    speed = speed+.5+(speed/maxspeed)
 
    if speed > maxspeed then
 
     speed = maxspeed
 
    end
 
   elseif not (ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0) and speed ~= 0 then
 
    speed = speed-1
 
    if speed < 0 then
 
     speed = 0
 
    end
 
   end
 
   if (ctrl.l + ctrl.r) ~= 0 or (ctrl.f + ctrl.b) ~= 0 then
 
    bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (ctrl.f+ctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(ctrl.l+ctrl.r,(ctrl.f+ctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed
 
    lastctrl = {f = ctrl.f, b = ctrl.b, l = ctrl.l, r = ctrl.r}
 
   elseif (ctrl.l + ctrl.r) == 0 and (ctrl.f + ctrl.b) == 0 and speed ~= 0 then
 
    bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (lastctrl.f+lastctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(lastctrl.l+lastctrl.r,(lastctrl.f+lastctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed
 
   else
 
    bv.velocity = Vector3.new(0,0,0)
 
   end
 
   -- game.Players.LocalPlayer.Character.Animate.Disabled = true
 
   bg.cframe = game.Workspace.CurrentCamera.CoordinateFrame * CFrame.Angles(-math.rad((ctrl.f+ctrl.b)*50*speed/maxspeed),0,0)
 
  end
 
  ctrl = {f = 0, b = 0, l = 0, r = 0}
 
  lastctrl = {f = 0, b = 0, l = 0, r = 0}
 
  speed = 0
 
  bg:Destroy()
 
  bv:Destroy()
 
  plr.Character.Humanoid.PlatformStand = false
 
  game.Players.LocalPlayer.Character.Animate.Disabled = false
 
  tpwalking = false
 
 
 
 
 
 
 
 
 
 else
 
  local plr = game.Players.LocalPlayer
 
  local UpperTorso = plr.Character.UpperTorso
 
  local flying = true
 
  local deb = true
 
  local ctrl = {f = 0, b = 0, l = 0, r = 0}
 
  local lastctrl = {f = 0, b = 0, l = 0, r = 0}
 
  local maxspeed = 50
 
  local speed = 0
 
 
 
 
 
  local bg = Instance.new("BodyGyro", UpperTorso)
 
  bg.P = 9e4
 
  bg.maxTorque = Vector3.new(9e9, 9e9, 9e9)
 
  bg.cframe = UpperTorso.CFrame
 
  local bv = Instance.new("BodyVelocity", UpperTorso)
 
  bv.velocity = Vector3.new(0,0.1,0)
 
  bv.maxForce = Vector3.new(9e9, 9e9, 9e9)
 
  if nowe == true then
 
   plr.Character.Humanoid.PlatformStand = true
 
  end
 
  while nowe == true or game:GetService("Players").LocalPlayer.Character.Humanoid.Health == 0 do
 
   wait()
 
 
 
   if ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0 then
 
    speed = speed+.5+(speed/maxspeed)
 
    if speed > maxspeed then
 
     speed = maxspeed
 
    end
 
   elseif not (ctrl.l + ctrl.r ~= 0 or ctrl.f + ctrl.b ~= 0) and speed ~= 0 then
 
    speed = speed-1
 
    if speed < 0 then
 
     speed = 0
 
    end
 
   end
 
   if (ctrl.l + ctrl.r) ~= 0 or (ctrl.f + ctrl.b) ~= 0 then
 
    bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (ctrl.f+ctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(ctrl.l+ctrl.r,(ctrl.f+ctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed
 
    lastctrl = {f = ctrl.f, b = ctrl.b, l = ctrl.l, r = ctrl.r}
 
   elseif (ctrl.l + ctrl.r) == 0 and (ctrl.f + ctrl.b) == 0 and speed ~= 0 then
 
    bv.velocity = ((game.Workspace.CurrentCamera.CoordinateFrame.lookVector * (lastctrl.f+lastctrl.b)) + ((game.Workspace.CurrentCamera.CoordinateFrame * CFrame.new(lastctrl.l+lastctrl.r,(lastctrl.f+lastctrl.b)*.2,0).p) - game.Workspace.CurrentCamera.CoordinateFrame.p))*speed
 
   else
 
    bv.velocity = Vector3.new(0,0,0)
 
   end
 
 
 
   bg.cframe = game.Workspace.CurrentCamera.CoordinateFrame * CFrame.Angles(-math.rad((ctrl.f+ctrl.b)*50*speed/maxspeed),0,0)
 
  end
 
  ctrl = {f = 0, b = 0, l = 0, r = 0}
 
  lastctrl = {f = 0, b = 0, l = 0, r = 0}
 
  speed = 0
 
  bg:Destroy()
 
  bv:Destroy()
 
  plr.Character.Humanoid.PlatformStand = false
 
  game.Players.LocalPlayer.Character.Animate.Disabled = false
 
  tpwalking = false
 
 
 
 
 
 
 
 end
 
 
 
 
 
 
 
 
 
 
 
end)
 
 
 
local tis
 
 
 
up.MouseButton1Down:connect(function()
 
 tis = up.MouseEnter:connect(function()
 
  while tis do
 
   wait()
 
   game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0,1,0)
 
  end
 
 end)
 
end)
 
 
 
up.MouseLeave:connect(function()
 
 if tis then
 
  tis:Disconnect()
 
  tis = nil
 
 end
 
end)
 
 
 
local dis
 
 
 
down.MouseButton1Down:connect(function()
 
 dis = down.MouseEnter:connect(function()
 
  while dis do
 
   wait()
 
   game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0,-1,0)
 
  end
 
 end)
 
end)
 
 
 
down.MouseLeave:connect(function()
 
 if dis then
 
  dis:Disconnect()
 
  dis = nil
 
 end
 
end)
 
 
 
 
 
game:GetService("Players").LocalPlayer.CharacterAdded:Connect(function(char)
 wait(0.7)
 game.Players.LocalPlayer.Character.Humanoid.PlatformStand = false
 game.Players.LocalPlayer.Character.Animate.Disabled = false
end)
plus.MouseButton1Down:connect(function()
 speeds = speeds + 1
 speed.Text = speeds
 if nowe == true then
  tpwalking = false
  for i = 1, speeds do
   spawn(function()
    local hb = game:GetService("RunService").Heartbeat 
    tpwalking = true
    local chr = game.Players.LocalPlayer.Character
    local hum = chr and chr:FindFirstChildWhichIsA("Humanoid")
    while tpwalking and hb:Wait() and chr and hum and hum.Parent do
     if hum.MoveDirection.Magnitude > 0 then
      chr:TranslateBy(hum.MoveDirection)
     end
    end
   end)
  end
 end
end)
mine.MouseButton1Down:connect(function()
 if speeds == 1 then
  speed.Text = '-1 speed fly bruh'
  wait(1)
  speed.Text = speeds
 else
  speeds = speeds - 1
  speed.Text = speeds
  if nowe == true then
   tpwalking = false
   for i = 1, speeds do
    spawn(function()
     local hb = game:GetService("RunService").Heartbeat 
     tpwalking = true
     local chr = game.Players.LocalPlayer.Character
     local hum = chr and chr:FindFirstChildWhichIsA("Humanoid")
     while tpwalking and hb:Wait() and chr and hum and hum.Parent do
      if hum.MoveDirection.Magnitude > 0 then
       chr:TranslateBy(hum.MoveDirection)
      end
     end
    end)
   end
  end
 end
end)
closebutton.MouseButton1Click:Connect(function()
 main:Destroy()
end)
mini.MouseButton1Click:Connect(function()
 up.Visible = false
 down.Visible = false
 onof.Visible = false
 plus.Visible = false
 speed.Visible = false
 mine.Visible = false
 mini.Visible = false
 mini2.Visible = true
 main.Frame.BackgroundTransparency = 1
 closebutton.Position = UDim2.new(0, 0, -1, 57)
end)
mini2.MouseButton1Click:Connect(function()
 up.Visible = true
 down.Visible = true
 onof.Visible = true
 plus.Visible = true
 speed.Visible = true
 mine.Visible = true
 mini.Visible = true
 mini2.Visible = false
 main.Frame.BackgroundTransparency = 0 
 closebutton.Position = UDim2.new(0, 0, -1, 27)
end)
        end
    })
 
Tabs.Main:AddButton({
        Title = "Promote our Discord",
        Description = "Sends a message in chat promoting our discord",
        Callback = function()
            local textchannel = game:GetService("TextChatService"):WaitForChild("TextChannels"):WaitForChild("RBXGeneral") -- References the general TextChannel
local message = "Join gg/C9Qwf6UxG5"
textchannel:SendAsync(message)
        end
    })
 
Tabs.Main:AddButton({
        Title = "Trash Talk",
        Description = "Pre-made texts that are sent in chat, some are tagged",
        Callback = function()
            local textchannel = game:GetService("TextChatService"):WaitForChild("TextChannels"):WaitForChild("RBXGeneral")
    local messages = { "Eat Poo lil bro", "You could probably eat a whole barn", "you suck lil bro", "You're like a cloud. When you disappear, it's a beautiful day", "You bring everyone so much joy... when you leave the room" }
    local randomMessage = messages[math.random(#messages)]
    textchannel:SendAsync(randomMessage)
        end
    })
 
 
--  GAME
 
Tabs.Game:AddButton({
        Title = "Test script",
        Description = "Test the script (There should be a notification)",
        Callback = function()
            Fluent:Notify({
    Title = "Script Test",
    Content = "The script is working for Blox Fruits!",
    Duration = 8
})
        end
    })
 
Tabs.Game:AddButton({
        Title = "Redz Hub",
        Description = "A good auto-farm script",
        Callback = function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/newredz/BloxFruits/refs/heads/main/Source.luau"))(Settings)
        end
    })
 
 
 
-- Addons:
-- SaveManager (Allows you to have a configuration system)
-- InterfaceManager (Allows you to have a interface managment system)
 
-- Hand the library over to our managers
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)
 
-- Ignore keys that are used by ThemeManager.
-- (we dont want configs to save themes, do we?)
SaveManager:IgnoreThemeSettings()
 
-- You can add indexes of elements the save manager should ignore
SaveManager:SetIgnoreIndexes({})
 
-- use case for doing it this way:
-- a script hub could have themes in a global folder
-- and game configs in a separate folder per game
InterfaceManager:SetFolder("FluentScriptHub")
SaveManager:SetFolder("FluentScriptHub/specific-game")
 
InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)
 
 
Window:SelectTab(1)
 
Fluent:Notify({
    Title = "Fluent",
    Content = "The script has been loaded.",
    Duration = 8
})
 
-- You can use the SaveManager:LoadAutoloadConfig() to load a config
-- which has been marked to be one that auto loads!
SaveManager:LoadAutoloadConfig()
