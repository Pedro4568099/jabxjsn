-- CONFIGURAÇÃO
local nomeItemTransformacao = "Monster"
local nomeDaArma = "Gun"
local remote = game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):FindFirstChild("ShootGun")
local player = game.Players.LocalPlayer

-- BOTÃO MÓVEL
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

-- DETECTAR KILLER
local function encontrarKiller()
	for _, plr in pairs(game.Players:GetPlayers()) do
		if plr ~= player then
			local mochila = plr:FindFirstChild("Backpack")
			local char = plr.Character
			if (mochila and mochila:FindFirstChild(nomeItemTransformacao)) or (char and char:FindFirstChild(nomeItemTransformacao)) then
				return char
			end
		end
	end
end

-- FUNÇÃO DE TIRO
local function atirarNoKiller()
	local char = player.Character
	if not char then return end

	local arma = char:FindFirstChild(nomeDaArma) or player.Backpack:FindFirstChild(nomeDaArma)
	if arma and arma.Parent == player.Backpack then
		arma.Parent = char
		task.wait(0.2)
	end

	local head = char:FindFirstChild("Head")
	local killer = encontrarKiller()

	if head and killer and killer:FindFirstChild("HumanoidRootPart") then
		local origem = head.Position
		local alvo = killer.HumanoidRootPart.Position
		local direcao = (alvo - origem).Unit

		remote:FireServer(direcao, Vector3.zero, origem, 1)
	else
		warn("Killer não encontrado ou partes ausentes.")
	end
end

-- BOTÃO CONECTADO
button.MouseButton1Click:Connect(atirarNoKiller)
