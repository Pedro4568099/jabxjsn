local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local ShootGun = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("ShootGun")

-- Criar a interface (se ainda não existir)
local gui = PlayerGui:FindFirstChild("ESP_GUI") or Instance.new("ScreenGui")
gui.Name = "ESP_GUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = PlayerGui

-- Criar um Frame para tornar o botão arrastável
local dragFrame = Instance.new("Frame")
dragFrame.Size = UDim2.new(0, 140, 0, 50)
dragFrame.Position = UDim2.new(0, 200, 0, 100)
dragFrame.BackgroundTransparency = 1
dragFrame.Active = true
dragFrame.Draggable = true
dragFrame.Parent = gui

-- Botão "SHOOT"
local shootButton = Instance.new("TextButton")
shootButton.Size = UDim2.new(1, 0, 1, 0)
shootButton.Position = UDim2.new(0, 0, 0, 0)
shootButton.Text = "SHOOT"
shootButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
shootButton.TextColor3 = Color3.new(1, 1, 1)
shootButton.Font = Enum.Font.SourceSansBold
shootButton.TextSize = 20
shootButton.Parent = dragFrame

-- Função: verificar se o jogador está com a arma
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

-- Função: obter posição do Killer
local function getKillerPosition()
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player:FindFirstChild("IsKiller") then
			local hrp = player.Character:FindFirstChild("HumanoidRootPart")
			if hrp then
				return hrp.Position
			end
		end
	end
	return nil
end

-- Evento de clique do botão
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

	local handle = gun:FindFirstChild("Handle")
	if not handle then
		warn("A arma não tem Handle.")
		return
	end

	local origin = handle.Position
	local direction = (targetPos - origin).Unit

	ShootGun:FireServer(direction, origin, targetPos, nil)
end)
