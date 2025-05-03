local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local VALID_KEY = "xurras"  -- A chave de validação

local espEnabled = false  -- Inicialmente, o ESP está desativado
local aimbotEnabled = false  -- Inicialmente, o Aimbot está desativado
local panelVisible = true  -- Controla a visibilidade do painel

-- Função de ESP (Visualizar jogadores)
function createESP(player)
    if player == LocalPlayer then return end

    -- Criando uma caixa de ESP simples
    local box = Drawing.new("Square")
    box.Color = Color3.new(1, 0, 0)  -- Cor da caixa (vermelho)
    box.Thickness = 1
    box.Transparency = 1
    box.Visible = espEnabled

    -- Atualizando a posição da caixa com base no jogador
    RunService.RenderStepped:Connect(function()
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
            if onScreen then
                box.Size = Vector2.new(50, 100)  -- Tamanho da caixa
                box.Position = Vector2.new(pos.X - 25, pos.Y - 50)  -- Centralizando a caixa em torno do jogador
                box.Visible = espEnabled
            else
