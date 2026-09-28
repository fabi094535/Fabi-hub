--// JOGO DE ANIMAIS - SCRIPT ÚNICO
--// Coloque este Script em ServerScriptService

local Players = game:GetService("Players")
local ServerStorage = game:GetService("ServerStorage")

local MIN_SPEED = 8
local MAX_SPEED = 100

-- Pasta dos animais
local animalsFolder = ServerStorage:FindFirstChild("Animals")

if not animalsFolder then
	animalsFolder = Instance.new("Folder")
	animalsFolder.Name = "Animals"
	animalsFolder.Parent = ServerStorage

	warn("Criei ServerStorage > Animals. Coloque seus modelos de animais nessa pasta.")
end

-- Animais disponíveis
local ANIMALS = {
	"Dog",
	"Cat",
	"Rabbit",
	"Fox",
	"Bear"
}

--------------------------------------------------
-- FUNÇÃO PARA CRIAR A INTERFACE
--------------------------------------------------

local function createGui(player)

	local playerGui = player:WaitForChild("PlayerGui")

	local oldGui = playerGui:FindFirstChild("AnimalGame")
	if oldGui then
		oldGui:Destroy()
	end

	local gui = Instance.new("ScreenGui")
	gui.Name = "AnimalGame"
	gui.ResetOnSpawn = false
	gui.Parent = playerGui

	--------------------------------------------------
	-- JANELA
	--------------------------------------------------

	local frame = Instance.new("Frame")
	frame.Name = "Main"
	frame.Size = UDim2.new(0, 320, 0, 420)
	frame.Position = UDim2.new(0.5, -160, 0.5, -210)
	frame.BackgroundColor3 = Color3.fromRGB(28, 28, 35)
	frame.BorderSizePixel = 0
	frame.Parent = gui

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 14)
	corner.Parent = frame

	--------------------------------------------------
	-- TÍTULO
	--------------------------------------------------

	local title = Instance.new("TextLabel")
	title.Size = UDim2.new(1, -60, 0, 50)
	title.Position = UDim2.new(0, 15, 0, 0)
	title.BackgroundTransparency = 1
	title.Text = "🐾 JOGO DE ANIMAIS"
	title.TextColor3 = Color3.fromRGB(255, 255, 255)
	title.TextSize = 20
	title.Font = Enum.Font.GothamBold
	title.TextXAlignment = Enum.TextXAlignment.Left
	title.Parent = frame

	--------------------------------------------------
	-- BOTÃO MINIMIZAR
	--------------------------------------------------

	local minimize = Instance.new("TextButton")
	minimize.Size = UDim2.new(0, 40, 0, 35)
	minimize.Position = UDim2.new(1, -50, 0, 8)
	minimize.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
	minimize.Text = "-"
	minimize.TextColor3 = Color3.fromRGB(255, 255, 255)
	minimize.TextSize = 24
	minimize.Font = Enum.Font.GothamBold
	minimize.Parent = frame

	local minCorner = Instance.new("UICorner")
	minCorner.CornerRadius = UDim.new(0, 8)
	minCorner.Parent = minimize

	--------------------------------------------------
	-- VELOCIDADE
	--------------------------------------------------

	local speedLabel = Instance.new("TextLabel")
	speedLabel.Size = UDim2.new(1, -30, 0, 30)
	speedLabel.Position = UDim2.new(0, 15, 0, 65)
	speedLabel.BackgroundTransparency = 1
	speedLabel.Text = "⚡ Velocidade"
	speedLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
	speedLabel.TextSize = 17
	speedLabel.Font = Enum.Font.GothamBold
	speedLabel.TextXAlignment = Enum.TextXAlignment.Left
	speedLabel.Parent = frame

	local speedBox = Instance.new("TextBox")
	speedBox.Size = UDim2.new(1, -30, 0, 40)
	speedBox.Position = UDim2.new(0, 15, 0, 100)
	speedBox.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
	speedBox.Text = "16"
	speedBox.PlaceholderText = "Digite a velocidade"
	speedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
	speedBox.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
	speedBox.TextSize = 17
	speedBox.Font = Enum.Font.Gotham
	speedBox.ClearTextOnFocus = false
	speedBox.Parent = frame

	local speedCorner = Instance.new("UICorner")
	speedCorner.CornerRadius = UDim.new(0, 8)
	speedCorner.Parent = speedBox

	local apply = Instance.new("TextButton")
	apply.Size = UDim2.new(1, -30, 0, 40)
	apply.Position = UDim2.new(0, 15, 0, 150)
	apply.BackgroundColor3 = Color3.fromRGB(55, 175, 90)
	apply.Text = "Aplicar velocidade"
	apply.TextColor3 = Color3.fromRGB(255, 255, 255)
	apply.TextSize = 16
	apply.Font = Enum.Font.GothamBold
	apply.Parent = frame

	local applyCorner = Instance.new("UICorner")
	applyCorner.CornerRadius = UDim.new(0, 8)
	applyCorner.Parent = apply

	--------------------------------------------------
	-- FUNÇÃO DE VELOCIDADE
	--------------------------------------------------

	apply.MouseButton1Click:Connect(function()

		local speed = tonumber(speedBox.Text)

		if not speed then
			speedBox.Text = "Digite um número"
			return
		end

		speed = math.clamp(speed, MIN_SPEED, MAX_SPEED)

		local character = player.Character

		if character then

			local humanoid = character:FindFirstChildOfClass("Humanoid")

			if humanoid then
				humanoid.WalkSpeed = speed
			end

		end

	end)

	--------------------------------------------------
	-- ANIMAIS
	--------------------------------------------------

	local animalTitle = Instance.new("TextLabel")
	animalTitle.Size = UDim2.new(1, -30, 0, 30)
	animalTitle.Position = UDim2.new(0, 15, 0, 205)
	animalTitle.BackgroundTransparency = 1
	animalTitle.Text = "🐾 Spawnar animal"
	animalTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
	animalTitle.TextSize = 17
	animalTitle.Font = Enum.Font.GothamBold
	animalTitle.TextXAlignment = Enum.TextXAlignment.Left
	animalTitle.Parent = frame

	--------------------------------------------------
	-- CRIA BOTÕES DOS ANIMAIS
	--------------------------------------------------

	for index, animalName in ipairs(ANIMALS) do

		local button = Instance.new("TextButton")

		local column = (index - 1) % 2
		local row = math.floor((index - 1) / 2)

		button.Size = UDim2.new(0.45, 0, 0, 40)

		button.Position = UDim2.new(
			column == 0 and 0.05 or 0.50,
			0,
			0,
			245 + row * 48
		)

		button.BackgroundColor3 = Color3.fromRGB(65, 65, 80)
		button.Text = animalName
		button.TextColor3 = Color3.fromRGB(255, 255, 255)
		button.TextSize = 15
		button.Font = Enum.Font.GothamBold
		button.Parent = frame

		local buttonCorner = Instance.new("UICorner")
		buttonCorner.CornerRadius = UDim.new(0, 8)
		buttonCorner.Parent = button

		--------------------------------------------------
		-- SPAWN
		--------------------------------------------------

		button.MouseButton1Click:Connect(function()

			local animal = animalsFolder:FindFirstChild(animalName)

			if not animal then
				warn("Animal não encontrado: " .. animalName)
				return
			end

			local character = player.Character

			if not character then
				return
			end

			local root = character:FindFirstChild("HumanoidRootPart")

			if not root then
				return
			end

			local clone = animal:Clone()

			local spawnPosition =
				root.Position +
				root.CFrame.LookVector * 8

			if clone:IsA("Model") then

				clone:PivotTo(
					CFrame.new(spawnPosition)
				)

			elseif clone:IsA("BasePart") then

				clone.Position = spawnPosition

			end

			clone.Parent = workspace

		end)

	end

	--------------------------------------------------
	-- MINIMIZAR / RESTAURAR
	--------------------------------------------------

	local minimized = false

	minimize.MouseButton1Click:Connect(function()

		minimized = not minimized

		if minimized then

			frame.Size = UDim2.new(0, 320, 0, 55)

			minimize.Text = "+"

			for _, object in ipairs(frame:GetChildren()) do

				if object ~= title
					and object ~= minimize
					and not object:IsA("UICorner") then

					object.Visible = false

				end

			end

		else

			frame.Size = UDim2.new(0, 320, 0, 420)

			minimize.Text = "-"

			for _, object in ipairs(frame:GetChildren()) do

				if object ~= title
					and object ~= minimize
					and not object:IsA("UICorner") then

					object.Visible = true

				end

			end

		end

	end)

end

--------------------------------------------------
-- QUANDO O JOGADOR ENTRA
--------------------------------------------------

Players.PlayerAdded:Connect(function(player)

	player.CharacterAdded:Connect(function(character)

		local humanoid = character:WaitForChild("Humanoid")

		humanoid.WalkSpeed = 16

		task.wait(1)

		createGui(player)

	end)

end)

--------------------------------------------------
-- JOGADORES QUE JÁ ESTÃO NO SERVIDOR
--------------------------------------------------

for _, player in ipairs(Players:GetPlayers()) do

	task.spawn(function()

		if player.Character then
			createGui(player)
		end

		player.CharacterAdded:Connect(function(character)

			character:WaitForChild("Humanoid").WalkSpeed = 16

			task.wait(1)

			createGui(player)

		end)

	end)

end
