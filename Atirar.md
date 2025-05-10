-- Criação do botão móvel
local btn = Instance.new("TextButton")
btn.Size = UDim2.new(0, 100, 0, 40)
btn.Position = UDim2.new(0.5, -50, 0.9, 0)
btn.Text = "SHOOT"
btn.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
btn.TextColor3 = Color3.new(1,1,1)
btn.TextScaled = true
btn.Parent = game.CoreGui

-- Tornar o botão arrastável
btn.Active = true
btn.Draggable = true

-- Função de pegar arma
local function pegarArma()
    local char = game.Players.LocalPlayer.Character
    local backpack = game.Players.LocalPlayer:FindFirstChild("Backpack")
    if backpack then
        local arma = backpack:FindFirstChild("Gun")
        if arma then
            arma.Parent = char
        end
    end
end

-- Função para encontrar o killer e atirar
local function atirarNoKiller()
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local shootRemote = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("ShootGun")
    local killer = nil

    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character then
            if player.Character:FindFirstChild("Monster") then
                killer = player
                break
            end
        end
    end

    if killer and killer.Character and killer.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = killer.Character.HumanoidRootPart
        local char = game.Players.LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if root then
            local dir = (hrp.Position - root.Position).Unit
            shootRemote:FireServer(dir, Vector3.new(), root.Position, 1)
        end
    else
        warn("Killer não encontrado")
    end
end

-- Ação do botão
btn.MouseButton1Click:Connect(function()
    pegarArma()
    wait(0.2)
    atirarNoKiller()
end)
