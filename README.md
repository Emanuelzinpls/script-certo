-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local gui = Instance.new("ScreenGui")
gui.Name = "XurrascoPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- Criar o painel de controle
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

-- Funcionalidade para abrir e minimizar o painel ao clicar no ícone
iconButton.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible  -- Alterna a visibilidade do painel
end)

-- Botão para ativar/desativar o ESP
local espButton = Instance.new("TextButton")
espButton.Size = UDim2.new(0, 150, 0, 40)
espButton.Position = UDim2.new(0.5, -75, 0, 60)
espButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
espButton.Text = "Ativar ESP"
espButton.Font = Enum.Font.GothamBold
espButton.TextSize = 18
espButton.TextColor3 = Color3.new(1, 1, 1)
espButton.Parent = frame

-- Função para ativar/desativar o ESP
local espActive = false  -- Controle de ativação do ESP
espButton.MouseButton1Click:Connect(function()
    espActive = not espActive
    if espActive then
        espButton.Text = "Desativar ESP"
        print("ESP Ativado")
    else
        espButton.Text = "Ativar ESP"
        print("ESP Desativado")
    end
end)

-- Função para desenhar ESP (caixa ao redor dos jogadores)
local function drawESP(character)
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Criar o BillboardGui para o ESP
        local espBox = Instance.new("BillboardGui")
        espBox.Parent = character.HumanoidRootPart
        espBox.Adornee = character.HumanoidRootPart
        espBox.Size = UDim2.new(0, 50, 0, 50)  -- Diminuir o tamanho da caixa
        espBox.StudsOffset = Vector3.new(0, 2, 0)

        local frame = Instance.new("Frame")
        frame.Parent = espBox
        frame.Size = UDim2.new(1, 0, 1, 0)
        frame.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Cor vermelha
        frame.BackgroundTransparency = 0.5
    end
end

-- Função para tornar os jogadores visíveis através das paredes (Wallhack)
local function wallhack(character)
    if character then
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            -- Tornar o personagem semi-transparente para "wallhack"
            humanoidRootPart.LocalTransparencyModifier = 0.5  -- Torna o jogador semi-transparente
        end
    end
end

-- Função principal para ativar o ESP em jogadores
local function displayESP()
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer.Character and otherPlayer ~= player then
            -- Chama a função para desenhar o ESP e tornar o jogador visível atrás das paredes
            drawESP(otherPlayer.Character)
            wallhack(otherPlayer.Character)
        end
    end
end

-- Função para ativar o ESP enquanto o jogador estiver na partida
game:GetService("RunService").RenderStepped:Connect(function()
    if espActive then
        displayESP()  -- Atualiza o ESP a cada frame
    end
end)
