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
    title.TextXAlignment = Enum.TextXAlignment.Center

    -- Caixa para digitar a chave
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

    -- Função de validação da chave e transição para o painel
    loginButton.MouseButton1Click:Connect(function()
        local keyInput = inputBox.Text
        if keyInput == VALID_KEY then
            statusLabel.Text = "Chave correta! Carregando painel..."
            wait(1)
            gui:Destroy()  -- Remove a tela de login
            showPanel()  -- Exibe o painel com as funcionalidades
        else
            statusLabel.Text = "Chave incorreta! Tente novamente."
        end
    end)
end

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

-- Função para criar o painel com ESP e Aimbot
local function showPanel()
    -- Garantindo que o painel só será criado uma vez
    if panelVisible then return end
    panelVisible = true

    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "XurrascoPanelUI"

    local panel = Instance.new("Frame", gui)
    panel.Size = UDim2.new(0, 400, 0, 300)
    panel.Position = UDim2.new(0.5, -200, 0.5, -150)
    panel.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    panel.BorderSizePixel = 2
    panel.Draggable = true  -- Torna o painel arrastável

    -- Fundo do painel com imagem "brr brr patapim"
    local imageBackground = Instance.new("ImageLabel", panel)
    imageBackground.Size = UDim2.new(1, 0, 1, 0)
    imageBackground.Image = "rbxassetid://86404027639991"  -- ID da sua imagem "brr brr patapim"
    imageBackground.BackgroundTransparency = 1
    imageBackground.ScaleType = Enum.ScaleType.Crop

    local title = Instance.new("TextLabel", panel)
    title.Size = UDim2.new(1, 0, 0, 40)
    title.Text = "Xurrasco"
    title.TextColor3 = Color3.new(1, 1, 1)
    title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    title.Font = Enum.Font.SourceSansBold
    title.TextSize = 24
    title.TextXAlignment = Enum.TextXAlignment.Center

    -- Botões de Ativar/Desativar ESP e Aimbot
    local espButton = Instance.new("TextButton", panel)
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

    local aimbotButton = Instance.new("TextButton", panel)
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
