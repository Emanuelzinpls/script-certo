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

-- Imagem de fundo do painel
local bg = Instance.new("ImageLabel")
bg.Size = UDim2.new(1, 0, 1, 0)
bg.Position = UDim2.new(0, 0, 0, 0)
bg.BackgroundTransparency = 1
bg.Image = "rbxassetid://86404027639991"  -- Imagem de fundo "brr brr patapim"
bg.ImageTransparency = 0.3
bg.Parent = frame

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

-- Criando o ícone flutuante de minimização com o meme italiano (ID fornecido)
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

-- Função para permitir o movimento do ícone "brr brr patapim"
local isIconDragging = false
local dragStart = Vector2.new()
local iconStartPos = UDim2.new(0.5, -25, 0, 10)

minimizeIcon.MouseButton1Down:Connect(function(input)
    isIconDragging = true
    dragStart = input.Position
    iconStartPos = minimizeIcon.Position
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
    if isIconDragging then
        local delta = input.Position - dragStart
        minimizeIcon.Position = UDim2.new(iconStartPos.X.Scale, iconStartPos.X.Offset + delta.X, iconStartPos.Y.Scale, iconStartPos.Y.Offset + delta.Y)
    end
end)

minimizeIcon.MouseButton1Up:Connect(function()
    isIconDragging = false
end)

-- Função para desenhar ESP (caixas ao redor dos jogadores)
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
        frame.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Cor da caixa
        frame.BackgroundTransparency = 0.5  -- Transparência
    end
end

-- Função para criar Wallhack (tornar o jogador semi-transparente)
local function wallhack(character)
    if character then
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            -- Tornar o personagem semi-transparente para "wallhack"
            humanoidRootPart.LocalTransparencyModifier = 0.5  -- Torna o jogador semi-transparente
            wait(10)  -- Aguarda 10 segundos antes de resetar a transparência
            humanoidRootPart.LocalTransparencyModifier = 0  -- Reset após 10 segundos
        end
    end
end

-- Função para desenhar o esqueleto (esqueleto do personagem)
local function drawSkeleton(character)
    if character and character:FindFirstChild("Humanoid") then
        for _, part in pairs(character:GetChildren()) do
            if part:IsA("MeshPart") then
                -- Desenhar linhas conectando as partes do personagem para formar o esqueleto
                local startPos = part.Position
                for _, nextPart in pairs(character:GetChildren()) do
                    if nextPart:IsA("MeshPart") and nextPart ~= part then
                        local endPos = nextPart.Position
                        
                        -- Criar um "Line" (Linha) entre as partes para representar o esqueleto
                        local line = Instance.new("LineHandleAdornment")
                        line.Parent = workspace
                        line.Adornee = part
                        line.WorldPosition = startPos
                        line.WorldPosition = endPos
                        line.Thickness = 0.2  -- Espessura da linha
                        line.Color3 = Color3.fromRGB(0, 255, 0)  -- Cor verde para o esqueleto
                    end
                end
            end
        end
    end
end

-- Função principal Esp que chama os outros métodos
local function Esp(character)
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Chama as funções
        drawESP(character)    -- Desenha a caixa ESP
        wallhack(character)   -- Ativa o wallhack
        drawSkeleton(character)  -- Desenha o esqueleto do jogador
    end
end

-- Função para o Aimbot
local function Aimbot()
    local target = nil
    local shortestDistance = math.huge

    for _, v in pairs(game.Players:GetPlayers()) do
        if v.Character and v.Character:FindFirstChild("HumanoidRootPart") and v.Team ~= player.Team then
            local dist = (v.Character.HumanoidRootPart.Position - camera.CFrame.p).Magnitude
            if dist < shortestDistance then
                target = v
                shortestDistance = dist
            end
        end
    end

    if target and target.Character then
        local targetPos = target.Character.HumanoidRootPart.Position
        local bodyGyro = Instance.new("BodyGyro")
        bodyGyro.MaxTorque = Vector3.new(4000, 4000, 4000)
        bodyGyro.CFrame = CFrame.new(camera.CFrame.p, targetPos)
        bodyGyro.Parent = camera

        -- Ajuste do tiro (se você tem um mecanismo de disparo, pode ativar isso aqui)
        -- Exemplo de disparo: game.ReplicatedStorage:FireServer()
    end
end

-- Conexão com o evento PlayerAdded
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        Esp(character)  -- Chama a função ESP sempre que o personagem do jogador for adicionado
    end)
end)

-- Executar Aimbot a cada 1 segundo
while wait(1) do
    Aimbot()
end
