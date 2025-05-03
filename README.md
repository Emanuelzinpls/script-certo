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

-- Ícone do Brr Brr Patapim
local iconButton = Instance.new("ImageButton")
iconButton.Size = UDim2.new(0, 50, 0, 50)
iconButton.Position = UDim2.new(0.5, -25, 0, 10)  -- Posição do ícone no topo
iconButton.BackgroundTransparency = 1
iconButton.Image = "rbxassetid://105182366707019"  -- Coloque o ID do seu asset aqui
iconButton.Parent = gui

-- Função para abrir e minimizar o painel ao clicar no ícone
iconButton.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible  -- Alterna a visibilidade do painel
end)

-- Botão para ativar/desativar o Wallhack
local wallhackButton = Instance.new("TextButton")
wallhackButton.Size = UDim2.new(0, 150, 0, 40)
wallhackButton.Position = UDim2.new(0.5, -75, 0, 60)
wallhackButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
wallhackButton.Text = "Ativar Wallhack"
wallhackButton.Font = Enum.Font.GothamBold
wallhackButton.TextSize = 18
wallhackButton.TextColor3 = Color3.new(1, 1, 1)
wallhackButton.Parent = frame

-- Variável para o Wallhack
local wallhackActive = false

-- Função para ativar/desativar o Wallhack
wallhackButton.MouseButton1Click:Connect(function()
    wallhackActive = not wallhackActive
    if wallhackActive then
        wallhackButton.Text = "Desativar Wallhack"
        print("Wallhack Ativado")
    else
        wallhackButton.Text = "Ativar Wallhack"
        print("Wallhack Desativado")
    end
end)

-- Função para aplicar o Wallhack e exibir os inimigos
local function wallhack(character)
    if character then
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        local humanoid = character:FindFirstChild("Humanoid")
        
        if humanoidRootPart and humanoid then
            -- Tornar o personagem semi-transparente para "wallhack"
            humanoidRootPart.LocalTransparencyModifier = 0.5  -- Torna o jogador semi-transparente
            -- Mudar a cor do personagem para um preto bem forte
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("MeshPart") or part:IsA("Part") then
                    part.BrickColor = BrickColor.new("Really black")  -- Usando a cor "Really black" que é o preto mais forte
                    part.LocalTransparencyModifier = 0.5  -- Aplica transparência para ver através das paredes
                end
            end
        end
    end
end

-- Função para aplicar o Wallhack
local function applyWallhack()
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer.Character and otherPlayer ~= player then
            -- Chama a função para tornar o jogador visível atrás das paredes
            wallhack(otherPlayer.Character)
        end
    end
end

-- Função para ativar o Wallhack enquanto o jogador estiver na partida
game:GetService("RunService").RenderStepped:Connect(function()
    if wallhackActive then
        applyWallhack()  -- Aplica o Wallhack a cada frame
    end
end)

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

            -- Atualizando a posição do mouse para mirar no alvo
            camera.CFrame = CFrame.new(camera.CFrame.Position, target.HumanoidRootPart.Position)
            
            -- Exemplo de ação de disparo
            print(player.Name .. " está mirando em " .. target.Name)
        end
    end
end

-- Botão para ativar/desativar o Aimbot
local aimbotButton = Instance.new("TextButton")
aimbotButton.Size = UDim2.new(0, 150, 0, 40)
aimbotButton.Position = UDim2.new(0.5, -75, 0, 120)
aimbotButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Cor do botão
aimbotButton.Text = "Ativar Aimbot"
aimbotButton.Font = Enum.Font.GothamBold
aimbotButton.TextSize = 18
aimbotButton.TextColor3 = Color3.new(1, 1, 1)
aimbotButton.Parent = frame

-- Variável para verificar se o Aimbot está ativado
local aimbotActive = false

-- Função para ativar e desativar o Aimbot
aimbotButton.MouseButton1Click:Connect(function()
    aimbotActive = not aimbotActive  -- Alterna o estado do Aimbot
    if aimbotActive then
        aimbotButton.Text = "Desativar Aimbot"
        print("Aimbot Ativado")
    else
        aimbotButton.Text = "Ativar Aimbot"
        print("Aimbot Desativado")
    end
end)

-- Função para rodar o Aimbot enquanto ele estiver ativado
game:GetService("RunService").RenderStepped:Connect(function()
    if aimbotActive then
        aimbot()  -- Chama a função do Aimbot para mirar nos alvos
    end
end)
