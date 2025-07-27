local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- Buscar y eliminar la Part "WalkWater" en Workspace
local part = game.Workspace:FindFirstChild("WalkWater")
if part then
    part:Destroy()
end

-- Buscar y eliminar el objeto "Rayfield-Old" en game.CoreGui.HiddenUI
local hiddenUI = game.CoreGui:FindFirstChild("HiddenUI")
if hiddenUI then
    local rayfieldOld = hiddenUI:FindFirstChild("Rayfield-Old")
    if rayfieldOld then
        rayfieldOld:Destroy()
    end
end


Rayfield:Notify({
   Title = "Cargando Espera.",
   Content = "",
   Duration = 3,
   Image = nil,
})

local Window = Rayfield:CreateWindow({
   Name = "SigmaRock Hub Blox Fruits",
   Icon = 0,
   LoadingTitle = "🍎Blox Fruits🍅",
   LoadingSubtitle = "Creado por lucasbekk3315",
   Theme = "Default",

   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false, -- Prevents Rayfield from warning when the script has a version mismatch with the interface

   ConfigurationSaving = {
      Enabled = false,
      FolderName = nil, -- Create a custom folder for your hub/game
      FileName = "Big Hub"
   },

   Discord = {
      Enabled = false, -- Prompt the user to join your Discord server if their executor supports it
      Invite = "mNy7WW98TA", -- The Discord invite code, do not include discord.gg/. E.g. discord.gg/ ABCD would be ABCD
      RememberJoins = true -- Set this to false to make them join the discord every time they load it up
   },

   KeySystem = true, -- Set this to true to use our key system
   KeySettings = {
      Title = "SigmaRock Hub",
      Subtitle = "Sistema De Key",
      Note = "Consigue la key en mi discord discord.com/invite/dp5GrqBqMg", -- Use this to tell the user how to get a key
      FileName = "", -- It is recommended to use something unique as other scripts using Rayfield may overwrite your key file
      SaveKey = true, -- The user's key will be saved, but if you change the key, they will be unable to use your script
      GrabKeyFromSite = false, -- If this is true, set Key below to the RAW site you would like Rayfield to get the key from
      Key = {"lucasbekk3315"} -- List of keys that will be accepted by the system, can be RAW file links (pastebin, github etc) or simple strings ("hello","key22")
   }
})

local Tab = Window:CreateTab("Menu Principal", nil)

Rayfield:Notify({
   Title = "Gracias Por Usar Mi Script!",
   Content = "Presiona la tecla K para minimizar o maximizar el menu.",
   Duration = 7,
   Image = nil,
})



-- Variables para controlar la Part y el seguimiento del caminar sobre el agua
local WalkWater11 = nil
local followCoroutine = nil
local player11 = game.Players.LocalPlayer
local character11 = player11.Character or player11.CharacterAdded:Wait()
local humanoidRootPart11 = character11:WaitForChild("HumanoidRootPart")

-- Crear el Toggle para activar/desactivar caminar sobre el agua
local ToggleWalkWater = Tab:CreateToggle({
   Name = "Caminar en el agua",
   CurrentValue = false,
   Flag = "ToggleWalkWater", -- Asegúrate de que este Flag sea único si estás usando guardado de configuración
   Callback = function(Value)
      if Value then
         -- Crear la Part para caminar sobre el agua si no existe
         if not WalkWater11 then
            WalkWater11 = Instance.new("Part")
            WalkWater11.Name = "WalkWater"
            WalkWater11.Size = Vector3.new(800, 4, 800)
            WalkWater11.Anchored = true
            WalkWater11.CanCollide = true
            WalkWater11.Transparency = 1
            WalkWater11.BrickColor = BrickColor.new("White") -- Cambia el color si lo deseas
            WalkWater11.Parent = workspace

            -- Posicionar la Part encima de "WaterBase-Plane"
            local waterBasePlane11 = workspace.Map:WaitForChild("WaterBase-Plane")
            WalkWater11.Position = waterBasePlane11.Position + Vector3.new(0, WalkWater11.Size.Y / 2, 0)

            -- Función para cambiar la altura de la Part
            local function setHeight11(newHeight)
                WalkWater11.Position = Vector3.new(
                   WalkWater11.Position.X,
                   newHeight + WalkWater11.Size.Y / 2,
                   WalkWater11.Position.Z
                )
            end

            -- Ajustar la altura inicial
            setHeight11(-7.7)
            
            -- Función de seguimiento del jugador en X y Z
            local function followPlayerPosition()
                while WalkWater11 do
                    if character11 and humanoidRootPart11 then
                        local newPos = humanoidRootPart11.Position
                        WalkWater11.Position = Vector3.new(newPos.X, WalkWater11.Position.Y, newPos.Z)
                    end
                    wait(0.1)  -- Actualiza la posición más rápidamente (10 veces por segundo)
                end
            end

            -- Crear la corrutina de seguimiento
            followCoroutine = coroutine.create(followPlayerPosition)
            coroutine.resume(followCoroutine)
         end
      else
         -- Detener el seguimiento y eliminar la Part
         if WalkWater11 then
            WalkWater11:Destroy()
            WalkWater11 = nil
         end

         -- Detener la corrutina si existe
         if followCoroutine then
            followCoroutine = nil
         end
      end
   end,
})

-- Escuchar el evento de respawn del jugador para seguir el nuevo personaje solo si el toggle está activado
player11.CharacterAdded:Connect(function(newCharacter)
    character11 = newCharacter
    humanoidRootPart11 = newCharacter:WaitForChild("HumanoidRootPart")
    
    -- Si el Toggle está activado y "WalkWater" ya está creado, reiniciar el seguimiento
    if ToggleWalkWater.CurrentValue and WalkWater11 then
        followCoroutine = coroutine.create(function()
            while WalkWater11 do
                if character11 and humanoidRootPart11 then
                    local newPos = humanoidRootPart11.Position
                    WalkWater11.Position = Vector3.new(newPos.X, WalkWater11.Position.Y, newPos.Z)
                end
                wait(0.1)  -- Actualiza la posición más rápidamente (10 veces por segundo)
            end
        end)
        coroutine.resume(followCoroutine)
    end
end)



local VirtualInputManager = game:GetService("VirtualInputManager")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local maxIdleTime = 20 * 60 -- 20 minutos antes del kick
local idleTime = 0
local isActive = false -- Toggle inicial
local updateConnection -- Conexión para actualizar el contador

-- Crear GUI
local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))

local timerLabel = Instance.new("TextLabel", screenGui)
timerLabel.Size = UDim2.new(0.3, 0, 0.05, 0)
timerLabel.Position = UDim2.new(0.35, 0, 0.05, 0)
timerLabel.BackgroundTransparency = 1
timerLabel.TextScaled = true
timerLabel.TextColor3 = Color3.new(1, 1, 1)
timerLabel.Font = Enum.Font.SourceSansBold
timerLabel.Visible = false

