-- Variáveis iniciais
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local mouse = player:GetMouse()
local ativo = false  -- Controla o estado do ESP

-- Cores para os inimigos (vermelho)
local inimigoCor = Color3.fromRGB(255, 0, 0)

-- Criar a interface de usuário (GUI)
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player.PlayerGui

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 200, 0, 50)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.Text = "Ativar/Desativar ESP"
toggleButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Parent = screenGui

-- Função para desenhar caixas e linhas para inimigos
local function desenharESP()
    -- Limpar os ESPs anteriores
    for _, v in ipairs(workspace:GetChildren()) do
        if v:IsA("Model") and v:FindFirstChild("HumanoidRootPart") then
            local humanoidRootPart = v.HumanoidRootPart
            if v:FindFirstChild("ESP") then
                v.ESP:Destroy()
            end
        end
    end

    -- Criar novos ESPs para inimigos
    for _, v in ipairs(game.Players:GetPlayers()) do
        if v ~= player and v.Team ~= player.Team then  -- Inimigos (não da mesma equipe)
            local char = v.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                local humanoidRootPart = char.HumanoidRootPart
                local dist = (humanoidRootPart.Position - camera.CFrame.Position).Magnitude

                if dist <= 100 then  -- FOV de 100 studs
                    -- Criar a caixa
                    local esp = Instance.new("BillboardGui")
                    esp.Adornee = humanoidRootPart
                    esp.Size = UDim2.new(0, 200, 0, 50)
                    esp.StudsOffset = Vector3.new(0, 2, 0)
                    esp.Parent = char

                    local texto = Instance.new("TextLabel")
                    texto.Text = v.Name
                    texto.Size = UDim2.new(1, 0, 1, 0)
                    texto.BackgroundTransparency = 1
                    texto.TextColor3 = inimigoCor  -- Cor vermelha para inimigos
                    texto.TextScaled = true
                    texto.Parent = esp
                end
            end
        end
    end
end

-- Função para desenhar as setas para os inimigos
local function desenharSetas()
    for _, v in ipairs(workspace:GetChildren()) do
        if v:IsA("Model") and v:FindFirstChild("HumanoidRootPart") then
            local humanoidRootPart = v:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                local screenPos = camera:WorldToScreenPoint(humanoidRootPart.Position)
                local dist = (humanoidRootPart.Position - camera.CFrame.Position).Magnitude
                if dist <= 100 then
                    local arrow = Instance.new("Frame")
                    arrow.Size = UDim2.new(0, 30, 0, 30)
                    arrow.Position = UDim2.new(0, screenPos.X - 15, 0, screenPos.Y - 15)
                    arrow.BackgroundColor3 = inimigoCor  -- Cor vermelha para inimigos
                    arrow.BorderSizePixel = 0
                    arrow.Parent = game.Players.LocalPlayer.PlayerGui

                    local arrowIcon = Instance.new("ImageLabel")
                    arrowIcon.Image = "rbxassetid://1234567890"  -- Defina a ID da seta
                    arrowIcon.Size = UDim2.new(1, 0, 1, 0)
                    arrowIcon.BackgroundTransparency = 1
                    arrowIcon.Parent = arrow
                end
            end
        end
    end
end

-- Função para alternar o estado do ESP
toggleButton.MouseButton1Click:Connect(function()
    ativo = not ativo
    if ativo then
        toggleButton.Text = "Desativar ESP"
    else
        toggleButton.Text = "Ativar ESP"
    end
end)

-- Loop para atualizar o ESP e as setas
while true do
    wait(0.1)  -- Atualiza a cada 0.1 segundos
    if ativo then
        desenharESP()
        desenharSetas()
    end
end
