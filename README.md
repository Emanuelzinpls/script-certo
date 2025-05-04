-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local rayParams = RaycastParams.new()  -- Parâmetros do Raycast
local fovRadius = 190  -- Raio do FOV
local aimbotActive = false -- Controle de ativação do Aimbot

-- Função para verificar se o alvo está dentro do FOV
local function isInFOV(targetPosition)
    local cameraPosition = camera.CFrame.Position
    local direction = (targetPosition - cameraPosition).unit
    local dotProduct = camera.CFrame.LookVector:Dot(direction)
    
    return dotProduct > math.cos(math.rad(fovRadius)) -- Se estiver dentro do FOV
end

-- Função do Aimbot que mira apenas nos NPCs (monstros) e não em jogadores
local function aimbotNPC()
    local closestNPC = nil
    local closestDistance = math.huge
    local targetHeadPosition = nil

    -- Verificar todos os NPCs no jogo
    for _, obj in pairs(workspace:GetDescendants()) do
        -- Verificar se o objeto é um NPC (monstro) e se ele tem uma cabeça (ou parte de corpo relevante)
        if obj:IsA("Model") and obj:FindFirstChild("HumanoidRootPart") and obj:FindFirstChild("Head") then
            local npc = obj
            local npcHead = npc.Head
            local distance = (camera.CFrame.Position - npcHead.Position).Magnitude

            -- Verifica a distância entre o jogador e o NPC, e escolhe o mais próximo
            if distance < closestDistance and isInFOV(npcHead.Position) then
                closestNPC = npc
                closestDistance = distance
                targetHeadPosition = npcHead.Position
            end
        end
    end

    -- Se um NPC foi encontrado, mira na cabeça dele
    if closestNPC then
        local character = player.Character
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")

        if humanoidRootPart and targetHeadPosition then
            -- Puxando a mira para o NPC
            local direction = (targetHeadPosition - humanoidRootPart.Position).unit
            camera.CFrame = CFrame.new(camera.CFrame.Position, targetHeadPosition)
            print("Mirando no NPC: " .. closestNPC.Name)
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

-- Função para ativar e desativar o Aimbot NPC
aimbotButton.MouseButton1Click:Connect(function()
    aimbotActive = not aimbotActive  -- Alterna o estado do Aimbot NPC
    if aimbotActive then
        aimbotButton.Text = "Desativar Aimbot NPC"
        print("Aimbot NPC Ativado")
    else
        aimbotButton.Text = "Ativar Aimbot NPC"
        print("Aimbot NPC Desativado")
    end
end)

-- Função para rodar o Aimbot enquanto ele estiver ativado
game:GetService("RunService").RenderStepped:Connect(function()
    if aimbotActive then
        aimbotNPC()  -- Chama a função do Aimbot para mirar nos NPCs (monstros)
    end
end)

-- Função para deletar todos os códigos, desativar funções e remover o painel
local function deleteScript()
    aimbotActive = false  -- Desativa o Aimbot NPC
    aimbotButton.Text = "Ativar Aimbot NPC"  -- Atualiza o texto do botão
    print("Funções desativadas e códigos removidos.")
    
    -- Remover o painel do jogo
    gui:Destroy()  -- Remove o painel e todos os seus elementos
    
    -- Se houver mais coisas a remover ou resetar, coloque aqui
end

-- Botão para deletar as funções e códigos
local deleteButton = Instance.new("TextButton")
deleteButton.Size = UDim2.new(0, 150, 0, 40)
deleteButton.Position = UDim2.new(0.5, -75, 0, 180)
deleteButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
deleteButton.Text = "Deletar Funções"
deleteButton.Font = Enum.Font.GothamBold
deleteButton.TextSize = 18
deleteButton.TextColor3 = Color3.new(1, 1, 1)
deleteButton.Parent = frame

-- Função para deletar quando o botão for pressionado
deleteButton.MouseButton1Click:Connect(function()
    deleteScript()  -- Chama a função para deletar os scripts, desativar as funções e remover o painel
end)
