--// ESP Script com Interface Simples e Team Check
--// Para uso em jogos com autorização para testes

-- Interface
local ScreenGui = Instance.new("ScreenGui", game.Players.LocalPlayer:WaitForChild("PlayerGui"))
local ToggleButton = Instance.new("TextButton", ScreenGui)
ToggleButton.Size = UDim2.new(0, 120, 0, 40)
ToggleButton.Position = UDim2.new(0, 10, 0, 10)
ToggleButton.Text = "ESP: OFF"
ToggleButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)

-- Variáveis principais
local ESP_Ativo = false
local ESP_Objects = {}

-- Função para criar ESP em um jogador
local function CriarESP(player)
    if player == game.Players.LocalPlayer then return end
    if player.Team == game.Players.LocalPlayer.Team then return end -- Team Check

    local box = Drawing.new("Text")
    box.Size = 16
    box.Center = true
    box.Outline = true
    box.Font = 2
    box.Color = Color3.fromRGB(255, 0, 0)
    box.Text = player.Name
    box.Visible = false

    ESP_Objects[player] = box
end

-- Atualização contínua
game:GetService("RunService").RenderStepped:Connect(function()
    if not ESP_Ativo then
        for _, box in pairs(ESP_Objects) do
            box.Visible = false
        end
        return
    end

    for player, box in pairs(ESP_Objects) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local pos, vis = workspace.CurrentCamera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
            if vis then
                box.Position = Vector2.new(pos.X, pos.Y)
                box.Visible = true
            else
                box.Visible = false
            end
        end
    end
end)

-- Detectar novos jogadores
game.Players.PlayerAdded:Connect(function(plr)
    wait(1)
    CriarESP(plr)
end)

-- Inicializar ESP para todos
for _, plr in pairs(game.Players:GetPlayers()) do
    CriarESP(plr)
end

-- Botão de ativação
ToggleButton.MouseButton1Click:Connect(function()
    ESP_Ativo = not ESP_Ativo
    ToggleButton.Text = ESP_Ativo and "ESP: ON" or "ESP: OFF"
    ToggleButton.BackgroundColor3 = ESP_Ativo and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(40, 40, 40)
end)
