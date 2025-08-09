-- LocalScript dentro de StarterPlayerScripts

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

local HEIGHT = 500 -- 100 personagens clássicos (5 studs cada)
local TWEEN_TIME = 3 -- tempo para subir/descer
local isUp = false
local platform -- referência à plataforma invisível

-- Atualiza referências quando respawnar
player.CharacterAdded:Connect(function(char)
	character = char
	humanoidRootPart = char:WaitForChild("HumanoidRootPart")
end)

-- Função para criar a plataforma invisível
local function createPlatform()
	platform = Instance.new("Part")
	platform.Size = Vector3.new(6, 1, 6)
	platform.Anchored = true
	platform.CanCollide = true
	platform.Transparency = 1 -- invisível
	platform.Parent = workspace
end

-- Função para mover a plataforma
local function movePlatform(targetPos)
	local tween = TweenService:Create(
		platform,
		TweenInfo.new(TWEEN_TIME, Enum.EasingStyle.Linear, Enum.EasingDirection.Out),
		{Position = targetPos}
	)
	tween:Play()
end

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

-- Função principal
local function platformElevator()
	if not platform then
		createPlatform()
	end
	
	-- Sempre posiciona a plataforma debaixo do jogador
	platform.Position = Vector3.new(humanoidRootPart.Position.X, humanoidRootPart.Position.Y - 3, humanoidRootPart.Position.Z)
	
	if not isUp then
		-- Subir
		local targetPos = platform.Position + Vector3.new(0, HEIGHT, 0)
		movePlatform(targetPos)
		isUp = true
	else
		-- Descer (procura o chão)
		local ray = workspace:Raycast(humanoidRootPart.Position, Vector3.new(0, -1000, 0))
		local groundY
		if ray then
			groundY = ray.Position.Y + 3
		else
			groundY = platform.Position.Y - HEIGHT
		end
		local targetPos = Vector3.new(platform.Position.X, groundY, platform.Position.Z)
		movePlatform(targetPos)
		isUp = false
	end
end

-- Mantém o jogador sobre a plataforma
game:GetService("RunService").Heartbeat:Connect(function()
	if platform and humanoidRootPart then
		local newPos = Vector3.new(platform.Position.X, platform.Position.Y + 3, platform.Position.Z)
		humanoidRootPart.CFrame = CFrame.new(newPos, humanoidRootPart.CFrame.LookVector + newPos)
	end
end)

-- Detecta clique no botão
button.MouseButton1Click:Connect(platformElevator)