-- Actualizar contador en pantalla
local function updateTimer()
    local remainingTime = maxIdleTime - idleTime
    local minutes = math.floor(remainingTime / 60)
    local tenths = math.floor((remainingTime % 60) / 6)
    
    timerLabel.Text = "Kick por inactividad en: " .. minutes .. "." .. tenths .. " mins"
    
    if remainingTime <= 0 then
        timerLabel.Text = "¡Serás kickeado en cualquier momento!"
    end
end

-- Reiniciar el contador si el jugador se mueve o presiona una tecla
local function resetIdleTime()
    if isActive then
        idleTime = 0
        updateTimer()
    end
end

UserInputService.InputBegan:Connect(resetIdleTime)
UserInputService.InputEnded:Connect(resetIdleTime)

-- Simular la tecla "M" cada 5 segundos para evitar el kick
local function preventKick()
    while isActive do
        wait(5)
        VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.M, false, game)
        wait(0.1)
        VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.M, false, game)
    end
end

-- Bucle para actualizar el contador cada 1 segundo
local function startIdleCheck()
    while isActive do
        wait(1)
        idleTime = idleTime + 1
        updateTimer()
    end
end

-- Toggle usando Rayfield
local Toggle = Tab:CreateToggle({
   Name = "Anti Afk",
   CurrentValue = false,
   Flag = "IdleToggle",
   Callback = function(Value)
       isActive = Value
       timerLabel.Visible = isActive
       
       if isActive then
           idleTime = 0
           updateTimer()
           task.spawn(preventKick)
           updateConnection = task.spawn(startIdleCheck)
       else
           if updateConnection then
               task.cancel(updateConnection)
           end
       end
   end,
})



local AimLockJugadorButton = Tab:CreateButton({
    Name = "Abrir menu de aimlock para jugadores",
    Callback = function()

        -- Crear la UI del menú de AimLock
        local PantallaGui = Instance.new("ScreenGui")
        PantallaGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
        
        local Marco = Instance.new("Frame")
        Marco.Size = UDim2.new(0, 300, 0, 150)
        Marco.Position = UDim2.new(0.5, -150, 0.5, -75)
        Marco.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
        Marco.Parent = PantallaGui
        Marco.Active = true
        Marco.Draggable = true
        
        local Titulo = Instance.new("TextLabel")
        Titulo.Size = UDim2.new(1, 0, 0, 30)
        Titulo.Text = "AimLock Blox"
        Titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
        Titulo.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        Titulo.TextSize = 20
        Titulo.Parent = Marco

        local BotonCerrar = Instance.new("TextButton")
        BotonCerrar.Size = UDim2.new(0, 30, 0, 30)
        BotonCerrar.Position = UDim2.new(1, -35, 0, 5)
        BotonCerrar.Text = "X"
        BotonCerrar.TextColor3 = Color3.fromRGB(255, 255, 255)
        BotonCerrar.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        BotonCerrar.Parent = Marco

        BotonCerrar.MouseButton1Click:Connect(function()
            PantallaGui:Destroy()  -- Cierra el menú cuando se presiona el botón "X"
        end)

        local CajaTexto = Instance.new("TextBox")
        CajaTexto.Size = UDim2.new(0, 260, 0, 40)
        CajaTexto.Position = UDim2.new(0, 20, 0, 40)
        CajaTexto.Text = "Presiona Aqui."
        CajaTexto.PlaceholderText = "Presiona una tecla... o escribe 'click' para asignar el clic derecho"
        CajaTexto.TextColor3 = Color3.fromRGB(255, 255, 255)
        CajaTexto.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
        CajaTexto.Parent = Marco
        
        local AimbotActivado = false
        local TeclaDeAimbot = Enum.KeyCode.E
        local Objetivo = nil
        local TeclaAsignada = false
        local ClickDerechoAsignado = false
        local UserInputService = game:GetService("UserInputService")
        local RunService = game:GetService("RunService")
        local Camera = workspace.CurrentCamera
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer
        local StarterGui = game:GetService("StarterGui")
        
        
        -- Función para obtener el jugador más cercano al mouse
        local function ObtenerJugadorMasCercanoAlMouse()
            local posicionMouse = UserInputService:GetMouseLocation()
            local jugadorMasCercano = nil
            local distanciaMasCercana = math.huge

            for _, jugador in ipairs(Players:GetPlayers()) do
                if jugador ~= LocalPlayer and jugador.Character and jugador.Character:FindFirstChild("HumanoidRootPart") then
                    local torso = jugador.Character.HumanoidRootPart
                    local posicionPantalla, enPantalla = Camera:WorldToScreenPoint(torso.Position)

                    if enPantalla then
                        local distancia = (Vector2.new(posicionPantalla.X, posicionPantalla.Y) - posicionMouse).Magnitude
                        if distancia < distanciaMasCercana then
                            distanciaMasCercana = distancia
                            jugadorMasCercano = jugador
                        end
                    end
                end
            end

            return jugadorMasCercano
        end
        
        -- Apuntar al objetivo
        local function ApuntarA(parteObjetivo)
            if parteObjetivo then
                Camera.CFrame = CFrame.new(Camera.CFrame.Position, parteObjetivo.Position)
            end
        end
        
        -- Actualizar constantemente el objetivo mientras el aimbot está activado
        RunService.Heartbeat:Connect(function()
            if AimbotActivado then
                if not Objetivo then
                    Objetivo = ObtenerJugadorMasCercanoAlMouse()
                end

                if Objetivo then
                    local parteObjetivo = Objetivo.Character:FindFirstChild("HumanoidRootPart")
                    if parteObjetivo then
                        ApuntarA(parteObjetivo)
                    end
                end
            end
        end)

        -- Asignar la tecla de activación del aimbot
        CajaTexto.FocusLost:Connect(function(enterPressed)
            if enterPressed then
                local tecla = CajaTexto.Text:upper()
                if tecla == "CLICK" then
                    ClickDerechoAsignado = true
                    TeclaDeAimbot = Enum.KeyCode.Unknown -- Desactivar asignación de tecla
                    TeclaAsignada = true

                else
                    local teclaValida = false
                    -- Validar si la tecla es válida
                    for _, keyCode in pairs(Enum.KeyCode:GetEnumItems()) do
                        if keyCode.Name == tecla then
                            TeclaDeAimbot = keyCode
                            TeclaAsignada = true
                            ClickDerechoAsignado = false

                            teclaValida = true
                            break
                        end
                    end
                    if not teclaValida then

                    end
                end
            end
        end)

        -- Detectar cuando se presiona la tecla de activación
        UserInputService.InputBegan:Connect(function(input, gameProcessed)
            if gameProcessed then return end

            if TeclaAsignada and input.UserInputType == Enum.UserInputType.Keyboard then
                if input.KeyCode == TeclaDeAimbot then
                    AimbotActivado = not AimbotActivado
                    if not AimbotActivado then
                        Objetivo = nil -- Limpiar el objetivo cuando se desactiva
                    end
                end
            elseif ClickDerechoAsignado and input.UserInputType == Enum.UserInputType.MouseButton2 then
                -- Activar/desactivar el aimbot con el clic derecho
                AimbotActivado = not AimbotActivado
                if not AimbotActivado then
                    Objetivo = nil -- Limpiar el objetivo cuando se desactiva
                end
            end
        end)
Rayfield:Notify({
Title = "Cargado!",
Content = "",
Duration = 1.5,
Image = nil,
})
    end,
})



