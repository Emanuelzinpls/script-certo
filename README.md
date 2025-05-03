local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local espEnabled = false  -- ESP inicialmente desativado
local aimbotEnabled = false  -- Aimbot inicialmente desativado

-- Função para criar a tela flutuante no estilo brainrot
local function showFloatingWindow()
    -- Cria a GUI da tela flutuante
    local gui = Instance.new("ScreenGui", game.CoreGui)
    gui.Name = "FloatingWindow"

    -- Criando a tela flutuante
    local floatingWindow = Instance.new("Frame", gui)
    floatingWindow.Size = UDim2.new(0, 400, 0, 300)
    floatingWindow.Position = UDim2.new(0.5, -200, 0.5, -150)
    floatingWindow.BackgroundColor3 = Color3.fromRGB(255, 50, 50)  -- Cor vermelha vibrante
    floatingWindow.BorderSizePixel = 5
    floatingWindow.Draggable = true  -- Permite arrastar a tela
    floatingWindow.Active = true  -- Permite interação com a tela
    floatingWindow.BackgroundTransparency = 0.2
    floatingWindow.BorderColor3 = Color3.fromRGB(0, 255, 255)  -- Bordas neon

    -- Título da tela flutuante
    local title = Instance.new("TextLabel", floatingWindow)
    title.Size = UDim2.new(1, 0, 0, 40)
    title.Text = "Xurrasco"
    title.TextColor3 = Color3.fromRGB(0, 255, 255)  -- Neon azul
    title.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Fundo vermelho
    title.Font = Enum.Font.GothamBold
    title.TextSize = 28
    title.TextXAlignment = Enum.TextXAlignment.Center
    title.TextYAlignment = Enum.TextYAlignment.Center
    title.TextStrokeTransparency = 0.6  -- Sombra forte para o título
    title.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)

    -- Criando a área de categorias (Esp e Aimbot)
    local categoriesFrame = Instance.new("Frame", floatingWindow)
    categoriesFrame.Size = UDim2.new(1, 0, 0, 50)
    categoriesFrame.Position = UDim2.new(0, 0, 0.1, 0)
    categoriesFrame.BackgroundTransparency = 1  -- Torna transparente para não bloquear os botões

    -- Botão de Ativar/Desativar ESP
    local espButton = Instance.new("TextButton", categoriesFrame)
    espButton.Size = UDim2.new(0, 120, 0, 40)
    espButton.Position = UDim2.new(0, 10, 0, 0)
    espButton.Text = "Ativar ESP"
    espButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    espButton.BackgroundColor3 = Color3.fromRGB(0, 255, 255)  -- Neon azul
    espButton.Font = Enum.Font.SourceSansBold
    espButton.TextSize = 18
    espButton.TextStrokeTransparency = 0.5
    espButton.TextStrokeColor3 = Color3.fromRGB(255, 0, 0)

    espButton.MouseEnter:Connect(function()  -- Efeito ao passar o mouse
        espButton.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
    end)

    espButton.MouseLeave:Connect(function()  -- Efeito ao tirar o mouse
        espButton.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
    end)

    espButton.MouseButton1Click:Connect(function()
        espEnabled = not espEnabled
        espButton.Text = espEnabled and "Desativar ESP" or "Ativar ESP"
    end)

    -- Botão de Ativar/Desativar Aimbot
    local aimbotButton = Instance.new("TextButton", categoriesFrame)
    aimbotButton.Size = UDim2.new(0, 120, 0, 40)
    aimbotButton.Position = UDim2.new(0, 140, 0, 0)
    aimbotButton.Text = "Ativar Aimbot"
    aimbotButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    aimbotButton.BackgroundColor3 = Color3.fromRGB(255, 0, 255)  -- Neon rosa
    aimbotButton.Font = Enum.Font.SourceSansBold
    aimbotButton.TextSize = 18
    aimbotButton.TextStrokeTransparency = 0.5
    aimbotButton.TextStrokeColor3 = Color3.fromRGB(0, 255, 0)

    aimbotButton.MouseEnter:Connect(function()  -- Efeito ao passar o mouse
        aimbotButton.BackgroundColor3 = Color3.fromRGB(255, 100, 255)
    end)

    aimbotButton.MouseLeave:Connect(function()  -- Efeito ao tirar o mouse
        aimbotButton.BackgroundColor3 = Color3.fromRGB(255, 0, 255)
    end)

    aimbotButton.MouseButton1Click:Connect(function()
        aimbotEnabled = not aimbotEnabled
        aimbotButton.Text = aimbotEnabled and "Desativar Aimbot" or "Ativar Aimbot"
    end)

    -- Botão de minimizar
    local minimizeButton = Instance.new("TextButton", floatingWindow)
    minimizeButton.Size = UDim2.new(0, 40, 0, 40)
    minimizeButton.Position = UDim2.new(1, -40, 0, 0)
    minimizeButton.Text = "-"
    minimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    minimizeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 255)  -- Neon rosa
    minimizeButton.Font = Enum.Font.SourceSansBold
    minimizeButton.TextSize = 24
    minimizeButton.TextStrokeTransparency = 0.5
    minimizeButton.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)

    -- Função para minimizar
    local isMinimized = false
    minimizeButton.MouseButton1Click:Connect(function()
        if not isMinimized then
            floatingWindow.Size = UDim2.new(0, 400, 0, 40)  -- Reduz a altura para apenas o título
            categoriesFrame.Visible = false  -- Esconde a área de categorias (ESP e Aimbot)
            isMinimized = true
        else
            floatingWindow.Size = UDim2.new(0, 400, 0, 300)  -- Restaura o tamanho original
            categoriesFrame.Visible = true  -- Mostra a área de categorias novamente
            isMinimized = false
        end
    end)
end

-- Exibe a tela flutuante assim que o script iniciar
showFloatingWindow()
