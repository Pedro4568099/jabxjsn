shootButton.MouseButton1Click:Connect(function()
	local gun = hasGun()
	if not gun then
		warn("Você não está com a arma!")
		return
	end

	local character = LocalPlayer.Character
	if not character then
		warn("Sem personagem.")
		return
	end

	local handle = gun:FindFirstChild("Handle")
	if not handle then
		warn("A arma não tem Handle!")
		return
	end

	local targetPos = getKillerPosition()
	if not targetPos then
		warn("Killer não encontrado.")
		return
	end

	-- Forçar a arma a estar equipada (ativa)
	if LocalPlayer.Backpack:FindFirstChild("Gun") then
		LocalPlayer.Character.Humanoid:EquipTool(gun)
		task.wait(0.1)
	end

	local origin = handle.Position
	local direction = (targetPos - origin).Unit

	-- Aqui você simula o disparo exatamente como o servidor espera
	-- Isso deve ser igual ao console que você mostrou: direction, origin, target, nil
	ShootGun:FireServer(
		direction,
		origin,
		targetPos,
		nil
	)

	print("Atirou na direção do Killer.")
end)
