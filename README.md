-- Variáveis locais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local rayParams = RaycastParams.new()  -- Parâmetros do Raycast

-- Parâmetros do Aimbot
local fovRadius = 50 -- Definindo o raio do FOV (quanto maior, maior a área em que o aimbot procurará alvos)
local aimbotActive = false -- Controle de ativação do Aimbot

-- Função para verificar se o alvo está dentro do FOV
local function isInFOV(targetPosition)
    -- Calcula a posição do alvo em relação à câmera
    local cameraPosition = camera.CFrame.Position
    local direction = (targetPosition - cameraPosition).unit
    local dotProduct = camera.CFrame.LookVector:Dot(direction)
    
    -- FOV é baseado no ângulo da câmera
    return dotProduct > math.cos(math.rad(fovRadius)) -- Se estiver dentro do FOV
end

-- Função do Aimbot com Câmera Livre e FOV
local function aimbot()
    local mouse = player:GetMouse()
    local target = nil
    local closestDistance = math.huge

    -- Encontra o alvo mais próximo (outro jogador da equipe adversária)
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer ~= player and otherPlayer.Team.Name ~= player.Team.Name then
            local targetCharacter = otherPlayer.Character
            if targetCharacter and targetCharacter:FindFirstChild("HumanoidRootPart") then
                local targetPosition = targetCharacter.HumanoidRootPart.Position

                -- Verifica se o alvo está dentro do FOV
                if isInFOV(targetPosition) then
                    local distance = (mouse.Hit.p - targetPosition).Magnitude
                    if distance < closestDistance then
                        target = targetCharacter
                        closestDistance = distance
                    end
                end
            end
        end
    end

    -- Mira para o alvo mais próximo dentro do FOV
    if target then
        local humanoidRootPart = player.Character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            local direction = (target.HumanoidRootPart.Position - humanoidRootPart.Position).unit
            local ray = Ray.new(humanoidRootPart.Position, direction * 1000)  -- Define o alcance da mira
            rayParams.FilterDescendantsInstances = {player.Character}  -- Ignora o próprio jogador para não detectar ele mesmo

            -- Realiza o Raycast, ignorando as colisões
            local hitPart, hitPosition = workspace:Raycast(humanoidRootPart.Position, direction * 1000, rayParams)

            -- Verifica se o raycast atingiu algo
            if hitPart then
                -- Aqui você pode simular o disparo ou a ação de atirar
                print(player.Name .. " está mirando em " .. target.Name)
                -- Exemplo de disparo (adicione a lógica de disparo do jogo aqui)
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
local
