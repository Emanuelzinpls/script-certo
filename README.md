-- Key system + Painel Xurrasco ESP/Aimbot
-- Feito para uso educacional e jogos próprios

local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- CONFIGURAÇÃO DO WEBHOOK
local webhookUrl = "https://discord.com/api/webhooks/1368048798382686288/9wIxXJC4d-RgLMbDlysH8IN7CReTjl24ys4s1cNcsK_oyAewscssKxh-DPo_s-Ts24Ay"

-- UI para a key
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "XurrascoKeyUI"

local box = Instance.new("TextBox", gui)
box.Size = UDim2.new(0, 250, 0, 40)
box.Position = UDim2.new(0.5, -125, 0.5, -20)
box.PlaceholderText = "Digite sua key"
box.Text = ""
box.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
box.TextColor3 = Color3.new(1, 1, 1)
box.Font = Enum.Font.SourceSansBold
box.TextSize = 20

local status = Instance.new("TextLabel", gui)
status.Size = UDim2.new(0, 250, 0, 20)
status.Position = UDim2.new(0.5, -125, 0.5, 30)
status.Text = "Aguardando..."
status.TextColor3 = Color3.new(1, 1, 1)
status.BackgroundTransparency = 1
status.Font = Enum.Font.SourceSans
status.TextSize = 16

-- função para pegar key do webhook
function getKeyFromWebhook()
    local response = syn and syn.request({
        Url = webhookUrl,
        Method = "GET"
    }) or http_request({
        Url = webhookUrl,
        Method = "GET"
    })

    if response.StatusCode == 200 then
        local data = HttpService:JSONDecode(response.Body)
        if data and data[1] and data[1].content then
            return data[1].content:gsub("%s+", "")
        end
    end
    return nil
end

-- ESP/AIMBOT vars
local espEnabled = true
local aimbotEnabled = false
local fov = 200
local boxes = {}

function createESP(player)
    if player == LocalPlayer then return end
    local box = Drawing.new("Square")
    box.Color = Color3.new(1, 0, 0)
    box.Thickness = 1
    box.Transparency = 1
    box.Visible = true
    boxes[player] = box
end

RunService.RenderStepped:Connect(function()
    for player, box in pairs(boxes) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Team ~= LocalPlayer.Team then
            local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
            if onScreen then
                local size = Vector2.new(50, 100)
                box.Size = size
                box.Position = Vector2.new(pos.X - size.X / 2, pos.Y - size.Y / 2)
                box.Visible = espEnabled
            else
                box.Visible = false
            end
        else
            box.Visible = false
        end
    end
end)

function getClosestTarget()
    local closest, shortest = nil, fov
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Team ~= LocalPlayer.Team and player.Character and player.Character:FindFirstChild("Head") then
            local pos, visible = workspace.CurrentCamera:WorldToViewportPoint(player.Character.Head.Position)
            if visible then
                local dist = (UserInputService:GetMouseLocation() - Vector2.new
