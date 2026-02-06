--// DRAGON BLOX - AUTO FARM + MISSIONS (UI)
--// USAR EM JOGO PRÓPRIO

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UIS = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")

-- CONFIG
_G.AutoFarm = false
_G.AutoMission = false
_G.SelectedEnemy = nil

-- REMOTES
local Remotes = ReplicatedStorage:WaitForChild("Remotes")
local Attack = Remotes:WaitForChild("Attack")
local AcceptMission = Remotes:WaitForChild("AcceptMission")
local CompleteMission = Remotes:WaitForChild("CompleteMission")

------------------------------------------------
-- UI
------------------------------------------------
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.Name = "DragonBloxUI"

local main = Instance.new("Frame", gui)
main.Size = UDim2.fromOffset(260, 300)
main.Position = UDim2.fromScale(0.05, 0.3)
main.BackgroundColor3 = Color3.fromRGB(20,20,20)
main.Active = true
main.Draggable = true

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1,0,0,40)
title.Text = "🐉 DRAGON BLOX"
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 20

-- BOTÃO PADRÃO
local function createButton(text, y)
	local btn = Instance.new("TextButton", main)
	btn.Size = UDim2.new(0.9,0,0,40)
	btn.Position = UDim2.new(0.05,0,0,y)
	btn.Text = text
	btn.BackgroundColor3 = Color3.fromRGB(40,40,40)
	btn.TextColor3 = Color3.new(1,1,1)
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 14
	return btn
end

local farmBtn = createButton("Auto Farm: OFF", 60)
local missionBtn = createButton("Auto Missões: OFF", 110)

-- LISTA DE INIMIGOS
local enemyLabel = Instance.new("TextLabel", main)
enemyLabel.Size = UDim2.new(0.9,0,0,30)
enemyLabel.Position = UDim2.new(0.05,0,0,170)
enemyLabel.Text = "Inimigo:"
enemyLabel.TextColor3 = Color3.new(1,1,1)
enemyLabel.BackgroundTransparency = 1
enemyLabel.Font = Enum.Font.Gotham
enemyLabel.TextSize = 14

local enemyBtn = createButton("Selecionar Inimigo", 200)

------------------------------------------------
-- FUNÇÕES
------------------------------------------------
local function getEnemy()
	for _, mob in pairs(workspace.Enemies:GetChildren()) do
		if mob.Name == _G.SelectedEnemy and mob:FindFirstChild("Humanoid") then
			if mob.Humanoid.Health > 0 then
				return mob
			end
		end
	end
end

------------------------------------------------
-- BOTÕES
------------------------------------------------
farmBtn.MouseButton1Click:Connect(function()
	_G.AutoFarm = not _G.AutoFarm
	farmBtn.Text = _G.AutoFarm and "Auto Farm: ON" or "Auto Farm: OFF"
end)

missionBtn.MouseButton1Click:Connect(function()
	_G.AutoMission = not _G.AutoMission
	missionBtn.Text = _G.AutoMission and "Auto Missões: ON" or "Auto Missões: OFF"
end)

enemyBtn.MouseButton1Click:Connect(function()
	for _, mob in pairs(workspace.Enemies:GetChildren()) do
		_G.SelectedEnemy = mob.Name
		enemyBtn.Text = "Inimigo: "..mob.Name
		break
	end
end)

------------------------------------------------
-- LOOPS
------------------------------------------------
task.spawn(function()
	while task.wait(0.3) do
		if _G.AutoFarm and _G.SelectedEnemy then
			local enemy = getEnemy()
			if enemy then
				hrp.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0,0,3)
				Attack:FireServer(enemy)
			end
		end
	end
end)

task.spawn(function()
	while task.wait(1) do
		if _G.AutoMission then
			AcceptMission:FireServer()
			task.wait(0.5)
			CompleteMission:FireServer()
		end
	end
end)
