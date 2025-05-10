local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local Remotes = ReplicatedStorage:WaitForChild("Remotes")
local ShootGun = Remotes:WaitForChild("ShootGun")

local ESPEnabled = true

-- UI
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.ResetOnSpawn = false

local toggleESPButton = Instance.new("TextButton", gui)
toggleESPButton.Size = UDim2.new(0, 120, 0, 30)
toggleESPButton.Position = UDim2.new(0, 10, 0, 80)
toggleESPButton.Text = "Toggle ESP"
toggleESPButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)

local shootButton = Instance.new("TextButton", gui)
shootButton.Size = UDim2.new(0, 120, 0, 30)
shootButton.Position = UDim2.new(0, 10, 0, 120)
shootButton.Text = "SHOOT"
shootButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
shootButton.TextColor3 = Color3.new(1, 1, 1)

-- Função para verificar se tem a arma
local function hasGun()
    local character = LocalPlayer.Character
    if not character then return nil end
    for _, item in ipairs(character:GetChildren()) do
        if item:IsA("Tool") and item.Name == "Gun" then
            return item
        end
    end
    return nil
end

-- Identificar Killer (ajuste se for por outro método!)
local function isKiller(player)
    return player:FindFirstChild("IsKiller") ~= nil
end

-- Função para pegar posição do Killer
local function getKillerPosition()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and isKiller(player) then
            local hrp = player.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                return hrp.Position
            end
        end
    end
    return nil
end

-- Disparo
shootButton.MouseButton1Click:Connect(function()
    local gun = hasGun()
    if not gun then
        warn("Você não está com a arma!")
        return
    end

    local targetPos = getKillerPosition()
    if not targetPos then
        warn("Killer não encontrado.")
        return
    end

    local gunPos = gun.Handle.Position
    local direction = (targetPos - gunPos).Unit

    ShootGun:FireServer(direction, gunPos, targetPos, nil)
end)

-- ESP
local function createESP(player)
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "ESP"
    billboard.Size = UDim2.new(0, 100, 0, 20)
    billboard.AlwaysOnTop = true
    billboard.StudsOffset = Vector3.new(0, 3, 0)

    local label = Instance.new("TextLabel", billboard)
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.new(1, 1, 1)
    label.TextStrokeTransparency = 0
    label.Font = Enum.Font.SourceSansBold
    label.TextScaled = true

    RunService.RenderStepped:Connect(function()
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and ESPEnabled then
            billboard.Parent = player.Character.HumanoidRootPart
            label.Text = player.Name
            if isKiller(player) then
                label.TextColor3 = Color3.fromRGB(255, 0, 0)
            else
                label.TextColor3 = Color3.fromRGB(0, 255, 0)
            end
        else
            billboard.Parent = nil
        end
    end)
end

-- Ativar ESP para todos os jogadores
for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer then
        createESP(player)
    end
end

Players.PlayerAdded:Connect(function(player)
    if player ~= LocalPlayer then
        createESP(player)
    end
end)

-- Toggle do ESP
toggleESPButton.MouseButton1Click:Connect(function()
    ESPEnabled = not ESPEnabled
    toggleESPButton.Text = ESPEnabled and "ESP ON" or "ESP OFF"
end)
