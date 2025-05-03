-- Criando a interface flutuante
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Criar a GUI principal
local gui = Instance.new("ScreenGui")
gui.Name = "XurrascoPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Criar o frame principal do painel
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 400, 0, 250)
frame.Position = UDim2.new(0.5, -200, 0.5, -125)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.5
frame.Active = true
frame.Draggable = true  -- Permite mover o painel
frame.Parent = gui
frame.Visible = true  -- Inicialmente, o painel está visível

-- Imagem de fundo do painel
local bg = Instance.new("ImageLabel")
bg.Size = UDim2.new(1, 0, 1, 0)
bg.Position = UDim2.new(0, 0, 0, 0)
bg.BackgroundTransparency = 1
bg.Image = "rbxassetid://86404027639991"  -- Imagem de fundo "brr brr patapim"
bg.ImageTransparency = 0.3
bg.Parent = frame

-- Título do painel
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(255, 85, 0)
title.Text = "🔥 Xurrasco Painel 🔥"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.Parent = frame

-- Função para criar botões dentro do painel
local function createButton(text, yPos, callback)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, -20, 0, 40)
    button.Position = UDim2.new(0, 10, 0, yPos)
    button.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    button.TextColor3 = Color3.new(1, 1, 1)
    button.Text = text
    button.Font = Enum.Font.Gotham
    button.TextSize = 16
    button.Parent = frame

    local uicorner = Instance.new("UICorner")
    uicorner.CornerRadius = UDim.new(0, 8)
    uicorner.Parent = button

    button.MouseButton1Click:Connect(callback)
end

-- Criando o ícone flutuante de minimização com o meme italiano (ID fornecido)
local minimizeIcon = Instance.new("ImageButton")
minimizeIcon.Size = UDim2.new(0, 50, 0, 50)
minimizeIcon.Position = UDim2.new(0.5, -25, 0.5, -100)  -- Posiciona o ícone no meio
minimizeIcon.BackgroundTransparency = 1
minimizeIcon.Image = "rbxassetid://105182366707019"  -- Ícone do meme "brr brr patapim" com o novo ID
minimizeIcon.Visible = true
minimizeIcon.Parent = gui

-- Função para alternar o painel (minimizar/restaurar)
local function togglePanel()
    if frame.Visible then
        frame.Visible = false  -- Minimiza o painel
    else
        frame.Visible = true  -- Restaura o painel
    end
end

-- Conecta o clique no ícone ao evento de alternar o painel
minimizeIcon.MouseButton1Click:Connect(togglePanel)

-- Função para exibir o painel de funções
createButton("Função ESP", 50, function()
    print("Função ESP ativada")
    -- Insira seu código de ESP aqui
end)

createButton("Função Aimbot", 100, function()
    print("Função Aimbot ativada")
    -- Insira seu código de Aimbot aqui
end)

-- Permitir arrastar o painel (apenas o painel, não o ícone)
frame.MouseButton1Down:Connect(function(input)
    local dragStart = input.Position
    local startPos = frame.Position

    local dragging = true
    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)

    frame.MouseButton1Up:Connect(function()
        dragging = false
    end)
end)

-- Permitir arrastar o ícone (agora o ícone pode ser movido também)
local isIconDragging = false
local iconStartPos = UDim2.new(0.5, -25, 0.5, -100) -- Posição inicial do ícone

minimizeIcon.MouseButton1Down:Connect(function(input)
    isIconDragging = true
    local dragStart = input.Position
    iconStartPos = minimizeIcon.Position

    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if isIconDragging then
            local delta = input.Position - dragStart
            minimizeIcon.Position = UDim2.new(iconStartPos.X.Scale, iconStartPos.X.Offset + delta.X, iconStartPos.Y.Scale, iconStartPos.Y.Offset + delta.Y)
        end
    end)
end)

minimizeIcon.MouseButton1Up:Connect(function()
    isIconDragging = false
end)
