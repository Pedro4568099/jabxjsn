local nomeItemTransformacao = "Monster"
local nomeDaArma = "Gun"
local player = game.Players.LocalPlayer
local cam = workspace.CurrentCamera

-- GUI
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

-- Encontra o killer
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

-- Atira com a câmera simulando um clique direto no killer
local function atirar()
	local char = player.Character
	if not char then return end

	local arma = char:FindFirstChild(nomeDaArma) or player.Backpack:FindFirstChild(nomeDaArma)
	if not arma then return end

	if arma.Parent == player.Backpack then
		arma.Parent = char
		task.wait(0.15)
	end

	local killer = encontrarKiller()
	if killer and killer:FindFirstChild("HumanoidRootPart") then
		-- Mira na cabeça ou no tronco do killer
		local alvo = killer:FindFirstChild("Head") or killer:FindFirstChild("HumanoidRootPart")
		if not alvo then return end

		-- Ajusta câmera
		cam.CFrame = CFrame.new(cam.CFrame.Position, alvo.Position)
		task.wait(0.05)

		-- Simula clique real mirando no alvo
		if arma:IsA("Tool") and arma:FindFirstChild("Activate") then
			-- Alguns jogos verificam se o Mouse está mirando — vamos usar Raycast só por garantia
			local direction = (alvo.Position - cam.CFrame.Position).Unit
			local ray = Ray.new(cam.CFrame.Position, direction * 500)
			local part, position = workspace:FindPartOnRay(ray, player.Character, false, true)

			-- Garante que a mira vai exatamente onde está o killer
			arma:Activate()
		end
	else
		warn("Killer não encontrado.")
	end
end

button.MouseButton1Click:Connect(atirar)
