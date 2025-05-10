shootButton.MouseButton1Click:Connect(function()
    local gun = hasGun()
    if not gun then
        warn("Você não está com a arma!")
        return
    end

    local character = LocalPlayer.Character
    local humanoidRootPart = character and character:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then
        warn("Sem HumanoidRootPart.")
        return
    end

    local targetPos = getKillerPosition()
    if not targetPos then
        warn("Killer não encontrado.")
        return
    end

    local gunHandle = gun:FindFirstChild("Handle")
    if not gunHandle then
        warn("A arma não tem Handle.")
        return
    end

    -- Cálculo de direção como aimbot
    local origin = gunHandle.Position
    local direction = (targetPos - origin).Unit

    -- Executa o disparo
    ShootGun:FireServer(
        direction, -- direção do tiro
        origin,    -- origem do tiro (posição da arma)
        targetPos, -- alvo (killer)
        nil        -- último argumento, conforme visto no console
    )
end)
