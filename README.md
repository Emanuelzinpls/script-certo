-- Xurrasco Panel FINAL com Aimbot, FOV, Delet, e 2x Hitbox
local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local mouse = player:GetMouse()
local runService = game:GetService("RunService")
local userInput = game:GetService("UserInputService")

-- Variáveis
local aimbotActive = false
local fovRadius = 190
local maxAimbotDistance = 100 -- 10 metros = 100 studs
local hitboxEnabled = false
local hitboxes = {}

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "XurrascoPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- FOV Visual
local fovCircle = Drawing.new("Circle")
fovCircle.Radius = fovRadius
fovCircle.Thickness = 2
fovCircle.Color = Color3.fromRGB(255, 0, 0)
fovCircle.Filled = false
fovCircle.Visible = false

-- Painel
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 400, 0, 300)
frame.Position = UDim2.new(0.5, -200, 0.5, -150)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.5
frame.Active = true
frame.Draggable = true
frame.Visible = false
frame.Parent = gui

-- Ícone flutuante
local icon = Instance.new("ImageButton")
icon.Size = UDim2.new(0, 50, 0, 50)
icon.Position = UDim2.new(0.5, -25, 0, 10)
icon.Image = "rbxassetid://105182366707019"
icon.BackgroundTransparency = 1
icon.Draggable = true
icon.Parent = gui

icon.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible
end)

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundColor3 = Color3.fromRGB(255, 85, 0)
title.Text = "🔥 Xurrasco Panel 🔥"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.Parent = frame

-- Aimbot Função
local function getClosestNPC()
    local closest = nil
    local shortest = math.huge
    for _, npc in pairs(workspace:GetDescendants()) do
        if npc:IsA("Model") and npc:FindFirstChild("Head") and npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 and not game.Players:GetPlayerFromCharacter(npc) then
            local headPos = npc.Head.Position
            local screenPos, onScreen = camera:WorldToViewportPoint(headPos)
            local distance = (camera.CFrame.Position - headPos).Magnitude
            if onScreen and distance <= maxAimbotDistance then
                local dist2D = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(mouse.X, mouse.Y)).Magnitude
                if dist2D < shortest and dist2D <= fovRadius then
                    shortest = dist2D
                    closest = npc.Head
                end
            end
        end
    end
    return closest
end

local function aimbotNPC()
    local target = getClosestNPC()
    if target then
        camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
    end
end

-- Botão Aimbot
local aimbotBtn = Instance.new("TextButton")
aimbotBtn.Size = UDim2.new(0, 150, 0, 40)
aimbotBtn.Position = UDim2.new(0.5, -75, 0, 60)
aimbotBtn.BackgroundColor3 = Color3.fromRGB(0, 0, 255)
aimbotBtn.Text = "🧠 Aimbot NPC"
aimbotBtn.Font = Enum.Font.GothamBold
aimbotBtn.TextSize = 18
aimbotBtn.TextColor3 = Color3.new(1, 1, 1)
aimbotBtn.Parent = frame

aimbotBtn.MouseButton1Click:Connect(function()
    aimbotActive = not aimbotActive
    fovCircle.Visible = aimbotActive
    aimbotBtn.Text = aimbotActive and "🧠 Aimbot NPC [ON]" or "🧠 Aimbot NPC"
end)

-- Tecla Q alterna Aimbot
userInput.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.Q then
        aimbotBtn:Activate()
    end
end)

-- Botão Delet
local deletBtn = Instance.new("TextButton")
deletBtn.Size = UDim2.new(0, 150, 0, 40)
deletBtn.Position = UDim2.new(0.5, -75, 0, 110)
deletBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
deletBtn.Text = "❌ Delet"
deletBtn.Font = Enum.Font.GothamBold
deletBtn.TextSize = 18
deletBtn.TextColor3 = Color3.new(1, 1, 1)
deletBtn.Parent = frame

deletBtn.MouseButton1Click:Connect(function()
    aimbotActive = false
    fovCircle.Visible = false
    gui:Destroy()
    fovCircle:Remove()
    for _, h in pairs(hitboxes) do
        h:Destroy()
    end
    table.clear(hitboxes)
end)

-- Botão 2x Hitbox
local hitboxBtn = Instance.new("TextButton")
hitboxBtn.Size = UDim2.new(0, 150, 0, 40)
hitboxBtn.Position = UDim2.new(0.5, -75, 0, 160)
hitboxBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 0)
hitboxBtn.Text = "⚔️ 2x Hitbox"
hitboxBtn.Font = Enum.Font.GothamBold
hitboxBtn.TextSize = 18
hitboxBtn.TextColor3 = Color3.new(0, 0, 0)
hitboxBtn.Parent = frame

local function toggleHitbox()
    hitboxEnabled = not hitboxEnabled

    if hitboxEnabled then
        for _, npc in pairs(workspace:GetDescendants()) do
            if npc:IsA("Model") and npc:FindFirstChild("Head") and npc:FindFirstChild("Humanoid") and npc.Humanoid.Health > 0 and not game.Players:GetPlayerFromCharacter(npc) then
                local billboard = Instance.new("BillboardGui")
                billboard.Name = "XurrascoHitbox"
                billboard.Adornee = npc.Head
                billboard.Size = UDim2.new(4, 0, 4, 0)
                billboard.AlwaysOnTop = true
                billboard.Parent = npc

                local box = Instance.new("Frame")
                box.Size = UDim2.new(1, 0, 1, 0)
                box.BackgroundColor3 = Color3.new(1, 1, 0)
                box.BackgroundTransparency = 0.3
                box.BorderSizePixel = 2
                box.BorderColor3 = Color3.new(1, 1, 0)
                box.Parent = billboard

                table.insert(hitboxes, billboard)
            end
        end
    else
        for _, gui in pairs(hitboxes) do
            if gui and gui.Parent then
                gui:Destroy()
            end
        end
        table.clear(hitboxes)
    end
end

hitboxBtn.MouseButton1Click:Connect(toggleHitbox)

-- Render loop
runService.RenderStepped:Connect(function()
    if aimbotActive then
        aimbotNPC()
    end

    if fovCircle.Visible then
        fovCircle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
    end
end)
