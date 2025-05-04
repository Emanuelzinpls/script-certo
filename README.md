-- Função para obter a posição da tela de um NPC
local function getNpcScreenPosition(npcHead)
    local screenPos, onScreen = camera:WorldToViewportPoint(npcHead.Position)
    return screenPos, onScreen
end

-- Função de ativação/desativação do 2x Hitbox
local function toggleHitbox()
    hitboxActive = not hitboxActive

    if hitboxActive then
        -- Criar e mostrar caixas de hitbox
        for _, npc in pairs(workspace:GetDescendants()) do
            if npc:IsA("Model") and npc:FindFirstChild("Head") and npc:FindFirstChild("Humanoid") and not game.Players:GetPlayerFromCharacter(npc) then
                local headPos = npc.Head.Position
                local box = Drawing.new("Square")
                box.Size = Vector2.new(100, 100) -- Defina o tamanho da caixa como desejar
                box.Color = Color3.fromRGB(255, 255, 0)  -- Amarelo
                box.Thickness = 2
                box.Filled = false
                box.Visible = hitboxActive
                box.Parent = npc
                
                -- Atualiza a posição da caixa de hitbox para acompanhar o NPC
                runService.RenderStepped:Connect(function()
                    local screenPos, onScreen = getNpcScreenPosition(npc.Head)
                    if onScreen then
                        -- Ajusta a posição da caixa com base na posição do NPC
                        box.Position = Vector2.new(screenPos.X - box.Size.X / 2, screenPos.Y - box.Size.Y / 2)
                    else
                        box.Visible = false
                    end
                end)

                -- Armazenar a caixa para garantir que será removido quando desativado
                npc.Humanoid.Died:Connect(function()
                    box.Visible = false
                end)
            end
        end
    else
        -- Remover as caixas de hitbox
        for _, npc in pairs(workspace:GetDescendants()) do
            if npc:IsA("Model") and npc:FindFirstChild("Head") then
                for _, child in pairs(npc:GetChildren()) do
                    if child:IsA("Drawing") then
                        child.Visible = false
                    end
                end
            end
        end
    end
end
