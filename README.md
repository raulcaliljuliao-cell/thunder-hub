-- LocalScript dentro de StarterPlayerScripts

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

local HEIGHT = 50 -- altura do teleporte
local isUp = false

-- Atualiza referência quando respawnar
player.CharacterAdded:Connect(function(char)
	character = char
	humanoidRootPart = char:WaitForChild("HumanoidRootPart")
end)

-- Cria GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 150, 0, 60)
button.Position = UDim2.new(0.8, 0, 0.8, 0) -- canto inferior direito
button.Text = "Subir/Descer"
button.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
button.TextSize = 20
button.Parent = screenGui

-- Função de teleporte
local function teleport()
	if humanoidRootPart then
		if not isUp then
			-- Sobe
			humanoidRootPart.CFrame = humanoidRootPart.CFrame + Vector3.new(0, HEIGHT, 0)
			isUp = true
		else
			-- Desce (Raycast para encontrar o chão)
			local ray = Ray.new(humanoidRootPart.Position, Vector3.new(0, -500, 0))
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
