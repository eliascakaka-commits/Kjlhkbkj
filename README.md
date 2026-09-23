--// TEST HUB - LUAU
--// Coloque como LocalScript em:
--// StarterPlayer > StarterPlayerScripts

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

local Character
local Humanoid

local function updateCharacter()
	Character = Player.Character or Player.CharacterAdded:Wait()
	Humanoid = Character:WaitForChild("Humanoid")
end

updateCharacter()

Player.CharacterAdded:Connect(function()
	task.wait(0.2)
	updateCharacter()
end)

--==================================================
-- CONFIGURAÇÃO DOS TESTES
--==================================================

local NORMAL_SPEED = 16
local TEST_SPEED = 80

local speedEnabled = false
local godEnabled = false
local espEnabled = false
local hitboxEnabled = false

--==================================================
-- GUI
--==================================================

local Gui = Instance.new("ScreenGui")
Gui.Name = "LuauTestHub"
Gui.ResetOnSpawn = false
Gui.Parent = PlayerGui

local Hub = Instance.new("Frame")
Hub.Size = UDim2.fromOffset(270, 330)
Hub.Position = UDim2.new(0.5, -135, 0.5, -165)
Hub.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Hub.BorderSizePixel = 0
Hub.Parent = Gui

local HubCorner = Instance.new("UICorner")
HubCorner.CornerRadius = UDim.new(0, 12)
HubCorner.Parent = Hub

--==================================================
-- TÍTULO
--==================================================

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -50, 0, 45)
Title.Position = UDim2.fromOffset(10, 5)
Title.BackgroundTransparency = 1
Title.Text = "TEST HUB"
Title.TextColor3 = Color3.new(1, 1, 1)
Title.TextSize = 22
Title.Font = Enum.Font.GothamBold
Title.Parent = Hub

--==================================================
-- BOTÃO FECHAR
--==================================================

local Close = Instance.new("TextButton")
Close.Size = UDim2.fromOffset(35, 35)
Close.Position = UDim2.new(1, -42, 0, 8)
Close.Text = "X"
Close.TextSize = 18
Close.TextColor3 = Color3.new(1, 1, 1)
Close.BackgroundColor3 = Color3.fromRGB(170, 45, 45)
Close.BorderSizePixel = 0
Close.Parent = Hub

Instance.new("UICorner", Close).CornerRadius = UDim.new(0, 8)

--==================================================
-- BOTÃO ABRIR
--==================================================

local Open = Instance.new("TextButton")
Open.Size = UDim2.fromOffset(130, 40)
Open.Position = UDim2.fromOffset(15, 200)
Open.Text = "ABRIR HUB"
Open.TextSize = 16
Open.TextColor3 = Color3.new(1, 1, 1)
Open.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Open.BorderSizePixel = 0
Open.Visible = false
Open.Parent = Gui

Instance.new("UICorner", Open).CornerRadius = UDim.new(0, 8)

--==================================================
-- CRIADOR DE BOTÕES
--==================================================

local function createButton(text, y)
	local Button = Instance.new("TextButton")

	Button.Size = UDim2.new(1, -30, 0, 48)
	Button.Position = UDim2.fromOffset(15, y)

	Button.Text = text
	Button.TextSize = 17
	Button.Font = Enum.Font.GothamBold

	Button.TextColor3 = Color3.new(1, 1, 1)
	Button.BackgroundColor3 = Color3.fromRGB(45, 45, 45)

	Button.BorderSizePixel = 0
	Button.Parent = Hub

	Instance.new("UICorner", Button).CornerRadius = UDim.new(0, 8)

	return Button
end

local ESPButton = createButton("ESP: OFF", 60)
local HitboxButton = createButton("HITBOX: OFF", 115)
local SpeedButton = createButton("SPEED: OFF", 170)
local GodButton = createButton("GODMODE: OFF", 225)

--==================================================
-- ESP DE TESTE
--==================================================

local function updateESP()

	for _, Target in ipairs(Players:GetPlayers()) do

		if Target ~= Player and Target.Character then

			local OldESP = Target.Character:FindFirstChild("TestESP")

			if espEnabled then

				if not OldESP then

					local Highlight = Instance.new("Highlight")
					Highlight.Name = "TestESP"

					Highlight.FillTransparency = 0.5
					Highlight.OutlineTransparency = 0

					Highlight.Parent = Target.Character

				end

			else

				if OldESP then
					OldESP:Destroy()
				end

			end

		end

	end