local AimLockNPCButton = Tab:CreateButton({
    Name = "Abrir menu de aimlock para npcs",
    Callback = function()

        -- Aquí se inicia el menú de AimLock para NPCs
        local menuOpen = false  -- Variable para verificar si el menú ya está abierto

        if menuOpen then
            return  -- Si el menú ya está abierto, no hacer nada
        end

        menuOpen = true  -- Marcamos que el menú está abierto

        local Players1 = game:GetService("Players")
        local RunService1 = game:GetService("RunService")
        local UserInputService1 = game:GetService("UserInputService")
        local Camera1 = workspace.CurrentCamera
        local StarterGui1 = game:GetService("StarterGui")
        local LocalPlayer1 = Players1.LocalPlayer

        local AimbotActivated1 = false
        local AimbotKey1 = Enum.KeyCode.E
        local Target1 = nil
        local KeyAssigned1 = false
        local GuiScreen1 = nil
        local RightClickAssigned1 = false

        -- Check if an NPC is valid (all NPCs in "workspace.Enemies" are considered enemies)
        local function IsEnemy1(npc)
            return npc and npc:FindFirstChild("HumanoidRootPart")
        end

        -- Get the closest NPC to the mouse
        local function GetClosestNpcToMouse1()
            local mousePosition = UserInputService1:GetMouseLocation()
            local closestNpc = nil
            local closestDistance = math.huge

            for _, npc in ipairs(workspace.Enemies:GetChildren()) do
                if IsEnemy1(npc) then
                    local torso = npc.HumanoidRootPart
                    local screenPosition, onScreen = Camera1:WorldToScreenPoint(torso.Position)

                    if onScreen then
                        local distance = (Vector2.new(screenPosition.X, screenPosition.Y) - mousePosition).Magnitude
                        if distance < closestDistance then
                            closestDistance = distance
                            closestNpc = npc
                        end
                    end
                end
            end

            return closestNpc
        end

        -- Aim at the target (NPC)
        local function AimAt1(targetPart)
            if targetPart then
                Camera1.CFrame = CFrame.new(Camera1.CFrame.Position, targetPart.Position)
            end
        end

        -- Validate the target (NPC)
        local function ValidateTarget1(npc)
            if npc and npc:FindFirstChild("HumanoidRootPart") then
                return true
            end
            return false
        end

        -- Deactivate aimbot if the NPC's humanoid health is 0, but instead of deactivating, switch to the next closest NPC
        local function CheckNpcHealth1()
            if Target1 then
                local humanoid = Target1:FindFirstChild("Humanoid")
                if humanoid and humanoid.Health == 0 then
                    -- Find a new target
                    Target1 = GetClosestNpcToMouse1()
                end
            end
        end

        -- Constantly update the target while the aimbot is activated
        RunService1.Heartbeat:Connect(function()
            if AimbotActivated1 then
                if not Target1 or not ValidateTarget1(Target1) then
                    Target1 = GetClosestNpcToMouse1()
                end

                if Target1 then
                    local targetPart = Target1:FindFirstChild("HumanoidRootPart")
                    if targetPart then
                        AimAt1(targetPart)
                    end
                end

                -- Check if the NPC has health 0 and deactivate the aimbot
                CheckNpcHealth1()
            end
        end)

        -- Detect when the activation key is pressed
        UserInputService1.InputBegan:Connect(function(input, gameProcessed)
            if gameProcessed then return end

            -- Check if the assigned key or right-click or mouse button 4 or 5 is pressed
            if KeyAssigned1 and input.UserInputType == Enum.UserInputType.Keyboard then
                -- Check if the pressed key matches the assigned key
                if input.KeyCode == AimbotKey1 then
                    AimbotActivated1 = not AimbotActivated1
                    if not AimbotActivated1 then
                        Target1 = nil -- Clear the target when deactivated
                    end
                end
            elseif RightClickAssigned1 and input.UserInputType == Enum.UserInputType.MouseButton2 then
                -- Toggle aimbot with right-click
                AimbotActivated1 = not AimbotActivated1
                if not AimbotActivated1 then
                    Target1 = nil -- Clear the target when deactivated
                end
            elseif input.UserInputType == Enum.UserInputType.MouseButton4 then
                -- Toggle aimbot with mouse button 4
                if KeyAssigned1 and AimbotKey1 == Enum.UserInputType.MouseButton4 then
                    AimbotActivated1 = not AimbotActivated1
                    if not AimbotActivated1 then
                        Target1 = nil -- Clear the target when deactivated
                    end
                end
            elseif input.UserInputType == Enum.UserInputType.MouseButton5 then
                -- Toggle aimbot with mouse button 5
                if KeyAssigned1 and AimbotKey1 == Enum.UserInputType.MouseButton5 then
                    AimbotActivated1 = not AimbotActivated1
                    if not AimbotActivated1 then
                        Target1 = nil -- Clear the target when deactivated
                    end
                end
            end
        end)

        -- Create the key assignment menu
        local function CreateKeyMenu1()
            GuiScreen1 = Instance.new("ScreenGui")
            GuiScreen1.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
            
            local Frame1 = Instance.new("Frame")
            Frame1.Size = UDim2.new(0, 300, 0, 150)
            Frame1.Position = UDim2.new(0.5, -150, 0.5, -75)
            Frame1.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
            Frame1.Parent = GuiScreen1
            Frame1.Active = true
            Frame1.Draggable = true
            
            local Title1 = Instance.new("TextLabel")
            Title1.Size = UDim2.new(1, 0, 0, 30)
            Title1.Text = "AimLock NPC"
            Title1.TextColor3 = Color3.fromRGB(255, 255, 255)
            Title1.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
            Title1.TextSize = 20
            Title1.Parent = Frame1

            local CloseButton1 = Instance.new("TextButton")
            CloseButton1.Size = UDim2.new(0, 30, 0, 30)
            CloseButton1.Position = UDim2.new(1, -35, 0, 5)
            CloseButton1.Text = "X"
            CloseButton1.TextColor3 = Color3.fromRGB(255, 255, 255)
            CloseButton1.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
            CloseButton1.Parent = Frame1

            CloseButton1.MouseButton1Click:Connect(function()
                AimbotActivated1 = false
                KeyAssigned1 = false
                AimbotKey1 = nil
                RightClickAssigned1 = false
                if GuiScreen1 then
                    GuiScreen1:Destroy() -- Destroy the ScreenGui
                end
                if Frame1 then
                    Frame1:Destroy() -- Destroy the menu frame
                end

                menuOpen = false
            end)

            local TextBox1 = Instance.new("TextBox")
            TextBox1.Size = UDim2.new(0, 260, 0, 40)
            TextBox1.Position = UDim2.new(0, 20, 0, 40)
            TextBox1.Text = "Presiona Aqui"
            TextBox1.PlaceholderText = "Apreta una tecla despues enter para asignar la tecla, o escribe click."
            TextBox1.TextColor3 = Color3.fromRGB(255, 255, 255)
            TextBox1.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
            TextBox1.Parent = Frame1
            
            TextBox1.FocusLost:Connect(function(enterPressed)
                if enterPressed then
                    local key = TextBox1.Text:upper()
                    if key == "CLICK" then
                        RightClickAssigned1 = true
                        AimbotKey1 = Enum.KeyCode.Unknown -- Disable key assignment
                        KeyAssigned1 = true

                    elseif key == "BUTTON4" then
                        AimbotKey1 = Enum.UserInputType.MouseButton4 -- Assign mouse button 4
                        KeyAssigned1 = true
                        RightClickAssigned1 = false
                    elseif key == "BUTTON5" then
                        AimbotKey1 = Enum.UserInputType.MouseButton5 -- Assign mouse button 5
                        KeyAssigned1 = true
                        RightClickAssigned1 = false
                    else
                        local validKey = false
                        -- Check if it's a valid key
                        for _, keyCode in pairs(Enum.KeyCode:GetEnumItems()) do
                            if keyCode.Name == key then
                                AimbotKey1 = keyCode
                                KeyAssigned1 = true
                                validKey = true
                                break
                            end
                        end
                        if validKey then

                        else

                        end
                    end
                end
            end)
        end

        -- Create the key assignment menu when the AimLockNPCButton is clicked
        CreateKeyMenu1()
Rayfield:Notify({
Title = "Cargado!",
Content = "",
Duration = 1.5,
Image = nil,
})
    end
})



