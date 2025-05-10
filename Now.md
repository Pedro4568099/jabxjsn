local nomeItemTransformacao = "Monster"
local nomeDaArma = "Gun"
local player = game.Players.LocalPlayer

-- Botão móvel
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.ResetOnSpawn = false

local button = Instance.new("TextButton", gui)
button.Text = "AIMBOT SHOOT"
button.Size = UDim2.new(0, 160, 0, 50)
button.Position = UDim2.new(0.5, -80, 0.9, 0)
button.BackgroundColor3 = Color3.new(1, 0, 0)
button.TextColor3 = Color3.new(1, 1, 1)
button.TextScaled = true
button.Active = true
button.Draggable = true

-- Encontra o killer com o item
local function encontrarKiller()
	for _, plr in pairs(game.Players:GetPlayers()) do
		if plr ~= player then
			local bp = plr:FindFirstChild("Backpack")
			local char = plr.Character
			if (bp and bp:FindFirstChild(nomeItemTransformacao)) or (char and char:FindFirstChild(nomeItemTransformacao)) then
				return char
			end
		end
	end
end

-- Ajusta a câmera para mirar
local function mirarNoKiller(killer)
	local camera = workspace.CurrentCamera
	local hrp = killer:FindFirstChild("HumanoidRootPart")
	if hrp then
		camera.CFrame = CFrame.new(camera.CFrame.Position, hrp.Position)
	end
end

-- Atira no killer usando Activate()
local function atirar()
	local char = player.Character
	if not char then return end

	local arma = char:FindFirstChild(nomeDaArma) or player.Backpack:FindFirstChild(nomeDaArma)
	if arma then
		if arma.Parent == player.Backpack then
			arma.Parent = char
			task.wait(0.15)
		end

		local killer = encontrarKiller()
		if killer and killer:FindFirstChild("HumanoidRootPart") then
			mirarNoKiller(killer)
			task.wait(0.1) -- espera a câmera ajustar
			if arma:FindFirstChild("Activate") or arma:IsA("Tool") then
				arma:Activate()
			end
		else
			warn("Killer não encontrado.")
		end
	end
end

button.MouseButton1Click:Connect(atirar)
