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

-- Função para mover o ícone flutuante
local dragging = false
local dragInput, dragStart, startPos

minimizeIcon.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = minimizeIcon.Position
    end
end)

minimizeIcon.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        minimizeIcon.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

minimizeIcon.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

-- Função para minimizar o painel
minimizeIcon.MouseButton1Click:Connect(function()
    frame.Visible = false  -- Oculta o painel
    minimizeIcon.Visible = true  -- Exibe o ícone para restaurar
end)

-- Função de ESP para mostrar jogadores próximos
createButton("👀 Ver Jogadores Próximos", 50, function()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
            local billboard = Instance.new("BillboardGui")
            billboard.Name = "DebugNameTag"
            billboard.Adornee = player.Character.Head
            billboard.Size = UDim2.new(0, 200, 0, 50)
            billboard.StudsOffset = Vector3.new(0, 2, 0)
            billboard.AlwaysOnTop = true
            billboard.Parent = player.Character

            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, 0, 1, 0)
            label.BackgroundTransparency = 1
            label.Text = "👤 " .. player.Name
            label.TextColor3 = Color3.new(1, 1, 1)
            label.Font = Enum.Font.Gotham
            label.TextScaled = true
            label.Parent = billboard
        end
    end
end)

-- Análise de movimento
createButton("📈 Análise de Movimento", 100, function()
    local char = LocalPlayer.Character
    if not char then return end
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return end

    -- Exibir velocidade e movimento do jogador
    game.StarterGui:SetCore("ChatMakeSystemMessage", {
        Text = "[DEBUG] Velocidade atual: " .. tostring(root.Velocity.Magnitude),
        Color = Color3.fromRGB(0, 200, 255)
    })
end)

-- Simular aimbot (só para visualização)
createButton("🎯 Simular Aimbot", 150, function()
    local target = workspace:FindFirstChild("AimbotTarget")
    if target and target:IsA("Model") then
        local camera = workspace.CurrentCamera
        camera.CameraSubject = target
        camera.CFrame = target.PrimaryPart.CFrame
    end
end)

-- Função para restaurar o painel ao clicar no ícone
minimizeIcon.MouseButton1Click:Connect(function()
    frame.Visible = true  -- Exibe o painel novamente
    minimizeIcon.Visible = false  -- Oculta o ícone após restaurar o painel
end)

-- Exibir painel novamente ao pressionar F6
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.F6 then
        frame.Visible = not frame.Visible
        minimizeIcon.Visible = not minimizeIcon.Visible
    end
end)
