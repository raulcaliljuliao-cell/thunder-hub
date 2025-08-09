-- LocalScript dentro de StarterPlayerScripts

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

local HEIGHT = 750 -- 150 personagens clássicos (5 studs cada)
local isUp = false

-- Atualiza referência quando respawnar
player.CharacterAdded:Connect(function(char)
	character = char
	humanoidRootPart = char:WaitForChild("HumanoidRootPart")
	humanoid = char:WaitForChild("Humanoid")
end)

-- Cria GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 180, 0, 60)
button.Position = UDim2.new(0.8, 0, 0.8, 0)
button.Text = "Subir/Descer"
button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
button.TextSize = 22
button.Parent = screenGui

-- Função de teleporte
local function teleport()
	if humanoidRootPart then
		-- Evita morte por queda
		humanoid:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
		humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
		humanoid:ChangeState(Enum.HumanoidStateType.Physics)
		
		if not isUp then
			-- Sobe
			humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(0, HEIGHT, 0)
			isUp = true
		else
			-- Desce para o chão
			local ray = Ray.new(humanoidRootPart.Position, Vector3.new(0, -1000, 0))
			local hit, pos = workspace:FindPartOnRay(ray, character)
			if hit then
				humanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0, 3, 0))
			else
				humanoidRootPart.CFrame = humanoidRootPart.CFrame - Vector3.new(0, HEIGHT, 0)
			end
			isUp = false
		end
	end
end

-- Detecta clique no botão
button.MouseButton1Click:Connect(teleport)
