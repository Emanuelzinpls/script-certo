-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local rayParams = RaycastParams.new()  -- Parâmetros do Raycast
local tool = nil  -- Ferramenta atual equipada pelo jogador

-- Parâmetros do Aimbot
local fovRadius = 50 -- Definindo o raio do FOV (quanto maior, maior a área em que o aimbot procurará alvos)
local npcAimbotActive = false  -- Controle de ativação do Aimbot para NPCs

-- Função para verificar se o alvo está dentro do FOV
local function isInFOV(targetPosition)
    -- Calcula a posição do alvo em relação à câmera
    local cameraPosition = camera.CFrame.Position
    local direction = (targetPosition - cameraPosition).unit
    local dotProduct = camera.CFrame.LookVector:Dot(direction)
    
    -- FOV é baseado no ângulo da câmera
    return dotProduct > math.cos(math.rad(fovRadius)) -- Se estiver dentro do FOV
end

-- Função para disparar a arma equipada
local function fireWeapon(target)
    -- Verificar qual ferramenta (arma) está equipada
    tool = player.Backpack:FindFirstChildOfClass("Tool")  -- Encontra a primeira ferramenta no inventário do jogador
    if tool then
        -- Assumindo que a ferramenta tem um método de disparo (adicione lógica dependendo da sua arma)
        local fireEvent = tool:FindFirstChild("FireEvent")  -- Substitua "FireEvent" pela função específica de disparo
        if fireEvent then
            fireEvent:FireServer(target)  -- Dispara a arma no alvo
            print(player.Name .. " disparou a arma no NPC " .. target.Name)
        end
    end
end

-- Função do Aimbot para NPCs
local function npcAimbot()
    local target = nil
    local closestDistance = math.huge

    -- Encontra o NPC mais próximo
    for _, npc in pairs(game.Workspace:GetChildren()) do
        if npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") then
            local npcPosition = npc.HumanoidRootPart.Position

            -- Verifica se o NPC está dentro do FOV
            if isInFOV(npcPosition) then
                local distance = (camera.CFrame.Position - npcPosition).Magnitude
                if distance < closestDistance then
                    target = npc
                    closestDistance = distance
                end
            end
        end
    end

    -- Mira para o NPC mais próximo dentro do FOV
    if target then
        local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            local direction = (target.HumanoidRootPart.Position - humanoidRootPart.Position).unit
            local ray = Ray.new(humanoidRootPart.Position, direction * 1000)  -- Define o alcance da mira
            rayParams.FilterDescendantsInstances = {player.Character}  -- Ignora o próprio jogador para não detectar ele mesmo

            -- Realiza o Raycast, ignorando as colisões
            local hitPart, hitPosition = workspace:Raycast(humanoidRootPart.Position, direction * 1000, rayParams)

            -- Verifica se o raycast atingiu o NPC
            if hitPart and hitPart.Parent == target then
                -- Aqui você pode disparar a arma no NPC
                fireWeapon(target)  -- Simula o disparo da arma
            end
        end
    end
end

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

-- Ícone do Brr Brr Patapim - Posicionado no topo e centralizado
local iconButton = Instance.new("ImageButton")
iconButton.Size = UDim2.new(0, 50, 0, 50)
iconButton.Position = UDim2.new(0.5, -25, 0, 0)  -- Coloca o ícone no topo da tela e no meio
iconButton.BackgroundTransparency = 1
iconButton.Image = "rbxassetid://105182366707019"  -- Coloque o ID do seu asset aqui
iconButton.Parent = gui

-- Função para abrir e minimizar o painel ao clicar no ícone
iconButton.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible  -- Alterna a visibilidade do painel
end)

-- Botão para ativar/desativar o Aimbot para NPCs
local npcAimbotButton = Instance.new("TextButton")
npcAimbotButton.Size = UDim2.new(0, 150, 0, 40)
npcAimbotButton.Position = UDim2.new(0.5, -75, 0, 180)
npcAimbotButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Cor do botão
npcAimbotButton.Text = "Ativar Aimbot NPC"
npcAimbotButton.Font = Enum.Font.GothamBold
npcAimbotButton.TextSize = 18
npcAimbotButton.TextColor3 = Color3.new(1, 1, 1)
npcAimbotButton.Parent = frame

-- Função para ativar/desativar o Aimbot para NPCs
npcAimbotButton.MouseButton1Click:Connect(function()
    npcAimbotActive = not npcAimbotActive  -- Alterna o estado do Aimbot
    if npcAimbotActive then
        npcAimbotButton.Text = "Desativar Aimbot NPC"
        print("Aimbot NPC Ativado")
    else
        npcAimbotButton.Text = "Ativar Aimbot NPC"
        print("Aimbot NPC Desativado")
    end
end)

-- Criação do FOV visível (círculo na tela)
local fovCircle = Instance.new("Frame")
fovCircle.Size = UDim2.new(0, fovRadius * 2, 0, fovRadius * 2)  -- Define o tamanho do círculo baseado no raio do FOV
fovCircle.Position = UDim2.new(0.5, -fovRadius, 0.5, -fovRadius)  -- Centraliza o círculo na tela
fovCircle.AnchorPoint = Vector2.new(0.5, 0.5)  -- Centraliza o círculo
fovCircle.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Cor do círculo (vermelho)
fovCircle.BackgroundTransparency = 0.5  -- Tornar o círculo semi-transparente
fovCircle.Parent = gui
fovCircle.Visible = false  -- Inicialmente invisível

-- Função para exibir o FOV na tela
local function showFOV()
    if npcAimbotActive then
        fovCircle.Visible = true  -- Torna o círculo visível quando o aimbot está ativado
    else
        fovCircle.Visible = false  -- Oculta o círculo quando o aimbot está desativado
    end
end

-- A cada 0.1 segundos, verifica o aimbot para NPCs e o FOV
while true do
    if npcAimbotActive then
        npcAimbot()  -- Chama a função do aimbot para NPCs
    end
    showFOV()  -- Verifica a visibilidade do FOV
    wait(0.1)
end