local InfiniteYieldButton = Tab:CreateButton({
   Name = "Abrir Infinite Yield",
   Callback = function()
Rayfield:Notify({
   Title = "Cargando.",
   Content = "",
   Duration = 2.5,
   Image = nil,
})

loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))() 


Rayfield:Notify({
   Title = "Cargado!",
   Content = "",
   Duration = 1.5,
   Image = nil,
})
   end,
})



local BloxFruitsButton = Tab:CreateButton({
   Name = "Abrir Redz Hub",
   Callback = function()
Rayfield:Notify({
   Title = "Cargando.",
   Content = "",
   Duration = 2.5,
   Image = nil,
})

loadstring(game:HttpGet("https://raw.githubusercontent.com/Lucasbekk3315-code/Blox-Fruits-redz-hub/refs/heads/main/Script%20blox%20fruits%20roblox%20redz%20hub"))()


Rayfield:Notify({
   Title = "Cargado!",
   Content = "",
   Duration = 1.5,
   Image = nil,
})
   end,
})



local RejoinButton = Tab:CreateButton({
    Name = "Rejoin",
    Callback = function()
        local teleportService = game:GetService("TeleportService")
        local player = game.Players.LocalPlayer

        if not player then
            warn("No se pudo encontrar el jugador local.")
            return
        end

        local queueTeleport = syn and syn.queue_on_teleport 
                            or queue_on_teleport 
                            or (fluxus and fluxus.queue_on_teleport)

        local scriptToExecute = [[
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Lucasbekk3315-code/blox-fruits-2.2-roblox-script/refs/heads/main/Blox%20Fruits%20Script%20roblox%202.0%20BETA"))()
        ]]

        -- Si el ejecutor admite `queue_on_teleport`, ejecuta el script después del teletransporte
        if queueTeleport then
            pcall(function()
                queueTeleport(scriptToExecute)
            end)
        end

        -- Mensaje de reingreso y teletransporte seguro
        player:Kick("\nRejoining...")

        -- Espera un poco antes de teletransportar para evitar errores
        task.wait(1)  

        pcall(function()
            teleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, player)
        end)
    end,
})



local OptimizarButton = Tab:CreateButton({
    Name = "Optimizar",
    Callback = function()
        -- Función para optimizar partes específicas
        local function optimizePart(part)
            part.CastShadow = false
            part.Material = Enum.Material.Plastic
        end

        -- Función para optimizar GUI y Luces
        local function optimizeObject(object)
            if object:IsA("Decal") then
                object.Texture = ""
            elseif object:IsA("SurfaceGui") or object:IsA("BillboardGui") then
                object.LightInfluence = 0
                for _, child in ipairs(object:GetDescendants()) do
                    if child:IsA("ImageLabel") or child:IsA("ImageButton") then
                        child.Image = ""
                    elseif child:IsA("TextLabel") or child:IsA("TextButton") then
                        child.Text = ""
                    end
                end
            elseif object:IsA("GuiObject") then
                if object:IsA("ImageLabel") or object:IsA("ImageButton") then
                    object.Image = ""
                elseif object:IsA("TextLabel") or object:IsA("TextButton") then
                    object.Text = ""
                end
            elseif object:IsA("PointLight") or object:IsA("SpotLight") or object:IsA("SurfaceLight") then
                object.Brightness = math.clamp(object.Brightness * 0.5, 0, 2) -- Reduce el brillo a la mitad
                object.Range = math.clamp(object.Range * 0.8, 2, 30) -- Reduce el rango un 20%
                object.Shadows = false -- Desactiva sombras para mejorar rendimiento
            end
        end

        -- Función para procesar descendientes de "Chest"
        local function processChestDescendants(chestModel)
            for _, descendant in ipairs(chestModel:GetDescendants()) do
                if not descendant.Name:lower():find("tree") then -- Evitar optimizar "Tree"
                    if descendant:IsA("BasePart") or descendant:IsA("MeshPart") or descendant:IsA("UnionOperation") then
                        optimizePart(descendant)
                    elseif descendant:IsA("Decal") or descendant:IsA("GuiObject") or descendant:IsA("Light") then
                        optimizeObject(descendant)
                    elseif descendant:IsA("Animation") then
                        descendant:Destroy()
                    end
                end
            end
        end

        -- Función para optimizar todo el Workspace
        local function processWorkspace()
            for _, descendant in ipairs(workspace:GetDescendants()) do
                if not descendant.Name:lower():find("tree") then -- No modificar "Tree"
                    if descendant:IsA("Model") and descendant.Name:find("Chest") then
                        processChestDescendants(descendant)
                    end
                    if descendant:IsA("BasePart") or descendant:IsA("MeshPart") or descendant:IsA("UnionOperation") then
                        optimizePart(descendant)
                    elseif descendant:IsA("Decal") or descendant:IsA("GuiObject") or descendant:IsA("Light") then
                        optimizeObject(descendant)
                    end
                end
            end
        end

        -- Ejecutar optimización
        processWorkspace()

        -- Notificación de éxito
        Rayfield:Notify({
            Title = "Optimizado!",
            Content = "Se aplicaron mejoras sin afectar los árboles.",
            Duration = 1.5,
            Image = nil,
        })
    end
})



local RunService = game:GetService("RunService")
local player = game.Players.LocalPlayer
local visionLoop = nil -- Variable para almacenar la conexión

local MaxVisionRangeButton = Tab:CreateButton({
   Name = "Ver de lejos con Haki de Observación",
   Callback = function()
      if visionLoop then
         visionLoop:Disconnect()
         visionLoop = nil
      else
         visionLoop = RunService.RenderStepped:Connect(function()
            if player and player.Character then
               if player:FindFirstChild("VisionRadius") then
                  player.VisionRadius.Value = math.huge
               end

               player.Character:SetAttribute("ObservationRange", math.huge)
            end
         end)
      end
   end,
})



