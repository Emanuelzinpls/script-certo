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

-- Função para aplicar o wallhack (tornar semi-transparente)
local function wallhack(character)
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Tornar o personagem semi-transparente para ser visto através de paredes
        for _, part in pairs(character:GetChildren()) do
            if part:IsA("BasePart") then
                part.LocalTransparencyModifier = 0.5  -- Torna as partes do personagem semi-transparentes
            end
        end
    end
end

-- Função principal ESP para ativar o Wallhack e mostrar os players
local function Esp(character)
    if character and character:FindFirstChild("HumanoidRootPart") then
        -- Aplicar wallhack para o personagem ser visto através das paredes
        wallhack(character)
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
