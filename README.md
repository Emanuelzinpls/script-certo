-- Xurrasco Panel com Aimbot NPC, FOV visível, tecla Q, botão Delet, hitbox aumentada para armas e visualização da hitbox em amarelo
local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local mouse = player:GetMouse()
local runService = game:GetService("RunService")
local userInput = game:GetService("UserInputService")

-- Variáveis de controle
local aimbotActive = false
local fovRadius = 190
local wallhackActive = false
local targetMargin = 5  -- Margem de erro para a hitbox aumentada
local maxAimbotDistance = 100  -- Distância máxima para o aimbot
local projectileDamage = 10  -- Dano do projétil
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

-- Função para disparar projétil com hitbox aumentada
local function fireProjectile()
    -- Tamanho do "alvo" (margem de erro)
    local targetPosition = camera.CFrame.Position + camera.CFrame.LookVector * 50  -- Aumente o valor para mais distância
    local enemies = {}  -- Para armazenar os inimigos atingidos

    -- Desenhando a hitbox aumentada (caixa amarela) em torno dos NPCs atingidos
    for _, obj in pairs(workspace:GetChildren()) do
        if obj:IsA("Model") and obj:FindFirstChild("Head") and obj:FindFirstChild("Humanoid") and not game.Players:GetPlayerFromCharacter(obj) then
            local headPosition = obj.Head.Position
            local distance = (headPosition - targetPosition).Magnitude

            -- Se o inimigo estiver dentro da margem do projétil (multiplicada pela hitbox)
            if distance <= targetMargin * hitboxMultiplier then
                -- Desenhar a hitbox como uma caixa amarela ao redor do NPC
                local screenPos, onScreen = camera:WorldToViewportPoint(headPosition)
                if onScreen then
                    local box = Drawing.new("Square")
                    box.Position = Vector2.new(screenPos.X - targetMargin * hitboxMultiplier, screenPos.Y - targetMargin * hitboxMultiplier)
                    box.Size = Vector2.new(targetMargin * 2 * hitboxMultiplier, targetMargin * 2 * hitboxMultiplier)
                    box.Thickness = 2
                    box.Color = Color3.fromRGB(255, 255, 0)  -- Cor amarela
                    box.Filled = false
                    box.Visible = hitboxVisible  -- Agora, a visibilidade é controlada pela variável 'hitboxVisible'

                    -- Atrasar a remoção da hitbox para que seja visível por um tempo
                    wait(0.2)
                    box.Visible = hitboxVisible
                end

                -- Aplicar dano ao inimigo
                local humanoid = obj:FindFirstChild("Humanoid")
                if humanoid then
                    humanoid:TakeDamage(projectileDamage)  -- Ajuste o dano conforme necessário
                    print("Inimigo atingido!")
                end
            end
        end
    end
end

-- Botão Aimbot NPC
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

-- Tecla Q ativa/desativa Aimbot e aumenta/diminui a hitbox
userInput.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.Q then
        aimbotActive = not aimbotActive
        fovCircle.Visible = aimbotActive
        aimbotBtn.Text = aimbotActive and "🧠 Aimbot NPC [ON]" or "🧠 Aimbot NPC"
        
        -- Toggle da hitbox
        if hitboxMultiplier == 1 then
            hitboxMultiplier = 2  -- Aumenta a hitbox
            hitboxVisible = true  -- Torna a hitbox visível
            print("Hitbox aumentada e visível!")
        else
            hitboxMultiplier = 1  -- Reseta para o tamanho original
            hitboxVisible = false  -- Torna a hitbox invisível
            print("Hitbox normalizada e invisível!")
        end
    end
end)

-- Botão Delet
local deletBtn = Instance.new("TextButton")
deletBtn.Size = UDim2.new(0, 150, 0, 40)
deletBtn.Position = UDim2.new(0.5, -75, 0, 180)
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
    print("Tudo removido com sucesso.")
end)

-- Atualização da mira, FOV e hitbox
runService.RenderStepped:Connect(function()
    if aimbotActive then
        aimbotNPC()
    end

    if fovCircle.Visible then
        fovCircle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
    end

    if hitboxVisible then
        fireProjectile()  -- Ativa a função de disparo com a hitbox aumentada
    end
end)
