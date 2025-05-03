local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local VALID_KEY = "xurras"  -- Chave de validação

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
                box.Visible = false
            end
        else
            box.Visible = false
        end
    end)
end

-- Função para ativar/desativar o Aimbot
RunService.RenderStepped:Connect(function()
    if aimbotEnabled then
        local closestPlayer = nil
        local shortestDistance = 200

        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
                local pos, visible = workspace.CurrentCamera:WorldToViewportPoint(player.Character.Head.Position)
                if visible then
                    local distance = (UserInputService:GetMouseLocation() - Vector2.new(pos.X, pos.Y)).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closestPlayer = player
                    end
                end
            end
        end

        if closestPlayer then
            workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, closestPlayer.Character.Head.Position)
        end
    end
end)

-- Função para criar o painel flutuante
local function showPanel()
    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "XurrascoPanelUI"

    local panel = Instance.new("Frame", gui)
    panel.Size = UDim2.new(0, 300, 0, 200)
    panel.Position = UDim2.new(0.5, -150, 0.5, -100)
    panel.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    panel.BorderSizePixel = 2
    panel.Draggable = true  -- Torna o painel arrastável

    local title = Instance.new("TextLabel", panel)
    title.Size = UDim2.new(1, 0, 0, 30)
    title.Text = "Xurrasco"
    title.TextColor3 = Color3.new(1, 1, 1)
    title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 20
    title.TextXAlignment = Enum.TextXAlignment.Center

    local statusLabel = Instance.new("TextLabel", panel)
    statusLabel.Size = UDim2.new(1, 0, 0, 40)
    statusLabel.Position = UDim2.new(0, 0, 0.6, 0)
    statusLabel.Text = "Espere pela validação da chave..."
    statusLabel.TextColor3 = Color3.new(1, 1, 1)
    statusLabel.BackgroundTransparency = 1
    statusLabel.Font = Enum.Font.SourceSans
    statusLabel.TextSize = 16

    -- Caixa para digitar a chave
    local inputBox = Instance.new("TextBox", panel)
    inputBox.Size = UDim2.new(0, 200, 0, 30)
    inputBox.Position = UDim2.new(0.5, -100, 0.8, 0)
    inputBox.PlaceholderText = "Digite a chave"
    inputBox.TextColor3 = Color3.new(1, 1, 1)
    inputBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    inputBox.Font = Enum.Font.SourceSans
    inputBox.TextSize = 18

    -- Botões de Minimizar, Maximizar e Ocultar
    local minimizeButton = Instance.new("TextButton", panel)
    minimizeButton.Size = UDim2.new(0, 30, 0, 30)
    minimizeButton.Position = UDim2.new(1, -40, 0, 5)
    minimizeButton.Text = "_"
    minimizeButton.TextColor3 = Color3.new(1, 1, 1)
    minimizeButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    minimizeButton.Font = Enum.Font.SourceSans
    minimizeButton.TextSize = 20
    minimizeButton.MouseButton1Click:Connect(function()
        panel.Visible = false
        panelVisible = false
    end)

    local maximizeButton = Instance.new("TextButton", panel)
    maximizeButton.Size = UDim2.new(0, 30, 0, 30)
    maximizeButton.Position = UDim2.new(1, -70, 0, 5)
    maximizeButton.Text = "+"
    maximizeButton.TextColor3 = Color3.new(1, 1, 1)
    maximizeButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    maximizeButton.Font = Enum.Font.SourceSans
    maximizeButton.TextSize = 20
    maximizeButton.MouseButton1Click:Connect(function()
        panel.Visible = true
        panelVisible = true
    end)

    local hideButton = Instance.new("TextButton", panel)
    hideButton.Size = UDim2.new(0, 30, 0, 30)
    hideButton.Position = UDim2.new(1, -100, 0, 5)
    hideButton.Text = "X"
    hideButton.TextColor3 = Color3.new(1, 1, 1)
    hideButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    hideButton.Font = Enum.Font.SourceSans
    hideButton.TextSize = 20
    hideButton.MouseButton1Click:Connect(function()
        gui:Destroy()
    end)

    -- Função para validar a chave e ativar o painel
    inputBox.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            local keyInput = inputBox.Text
            if keyInput == VALID_KEY then
                statusLabel.Text = "ESP e Aimbot Ativados!"
                espEnabled = true  -- Ativa o ESP
                aimbotEnabled = true  -- Ativa o Aimbot

                -- Cria o ESP para todos os jogadores já presentes
                for _, p in pairs(Players:GetPlayers()) do
                    createESP(p)
                end

                -- Cria o ESP para novos jogadores que entrarem
                Players.PlayerAdded:Connect(createESP)
            else
                statusLabel.Text = "Chave incorreta. Tente novamente!"
            end
        end
    end)

    -- Exibe o painel para o usuário
    panel.Visible = panelVisible
end

-- Chama a função para exibir o painel
showPanel()
