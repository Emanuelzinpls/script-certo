local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local VALID_KEY = "xurras"  -- A chave de validação

local espEnabled = false  -- Inicialmente, o ESP está desativado
local aimbotEnabled = false  -- Inicialmente, o Aimbot está desativado

-- Função de ESP (Visualizar jogadores)
function createESP(player)
    if player == LocalPlayer then return end

    -- Criando um simples caixa de ESP
    local box = Drawing.new("Square")
    box.Color = Color3.new(1, 0, 0)  -- Cor da caixa (vermelho)
    box.Thickness = 1
    box.Transparency = 1
    box.Visible = espEnabled

    -- Atualizando a posição do box com base no jogador
    RunService.RenderStepped:Connect(function()
        if player.Character and
