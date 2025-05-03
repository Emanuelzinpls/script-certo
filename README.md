-- Painel Flutuante Xurrasco
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Só mostrar se for o dono do jogo (altere aqui se quiser outro UserId)
if LocalPlayer.UserId ~= game.CreatorId then return end

-- GUI Setup
local gui = Instance.new("ScreenGui")
gui.Name = "XurrascoDevPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

-- Painel Principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 450, 0, 300)
frame.Position = UDim2.new(0.5, -225, 0.5, -150)
frame.BackgroundTransparency = 0
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.Active = true
frame.Draggable = true
frame.Parent = gui

-- Fundo do painel com imagem "brr brr patapim"
local bg = Instance.new("ImageLabel")
bg.Size = UDim2.new(1, 0, 1, 0)
bg.Position = UDim2.new(0, 0, 0, 0)
bg.BackgroundTransparency = 1
bg.Image = "rbxassetid://86404027639991"
bg.ImageTransparency = 0.2
bg.Parent = frame

-- Canto arredondado
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = frame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(255, 85, 0)
title.Text = "🔥 Xurrasco Painel 🔥"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.Parent = frame

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

-- Função de "ESP" de debug: Mostra nomes de jogadores próximos (sem wallhack)
createButton("👀 Ver Jogadores Próximos", 50, function()
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
			local billboard = Instance.new("BillboardGui")
			billboard.Name = "DebugNameTag"
			billboard.Adornee = player.Character.Head
			billboard.Size = UDim2.new(0, 200,
