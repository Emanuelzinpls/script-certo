-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local gui = Instance.new("ScreenGui")
gui.Name = "XurrascoPanel"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = player:WaitForChild("PlayerGui")

-- Criar o frame do painel
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 400, 0, 250)
frame.Position = UDim2.new(0.5, -200, 0.5, -125)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.5
frame.Active = true
frame.Draggable = true
frame.Parent = gui
frame.Visible = true

-- Título do painel
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(255, 85, 0)
title.Text = "🔥 Xurrasco Painel 🔥"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.Parent = frame

-- Botão de ESP
local espButton = Instance.new("TextButton")
espButton.Size = UDim2.new(0, 150, 0, 40)
espButton.Position = UDim2.new(0.5, -75, 0, 60)
espButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
espButton.Text = "Ativar ESP"
espButton.Font = Enum.Font.GothamBold
espButton.TextSize = 18
espButton.TextColor3 = Color3.new(1, 1, 1)
espButton.Parent = frame

-- Função para ativar ESP (com controle manual)
local espActive = false
espButton.MouseButton1Click:Connect(function()
    espActive = not espActive
    if espActive then
        espButton.Text = "Desativar ESP"
        print("ESP Ativado")
    else
        espButton.Text = "Ativar ESP"
        print("ESP Desativado")
    end
end)

-- Criando o ícone flutuante de minimização
local minimizeIcon = Instance.new("ImageButton")
minimizeIcon.Size = UDim2.new(0, 50, 0, 50)
minimizeIcon.Position = UDim2.new(0.5, -25, 0, 10)  -- Ícone no topo da tela (fixo no topo)
minimizeIcon.BackgroundTransparency = 1
minimizeIcon.Image = "rbxassetid://105182366707019"  -- Ícone do meme "brr brr patapim"
minimizeIcon.Visible = true
minimizeIcon.Parent = gui

-- Função para alternar o painel (minimizar/restaurar)
local function togglePanel()
    if frame.Visible then
        frame.Visible = false  -- Minimiza o painel
    else
        frame.Visible = true  -- Restaura o painel
    end
end

-- Conecta o clique no ícone ao evento de alternar o painel
minimizeIcon.MouseButton1Click:Connect(togglePanel)

-- Função para desenhar a barra de vida do jogador
local function drawHealthBar(character)
    if character and character:FindFirstChild("Humanoid") then
        local humanoid = character.Humanoid
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        
        -- Criar uma barra de vida
        local healthBarBackground = Instance.new("Frame")
        healthBarBackground.Size = UDim2.new(0, 100, 0, 10)
        healthBarBackground.Position = UDim2.new(0, -50, 0, -20)  -- Acima do personagem
        healthBarBackground.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        healthBarBackground.BackgroundTransparency = 0.5
        healthBarBackground.Parent = character
        
        local healthBar = Instance.new("Frame")
        healthBar.Size = UDim2.new(humanoid.Health / humanoid.MaxHealth, 0, 1, 0)  -- Tamanho de acordo com a vida
        healthBar.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Cor vermelha
        healthBar.Parent = healthBarBackground
        
        -- Atualizar a barra de vida com base na saúde
        humanoid.HealthChanged:Connect(function()
            healthBar.Size = UDim2.new(humanoid.Health / humanoid.MaxHealth, 0, 1, 0)  -- Atualiza tamanho conforme a vida
        end)
        
        -- A barra de vida é removida quando o personagem morre
        humanoid.Died:Connect(function()
            healthBarBackground:Destroy()
        end)
    end
end

-- Função para desenhar ESP e Wallhack (visível através das paredes)
local function drawESP(character)
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Criar um BillboardGui para exibir a caixa ESP
        local espBox = Instance.new("BillboardGui")
        espBox.Parent = character.HumanoidRootPart
        espBox.Adornee = character.HumanoidRootPart
        espBox.Size = UDim2.new(0, 100, 0, 100)  -- Tamanho da caixa
        espBox.StudsOffset = Vector3.new(0, 2, 0)
        
        local frame = Instance.new("Frame")
        frame.Parent = espBox
        frame.Size = UDim2.new(1, 0, 1, 0)
        frame.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Cor vermelha
        frame.BackgroundTransparency = 0.5
    end
end

-- Função para verificar se o jogador está atrás de uma parede
local function isPlayerVisible(character)
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if rootPart then
        local ray = Ray.new(camera.CFrame.p, (rootPart.Position - camera.CFrame.p).unit * 100)
        local hitPart, hitPosition = workspace:FindPartOnRay(ray, game.Players.LocalPlayer.Character, false, true)
        return hitPart == nil  -- Retorna true se não houve colisão (significa que o jogador está visível)
    end
    return false
end

-- Função para ativar ESP em todos os jogadores
local function Esp(character)
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Desenhar ESP apenas se o jogador estiver visível
        if isPlayerVisible(character) then
            drawESP(character)  -- Chama a função de ESP
        end
        -- Desenha a barra de vida
        drawHealthBar(character)
    end
end

-- Conexão com o evento PlayerAdded
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        if espActive then
            Esp(character)  -- Chama a função ESP sempre que o personagem do jogador for adicionado
        end
    end)
end)

-- Para exibir o ESP enquanto o jogador estiver na partida
game:GetService("RunService").RenderStepped:Connect(function()
    if espActive then
        for _, otherPlayer in pairs(game.Players:GetPlayers()) do
            if otherPlayer.Character and otherPlayer ~= player then
                Esp(otherPlayer.Character)  -- Atualiza o ESP a cada frame
            end
        end
    end
end)
