local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local espEnabled = false  -- ESP inicialmente desativado
local aimbotEnabled = false  -- Aimbot inicialmente desativado

-- Função para criar a tela flutuante com lista de funções
local function showFloatingWindow()
    -- Cria a GUI da tela flutuante
    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "FloatingWindow"

    -- Criando a tela flutuante
    local floatingWindow = Instance.new("Frame", gui)
    floatingWindow.Size = UDim2.new(0, 400, 0, 300)
    floatingWindow.Position = UDim2.new(0.5, -200, 0.5, -150)
    floatingWindow.BackgroundColor3 = Color3.fromRGB(255, 50, 50)  -- Cor vibrante
    floatingWindow.BorderSizePixel = 5
    floatingWindow.Draggable = true  -- Permite arrastar a tela
    floatingWindow.Active = true  -- Permite interação com a tela
    floatingWindow.BackgroundTransparency = 0.2
    floatingWindow.BorderColor3 = Color3.fromRGB(0, 255, 255)  -- Bordas neon

    -- Título da tela flutuante
    local title = Instance.new("TextLabel", floatingWindow)
    title.Size = UDim2.new(1, 0, 0, 40)
    title.Text = "Xurrasco"
    title.TextColor3 = Color3.fromRGB(0, 255, 255)  -- Neon azul
    title.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Fundo vermelho
    title.Font = Enum.Font.GothamBold
    title.TextSize = 28
    title.TextXAlignment = Enum.TextXAlignment.Center
    title.TextYAlignment = Enum.TextYAlignment.Center
    title.TextStrokeTransparency = 0.6  -- Sombra forte
    title.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)

    -- Seção de categorias (Lista de Funções)
    local functionsFrame = Instance.new("Frame", floatingWindow)
    functionsFrame.Size = UDim2.new(1, 0, 1, -40)  -- O restante da tela
    functionsFrame.Position = UDim2.new(0, 0, 0, 40)
    functionsFrame.BackgroundTransparency = 1

    -- Funções: Agrupando os botões em categorias

    -- Funções Principais
    local mainFunctionsLabel = Instance.new("TextLabel", functionsFrame)
    mainFunctionsLabel.Size = UDim2.new(1, 0, 0, 40)
    mainFunctionsLabel.Text = "Funções Principais"
    mainFunctionsLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    mainFunctionsLabel.BackgroundColor3 = Color3.fromRGB(0, 255, 255)  -- Neon Azul
    mainFunctionsLabel.Font = Enum.Font.GothamBold
    mainFunctionsLabel.TextSize = 20
    mainFunctionsLabel.TextXAlignment = Enum.TextXAlignment.Center
    mainFunctionsLabel.TextYAlignment = Enum.TextYAlignment.Center
    mainFunctionsLabel.TextStrokeTransparency = 0.8  -- Sombra para o título
    mainFunctionsLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)

    -- Botão de Ativar/Desativar ESP
    local espButton = Instance.new("TextButton", functionsFrame)
    espButton.Size = UDim2.new(1, 0, 0, 40)
    espButton.Position = UDim2.new(0, 0, 0, 40)
    espButton.Text = "Ativar ESP"
    espButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    espButton.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
    espButton.Font = Enum.Font.SourceSansBold
    espButton.TextSize = 18
    espButton.TextStrokeTransparency = 0.5
    espButton.TextStrokeColor3 = Color3.fromRGB
