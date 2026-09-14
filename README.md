local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local Remote = ReplicatedStorage:WaitForChild("HubRemote")

-- COLOQUE AQUI OS USERIDs DOS SEUS TESTERS
local AUTHORIZED = {
	[123456789] = true,
	[987654321] = true,
}

local function isAuthorized(player)
	return AUTHORIZED[player.UserId] == true
end

local function getCharacter(player)
	return player.Character
end

Remote.OnServerEvent:Connect(function(player, action, data)
	if not isAuthorized(player) then
		return
	end

	local character = getCharacter(player)
	if not character then
		return
	end

	local humanoid = character:FindFirstChildOfClass("Humanoid")
	local root = character:FindFirstChild("HumanoidRootPart")

	if action == "Speed" and humanoid then
		local value = tonumber(data)

		if value then
			humanoid.WalkSpeed = math.clamp(value, 8, 100)
		end

	elseif action == "Jump" and humanoid then
		local value = tonumber(data)

		if value then
			humanoid.JumpPower = math.clamp(value, 25, 150)
		end

	elseif action == "Heal" and humanoid then
		humanoid.Health = humanoid.MaxHealth

	elseif action == "Teleport" and root then
		local positions = {
			Starter = CFrame.new(0, 10, 0),
			Island1 = CFrame.new(500, 20, 500),
			Island2 = CFrame.new(-500, 20, 800),
			Island3 = CFrame.new(1000, 30, -500),
		}

		local destination = positions[data]

		if destination then
			root.CFrame = destination
		end

	elseif action == "GiveFruit" then
		-- Aqui você conecta ao sistema de frutas do seu jogo.
		print(player.Name .. " recebeu a fruta: " .. tostring(data))

	elseif action == "ResetCharacter" then
		player:LoadCharacter()
	end
end)local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local Remote = ReplicatedStorage:WaitForChild("HubRemote")

local gui = Instance.new("ScreenGui")
gui.Name = "TesterHub"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local main = Instance.new("Frame")
main.Size = UDim2.fromOffset(620, 390)
main.Position = UDim2.fromScale(0.5, 0.5)
main.AnchorPoint = Vector2.new(0.5, 0.5)
main.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
main.BorderSizePixel = 0
main.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = main

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 55)
title.BackgroundTransparency = 1
title.Text = "TESTER HUB"
title.TextColor3 = Color3.new(1, 1, 1)
title.TextSize = 25
title.Font = Enum.Font.GothamBold
title.Parent = main

local sidebar = Instance.new("Frame")
sidebar.Size = UDim2.new(0, 145, 1, -55)
sidebar.Position = UDim2.fromOffset(0, 55)
sidebar.BackgroundColor3 = Color3.fromRGB(15, 15, 19)
sidebar.BorderSizePixel = 0
sidebar.Parent = main

local content = Instance.new("Frame")
content.Size = UDim2.new(1, -145, 1, -55)
content.Position = UDim2.fromOffset(145, 55)
content.BackgroundTransparency = 1
content.Parent = main

local function createButton(parent, text, position, callback)
	local button = Instance.new("TextButton")

	button.Size = UDim2.fromOffset(125, 40)
	button.Position = position
	button.BackgroundColor3 = Color3.fromRGB(35, 35, 43)
	button.TextColor3 = Color3.new(1, 1, 1)
	button.Text = text
	button.TextSize = 14
	button.Font = Enum.Font.GothamMedium
	button.BorderSizePixel = 0
	button.Parent = parent

	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0, 7)
	c.Parent = button

	button.MouseButton1Click:Connect(callback)

	return button
end

local function clearContent()
	for _, object in ipairs(content:GetChildren()) do
		object:Destroy()
	end
end

local function label(text, position)
	local l = Instance.new("TextLabel")

	l.Size = UDim2.fromOffset(400, 35)
	l.Position = position
	l.BackgroundTransparency = 1
	l.Text = text
	l.TextColor3 = Color3.new(1, 1, 1)
	l.TextSize = 17
	l.Font = Enum.Font.GothamMedium
	l.TextXAlignment = Enum.TextXAlignment.Left
	l.Parent = content

	return l
end

local function makeAction(text, y, callback)
	return createButton(content, text, UDim2.fromOffset(25, y), callback)
end

local function showMain()
	clearContent()

	label("Player", UDim2.fromOffset(25, 20))

	makeAction("Heal", 65, function()
		Remote:FireServer("Heal")
	end)

	makeAction("Reset Character", 115, function()
		Remote:FireServer("ResetCharacter")
	end)
end

local function showMovement()
	clearContent()

	label("Movement Tester", UDim2.fromOffset(25, 20))

	makeAction("Speed 30", 65, function()
		Remote:FireServer("Speed", 30)
	end)

	makeAction("Speed 60", 115, function()
		Remote:FireServer("Speed", 60)
	end)

	makeAction("Jump 75", 165, function()
		Remote:FireServer("Jump", 75)
	end)

	makeAction("Jump 120", 215, function()
		Remote:FireServer("Jump", 120)
	end)
end

local function showTeleport()
	clearContent()

	label("Teleport", UDim2.fromOffset(25, 20))

	makeAction("Starter Island", 65, function()
		Remote:FireServer("Teleport", "Starter")
	end)

	makeAction("Island 1", 115, function()
		Remote:FireServer("Teleport", "Island1")
	end)

	makeAction("Island 2", 165, function()
		Remote:FireServer("Teleport", "Island2")
	end)

	makeAction("Island 3", 215, function()
		Remote:FireServer("Teleport", "Island3")
	end)
end

local function showFruits()
	clearContent()

	label("Fruit Tester", UDim2.fromOffset(25, 20))

	makeAction("Flame", 65, function()
		Remote:FireServer("GiveFruit", "Flame")
	end)

	makeAction("Ice", 115, function()
		Remote:FireServer("GiveFruit", "Ice")
	end)

	makeAction("Light", 165, function()
		Remote:FireServer("GiveFruit", "Light")
	end)

	makeAction("Dragon", 215, function()
		Remote:FireServer("GiveFruit", "Dragon")
	end)
end

createButton(sidebar, "Main", UDim2.fromOffset(10, 15), showMain)
createButton(sidebar, "Movement", UDim2.fromOffset(10, 65), showMovement)
createButton(sidebar, "Teleport", UDim2.fromOffset(10, 115), showTeleport)
createButton(sidebar, "Fruits", UDim2.fromOffset(10, 165), showFruits)

showMain()

-- Abrir/fechar com RightShift
UserInputService.InputBegan:Connect(function(input, processed)
	if processed then
		return
	end

	if input.KeyCode == Enum.KeyCode.RightShift then
		main.Visible = not main.Visible
	end
end)local AUTHORIZED = {
	[123456789] = true,
	[987654321] = true,
}
