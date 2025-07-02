-- SERVIÇOS
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()
local ESPEnabled = false
local ESPFolder = Instance.new("Folder", game.CoreGui)
ESPFolder.Name = "ESP_Highlights"

-- FUNÇÃO: Ativar ESP
local function toggleESP()
    ESPEnabled = not ESPEnabled

    if not ESPEnabled then
        ESPFolder:ClearAllChildren()
        return
    end

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local highlight = Instance.new("Highlight")
            highlight.Name = "ESP_Highlight"
            highlight.Adornee = player.Character
            highlight.FillColor = Color3.fromRGB(255, 0, 0)
            highlight.OutlineColor = Color3.new(1, 1, 1)
            highlight.FillTransparency = 0.5
            highlight.OutlineTransparency = 0
            highlight.Parent = ESPFolder
        end
    end
end

-- Atualizar ESP em jogadores que entrarem depois
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        if ESPEnabled then
            wait(1)
            local highlight = Instance.new("Highlight")
            highlight.Name = "ESP_Highlight"
            highlight.Adornee = character
            highlight.FillColor = Color3.fromRGB(255, 0, 0)
            highlight.OutlineColor = Color3.new(1, 1, 1)
            highlight.FillTransparency = 0.5
            highlight.OutlineTransparency = 0
            highlight.Parent = ESPFolder
        end
    end)
end)

-- FUNÇÃO: Teleportar para onde o mouse aponta
local function teleportToMouse()
    local character = LocalPlayer.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return end

    local camera = workspace.CurrentCamera
    local origin = camera.CFrame.Position
    local direction = (Mouse.Hit.Position - origin).Unit * 1000

    local raycastParams = RaycastParams.new()
    raycastParams.FilterDescendantsInstances = {character}
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist

    local result = workspace:Raycast(origin, direction, raycastParams)

    if result then
        character:MoveTo(result.Position + Vector3.new(0, 3, 0)) -- Evita cair no chão
    end
end

-- DETECÇÃO DE TECLAS
UIS.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.K then
        toggleESP()
    elseif input.KeyCode == Enum.KeyCode.G then
        teleportToMouse()
    end
end)
