-- Criar GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ESP_GUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Criar botão
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 100, 0, 40)
toggleButton.Position = UDim2.new(0, 20, 0, 100)
toggleButton.Text = "Ativar ESP"
toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Parent = ScreenGui

-- Variáveis
local ESPEnabled = false
local ESPObjects = {}

-- Criar ESP para jogador
local function CreateESP(player)
    if player == game.Players.LocalPlayer then return end
    local Billboard = Instance.new("BillboardGui")
    Billboard.Name = "ESP_" .. player.Name
    Billboard.Size = UDim2.new(0, 200, 0, 30)
    Billboard.StudsOffset = Vector3.new(0, 3, 0)
    Billboard.AlwaysOnTop = true

    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(1, 0, 1, 0)
    Label.BackgroundTransparency = 1
    Label.TextColor3 = Color3.fromRGB(255, 0, 0)
    Label.TextStrokeTransparency = 0.5
    Label.Text = player.Name
    Label.Font = Enum.Font.SourceSansBold
    Label.TextSize = 14
    Label.Parent = Billboard

    ESPObjects[player] = Billboard

    local function update()
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Team ~= game.Players.LocalPlayer.Team then
            Billboard.Adornee = player.Character:FindFirstChild("HumanoidRootPart")
            Billboard.Parent = ESPEnabled and game.CoreGui or nil
        else
            Billboard.Parent = nil
        end
    end

    -- Atualização automática
    player.CharacterAdded:Connect(function()
        wait(1)
        update()
    end)

    game:GetService("RunService").RenderStepped:Connect(update)
end

-- Criar ESP para jogadores existentes
for _, player in ipairs(game.Players:GetPlayers()) do
    CreateESP(player)
end

-- Criar ESP para novos jogadores
game.Players.PlayerAdded:Connect(function(player)
    CreateESP(player)
end)

-- Remover ESP ao sair
game.Players.PlayerRemoving:Connect(function(player)
    if ESPObjects[player] then
        ESPObjects[player]:Destroy()
        ESPObjects[player] = nil
    end
end)

-- Alternar ESP com botão
toggleButton.MouseButton1Click:Connect(function()
    ESPEnabled = not ESPEnabled
    toggleButton.Text = ESPEnabled and "Desativar ESP" or "Ativar ESP"

    for _, esp in pairs(ESPObjects) do
        esp.Parent = ESPEnabled and game.CoreGui or nil
    end
end)
