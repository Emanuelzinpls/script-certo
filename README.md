-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local rayParams = RaycastParams.new()
rayParams.FilterType = Enum.RaycastFilterType.Blacklist

-- Parâmetros do Aimbot
local fovRadius = 200
local aimbotNpcActive = false
local lastAimbotUpdate = 0  -- Tempo da última atualização do Aimbot

-- Desenhar FOV
local fovCircle = Drawing.new("Circle")
fovCircle.Visible = false  -- Inicialmente invisível
fovCircle.Radius = fovRadius
fovCircle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
fovCircle.Color = Color3.new(1, 1, 0)
fovCircle.Thickness = 2
fovCircle.Transparency = 1

-- Função para verificar se o alvo está dentro do FOV
local function isInFOV(targetPosition)
    local screenPos, onScreen = camera:WorldToViewportPoint(targetPosition)
    if onScreen then
        local distance = (Vector2.new(screenPos.X, screenPos.Y) - fovCircle.Position).Magnitude
        return distance <= fovRadius
    end
    return false
end

-- Aimbot NPC: Mira na cabeça de NPCs
local function aimbotNpc()
    local closestTarget = nil
    local closestDistance = math.huge

    -- Verificar NPCs próximos (para evitar travamento)
    local playerPos = player.Character and player.Character.HumanoidRootPart.Position
    if playerPos then
        for _, npc in pairs(workspace:GetDescendants()) do
            if npc:IsA("Model") and npc:FindFirstChild("Head") and npc:FindFirstChild("Humanoid") then
                if not npc:FindFirstChild("HumanoidRootPart") then  -- Ignora players
                    local head = npc.Head
                    if head and isInFOV(head.Position) then
                        local distance = (head.Position - playerPos).Magnitude
                        if distance < closestDistance then
                            closestDistance = distance
                            closestTarget = head
                        end
                    end
                end
            end
        end
    end

    -- Mira no NPC mais próximo
    if closestTarget then
        -- Ajusta a CFrame da câmera para mirar na cabeça do NPC suavemente
        local humanoidRootPart = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            local targetPosition = closestTarget.Position
            local currentCameraPos = camera.CFrame.Position
            local direction = (targetPosition - currentCameraPos).unit
            local smoothCameraPos = currentCameraPos + direction * 0.1  -- Suaviza a câmera

            -- Define a nova CFrame da câmera suavemente
            camera.CFrame = CFrame.new(smoothCameraPos, targetPosition)
        end
    end
end

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "XurrascoPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- Criando o painel flutuante
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 400, 0, 300)
frame.Position = UDim2.new(0.5, -200, 0.5, -150)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.5
frame.Active = true
frame.Draggable = true
frame.Parent = gui
frame.Visible = false

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

-- Ícone do painel
local iconButton = Instance.new("ImageButton")
iconButton.Size = UDim2.new(0, 50, 0, 50)
iconButton.Position = UDim2.new(0.5, -25, 0, 10)
iconButton.BackgroundTransparency = 1
iconButton.Image = "rbxassetid://105182366707019"
iconButton.Parent = gui

-- Alterna a visibilidade do painel
iconButton.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible
end)

-- Botão para ativar/desativar o Aimbot NPC
local aimbotNpcButton = Instance.new("TextButton")
aimbotNpcButton.Size = UDim2.new(0, 150, 0, 40)
aimbotNpcButton.Position = UDim2.new(0.5, -75, 0, 60)
aimbotNpcButton.BackgroundColor3 = Color3.fromRGB(255, 170, 0)
aimbotNpcButton.Text = "Ativar Aimbot NPC"
aimbotNpcButton.Font = Enum.Font.GothamBold
aimbotNpcButton.TextSize = 18
aimbotNpcButton.TextColor3 = Color3.new(1, 1, 1)
aimbotNpcButton.Parent = frame

-- Adicionando o ícone do Pica-Pau ao botão
local iconLabel = Instance.new("ImageLabel")
iconLabel.Size = UDim2.new(0, 30, 0, 30)
iconLabel.Position = UDim2.new(0, 0, 0.5, -15)  -- Alinha o ícone à esquerda do texto
iconLabel.BackgroundTransparency = 1
iconLabel.Image = "rbxassetid://1225495948"  -- ID do ícone do Pica-Pau (substitua pelo ID correto)
iconLabel.Parent = aimbotNpcButton

-- Função para ativar/desativar o Aimbot NPC
aimbotNpcButton.MouseButton1Click:Connect(function()
    aimbotNpcActive = not aimbotNpcActive
    if aimbotNpcActive then
        fovCircle.Visible = true  -- Ativa o FOV
        aimbotNpcButton.Text = "Desativar Aimbot NPC"
    else
        fovCircle.Visible = false  -- Desativa o FOV
        aimbotNpcButton.Text = "Ativar Aimbot NPC"
    end
end)

-- Função de Deletação
local function delet()
    -- Desativa o Aimbot NPC
    aimbotNpcActive = false
    fovCircle.Visible = false

    -- Remove a interface do painel
    if gui then
        gui:Destroy()
    end

    -- Desativa o script atual
    game:GetService("RunService").RenderStepped:Disconnect()  -- Remove o loop de RenderStepped
    print("Todos os recursos foram removidos com sucesso.")
end

-- Botão para ativar a função "Delet"
local deleteButton = Instance.new("TextButton")
deleteButton.Size = UDim2.new(0, 150, 0, 40)
deleteButton.Position = UDim2.new(0.5, -75, 0, 120)
deleteButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
deleteButton.Text = "Deletar"
deleteButton.Font = Enum.Font.GothamBold
deleteButton.TextSize = 18
deleteButton.TextColor3 = Color3.new(1, 1, 1)
deleteButton.Parent = frame

-- Conecta a função de deletação ao botão
deleteButton.MouseButton1Click:Connect(function()
    delet()  -- Chama a função para remover os recursos
end)

-- Executa o Aimbot NPC se estiver ativo, mas limitando a atualização para melhorar o desempenho
game:GetService("RunService").RenderStepped:Connect(function()
    -- Atualiza a posição do FOV
    fovCircle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)

    -- A cada 0.1 segundos, atualiza a mira do aimbot (evita travamentos)
    if tick() - lastAimbotUpdate >= 0.1 then
        if aimbotNpcActive then
            aimbotNpc()  -- Chama a função do Aimbot NPC
        end
        lastAimbotUpdate = tick()  -- Atualiza o tempo da última execução do Aimbot
    end
end)
