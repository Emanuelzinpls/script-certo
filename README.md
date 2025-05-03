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

-- Botão de Fechar o Script
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 150, 0, 40)
closeButton.Position = UDim2.new(0.5, -75, 0, 120)
closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
closeButton.Text = "Fechar Script"
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 18
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.Parent = frame

-- Função para fechar o script
closeButton.MouseButton1Click:Connect(function()
    -- Desabilitar o painel e todas as funções
    frame.Visible = false
    espActive = false  -- Desativar o ESP
    espButton.Text = "Ativar ESP"
    print("Script Desativado")
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

-- Função para desenhar linha e mostrar a distância
local function drawLineToPlayer(character)
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Criar uma linha entre o jogador e o outro jogador
        local rootPart = character.HumanoidRootPart
        local distance = (rootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
        
        -- Criando o part que será a linha
        local line = Instance.new("Part")
        line.Size = Vector3.new(0.1, 0.1, distance)
        line.CFrame = CFrame.new(player.Character.HumanoidRootPart.Position, rootPart.Position) * CFrame.new(0, 0, -distance / 2)
        line.Anchored = true
        line.CanCollide = false
        line.Color = Color3.fromRGB(255, 0, 0)
        line.Parent = game.Workspace
        
        -- Exibindo a distância na tela
        local distanceLabel = Instance.new("TextLabel")
        distanceLabel.Text = math.floor(distance) .. " metros"
        distanceLabel.Position = UDim2.new(0.5, -50, 0.5, -100)
        distanceLabel.Size = UDim2.new(0, 100, 0, 40)
        distanceLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        distanceLabel.BackgroundTransparency = 1
        distanceLabel.Font = Enum.Font.GothamBold
        distanceLabel.TextSize = 18
        distanceLabel.Parent = gui
        
        -- Deletar a linha e o label após 1 segundo
        wait(1)
        line:Destroy()
        distanceLabel:Destroy()
    end
end

-- Função principal ESP para ativar o Wallhack e mostrar os players
local function Esp(character)
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Desenhar linha e calcular distância
        drawLineToPlayer(character)
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
