local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local ShootGun = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("ShootGun")

-- Verifica se o jogador está com a arma
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

-- Posição do Killer
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

-- Detecta toque na tela
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end -- ignora se foi UI

	if input.UserInputType == Enum.UserInputType.Touch then
		local gun = hasGun()
		if not gun then return end

		local handle = gun:FindFirstChild("Handle")
		if not handle then return end

		local targetPos = getKillerPosition()
		if not targetPos then return end

		local origin = handle.Position
		local direction = (targetPos - origin).Unit

		-- Disparo automático
		ShootGun:FireServer(direction, origin, targetPos, nil)
	end
end)