local DestroyRayfieldButton = Tab:CreateButton({
   Name = "Destruir Menu",
   Callback = function()

    Rayfield:Destroy()

-- Buscar y eliminar la Part "WalkWater" en Workspace
local part = game.Workspace:FindFirstChild("WalkWater")
if part then
    part:Destroy()
end

-- Buscar y eliminar el objeto "Rayfield-Old" en game.CoreGui.HiddenUI
local hiddenUI = game.CoreGui:FindFirstChild("HiddenUI")
if hiddenUI then
    local rayfieldOld = hiddenUI:FindFirstChild("Rayfield-Old")
    if rayfieldOld then
        rayfieldOld:Destroy()
    end
end

   end,
})




local VelocidaddecaminarSlider = Tab:CreateSlider({
   Name = "Cambiar Velocidad De Caminar",
   Range = {1, 10},
   Increment = 1,
   Suffix = "Velocidad",
   CurrentValue = 1,
   Flag = "Slider1",
   Callback = function(Value)
      local player = game.Players.LocalPlayer
      if not player or not player.Character then
         warn("El personaje no está disponible o no se ha cargado correctamente.")
         return
      end

      local character = player.Character

      -- Establecer el atributo directamente (lo crea si no existe)
      character:SetAttribute("SpeedMultiplier", Value)
   end,
})




local SliderDash = Tab:CreateSlider({
   Name = "DashSpeed",
   Range = {1, 200},
   Increment = 5,
   Suffix = "Velocidad + Longitud",
   CurrentValue = 1,
   Flag = "SliderDash",
   Callback = function(Value)
      local player = game.Players.LocalPlayer
      if not player or not player.Character then
         warn("El personaje no está disponible o no se ha cargado correctamente.")
         return
      end

      local character = player.Character

      -- Lista de atributos a modificar
      local atributos = { "DashLength", "DashSpeed", "DashLengthAir", "DashLengthGround" }

      -- Asignar el valor a cada atributo
      for _, atributo in ipairs(atributos) do
         character:SetAttribute(atributo, Value)
      end
   end,
})



local Tab = Window:CreateTab("Teletransportes", nil)



local TweenService = game:GetService("TweenService")
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

local isTweening = false
local speed = 340 -- Velocidad equivalente a WalkSpeed 50 (ajustable)
local currentTween = nil  -- Variable para almacenar el tween en curso

-- Función para verificar si un objeto tiene un TouchInterest
local function hasTouchInterest(object)
    for _, descendant in ipairs(object:GetDescendants()) do
        if descendant:IsA("TouchTransmitter") then
            return true
        end
    end
    return false
end

local function tweenToPosition(destinationCFrame)
    if not humanoidRootPart then return end

    local distance = (humanoidRootPart.Position - destinationCFrame.Position).Magnitude
    local time = distance / speed

    local tweenInfo = TweenInfo.new(time, Enum.EasingStyle.Linear)
    currentTween = TweenService:Create(humanoidRootPart, tweenInfo, {CFrame = destinationCFrame})

    currentTween:Play()
    currentTween.Completed:Wait()
end

local function teleportToPosition(destinationCFrame)
    if not humanoidRootPart then return end
    humanoidRootPart.CFrame = destinationCFrame
end

-- Escuchar cuando el personaje es agregado (después de morir)
player.CharacterAdded:Connect(function(newCharacter)
    character = newCharacter
    humanoidRootPart = character:WaitForChild("HumanoidRootPart")
end)

-- Conectar el toggle para ir a todos los cofres
local AllChestTweenToggle = Tab:CreateToggle({
   Name = "Ir a todos los cofres",
   CurrentValue = false,
   Flag = "Toggle1", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
   Callback = function(Value)
       if Value then
           -- Activar el sistema de tweening
           isTweening = true

           coroutine.wrap(function()
               -- Buscar directamente en workspace.Map
               local map = workspace.Map
               local closestChest = nil
               local closestDistance = math.huge -- Inicializar con un valor muy alto

               while isTweening do
                   closestChest = nil
                   closestDistance = math.huge -- Reiniciar la distancia más cercana

                   -- Buscar el cofre más cercano con TouchInterest
                   for _, object in ipairs(map:GetDescendants()) do
                       if object:IsA("BasePart") and object.Name:find("^Chest") then
                           local distance = (humanoidRootPart.Position - object.Position).Magnitude

                           -- Verificar si el objeto tiene TouchInterest
                           if hasTouchInterest(object) and distance < closestDistance then
                               closestChest = object
                               closestDistance = distance
                           end
                       end
                   end

                   -- Si encontramos un cofre con TouchInterest
                   if closestChest then
                       if closestDistance <= 340 then
                           -- Si está a 100 studs o menos, teletransportarse directamente
                           teleportToPosition(closestChest.CFrame)
                       else
                           -- Si está más lejos de 100 studs, usar tween
                           tweenToPosition(closestChest.CFrame)
                       end
                       wait(0.5) -- Esperar un poco antes de seguir buscando

                       -- Si el cofre ya no tiene TouchInterest, buscar el siguiente más cercano
                       if not hasTouchInterest(closestChest) then
                           closestChest = nil
                       end
                   else
                       -- Si no hay cofres con TouchInterest, esperar antes de buscar de nuevo
                       wait(0.4)
                   end
               end
           end)()
       else
           -- Desactivar el sistema de tweening
           isTweening = false

           -- Detener el tween actual si está en curso
           if currentTween then
               currentTween:Cancel()  -- Cancela el tween en curso
               currentTween = nil  -- Restablecer la variable del tween
           end
       end
   end,
})



-- Función para teletransportarse a una Tool o Modelo, exactamente a la posición del objeto Handle dentro de la fruta
local function TeletransportarAObjeto(objeto, alCentro)
    -- Verificar si el objeto es una herramienta o un modelo con PrimaryPart
    if objeto:IsA("Tool") then
        -- Si es una herramienta, teletransportar al jugador a la posición de la herramienta
        local jugador = game.Players.LocalPlayer
        local personaje = jugador.Character
        local humanoidRootPart = personaje and personaje:FindFirstChild("HumanoidRootPart")

        if humanoidRootPart then
            -- Teletransportar al jugador a la posición exacta de la herramienta
            humanoidRootPart.CFrame = objeto.Handle.CFrame
            print("Teletransportado a la Tool: " .. objeto.Name) -- Depuración
        else
            print("No se encontró el HumanoidRootPart del jugador.") -- Depuración
        end
    elseif objeto:IsA("Model") and objeto.PrimaryPart then
        -- Si es un modelo, teletransportar al jugador a su PrimaryPart
        local jugador = game.Players.LocalPlayer
        local personaje = jugador.Character
        local humanoidRootPart = personaje and personaje:FindFirstChild("HumanoidRootPart")

        if humanoidRootPart then
            local destino = alCentro and objeto.PrimaryPart.CFrame or objeto.PrimaryPart.CFrame
            -- Teletransportar al jugador a la posición exacta del PrimaryPart del modelo
            humanoidRootPart.CFrame = destino
            print("Teletransportado al Modelo: " .. objeto.Name) -- Depuración
        else
            print("No se encontró el HumanoidRootPart del jugador.") -- Depuración
        end
    else
        print("El objeto no es una herramienta ni un modelo con PrimaryPart.") -- Depuración
    end
