local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local StarterGui = game:GetService("StarterGui")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local ShootGun = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("ShootGun")

-- Criar GUI
local gui = Instance.new("ScreenGui")
gui.Name = "KillerAimbotGUI"
gui.ResetOnSpawn = false
gui.Parent = PlayerGui

-- Botão de Aimbot
local shootButton = Instance.new("TextButton")
shootButton.Size = UDim2.new(0, 180, 0, 50)
shootButton.Position = UDim2.new(0, 20, 1, -60)
shootButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
shootButton.TextColor3 = Color3.fromRGB(255, 255, 255)
shootButton.Text = "AIMBOT SHOOT"
shootButton.Font = Enum.Font.GothamBold
shootButton.TextSize = 20
shootButton.Parent = gui

-- Pega a Tool "Gun"
local function getGun()
	local char = LocalPlayer.Character
	if char then
		for _, tool in pairs(char:GetChildren()) do
			if tool:IsA("Tool") and tool.Name == "Gun" then
				return tool
			end
		end
	end
	-- Se estiver na mochila
	local bpGun = LocalPlayer.Backpack:FindFirstChild("Gun")
	if bpGun then
		char.Humanoid:EquipTool(bpGun)
		task.wait(0.2)
		return bpGun
	end
	return nil
end

-- Pega a posição do killer
local function getKillerPosition()
	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer and plr.Character and plr:FindFirstChild("IsKiller") then
			local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
			if hrp then
				return hrp.Position
			end
		end
	end
	return nil
end

-- Ação ao clicar no botão
shootButton.MouseButton1Click:Connect(function()
	local gun = getGun()
	if not gun then
		StarterGui:SetCore("SendNotification", {
			Title = "Erro",
			Text = "Você não está com a arma!",
			Duration = 2
		})
		return
	end

	local handle = gun:FindFirstChild("Handle")
	if not handle then return end

	local killerPos = getKillerPosition()
	if not killerPos then
		StarterGui:SetCore("SendNotification", {
			Title = "Erro",
			Text = "Killer não encontrado!",
			Duration = 2
		})
		return
	end

	-- Simula clique na arma
	pcall(function() gun:Activate() end)
	task.wait(0.05)

	local origin = handle.Position
	local direction = (killerPos - origin).Unit

	ShootGun:FireServer(direction, origin, killerPos, nil)
end)
