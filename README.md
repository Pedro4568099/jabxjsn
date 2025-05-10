-- ESP Highlight básico com Team Check
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Cores por equipe
local teamColors = {
    ["Monstro"] = Color3.fromRGB(255, 0, 0),      -- Vermelho
    ["Policial"] = Color3.fromRGB(0, 0, 255),     -- Azul
    ["Inocente"] = Color3.fromRGB(0, 255, 0),     -- Verde
}

-- Função para aplicar o ESP
local function applyESP(player)
    if player == LocalPlayer then return end

    local character = player.Character or player.CharacterAdded:Wait()
    if character:FindFirstChild("Highlight") then return end

    local highlight = Instance.new("Highlight")
    highlight.Name = "Highlight"
    highlight.FillTransparency = 1
    highlight.OutlineTransparency = 0
    highlight.Adornee = character
    highlight.Parent = character

    -- Definir cor com base no nome do time (ajuste se necessário)
    local teamName = player.Team and player.Team.Name or "Inocente"
    highlight.OutlineColor = teamColors[teamName] or Color3.fromRGB(255, 255, 255)
end

-- Aplicar ESP a todos os jogadores existentes
for _, player in pairs(Players:GetPlayers()) do
    applyESP(player)
end

-- Monitorar novos jogadores
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        wait(1) -- espera o personagem carregar
        applyESP(player)
    end)
end)

-- Atualizar quando personagem muda
for _, player in pairs(Players:GetPlayers()) do
    player.CharacterAdded:Connect(function()
        wait(1)
        applyESP(player)
    end)
end
