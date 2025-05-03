-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera

-- Criar o painel flutuante
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
iconButton.Position = UDim2.new(0, 10, 0, 10)  -- Posição inicial do ícone no canto superior esquerdo
iconButton.BackgroundTransparency = 1
iconButton.Image = "rbxassetid://105182366707019"  -- Coloque o ID do seu asset aqui
iconButton.Parent = gui

-- Permitir que o ícone seja movido pela tela
iconButton.Active = true  -- Habilita a interatividade do ícone
iconButton.Draggable = true  -- Permite mover o ícone

-- Funcionalidade para abrir e minimizar o painel ao clicar no ícone
iconButton.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible  -- Alterna a visibilidade do painel
end)

-- Botão para ativar/desativar o Wallhack
local wallhackButton = Instance.new("TextButton")
wallhackButton.Size = UDim2.new(0, 150, 0, 40)
wallhackButton.Position = UDim2.new(0.5, -75, 0, 60)
wallhackButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
wallhackButton.Text = "Ativar Wallhack"
wallhackButton.Font = Enum.Font.GothamBold
wallhackButton.TextSize = 18
wallhackButton.TextColor3 = Color3.new(1, 1, 1)
wallhackButton.Parent = frame

-- Função para ativar