end

-- Función para teletransportarse al "Handle" de una fruta
local function TeletransportarAHandleDeFruta(fruta)
    -- Verificar si la fruta tiene un Handle
    local handle = fruta:FindFirstChild("Handle")
    if handle then
        local jugador = game.Players.LocalPlayer
        local personaje = jugador.Character
        local humanoidRootPart = personaje and personaje:FindFirstChild("HumanoidRootPart")

        if humanoidRootPart then
            -- Teletransportar al jugador a la posición del Handle de la fruta
            humanoidRootPart.CFrame = handle.CFrame
            print("Teletransportado al Handle de la fruta: " .. fruta.Name) -- Depuración
        else
            print("No se encontró el HumanoidRootPart del jugador.") -- Depuración
        end
    else
        print("La fruta no tiene un Handle.") -- Depuración
    end
end

-- Variable para saber si el teletransporte está activado
local isTeletransportActive = false

-- Funcionalidad del toggle para Teletransportarse a Frutas
local TeletransportarseAFrutasToggle = Tab:CreateToggle({
    Name = "Teletransportarse a frutas",
    CurrentValue = false,
    Flag = "Toggle1", -- A flag is the identifier for the configuration file, make sure every element has a different flag if you're using configuration saving to ensure no overlaps
    Callback = function(Value)
        if Value then
            -- Activar la búsqueda de frutas en Workspace
            isTeletransportActive = true
            print("Teletransporte activado.") -- Depuración

            -- Activar la búsqueda de frutas
            coroutine.wrap(function()
                while isTeletransportActive do
                    -- Comprobar si hay algún objeto con "Fruit" en su nombre
                    for _, child in pairs(workspace:GetChildren()) do
                        if string.lower(child.Name):find("fruit") then
                            print("Objeto encontrado: " .. child.Name) -- Depuración
                            
                            -- Teletransportarse al Handle de la fruta
                            TeletransportarAHandleDeFruta(child)
                            break -- Teletransportarse solo a la primera fruta que encuentre
                        end
                    end
                    wait(0.005) -- Esperar un poco antes de comprobar nuevamente
                end
            end)()
        else
            -- Desactivar el teletransporte
            isTeletransportActive = false
            print("Teletransporte desactivado.") -- Depuración
        end
    end,
})

-- Detectar cuando se agrega un nuevo hijo a Workspace
workspace.ChildAdded:Connect(function(child)
    -- Verificar si el nombre del nuevo hijo contiene "fruit" (sin importar mayúsculas/minúsculas)
    if string.lower(child.Name):find("fruit") then
        print("Nuevo objeto agregado: " .. child.Name) -- Depuración
        -- Verificar si el teletransporte está activado
        if isTeletransportActive then
            -- Teletransportarse al Handle de la fruta recién agregada
            TeletransportarAHandleDeFruta(child)
        end
    end
end)

-- Función para manejar la reapertura del personaje después de la muerte
local function onCharacterAdded(newCharacter)
    -- Esperar a que el personaje reaparezca
    local jugador = game.Players.LocalPlayer
    local personaje = newCharacter
    local humanoidRootPart = personaje:WaitForChild("HumanoidRootPart")

    -- Si el teletransporte está activado, continuar buscando frutas
    if isTeletransportActive then
        coroutine.wrap(function()
            while isTeletransportActive do
                -- Comprobar si hay algún objeto con "Fruit" en su nombre
                for _, child in pairs(workspace:GetChildren()) do
                    if string.lower(child.Name):find("fruit") then
                        print("Objeto encontrado: " .. child.Name) -- Depuración
                        
                        -- Teletransportarse al Handle de la fruta recién agregada
                        TeletransportarAHandleDeFruta(child)
                        break -- Teletransportarse solo a la primera fruta que encuentre
                    end
                end
                wait(0.005) -- Esperar un poco antes de comprobar nuevamente
            end
        end)()
    end
end

-- Escuchar cuando el personaje es agregado (después de morir)
game.Players.LocalPlayer.CharacterAdded:Connect(onCharacterAdded)



local TweenService7 = game:GetService("TweenService")
local StarterGui7 = game:GetService("StarterGui")

-- Los lugares a los que te teletransportarás
local locations7 = {
    -- (Los lugares de teleportación permanecen iguales)
}

-- Función para teletransportarse con Tween
local function teleportWithTween7()
    local speed7 = 340  -- Velocidad del movimiento (en studs por segundo)

    for i, location7 in ipairs(locations7) do
        -- Calcula la distancia entre el lugar actual y el siguiente
        local currentPos7 = game.Players.LocalPlayer.Character.HumanoidRootPart.Position
        local distance7 = (currentPos7 - location7.Position).Magnitude
        
        -- Calcula la duración del tween en función de la distancia y la velocidad
        local duration7 = distance7 / speed7
        
        -- Crea el Tween
        local tweenInfo7 = TweenInfo.new(duration7, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut)
        local goal7 = {CFrame = location7}
        local tween7 = TweenService7:Create(game.Players.LocalPlayer.Character.HumanoidRootPart, tweenInfo7, goal7)
        
        tween7:Play()
        tween7.Completed:Wait()  -- Espera a que el tween termine

        -- Asegura que el jugador se quede flotando en la posición durante 1 segundo
        local elapsedTime7 = 0
        while elapsedTime7 < 1 do
            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = location7
            elapsedTime7 = elapsedTime7 + 0.1
            wait(0.1)
        end

        -- En la posición 7, espera 10 segundos antes de continuar
        if i == 7 then
            wait(10)
        end

        -- En la posición 9, espera 4 segundos antes de continuar
        if i == 9 then
            wait(4)
        end

        -- En la posición 6, muestra un mensaje
        if i == 6 then
            StarterGui7:SetCore("SendNotification", {
                Title = "Aviso",
                Text = "Equipa la antorcha antes de llegar.",
                Duration = 5
            })
        end

        -- En la posición 8, muestra un mensaje
        if i == 8 then
            StarterGui7:SetCore("SendNotification", {
                Title = "Aviso",
                Text = "Equipa la taza antes de llegar.",
                Duration = 5
            })
        end

        -- En la posición 10, muestra un mensaje
        if i == 10 then
            StarterGui7:SetCore("SendNotification", {
                Title = "Aviso",
                Text = "Habla con el npc tienes 10 segundos.",
                Duration = 10
            })
            wait(10)
        end

        -- En la posición 11, muestra un mensaje
        if i == 11 then
            StarterGui7:SetCore("SendNotification", {
                Title = "Aviso",
                Text = "Habla con el npc tienes 10 segundos.",
                Duration = 10
            })
            wait(10)
        end

        -- En la posición 12, muestra un mensaje
        if i == 12 then
            StarterGui7:SetCore("SendNotification", {
                Title = "Aviso",
                Text = "Mata al npc tienes 30 segundos.",
                Duration = 29
            })
            wait(30)
        end

        -- En la posición 13, muestra un mensaje
        if i == 13 then
            StarterGui7:SetCore("SendNotification", {
                Title = "Aviso",
                Text = "Habla con el npc tienes 10 segundos.",
                Duration = 10
            })
            wait(10)
        end

        -- En la posición 14, muestra un mensaje y espera 45 segundos
        if i == 14 then
            StarterGui7:SetCore("SendNotification", {
                Title = "Aviso!",
                Text = "Equipa la reliquia y mata al npc tienes 30 segundos.",
                Duration = 5
            })
            wait(45)  -- Espera 45 segundos antes de continuar con la compra del Ken

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "KenTalk",
    [2] = "Buy"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

            -- Notificación de compra completada
            StarterGui7:SetCore("SendNotification", {
                Title = "Completado!",
                Text = "Comprado/solo si tienes los 750K",
                Duration = 6
            })
        end
    end
