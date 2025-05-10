-- ESP Script com Highlight por item + Botão de Ativar/Desativar
-- Verde: Sobrevivente (sem item)
-- Vermelho: Monstro (item "Monster Transform")
-- Azul: Policial (arma)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Interface gráfica
local gui = Instance.new("ScreenGui", game:GetService("CoreGui"))
gui.Name = "ESP_GUI"
gui.ResetOnSpawn = false

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 120, 0, 30)
toggleButton.Position = UDim2.new(0, 20, 0, 80)
toggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Text = "ESP: ON"
toggleButton.Parent = gui

local espEnabled = true

-- Cria ou atualiza o Highlight
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

-- Atualiza ESP com base nos itens
local function updatePlayerESP(player)
    if player == LocalPlayer or not player.Character then return end
    local backpack = player:FindFirstChild("Backpack") or player:FindFirstChildOfClass("Backpack")
    if not backpack then return end

    local hasGun, isMonster = false, false

    for _, item in ipairs(backpack:GetChildren()) do
        local itemName = item.Name:lower()
        if itemName:find("gun") or itemName:find("pistol") or itemName:find("revolver") then
            hasGun = true
        elseif itemName:find("monster") or itemName:find("transform") then
            isMonster = true
        end
    end

    if isMonster then
        createHighlight(player, Color3.fromRGB(255, 0, 0)) -- Monstro: Vermelho
    elseif hasGun then
        createHighlight(player, Color3.fromRGB(0, 0, 255)) -- Policial: Azul
    else
        createHighlight(player, Color3.fromRGB(0, 255, 0)) -- Sobrevivente: Verde
    end
end

-- Loop de atualização
task.spawn(function()
    while true do
        if espEnabled then
            for _, player in pairs(Players:GetPlayers()) do
                pcall(function()
                    updatePlayerESP(player)
                end)
            end
        else
            -- Remove os highlights se desativado
            for _, player in pairs(Players:GetPlayers()) do
                if player.Character then
                    local hl = player.Character:FindFirstChild("RoleHighlight")
                    if hl then hl:Destroy() end
                end
            end
        end
        task.wait(2)
    end
end)

-- Função do botão
toggleButton.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    toggleButton.Text = espEnabled and "ESP: ON" or "ESP: OFF"
    toggleButton.BackgroundColor3 = espEnabled and Color3.fromRGB(30, 130, 30) or Color3.fromRGB(130, 30, 30)
end)
