-- Criar a interface do painel flutuante
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

-- Ícone do Brr Brr Patapim (Centralizado no topo da tela)
local iconButton = Instance.new("ImageButton")
iconButton.Size = UDim2.new(0, 50, 0, 50)  -- Tamanho do ícone
iconButton.Position = UDim2.new(0.5, -25, 0, 10)  -- Posição no topo da tela, centralizado
iconButton.BackgroundTransparency = 1
iconButton.Image = "rbxassetid://105182366707019"  -- Coloque o ID do seu asset aqui
iconButton.Parent = gui

-- Tornar o ícone arrastável
iconButton.Active = true
iconButton.Draggable = true  -- Permite arrastar o ícone
iconButton.TextButton1Click:Connect(function()
    frame.Visible = not frame.Visible  -- Alterna a visibilidade do painel
end)

-- Botão para ativar/desativar o Aimbot NPC
local aimbotButton = Instance.new("TextButton")
aimbotButton.Size = UDim2.new(0, 150, 0, 40)
aimbotButton.Position = UDim2.new(0.5, -75, 0, 120)
aimbotButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Cor do botão
aimbotButton.Text = "Ativar Aimbot NPC"
aimbotButton.Font = Enum.Font.GothamBold
aimbotButton.TextSize = 18
aimbotButton.TextColor3 = Color3.new(1, 1, 1)
aimbotButton.Parent = frame

-- Função para ativar/desativar o Aimbot NPC
aimbotButton.MouseButton1Click:Connect(function()
    aimbotActive = not aimbotActive  -- Alterna o estado do Aimbot
    if aimbotActive then
        aimbotButton.Text = "Desativar Aimbot NPC"
        print("Aimbot NPC Ativado")
    else
        aimbotButton.Text = "Ativar Aimbot NPC"
        print("Aimbot NPC Desativado")
    end
end)

-- Rodando o Aimbot enquanto ele estiver ativado
game:GetService("RunService").RenderStepped:Connect(function()
    if aimbotActive then
        aimbotNPC()  -- Chama a função do Aimbot para mirar nos NPCs
        drawFOV()  -- Desenha o FOV enquanto o aimbot estiver ativo
    end
end)