end

-- Crear el toggle para activar el teletransporte y la compra del Ken
local AutoHakiKenToggle = Tab:CreateToggle({
    Name = "Auto Haki De Observación",
    CurrentValue = false,
    Flag = "AutoHakiKenToggle",
    Callback = function(Value)
        if Value then
            teleportWithTween7()
        end
    end,
})



local TweenService1 = game:GetService("TweenService")
local player1 = game.Players.LocalPlayer
local character1 = player1.Character or player1.CharacterAdded:Wait()

-- CFrames objetivo para diferentes juegos
local targetCFrameGame3 = CFrame.new(
    106.253479, 19.3599586, 2832.37158, 
    -0.0656991452, 5.74360159e-09, -0.997839451, 
    1.88883575e-10, 1, 5.74360159e-09, 
    0.997839451, 1.88883575e-10, -0.0656991452
)

-- Cambia este CFrame al que desees para el juego con ID 7449423635
local targetCFrameGame4 = CFrame.new(
    -387.621246, 20.6934395, 5441.47021, 0.728105068, -1.03781339e-08, -0.685465515, -7.74841169e-09, 1, -2.33706743e-08, 0.685465515, 2.23275762e-08, 0.728105068
)

local function moveToCFrame()
    -- Determinar el CFrame objetivo según el ID del juego
    local targetCFrame
    if game.PlaceId == 4442272183 then
        targetCFrame = targetCFrameGame3
    elseif game.PlaceId == 7449423635 then
        targetCFrame = targetCFrameGame4
    else
        print("Este script no está configurado para este juego.")
        return
    end

    if character1 and character1:FindFirstChild("HumanoidRootPart") then
        -- Calcular la distancia entre el jugador y el CFrame objetivo
        local distance1 = (targetCFrame.Position - character1.HumanoidRootPart.Position).Magnitude
        local walkSpeed1 = 340
        local timeToReach1 = distance1 / walkSpeed1

        -- Crear el tween para mover al jugador
        local goal1 = {CFrame = targetCFrame}
        local tweenInfo1 = TweenInfo.new(timeToReach1, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut)
        local tween1 = TweenService1:Create(character1.HumanoidRootPart, tweenInfo1, goal1)

        -- Reproducir el tween
        tween1:Play()
        tween1.Completed:Connect(function()
            print("Movimiento completado.")
        end)
    else
        print("El personaje o su HumanoidRootPart no están disponibles.")
    end
end

-- Conectar el botón para mover al CFrame
local Mar1Button = Tab:CreateButton({ 
   Name = "Mar 1", 
   Callback = function()
       -- Llamar a la función para mover al jugador cuando el botón sea presionado
       moveToCFrame()
       print("Movimiento activado a Mar 1.")
   end,
})




local TweenService2 = game:GetService("TweenService")
local player2 = game.Players.LocalPlayer
local character2 = player2.Character or player2.CharacterAdded:Wait()

-- CFrames objetivo para diferentes juegos
local targetCFrameGame1 = CFrame.new(
    -1166.52283, 7.73542881, 1729.3916, 
    0.353035539, 2.71882357e-08, -0.935609937, 
    2.47146481e-08, 1, 3.83849965e-08, 
    0.935609937, -3.6674539e-08, 0.353035539
)

-- Cambia este CFrame al que desees para el juego con ID 7449423635
local targetCFrameGame2 = CFrame.new(
    -387.621246, 20.6934395, 5441.47021, 0.728105068, -1.03781339e-08, -0.685465515, -7.74841169e-09, 1, -2.33706743e-08, 0.685465515, 2.23275762e-08, 0.728105068
)

local function moveToCFrame2()
    -- Determinar el CFrame objetivo según el ID del juego
    local targetCFrame
    if game.PlaceId == 2753915549 then
        targetCFrame = targetCFrameGame1
    elseif game.PlaceId == 7449423635 then
        targetCFrame = targetCFrameGame2
    else
        print("Este script no está configurado para este juego.")
        return
    end

    if character2 and character2:FindFirstChild("HumanoidRootPart") then
        -- Calcular la distancia entre el jugador y el CFrame objetivo
        local distance2 = (targetCFrame.Position - character2.HumanoidRootPart.Position).Magnitude
        local walkSpeed2 = 340
        local timeToReach2 = distance2 / walkSpeed2

        -- Crear el tween para mover al jugador
        local goal2 = {CFrame = targetCFrame}
        local tweenInfo2 = TweenInfo.new(timeToReach2, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut)
        local tween2 = TweenService2:Create(character2.HumanoidRootPart, tweenInfo2, goal2)

        -- Reproducir el tween
        tween2:Play()
        tween2.Completed:Connect(function()
            print("Movimiento completado.")
        end)
    else
        print("El personaje o su HumanoidRootPart no están disponibles.")
    end
end

-- Conectar el botón para mover al CFrame
local Mar2Button = Tab:CreateButton({ 
   Name = "Mar 2", 
   Callback = function()
       -- Llamar a la función para mover al jugador cuando el botón sea presionado
       moveToCFrame2()
       print("Movimiento activado a Mar 2.")
   end,
})



local TweenService3 = game:GetService("TweenService")
local player3 = game.Players.LocalPlayer
local character3 = player3.Character or player3.CharacterAdded:Wait()

-- CFrames objetivo para diferentes juegos
local targetCFrameGame5 = CFrame.new(
    -1908.35522, 6.5207653, -2536.69995, 
    -0.836797655, 6.05827424e-08, -0.547512293, 
    6.91875783e-08, 1, 4.90718666e-09, 
    0.547512293, -3.37747252e-08, -0.836797655
)

-- Cambia este CFrame al que desees para el juego con ID 7449423635
local targetCFrameGame6 = CFrame.new(
    -1166.52283, 7.73542881, 1729.3916, 
    0.353035539, 2.71882357e-08, -0.935609937, 
    2.47146481e-08, 1, 3.83849965e-08, 
    0.935609937, -3.6674539e-08, 0.353035539
)

