local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local VALID_KEY = "xurras"  -- A chave de validação

local espEnabled = false  -- Inicialmente, o ESP está desativado
local aimbotEnabled = false  -- Inicialmente, o Aimbot está desativado
local panelVisible = false  -- Inicialmente, o painel está oculto

-- Função para criar a tela de login
local function showLoginScreen()
    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "LoginScreen"

    -- Tela de fundo da tela de login
    local background = Instance.new("Frame", gui)
    background.Size = UDim2.new(1, 0, 1, 0)
    background.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    background.BackgroundTransparency = 0.5

    -- Caixa de título da tela de login
    local title = Instance.new("TextLabel", background)
    title.Size = UDim2.new(1, 0, 0, 50)
    title.Text = "Tela de Login"
    title.TextColor3 = Color3.new(1, 1, 1)
    title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 24
    title.TextXAlignment = Enum.TextXAlignment
