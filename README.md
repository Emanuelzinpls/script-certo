-- Xurrasco Panel com Aimbot NPC, FOV visível, tecla Q, botão Delet, 2x Hitbox e visualização da hitbox em amarelo
local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local mouse = player:GetMouse()
local runService = game:GetService("RunService")
local userInput = game:GetService("UserInputService")

-- Variáveis de controle
local aimbotActive = false
local fovRadius = 190
local wallhackActive = false
local hitboxMultiplier = 1  -- Controla o aumento da hitbox (inicializado em 1)
local hitboxVisible = false  -- Controle de visibilidade da hitbox aumentada

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

-- Função para aumentar a hitbox de todos os NPCs
local function increaseHitboxForAllNPCs()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and obj:FindFirstChild("Humanoid") then
            -- Aumentando o tamanho das partes principais (Head, HumanoidRootPart, etc)
            local humanoid = obj:FindFirstChild("Humanoid")
            local head = obj:FindFirstChild("Head")
            local humanoidRootPart = obj:FindFirstChild("HumanoidRootPart")

            if head then
                head.Size = head.Size * hitboxMultiplier  -- Aumenta a hitbox da cabeça
            end

            if humanoidRootPart then
                humanoidRootPart.Size = humanoidRootPart.Size * hitboxMultiplier  -- Aumenta a hitbox do corpo
            end

            -- Aumenta o tamanho das partes do corpo do NPC (caso haja outras partes, como braços, pernas)
            for _, part in pairs(obj:GetChildren()) do
                if part:IsA("Part") then
                    part.Size = part.Size * hitboxMultiplier  -- Aumenta a hitbox das partes
                end
            end
        end
    end
end

-- Função para desenhar a hitbox
local function drawHitboxForNPCs()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and obj:FindFirstChild("Humanoid") then
            local humanoidRootPart = obj:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                local screenPos, onScreen = camera:WorldToViewportPoint(humanoidRootPart.Position)
                if onScreen then
                    -- Desenhando a hitbox aumentada (caixa amarela) ao redor dos NPCs
                    local box = Drawing.new("Square")
                    box.Position = Vector2.new(screenPos.X - humanoidRootPart.Size.X / 2, screenPos.Y - humanoidRootPart.Size.Y / 2)
                    box.Size = Vector2.new(humanoidRootPart.Size.X * hitboxMultiplier, humanoidRootPart.Size.Y * hitboxMultiplier)
                    box.Thickness = 2
                    box.Color = Color3.fromRGB(255, 255, 0)  -- Cor amarela
                    box.Filled = false
                    box.Visible = hitboxVisible  -- Agora, a visibilidade é controlada pela variável 'hitboxVisible'

                    -- Atrasar a remoção da hitbox para que seja visível por um tempo
                    wait(0.2)
                    box.Visible = hitboxVisible
                end
            end
        end
    end
end

-- Função Aimbot NPC
local function getClosestNPC()
    local closest = nil
    local shortest = math.huge

    for _, npc in pairs(workspace:GetDescendants()) do
        if npc:IsA("Model") and npc:FindFirstChild("Head") and npc:FindFirstChild("Humanoid") and not game.Players:GetPlayerFromCharacter(npc) then
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

    return closest
end

local function aimbotNPC()
    local target = getClosestNPC()
    if target then
        camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
    end
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
    if hitboxMultiplier == 1 then
        hitboxMultiplier = 2  -- Aumenta a hitbox
        hitboxVisible = true  -- Torna a hitbox visível
        hitboxBtn.Text = "2x Hitbox [ON]"
        increaseHitboxForAllNPCs()  -- Aumenta a hitbox de todos os NPCs
        print("Hitbox aumentada de todos os NPCs!")
    else
        hitboxMultiplier = 1  -- Reseta para o tamanho original
        hitboxVisible = false  -- Torna a hitbox invisível
        hitboxBtn.Text = "2x Hitbox"
        print("Hitbox normalizada de todos os NPCs!")
    end
end)

-- Tecla Q ativa/desativa Aimbot
userInput.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.Q then
        aimbotActive = not aimbotActive
        fovCircle.Visible = aimbotActive
        aimbotBtn.Text = aimbotActive and "🧠 Aimbot NPC [ON]" or "🧠 Aimbot NPC"
    end

    -- Tecla H ativa/desativa o aumento da hitbox de todos os NPCs
    if not processed and input.KeyCode == Enum.KeyCode.H then
        if hitboxMultiplier == 1 then
            hitboxMultiplier = 2  -- Aumenta a hitbox
            hitboxVisible = true  -- Torna a hitbox visível
            increaseHitboxForAllNPCs()  -- Aumenta a hitbox de todos os NPCs
            print("Hitbox aumentada de todos os NPCs!")
        else
            hitboxMultiplier = 1  -- Reseta para o tamanho original
            hitboxVisible = false  -- Torna a hitbox invisível
            print("Hitbox normalizada de todos os NPCs!")
        end
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

    -- Desenha a hitbox aumentada se estiver ativada
    if hitboxVisible then
        drawHitboxForNPCs()
    end
end)
