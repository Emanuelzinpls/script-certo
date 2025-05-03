-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera

-- Criar o painel flutuante
local gui = Instance.new("ScreenGui")
gui.Name = "XurrascoPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- Criar a interface do painel
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 400, 0, 250)
frame.Position = UDim2.new(0.5, -200, 0.5, -125)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.5
frame.Active = true
frame.Draggable = true
frame.Parent = gui
frame.Visible = false  -- Inicialmente invisível

-- Título do painel
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(255, 85, 0)
title.Text = "🔥 Xurrasco Panel 🔥"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.Parent = frame

-- Ícone do Brr Brr Patapim (Posicionado no topo e no meio da tela)
local iconButton = Instance.new("ImageButton")
iconButton.Size = UDim2.new(0, 50, 0, 50)
iconButton.Position = UDim2.new(0.5, -25, 0, 10)  -- Posição no topo, no meio da tela
iconButton.BackgroundTransparency = 1
iconButton.Image = "rbxassetid://105182366707019"  -- Coloque o ID do seu asset aqui
iconButton.Parent = gui

-- Permitir que o ícone seja movido
iconButton.Active = true  -- Permite interação
iconButton.Draggable = true  -- Permite o movimento do ícone

-- Funcionalidade para abrir e minimizar o painel ao clicar no ícone
iconButton.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible  -- Alterna a visibilidade do painel
end)

-- Controle de ativação do Aimbot
local aimbotActive = false  -- Controle de ativação do Aimbot

-- Função do Aimbot
local function aimbot()
    local mouse = player:GetMouse()
    local target = nil
    local closestDistance = math.huge

    -- Encontra o alvo mais próximo (outro jogador da equipe adversária)
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer ~= player and otherPlayer.Team.Name ~= player.Team.Name then
            local targetCharacter = otherPlayer.Character
            if targetCharacter and targetCharacter:FindFirstChild("HumanoidRootPart") then
                local distance = (mouse.Hit.p - targetCharacter.HumanoidRootPart.Position).Magnitude
                if distance < closestDistance then
                    target = targetCharacter
                    closestDistance = distance
                end
            end
        end
    end

    -- Mira para o alvo mais próximo
    if target then
        local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            local direction = (target.HumanoidRootPart.Position - humanoidRootPart.Position).unit
            local ray = Ray.new(humanoidRootPart.Position, direction * 1000)  -- Define o alcance da mira
            local hitPart, hitPosition = workspace:FindPartOnRay(ray, player.Character)

            -- Aqui você pode simular o disparo ou a ação de atirar
            -- Exemplo:
            print(player.Name .. " está mirando em " .. target.Name)
        end
    end
end

-- Botão para ativar/desativar o Aimbot
local aimbotButton = Instance.new("TextButton")
aimbotButton.Size = UDim2.new(0, 150, 0, 40)
aimbotButton.Position = UDim2.new(0.5, -75, 0, 110)
aimbotButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)
aimbotButton.Text = "Ativar Aimbot"
aimbotButton.Font = Enum.Font.GothamBold
aimbotButton.TextSize = 18
aimbotButton.TextColor3 = Color3.new(1, 1, 1)
aimbotButton.Parent = frame

-- Função para ativar/desativar o Aimbot
aimbotButton.MouseButton1Click:Connect(function()
    aimbotActive = not aimbotActive
    if aimbotActive then
        aimbotButton.Text = "Desativar Aimbot"
        print("Aimbot Ativado")
    else
        aimbotButton.Text = "Ativar Aimbot"
        print("Aimbot Desativado")
    end
end)

-- Função para executar o Aimbot enquanto o jogador estiver na partida
game:GetService("RunService").RenderStepped:Connect(function()
    if aimbotActive then
        aimbot()  -- Executa o aimbot a cada frame
    end
end)
