-- Xurrasco Panel com Aimbot NPC, FOV visível, tecla Q, botão Delet e 2x Hitbox
local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local mouse = player:GetMouse()
local runService = game:GetService("RunService")
local userInput = game:GetService("UserInputService")

-- Variáveis de controle
local aimbotActive = false
local fovRadius = 190
local hitboxActive = false
local hitboxMultiplier = 2 -- 2x Hitbox
local hitboxes = {} -- Tabela para armazenar as hitboxes desenhadas

-- Raycast parameters
local rayParams = RaycastParams.new()
rayParams.FilterDescendantsInstances = {player.Character}
rayParams.FilterType = Enum.RaycastFilterType.Blacklist

-- GUI Principal
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

-- Frame principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 400, 0, 250)
frame.Position = UDim2.new(0.5, -200, 0.5, -125)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.5
frame.Active = true
frame.Draggable = true
frame.Parent = gui
frame.Visible = false

-- Ícone flutuante central superior
local icon = Instance.new("ImageButton")
icon.Size = UDim2.new(0, 50, 0, 50)
icon.Position = UDim2.new(0.5, -25, 0, 10)
icon.Image = "rbxassetid://105182366707019"
icon.BackgroundTransparency = 1
icon.Parent = gui
icon.Active = true
icon.Draggable = true

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

-- Função Aimbot NPC
local function getClosestNPC()
    local closest = nil
    local shortest = math.huge

    for _, npc in pairs(workspace:GetDescendants()) do
        if npc:IsA("Model") and npc:FindFirstChild("Head") and npc:FindFirstChild("Humanoid") and not game.Players:GetPlayerFromCharacter(npc) then
            local humanoid = npc:FindFirstChild("Humanoid")
            
            -- Verifica se o NPC está vivo
            if humanoid and humanoid.Health > 0 then
                local headPos = npc.Head.Position
                local screenPos, onScreen = camera:WorldToViewportPoint(headPos)
                if onScreen then
                    local distance = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(mouse.X, mouse.Y)).Magnitude
                    if distance < shortest and distance <= fovRadius then
                        shortest = distance
                        closest = npc.Head
                    end
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

-- Função 2x Hitbox
local function drawHitbox(npc)
    if hitboxActive then
        if npc:FindFirstChild("Head") then
            local headPos = npc.Head.Position
            local headSize = npc.Head.Size
            local hitboxSize = headSize * hitboxMultiplier
            local screenPos, onScreen = camera:WorldToViewportPoint(headPos)
            
            -- Desenha a caixa de hitbox apenas se o NPC estiver visível
            if onScreen then
                -- Criar a caixa de hitbox
                local hitboxBox = Drawing.new("Square")
                hitboxBox.Position = Vector2.new(screenPos.X - hitboxSize.X/2, screenPos.Y - hitboxSize.Y/2)
                hitboxBox.Size = Vector2.new(hitboxSize.X, hitboxSize.Y)
                hitboxBox.Color = Color3.fromRGB(255, 255, 0)
                hitboxBox.Thickness = 2
                hitboxBox.Filled = false
                table.insert(hitboxes, hitboxBox)
            end
        end
    end
end

local function clearHitboxes()
    -- Limpa as hitboxes desenhadas
    for _, box in ipairs(hitboxes) do
        box:Remove()
    end
    hitboxes = {}
end

-- Botão 2x Hitbox
local hitboxBtn = Instance.new("TextButton")
hitboxBtn.Size = UDim2.new(0, 150, 0, 40)
hitboxBtn.Position = UDim2.new(0.5, -75, 0, 120)
hitboxBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 0)
hitboxBtn.Text = "2x Hitbox"
hitboxBtn.Font = Enum.Font.GothamBold
hitboxBtn.TextSize = 18
hitboxBtn.TextColor3 = Color3.new(1, 1, 1)
hitboxBtn.Parent = frame

hitboxBtn.MouseButton1Click:Connect(function()
    hitboxActive = not hitboxActive
    hitboxBtn.Text = hitboxActive and "2x Hitbox [ON]" or "2x Hitbox"
    if not hitboxActive then
        clearHitboxes() -- Limpa as hitboxes
    end
end)

-- Atualização da mira e FOV
runService.RenderStepped:Connect(function()
    if aimbotActive then
        aimbotNPC()
    end

    if fovCircle.Visible then
        fovCircle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
    end

    -- Limpa e redesenha as hitboxes para NPCs
    clearHitboxes()
    for _, npc in pairs(workspace:GetDescendants()) do
        if npc:IsA("Model") and npc:FindFirstChild("Humanoid") then
            drawHitbox(npc)
        end
    end
end)

