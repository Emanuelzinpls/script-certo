-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local rayParams = RaycastParams.new()  -- Parâmetros do Raycast

-- Configuração de distância máxima que o Aimbot pode alcançar
local maxAimbotDistance = 100 -- 10 metros = 100 studs
local aimbotActive = false -- Controle de ativação do Aimbot

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

-- Botão para ativar/desativar o Aimbot
local aimbotButton = Instance.new("TextButton")
aimbotButton.Size = UDim2.new(0, 150, 0, 40)
aimbotButton.Position = UDim2.new(0.5, -75, 0, 60)
aimbotButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Cor do botão
aimbotButton.Text = "Ativar Aimbot NPC"
aimbotButton.Font = Enum.Font.GothamBold
aimbotButton.TextSize = 18
aimbotButton.TextColor3 = Color3.new(1, 1, 1)
aimbotButton.Parent = frame

-- Função para ativar/desativar o Aimbot para NPCs
aimbotButton.MouseButton1Click:Connect(function()
    aimbotActive = not aimbotActive  -- Alterna o estado do Aimbot
    if aimbotActive then
        aimbotButton.Text = "Desativar Aimbot NPC"
        print("Aimbot Ativado para NPCs")
    else
        aimbotButton.Text = "Ativar Aimbot NPC"
        print("Aimbot Desativado")
    end
end)

-- Função de Aimbot para NPCs
local function aimbotNPC()
    local closestNPC = nil
    local closestDistance = maxAimbotDistance -- Começa com a distância máxima de 10 metros
    local cameraPosition = camera.CFrame.Position
    local closestHead = nil

    -- Percorre todos os NPCs no jogo
    for _, obj in pairs(workspace:GetChildren()) do
        -- Verifica se o objeto é um NPC (um modelo que contém uma cabeça)
        if obj:IsA("Model") and obj:FindFirstChild("Head") then
            local npcHead = obj:FindFirstChild("Head")
            local npcPosition = npcHead.Position
            local distance = (cameraPosition - npcPosition).Magnitude

            -- Verifica se o NPC está dentro do alcance e se é o mais próximo
            if distance < closestDistance then
                closestDistance = distance
                closestNPC = obj
                closestHead = npcHead
            end
        end
    end

    -- Se encontrou um NPC dentro da distância
    if closestNPC then
        -- Ajuste para que a mira fique na cabeça do NPC
        local direction = (closestHead.Position - cameraPosition).unit
        camera.CFrame = CFrame.lookAt(cameraPosition, closestHead.Position)
    end
end

-- Função para rodar o Aimbot enquanto estiver ativado
game:GetService("RunService").RenderStepped:Connect(function()
    if aimbotActive then
        aimbotNPC()  -- Chama a função para mirar nos NPCs
    end
end)

-- Função para limpar o painel e desativar tudo
local function deletar()
    frame.Visible = false  -- Torna o painel invisível
    aimbotActive = false   -- Desativa o aimbot
    aimbotButton.Text = "Ativar Aimbot NPC"  -- Restaura o texto do botão
    print("Todas as funções desativadas e painel removido")
end

-- Botão para deletar o painel e desativar as funções
local deletButton = Instance.new("TextButton")
deletButton.Size = UDim2.new(0, 150, 0, 40)
deletButton.Position = UDim2.new(0.5, -75, 0, 120)
deletButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
deletButton.Text = "Deletar Funções"
deletButton.Font = Enum.Font.GothamBold
deletButton.TextSize = 18
deletButton.TextColor3 = Color3.new(1, 1, 1)
deletButton.Parent = frame

-- Função para deletar tudo
deletButton.MouseButton1Click:Connect(function()
    deletar()
end)
