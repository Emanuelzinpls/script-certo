local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local espEnabled = false  -- ESP inicialmente desativado
local aimbotEnabled = false  -- Aimbot inicialmente desativado

-- Função para criar a tela flutuante
local function showFloatingWindow()
    -- Cria a GUI da tela flutuante
    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "FloatingWindow"

    -- Criando a tela flutuante
    local floatingWindow = Instance.new("Frame", gui)
    floatingWindow.Size = UDim2.new(0, 400, 0, 300)
    floatingWindow.Position = UDim2.new(0.5, -200, 0.5, -150)
    floatingWindow.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    floatingWindow.BorderSizePixel = 2
    floatingWindow.Draggable = true  -- Permite arrastar a tela

    -- Título da tela flutuante
    local title = Instance.new("TextLabel", floatingWindow)
    title.Size = UDim2.new(1, 0, 0, 40)
    title.Text = "Xurrasco"
    title.TextColor3 = Color3.new(1, 1, 1)
    title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 24
    title.TextXAlignment = Enum.TextXAlignment.Center

    -- Botão de Ativar/Desativar ESP
    local espButton = Instance.new("TextButton", floatingWindow)
    espButton.Size = UDim2.new(0, 120, 0, 40)
    espButton.Position = UDim2.new(0.5, -130, 0.6, 0)
    espButton.Text = "Ativar ESP"
    espButton.TextColor3 = Color3.new(1, 1, 1)
    espButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    espButton.Font = Enum.Font.SourceSans
    espButton.TextSize = 18
    espButton.MouseButton1Click:Connect(function()
        espEnabled = not espEnabled
        espButton.Text = espEnabled and "Desativar ESP" or "Ativar ESP"
    end)

    -- Botão de Ativar/Desativar Aimbot
    local aimbotButton = Instance.new("TextButton", floatingWindow)
    aimbotButton.Size = UDim2.new(0, 120, 0, 40)
    aimbotButton.Position = UDim2.new(0.5, -130, 0.7, 0)
    aimbotButton.Text = "Ativar Aimbot"
    aimbotButton.TextColor3 = Color3.new(1, 1, 1)
    aimbotButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    aimbotButton.Font = Enum.Font.SourceSans
    aimbotButton.TextSize = 18
    aimbotButton.MouseButton1Click:Connect(function()
        aimbotEnabled = not aimbotEnabled
        aimbotButton.Text = aimbotEnabled and "Desativar Aimbot" or "Ativar Aimbot"
    end)
end

-- Exibe a tela flutuante assim que o script iniciar
showFloatingWindow()
