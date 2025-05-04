-- Função do Aimbot que mira apenas nos NPCs (monstros) e não em jogadores
local function aimbotNPC()
    local closestNPC = nil
    local closestDistance = math.huge
    local targetHeadPosition = nil

    -- Verificar todos os NPCs no jogo
    for _, obj in pairs(workspace:GetDescendants()) do
        -- Verificar se o objeto é um NPC (monstro) e se ele tem uma cabeça (ou parte de corpo relevante)
        if obj:IsA("Model") and obj:FindFirstChild("HumanoidRootPart") and obj:FindFirstChild("Head") then
            local npc = obj
            local npcHead = npc.Head
            local distance = (camera.CFrame.Position - npcHead.Position).Magnitude

            -- Verifica a distância entre o jogador e o NPC, e escolhe o mais próximo
            if distance < closestDistance then
                closestNPC = npc
                closestDistance = distance
                targetHeadPosition = npcHead.Position
            end
        end
    end

    -- Se um NPC foi encontrado, mira na cabeça dele
    if closestNPC then
        local character = player.Character
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")

        if humanoidRootPart and targetHeadPosition then
            -- Puxando a mira para o NPC
            local direction = (targetHeadPosition - humanoidRootPart.Position).unit
            camera.CFrame = CFrame.new(camera.CFrame.Position, targetHeadPosition)
            print("Mirando no NPC: " .. closestNPC.Name)
        end
    end
end

-- Rodando o Aimbot enquanto ele estiver ativado
game:GetService("RunService").RenderStepped:Connect(function()
    if aimbotActive then
        aimbotNPC()  -- Chama a função do Aimbot para mirar nos NPCs (monstros)
    end
end)