end

--==================================================
-- HITBOX DE TESTE
--==================================================

local function updateHitbox()

	if not Character then
		return
	end

	local Root = Character:FindFirstChild("HumanoidRootPart")

	if not Root then
		return
	end

	local OldHitbox = Character:FindFirstChild("TestHitbox")

	if hitboxEnabled then

		if not OldHitbox then

			local Hitbox = Instance.new("Part")

			Hitbox.Name = "TestHitbox"
			Hitbox.Size = Vector3.new(7, 8, 5)

			Hitbox.Transparency = 0.65
			Hitbox.CanCollide = false
			Hitbox.CanTouch = false
			Hitbox.CanQuery = true
			Hitbox.Massless = true

			Hitbox.CFrame = Root.CFrame

			Hitbox.Parent = Character

			local Weld = Instance.new("WeldConstraint")

			Weld.Part0 = Root
			Weld.Part1 = Hitbox

			Weld.Parent = Hitbox

		end

	else

		if OldHitbox then
			OldHitbox:Destroy()
		end

	end

end

--==================================================
-- SPEED
--==================================================

SpeedButton.MouseButton1Click:Connect(function()

	speedEnabled = not speedEnabled

	if speedEnabled then

		SpeedButton.Text = "SPEED: ON"

		if Humanoid then
			Humanoid.WalkSpeed = TEST_SPEED
		end

	else

		SpeedButton.Text = "SPEED: OFF"

		if Humanoid then
			Humanoid.WalkSpeed = NORMAL_SPEED
		end

	end

end)

--==================================================
-- GODMODE
--==================================================

GodButton.MouseButton1Click:Connect(function()

	godEnabled = not godEnabled

	if godEnabled then

		GodButton.Text = "GODMODE: ON"

		if Humanoid then
			Humanoid.MaxHealth = 999999
			Humanoid.Health = 999999
		end

	else

		GodButton.Text = "GODMODE: OFF"

		if Humanoid then
			Humanoid.MaxHealth = 100
			Humanoid.Health = math.min(Humanoid.Health, 100)
		end

	end

end)

--==================================================
-- ESP
--==================================================

ESPButton.MouseButton1Click:Connect(function()

	espEnabled = not espEnabled

	if espEnabled then
		ESPButton.Text = "ESP: ON"
	else
		ESPButton.Text = "ESP: OFF"
	end

	updateESP()

end)

--==================================================
-- HITBOX
--==================================================

HitboxButton.MouseButton1Click:Connect(function()

	hitboxEnabled = not hitboxEnabled

	if hitboxEnabled then
		HitboxButton.Text = "HITBOX: ON"
	else
		HitboxButton.Text = "HITBOX: OFF"
	end

	updateHitbox()

end)

--==================================================
-- FECHAR / ABRIR HUB
--==================================================

Close.MouseButton1Click:Connect(function()

	Hub.Visible = false
	Open.Visible = true

end)

Open.MouseButton1Click:Connect(function()

	Hub.Visible = true
	Open.Visible = false

end)

--==================================================
-- HUB ARRASTÁVEL
--==================================================

local dragging = false
local dragStart
local startPosition

Title.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		dragging = true
		dragStart = input.Position
		startPosition = Hub.Position

	end

end)

UserInputService.InputChanged:Connect(function(input)

	if not dragging then
		return
	end

	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then

		local Delta = input.Position - dragStart

		Hub.Position = UDim2.new(
			startPosition.X.Scale,
			startPosition.X.Offset + Delta.X,
			startPosition.Y.Scale,
			startPosition.Y.Offset + Delta.Y
		)

	end

end)

UserInputService.InputEnded:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		dragging = false

	end

end)

--==================================================
-- ATUALIZAÇÕES
--==================================================

Players.PlayerAdded:Connect(function()

	task.wait(1)

	if espEnabled then
		updateESP()
	end

end)

while task.wait(0.25) do

	if speedEnabled and Humanoid then
		Humanoid.WalkSpeed = TEST_SPEED
	end

	if godEnabled and Humanoid then
		Humanoid.Health = 999999
	end

	if hitboxEnabled then
		updateHitbox()
	end

	if espEnabled then
		updateESP()
	end

end
