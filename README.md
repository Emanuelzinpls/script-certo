local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local VALID_KEY = "xurras"  -- Chave válida
local espEnabled = false  -- ESP inicialmente desativado
local aimbotEnabled = false  -- Aimbot inicialmente desativado

-- Função para criar a tela de login
local function showLoginScreen()
    -- Cria a GUI da tela de login
    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "LoginScreen"

    -- Tela de fundo da tela de login
    local background = Instance.new("Frame", gui)
    background.Size = UDim2.new(1, 0, 1, 0)
    background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    background.BackgroundTransparency = 0.5

    -- Caixa de título
    local title = Instance.new("TextLabel", background)
    title.Size = UDim2.new(1, 0, 0, 50)
    title.Text = "Tela de Login"
    title.TextColor3 = Color3.new(1, 1, 1)
    title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 24
    title.TextXAlignment = Enum.TextXAlignment.Center

    -- Caixa de input para chave
    local inputBox = Instance.new("TextBox", background)
    inputBox.Size = UDim2.new(0, 250, 0, 30)
    inputBox.Position = UDim2.new(0.5, -125, 0.4, 0)
    inputBox.PlaceholderText = "Digite a chave"
    inputBox.TextColor3 = Color3.new(1, 1, 1)
    inputBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    inputBox.Font = Enum.Font.SourceSans
    inputBox.TextSize = 18

    -- Botão de login
    local loginButton = Instance.new("TextButton", background)
    loginButton.Size = UDim2.new(0, 250, 0, 40)
    loginButton.Position = UDim2.new(0.5, -125, 0.6, 0)
    loginButton.Text = "Entrar"
    loginButton.TextColor3 = Color3.new(1, 1, 1)
    loginButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    loginButton.Font = Enum.Font.SourceSans
    loginButton.TextSize = 20

    -- Mensagem de status
    local statusLabel = Instance.new("TextLabel", background)
    statusLabel.Size = UDim2.new(1, 0, 0, 40)
    statusLabel.Position = UDim2.new(0, 0, 0.7, 0)
    statusLabel.Text = "Digite a chave para continuar..."
    statusLabel.TextColor3 = Color3.new(1, 1, 1)
    statusLabel.BackgroundTransparency = 1
    statusLabel.Font = Enum.Font.SourceSans
    statusLabel.TextSize = 16
    statusLabel.TextXAlignment = Enum.TextXAlignment.Center

    -- Função de login
    loginButton.MouseButton1Click:Connect(function()
        local keyInput = inputBox.Text
        if keyInput == VALID_KEY then
            statusLabel.Text = "Chave correta! Carregando tela flutuante..."
            wait(1)
            gui:Destroy()  -- Remove a tela de login
            showFloatingWindow()  -- Exibe a tela flutuante
        else
            statusLabel.Text = "Chave incorreta! Tente novamente."
        end
    end)
end

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

-- Exibe a tela de login assim que o script iniciar
showLoginScreen()
