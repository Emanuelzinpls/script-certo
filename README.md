-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local rayParams = RaycastParams.new()  -- Parâmetros do Raycast

-- Parâmetros do Aimbot
local fovRadius = 100 -- Definindo o raio do FOV (quanto maior, maior a área em que o aimbot procurará alvos)
local aimbotActive = false -- Controle de ativação do Aimbot

-- Função para verificar se o alvo está dentro do FOV
local function isInFOV(targetPosition)
    -- Calcula a posição do alvo em relação à câmera
    local cameraPosition = camera.CFrame.Position
    local direction = (targetPosition - cameraPosition).unit
    local dotProduct = camera.CFrame.LookVector:Dot(direction)
    
    -- FOV é baseado no ângulo da câmera
    return dotProduct > math.cos(math.rad(fovRadius)) -- Se estiver dentro do FOV
end

-- Função para desenhar o FOV na tela
local function drawFOV()
    local fovCircle = Instance.new("Frame")
    fovCircle.Size = UDim2.new(0, fovRadius * 2, 0, fovRadius * 2)
    fovCircle.Position = UDim2.new(0.5, -fovRadius, 0.5, -fovRadius)
    fovCircle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    fovCircle.BackgroundTransparency = 0.5
    fovCircle.Parent = player.PlayerGui
end

-- Função para mirar na cabeça dos NPCs
local function aimbotNPC()
    local mouse = player:GetMouse()
    local target = nil
    local closestDistance = math.huge

    -- Encontra o NPC mais próximo
    for _, npc in pairs(game.Workspace:GetChildren()) do
        if npc:IsA("Model") and npc:FindFirstChild("Humanoid") and npc:FindFirstChild("Head") then
            local targetPosition = npc.Head.Position

            -- Verifica se o NPC está dentro do FOV
            if isInFOV(targetPosition) then
                local distance = (mouse.Hit.p - targetPosition).Magnitude
                if distance < closestDistance then
                    target = npc
                    closestDistance = distance
                end
            end
        end
    end

    -- Mira para o NPC mais próximo dentro do FOV
    if target then
        local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            local direction = (target.Head.Position - humanoidRootPart.Position).unit
            camera.CFrame = CFrame.lookAt(camera.CFrame.Position, target.Head.Position)  -- Mira para a cabeça do NPC

            -- Aqui você pode simular o disparo ou a ação de atirar
            print(player.Name .. " está mirando em " .. target.Name)
        end
    end
end

-- Criar a interface do painel flutuante
local gui = Instance.new("ScreenGui")
gui.Name = "XurrascoPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- Criar a interface do painel
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 400, 0, 250)
frame.Position = UDim2.new(0.5, -200, 0.5, -125)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.5
frame.Active = true
frame.Draggable = true
frame.Parent = gui
frame.Visible = false  -- Inicialmente invisível

-- Título do painel
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(255, 85, 0)
title.Text = "🔥 Xurrasco Panel 🔥"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.Parent = frame

-- Ícone do Brr Brr Patapim
local iconButton = Instance.new("ImageButton")
iconButton.Size = UDim2.new(0, 50, 0, 50)
iconButton.Position = UDim2.new(0, 10, 0, 10)  -- Posição do ícone no canto superior esquerdo
iconButton.BackgroundTransparency = 1
iconButton.Image = "rbxassetid://105182366707019"  -- Coloque o ID do seu asset aqui
iconButton.Parent = gui

-- Função para abrir e minimizar o painel ao clicar no ícone
iconButton.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible  -- Alterna a visibilidade do painel
end)

-- Botão para ativar/desativar o Aimbot NPC
local aimbotButton = Instance.new("TextButton")
aimbotButton.Size = UDim2.new(0, 150, 0, 40)
aimbotButton.Position = UDim2.new(0.5, -75, 0, 120)
aimbotButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Cor do botão
aimbotButton.Text = "Ativar Aimbot NPC"
aimbotButton.Font = Enum.Font.GothamBold
aimbotButton.TextSize = 18
aimbotButton.TextColor3 = Color3.new(1, 1, 1)
aimbotButton.Parent = frame

-- Função para ativar/desativar o Aimbot NPC
aimbotButton.MouseButton1Click:Connect(function()
    aimbotActive = not aimbotActive  -- Alterna o estado do Aimbot
    if aimbotActive then
        aimbotButton.Text = "Desativar Aimbot NPC"
        print("Aimbot NPC Ativado")
    else
        aimbotButton.Text = "Ativar Aimbot NPC"
        print("Aimbot NPC Desativado")
    end
end)

-- Rodando o Aimbot enquanto ele estiver ativado
game:GetService("RunService").RenderStepped:Connect(function()
    if aimbotActive then
        aimbotNPC()  -- Chama a função do Aimbot para mirar nos NPCs
        drawFOV()  -- Desenha o FOV enquanto o aimbot estiver ativo
    end
end)
