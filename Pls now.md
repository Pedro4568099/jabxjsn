shootButton.MouseButton1Click:Connect(function()
	local gun = hasGun()
	if not gun then warn("Você não está com a arma!"); return end

	local handle = gun:FindFirstChild("Handle")
	if not handle then warn("A arma não tem Handle!"); return end

	local killerPos = getKillerPosition()
	if not killerPos then warn("Killer não encontrado!"); return end

	-- Força equipar a arma, se estiver na mochila
	if LocalPlayer.Backpack:FindFirstChild("Gun") then
		LocalPlayer.Character.Humanoid:EquipTool(gun)
		task.wait(0.2)
	end

	-- Ativa a Tool (muito importante em alguns jogos)
	pcall(function()
		gun:Activate()
	end)

	task.wait(0.05) -- Pequeno delay para garantir ativação

	local origin = handle.Position
	local direction = (killerPos - origin).Unit

	-- Disparo
	ShootGun:FireServer(direction, origin, killerPos, nil)
end)
