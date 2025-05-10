local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local ShootGun = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("ShootGun")

-- Criar a interface
local gui = Instance.new("ScreenGui")
gui.Name = "AutoShootGUI"
gui.Parent = PlayerGui
gui.ResetOnSpawn = false

-- Criar botão
local shootButton = Instance.new("TextButton")
shootButton.Size = UDim2.new(0, 160, 0, 50)
shootButton.Position = UDim2.new(0, 20, 1, -70)
shootButton.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
shootButton.TextColor3 = Color3.new(1, 1, 1)
shootButton.Font = Enum.Font.SourceSansBold
shootButton.TextSize = 18
shootButton.Text = "Shoot Killer"
shootButton.Parent = gui

-- Função: checar se está com a arma
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

-- Função: pegar posição do killer
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

-- Ao clicar no botão
shootButton.MouseButton1Click:Connect(function()
	local gun = hasGun()
	if not gun then warn("Você não está com a arma!"); return end

	local handle = gun:FindFirstChild("Handle")
	if not handle then warn("A arma não tem Handle!"); return end

	local killerPos = getKillerPosition()
	if not killerPos then warn("Killer não encontrado!"); return end

	-- Força equipar a arma (caso esteja na mochila)
	if LocalPlayer.Backpack:FindFirstChild("Gun") then
		LocalPlayer.Character.Humanoid:EquipTool(gun)
		task.wait(0.1)
	end

	local origin = handle.Position
	local direction = (killerPos - origin).Unit

	-- Disparo automático no killer
	ShootGun:FireServer(direction, origin, killerPos, nil)
end)
