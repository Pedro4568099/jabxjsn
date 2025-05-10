--[[
  Secret Killer: Sheriff Aim Assist + ESP (por ChatGPT)
  REQUISITOS: executor com Drawing ou Highlight support
]]

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local LocalPlayer = Players.LocalPlayer

local aimbotEnabled = true
local espEnabled = true

-- Configurações de Highlight
local function createHighlight(target)
    if target and not target:FindFirstChild("AimESP") then
        local highlight = Instance.new("Highlight")
        highlight.Name = "AimESP"
        highlight.FillColor = Color3.fromRGB(255, 0, 0)
        highlight.OutlineColor = Color3.fromRGB(0, 0, 0)
        highlight.FillTransparency = 0.5
        highlight.OutlineTransparency = 0
        highlight.Adornee = target
        highlight.Parent = target
    end
end

-- Detectar se jogador é o sheriff (arma equipada)
local function isSheriff()
    local char = LocalPlayer.Character
    if char then
        local tool = char:FindFirstChildOfClass("Tool")
        return tool and tool.Name:lower():find("gun")
    end
    return false
end

-- Detectar o Monstro com base em tags visuais (ajuste se necessário)
local function getMonster()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            -- Detectar se há algum identificador do monstro (ajuste se necessário)
            if player.Character:FindFirstChild("MonsterTag") or player:FindFirstChild("Monster") or player.DisplayName:lower():find("monster") then
                return player
            end
        end
    end
end

-- ESP automático
task.spawn(function()
    while espEnabled do
        local monster = getMonster()
        if monster and monster.Character then
            createHighlight(monster.Character)
        end
        task.wait(1)
    end
end)

-- Auto mira quando clicar
LocalPlayer:GetMouse().Button1Down:Connect(function()
    if not aimbotEnabled then return end
    if not isSheriff() then return end

    local monster = getMonster()
    if monster and monster.Character and monster.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = monster.Character.HumanoidRootPart
        -- Tenta simular mira direta (não funciona em todos os jogos, depende do sistema de tiro)
        workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, hrp.Position)
        -- Simula clique (caso use Raycast ou client-side fire)
        mouse1click()
    end
end)
