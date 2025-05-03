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

-- Controle de ativação do Wallhack
local wallhackActive = false  -- Controle de ativação do Wallhack
local healthBars = {}  -- Armazena as barras de vida para cada jogador

-- Função para ativar/desativar o Wallhack
wallhackButton.MouseButton1Click:Connect(function()
    wallhackActive = not wallhackActive
    if wallhackActive then
        wallhackButton.Text = "Desativar Wallhack"
        print("Wallhack Ativado")
    else
        wallhackButton.Text = "Ativar Wallhack"
        print("Wallhack Desativado")
        
        -- Restaurar todos os jogadores para o estado original (desativa o wallhack)
        for _, otherPlayer in pairs(game.Players:GetPlayers()) do
            if otherPlayer.Character and otherPlayer ~= player then
                local character = otherPlayer.Character
                -- Restaurando a aparência do jogador
                for _, part in pairs(character:GetChildren()) do
                    if part:IsA("MeshPart") or part:IsA("Part") then
                        part.BrickColor = BrickColor.new("Bright blue")  -- Cor original ou qualquer outra
                        part.Transparency = 0  -- Restaurando a opacidade
                    end
                end
                -- Remover a barra de vida, se existir
                if healthBars[otherPlayer] then
                    healthBars[otherPlayer]:Destroy()
                    healthBars[otherPlayer] = nil
                end
            end
        end
    end
end)

-- Função para ativar o Wallhack
local function activateWallhack()
    wallhackActive = true
    wallhackButton.Text = "Desativar Wallhack"
    print("Wallhack Ativado Programaticamente")
end

-- Função para desativar o Wallhack
local function deactivateWallhack()
    wallhackActive = false
    wallhackButton.Text = "Ativar Wallhack"
    print("Wallhack Desativado Programaticamente")
    
    -- Desfazendo o Wallhack em todos os jogadores
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer.Character and otherPlayer ~= player then
            local character = otherPlayer.Character
            -- Restaurando a aparência do jogador
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("MeshPart") or part:IsA("Part") then
                    part.BrickColor = BrickColor.new("Bright blue")  -- Cor original ou qualquer outra
                    part.Transparency = 0  -- Restaurando a opacidade
                end
            end
            -- Remover a barra de vida, se existir
            if healthBars[otherPlayer] then
                healthBars[otherPlayer]:Destroy()
                healthBars[otherPlayer] = nil
            end
        end
    end
end

-- Função para tornar os jogadores visíveis atrás das paredes e deixá-los de cor preta forte
local function wallhack(character)
    if character then
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        local humanoid = character:FindFirstChild("Humanoid")
        
        if humanoidRootPart and humanoid then
            -- Tornar o personagem semi-transparente para "wallhack"
            for _, part in pairs(character:GetChildren()) do
                if part:IsA("MeshPart") or part:IsA("Part") then
                    -- Define a cor preta bem forte e destacada
                    part.BrickColor = BrickColor.new("Really black")  -- Usando a cor "Really black" que é o preto mais forte
                    part.Transparency = 0.5  -- Aplica transparência para ver através das paredes
                end
            end
        end
    end
end

-- Função para criar a barra de vida dos jogadores
local function createHealthBar(character)
    if character and character:FindFirstChild("Humanoid") then
        local humanoid = character:FindFirstChild("Humanoid")
        
        -- Criar a barra de vida
        local healthBar = Instance.new("BillboardGui")
        healthBar.Adornee = character.HumanoidRootPart
        healthBar.Size = UDim2.new(0, 100, 0, 10)
        healthBar.StudsOffset = Vector3.new(0, 2, 0)
        healthBar.Parent = character.HumanoidRootPart
        
        local bar = Instance.new("Frame")
        bar.Size = UDim2.new(1, 0, 1, 0)
        bar.BackgroundColor3 = Color3.fromRGB(0, 255, 0)  -- Cor inicial da barra de vida
        bar.Parent = healthBar

        -- Atualizar a barra de vida com o valor atual de saúde
        humanoid.HealthChanged:Connect(function()
            local healthPercentage = humanoid.Health / humanoid.MaxHealth
            bar.Size = UDim2.new(healthPercentage, 0, 1, 0)

            -- Se a saúde for baixa, a barra ficará vermelha
            if humanoid.Health / humanoid.MaxHealth < 0.3 then
                bar.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
            else
                bar.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
            end
        end)

        -- Armazenar a barra de vida para remover depois
        healthBars[game.Players:GetPlayerFromCharacter(character)] = healthBar
    end
end

-- Função para aplicar Wallhack e barra de vida
local function applyWallhackAndHealthBar()
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer.Character and otherPlayer ~= player then
            -- Aplica o Wallhack
            wallhack(otherPlayer.Character)
            
            -- Cria a barra de vida
            createHealthBar(otherPlayer.Character)
        end
    end
end

-- Função para ativar o Wallhack enquanto o jogador estiver na partida
game:GetService("RunService").RenderStepped:Connect(function()
    if wallhackActive then
        applyWallhackAndHealthBar()  -- Aplica o wallhack e a barra de vida a cada frame
    end
end)

-- Função para desativar todas as funcionalidades ativadas
local function deactivateAllFunctions()
    -- Desativar Wallhack
    deactivateWallhack()
    
    -- Remover as barras de vida
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer.Character and healthBars[otherPlayer] then
            healthBars[otherPlayer]:Destroy()  -- Remove as barras de vida
            healthBars[otherPlayer] = nil
        end
    end

    -- Outras desativações podem ser colocadas aqui (caso você tenha mais funcionalidades ativadas)
    print("Todas as funções foram desativadas.")
end

-- Exemplo de uso:
-- activateWallhack()  -- Ativa o Wallhack programaticamente
-- deactivateWallhack()  -- Desativa o Wallhack programaticamente
-- deactivateAllFunctions()  -- Desativa todas as funções programaticamente
