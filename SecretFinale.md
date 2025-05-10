-- ESP com Highlight por item + Botão de Ativar/Desativar (corrigido)
-- Verde: Sobrevivente
-- Vermelho: Monstro
-- Azul: Policial (único com a arma específica)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- GUI
local gui = Instance.new("ScreenGui", game:GetService("CoreGui"))
gui.Name = "ESP_GUI"
gui.ResetOnSpawn = false

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 120, 0, 30)
toggleButton.Position = UDim2.new(0, 20, 0, 80)
toggleButton.BackgroundColor3 = Color3.fromRGB(30, 130, 30)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Text = "ESP: ON"
toggleButton.Parent = gui

local espEnabled = true

-- Função para adicionar ou atualizar Highlight
local function createHighlight(player, color)
    if not player.Character then return end
    local highlight = player.Character:FindFirstChild("RoleHighlight")
    if not highlight then
        highlight = Instance.new("Highlight")
        highlight.Name = "RoleHighlight"
        highlight.FillTransparency = 0.5
        highlight.OutlineTransparency = 1
        highlight.Parent = player.Character
    end
    highlight.FillColor = color
end

-- Detecta os itens (Backpack + Character)
local function updatePlayerESP(player)
    if player == LocalPlayer or not player.Character then return end
    local isMonster, isCop = false, false

    -- Verifica itens na mochila e no personagem
    local sources = {}
    if player:FindFirstChild("Backpack") then table.insert(sources, player.Backpack:GetChildren()) end
    if player.Character then table.insert(sources, player.Character:GetChildren()) end

    for _, source in pairs(sources) do
        for _, item in pairs(source) do
            local itemName = item.Name:lower()
            if itemName:find("monster") or itemName:find("transform") then
                isMonster = true
            elseif item.Name == "Gun" or item.Name == "Revolver" or item.Name == "Pistol" then
                isCop = true
            end
        end
    end

    -- Define a cor com base no item
    if isMonster then
        createHighlight(player, Color3.fromRGB(255, 0, 0)) -- Vermelho: Monstro
    elseif isCop then
        createHighlight(player, Color3.fromRGB(0, 0, 255)) -- Azul: Policial
    else
        createHighlight(player, Color3.fromRGB(0, 255, 0)) -- Verde: Sobrevivente
    end
end

-- Loop principal
task.spawn(function()
    while true do
        if espEnabled then
            for _, player in pairs(Players:GetPlayers()) do
                pcall(function()
                    updatePlayerESP(player)
                end)
            end
        else
            for _, player in pairs(Players:GetPlayers()) do
                if player.Character then
                    local hl = player.Character:FindFirstChild("RoleHighlight")
                    if hl then hl:Destroy() end
                end
            end
        end
        task.wait(1.5)
    end
end)

-- Toggle do botão
toggleButton.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    toggleButton.Text = espEnabled and "ESP: ON" or "ESP: OFF"
    toggleButton.BackgroundColor3 = espEnabled and Color3.fromRGB(30, 130, 30) or Color3.fromRGB(130, 30, 30)
end)