local function moveToCFrame3()
    -- Determinar el CFrame objetivo según el ID del juego
    local targetCFrame
    if game.PlaceId == 4442272183 then
        targetCFrame = targetCFrameGame5
    elseif game.PlaceId == 2753915549 then
        targetCFrame = targetCFrameGame6
    else
        print("Este script no está configurado para este juego.")
        return
    end

    if character3 and character3:FindFirstChild("HumanoidRootPart") then
        -- Calcular la distancia entre el jugador y el CFrame objetivo
        local distance3 = (targetCFrame.Position - character3.HumanoidRootPart.Position).Magnitude
        local walkSpeed3 = 340
        local timeToReach3 = distance3 / walkSpeed3

        -- Crear el tween para mover al jugador
        local goal3 = {CFrame = targetCFrame}
        local tweenInfo3 = TweenInfo.new(timeToReach3, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut)
        local tween3 = TweenService3:Create(character3.HumanoidRootPart, tweenInfo3, goal3)

        -- Reproducir el tween
        tween3:Play()
        tween3.Completed:Connect(function()
            print("Movimiento completado.")
        end)
    else
        print("El personaje o su HumanoidRootPart no están disponibles.")
    end
end

-- Conectar el botón para mover al CFrame
local Mar3Button = Tab:CreateButton({ 
   Name = "Mar 3", 
   Callback = function()
       -- Llamar a la función para mover al jugador cuando el botón sea presionado
       moveToCFrame3()
       print("Movimiento activado a Mar 3.")
   end,
})



local PiratesButton = Tab:CreateButton({ 
   Name = "Unirse a Piratas", 
   Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "SetTeam",
    [2] = "Pirates"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

   end,
})



local MarinesButton = Tab:CreateButton({ 
   Name = "Unirse a Marineros", 
   Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "SetTeam",
    [2] = "Marines"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

   end,
})



local Tab = Window:CreateTab("Comprar Chips", nil)



local BuyChipFlame = Tab:CreateButton({
   Name = "Comprar Chip De Fuego",
   Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "RaidsNpc",
    [2] = "Select",
    [3] = "Flame"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

   end,
})



local BuyChipIce = Tab:CreateButton({
   Name = "Comprar Chip De Hielo",
   Callback = function()


function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "RaidsNpc",
    [2] = "Select",
    [3] = "Ice"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


   end,
})



local BuyChipLight = Tab:CreateButton({
   Name = "Comprar Chip De Luz",
   Callback = function()


function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "RaidsNpc",
    [2] = "Select",
    [3] = "Light"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


   end,
})



local BuyChipBuddha = Tab:CreateButton({
   Name = "Comprar Chip De Budda",
   Callback = function()


function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "RaidsNpc",
    [2] = "Select",
    [3] = "Buddha"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


   end,
})



local BuyChipRumble = Tab:CreateButton({
   Name = "Comprar Chip De Trueno",
   Callback = function()


function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "RaidsNpc",
    [2] = "Select",
    [3] = "Rumble"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


   end,
})



local BuyChipDark = Tab:CreateButton({
   Name = "Comprar Chip De Oscuridad",
   Callback = function()


function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "RaidsNpc",
    [2] = "Select",
    [3] = "Dark"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


   end,
})



local BuyChipPhoenix = Tab:CreateButton({
   Name = "Comprar Chip De Fenix",
   Callback = function()


function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "RaidsNpc",
    [2] = "Select",
    [3] = "Phoenix"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


   end,
})



local BuyChipDough = Tab:CreateButton({
   Name = "Comprar Chip De Masa",
   Callback = function()


function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "RaidsNpc",
    [2] = "Select",
    [3] = "Dough"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


   end,
})

local Tab = Window:CreateTab("Compras", nil)



local BuyRandomFruitButton = Tab:CreateButton({
   Name = "Comprar Fruta Random",
   Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "Cousin",
    [2] = "Buy"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

   end,
})



local Divider = Tab:CreateDivider()



local BuyBlackLeg = Tab:CreateButton({
    Name = "Comprar Black Leg",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyBlackLeg"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyElectro = Tab:CreateButton({
    Name = "Comprar Electro",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyElectro"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyFishManKarate = Tab:CreateButton({
    Name = "Comprar Fishman Karate",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyFishmanKarate"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyDragonClaw = Tab:CreateButton({
    Name = "Comprar Dragon Claw/Breath",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BlackbeardReward",
    [2] = "DragonClaw",
    [3] = "2"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuySuperHuman = Tab:CreateButton({
    Name = "Comprar SuperHuman",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuySuperhuman"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyDeathStep = Tab:CreateButton({
    Name = "Comprar Death Step",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyDeathStep"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuySharkmanKarate = Tab:CreateButton({
    Name = "Comprar Sharkman Karate",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuySharkmanKarate"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyDragonTalon = Tab:CreateButton({
    Name = "Comprar Dragon Talon",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyDragonTalon"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyGodHuman = Tab:CreateButton({
    Name = "Comprar God Human",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyGodhuman"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuySanguineArt = Tab:CreateButton({
    Name = "Comprar Sanguine Art",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuySanguineArt"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local Divider = Tab:CreateDivider()



local BuyHakiGeppo = Tab:CreateButton({
    Name = "Comprar Haki Geppo",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyHaki",
    [2] = "Geppo"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


    end,
})



local BuyHakiGeppo = Tab:CreateButton({
    Name = "Comprar Haki Buso",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyHaki",
    [2] = "Buso"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


    end,
})



local BuyHakiGeppo = Tab:CreateButton({
    Name = "Comprar Haki Soru",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyHaki",
    [2] = "Soru"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


    end,
})



local BuyHakiGeppo = Tab:CreateButton({
    Name = "Comprar Haki De Observacion",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "KenTalk",
    [2] = "Buy"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))


    end,
})



local Divider = Tab:CreateDivider()



local BuyKatana = Tab:CreateButton({
    Name = "Comprar Katana",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyItem",
    [2] = "Katana"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyCutlass = Tab:CreateButton({
    Name = "Comprar Cutlass",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyItem",
    [2] = "Cutlass"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyDualKatana = Tab:CreateButton({
    Name = "Comprar Dual Katana",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyItem",
    [2] = "Dual Katana"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyIronMace = Tab:CreateButton({
    Name = "Comprar Iron Mace",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyItem",
    [2] = "Iron Mace"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyTripleKatana = Tab:CreateButton({
    Name = "Comprar Triple Katana",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyItem",
    [2] = "Triple Katana"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyPipe = Tab:CreateButton({
    Name = "Comprar Pipe",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyItem",
    [2] = "Pipe"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyDualHeadedBlade = Tab:CreateButton({
    Name = "Comprar Dual-Headed Blade",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyItem",
    [2] = "Dual-Headed Blade"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuySoulCane = Tab:CreateButton({
    Name = "Comprar Soul Cane",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyItem",
    [2] = "Soul Cane"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})



local BuyBisento = Tab:CreateButton({
    Name = "Comprar Bisento",
    Callback = function()

function getNil(name,class) for _,v in pairs(getnilinstances())do if v.ClassName==class and v.Name==name then return v;end end end

local args = {
    [1] = "BuyItem",
    [2] = "Bisento"
}

game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))

    end,
})
