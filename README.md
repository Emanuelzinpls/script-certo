-- Criando o painel flutuante "Xurrasco"
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Criar uma interface de usuário flutuante
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
frame.Draggable = true
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

-- Função para criar botões
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

-- Função para minimizar o painel e esconder o ícone
minimizeIcon.MouseButton1Click:Connect(function()
    frame.Visible = false  -- Oculta o painel
    minimizeIcon.Visible = true  -- Exibe o ícone para restaurar
end)

-- Função para restaurar o painel ao clicar no ícone
local function restorePanel()
    frame.Visible = true  -- Exibe o painel novamente
    minimizeIcon.Visible = false  -- Oculta o ícone após restaurar o painel
end

minimizeIcon.MouseButton1Click:Connect(restorePanel)

-- Função para permitir arrastar o ícone
local isDragging = false
local dragStart = nil
local startPos = nil

minimizeIcon.MouseButton1Down:Connect(function(input)
    isDragging = true
    dragStart = input.Position
    startPos = minimizeIcon.Position
end)

minimizeIcon.MouseButton1Up:Connect(function()
    isDragging = false
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
    if isDragging then
        local delta = input.Position - dragStart
        minimizeIcon.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

-- Função para exibir o painel de funções
createButton("Função ESP", 50, function()
    print("Função ESP ativada")
    -- Insira seu código de ESP aqui
end)

createButton("Função Aimbot", 100, function()
    print("Função Aimbot ativada")
    -- Insira seu código de Aimbot aqui
end)

-- Exibir painel novamente ao pressionar F6
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.F6 then
        if frame.Visible then
            frame.Visible = false
            minimizeIcon.Visible = true
        else
            restorePanel()
        end
    end
end)
