-- Services
local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Stats = game:GetService("Stats")
local TeleportService = game:GetService("TeleportService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
player.CharacterAdded:Connect(function(char) character = char end)

-- Version Tracker
local SCRIPT_VERSION = "V-20.11"

-- Connections & Storage
local activeConnections = {}
local activeHighlights = {Players = {}, NPCs = {}, Ores = {}}
local espRadii = {Players = 500, NPCs = 500, Ores = 300}
local savedTeleportAreas = {}

local defaultLightingSettings = {
	FogEnd = Lighting.FogEnd,
	FogStart = Lighting.FogStart,
	Ambient = Lighting.Ambient,
	OutdoorAmbient = Lighting.OutdoorAmbient,
	Brightness = Lighting.Brightness,
	Atmosphere = workspace:FindFirstChildOfClass("Atmosphere")
}

-- ScreenGui Setup
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "WScriptsHub"
screenGui.ResetOnSpawn = false

if gethui then
	screenGui.Parent = gethui()
elseif syn and syn.protect_gui then
	syn.protect_gui(screenGui)
	screenGui.Parent = game:GetService("CoreGui")
else
	screenGui.Parent = player:WaitForChild("PlayerGui")
end

--------------------------------------------------------------------------------
-- Rounded Font Styling
--------------------------------------------------------------------------------
local currentFont = Enum.Font.Gotham

--------------------------------------------------------------------------------
-- Expanded Theme Palettes (8 Modern Themes)
--------------------------------------------------------------------------------
local ThemePalettes = {
	{
		Name = "Modern Obsidian (Default)",
		MainBg = Color3.fromRGB(18, 18, 22),
		HeaderBg = Color3.fromRGB(12, 12, 15),
		SectionBg = Color3.fromRGB(24, 24, 30),
		ButtonBg = Color3.fromRGB(35, 35, 45),
		ButtonActiveBg = Color3.fromRGB(0, 122, 255),
		TextColour = Color3.fromRGB(245, 245, 250),
		SubTextColour = Color3.fromRGB(150, 150, 165),
		BorderColour = Color3.fromRGB(45, 45, 60),
		SliderBg = Color3.fromRGB(15, 15, 20),
		SliderFill = Color3.fromRGB(0, 122, 255)
	},
	{
		Name = "Cyberpunk Neon",
		MainBg = Color3.fromRGB(20, 18, 28),
		HeaderBg = Color3.fromRGB(14, 12, 20),
		SectionBg = Color3.fromRGB(28, 24, 38),
		ButtonBg = Color3.fromRGB(42, 35, 55),
		ButtonActiveBg = Color3.fromRGB(255, 0, 128),
		TextColour = Color3.fromRGB(255, 240, 250),
		SubTextColour = Color3.fromRGB(170, 150, 185),
		BorderColour = Color3.fromRGB(70, 45, 95),
		SliderBg = Color3.fromRGB(14, 12, 20),
		SliderFill = Color3.fromRGB(255, 0, 128)
	},
	{
		Name = "Nordic Slate",
		MainBg = Color3.fromRGB(35, 42, 52),
		HeaderBg = Color3.fromRGB(25, 31, 39),
		SectionBg = Color3.fromRGB(43, 52, 65),
		ButtonBg = Color3.fromRGB(56, 68, 85),
		ButtonActiveBg = Color3.fromRGB(96, 165, 250),
		TextColour = Color3.fromRGB(240, 244, 248),
		SubTextColour = Color3.fromRGB(160, 175, 195),
		BorderColour = Color3.fromRGB(68, 82, 103),
		SliderBg = Color3.fromRGB(25, 31, 39),
		SliderFill = Color3.fromRGB(96, 165, 250)
	},
	{
		Name = "Emerald Clean",
		MainBg = Color3.fromRGB(18, 24, 20),
		HeaderBg = Color3.fromRGB(12, 16, 14),
		SectionBg = Color3.fromRGB(24, 33, 28),
		ButtonBg = Color3.fromRGB(35, 48, 40),
		ButtonActiveBg = Color3.fromRGB(16, 185, 129),
		TextColour = Color3.fromRGB(240, 255, 245),
		SubTextColour = Color3.fromRGB(150, 180, 165),
		BorderColour = Color3.fromRGB(40, 65, 52),
		SliderBg = Color3.fromRGB(12, 16, 14),
		SliderFill = Color3.fromRGB(16, 185, 129)
	},
	{
		Name = "Sunset Orange",
		MainBg = Color3.fromRGB(28, 20, 18),
		HeaderBg = Color3.fromRGB(20, 14, 12),
		SectionBg = Color3.fromRGB(38, 27, 24),
		ButtonBg = Color3.fromRGB(55, 40, 35),
		ButtonActiveBg = Color3.fromRGB(249, 115, 22),
		TextColour = Color3.fromRGB(255, 245, 240),
		SubTextColour = Color3.fromRGB(190, 160, 150),
		BorderColour = Color3.fromRGB(75, 50, 45),
		SliderBg = Color3.fromRGB(20, 14, 12),
		SliderFill = Color3.fromRGB(249, 115, 22)
	},
	-- wallubo made this
	{
		Name = "Royal Purple",
		MainBg = Color3.fromRGB(24, 18, 32),
		HeaderBg = Color3.fromRGB(16, 12, 22),
		SectionBg = Color3.fromRGB(34, 25, 46),
		ButtonBg = Color3.fromRGB(48, 36, 65),
		ButtonActiveBg = Color3.fromRGB(168, 85, 247),
		TextColour = Color3.fromRGB(250, 240, 255),
		SubTextColour = Color3.fromRGB(180, 150, 200),
		BorderColour = Color3.fromRGB(65, 45, 90),
		SliderBg = Color3.fromRGB(16, 12, 22),
		SliderFill = Color3.fromRGB(168, 85, 247)
	},
	{
		Name = "Crimson Dark",
		MainBg = Color3.fromRGB(26, 18, 18),
		HeaderBg = Color3.fromRGB(18, 12, 12),
		SectionBg = Color3.fromRGB(36, 24, 24),
		ButtonBg = Color3.fromRGB(52, 35, 35),
		ButtonActiveBg = Color3.fromRGB(239, 68, 68),
		TextColour = Color3.fromRGB(255, 240, 240),
		SubTextColour = Color3.fromRGB(190, 150, 150),
		BorderColour = Color3.fromRGB(70, 45, 45),
		SliderBg = Color3.fromRGB(18, 12, 12),
		SliderFill = Color3.fromRGB(239, 68, 68)
	},
	{
		Name = "Clean Light",
		MainBg = Color3.fromRGB(240, 242, 245),
		HeaderBg = Color3.fromRGB(225, 228, 235),
		SectionBg = Color3.fromRGB(255, 255, 255),
		ButtonBg = Color3.fromRGB(215, 220, 230),
		ButtonActiveBg = Color3.fromRGB(37, 99, 235),
		TextColour = Color3.fromRGB(30, 35, 45),
		SubTextColour = Color3.fromRGB(100, 110, 125),
		BorderColour = Color3.fromRGB(200, 205, 215),
		SliderBg = Color3.fromRGB(215, 220, 230),
		SliderFill = Color3.fromRGB(37, 99, 235)
	}
}

local currentTheme = ThemePalettes[1]
local isMinimized = false

--------------------------------------------------------------------------------
-- Size Modes Configuration (5 Modes matching standard layout sizing proportions)
--------------------------------------------------------------------------------
local sizeModes = {
	{Name = "Compact", Size = UDim2.new(0, 380, 0, 400)},
	{Name = "Small", Size = UDim2.new(0, 410, 0, 440)},
	{Name = "Medium (Default)", Size = UDim2.new(0, 440, 0, 480)},
	{Name = "Large", Size = UDim2.new(0, 500, 0, 540)},
	{Name = "Extra Large", Size = UDim2.new(0, 560, 0, 600)}
}
local currentSizeModeIndex = 3

--------------------------------------------------------------------------------
-- Main Visual Elements Tracking Lists
--------------------------------------------------------------------------------
local registeredFrames = {}
local registeredHeaders = {}
local registeredSections = {}
local registeredStrokes = {}
local registeredButtons = {}
local registeredSliders = {}
local registeredLabels = {}
-- wallubo made this
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = sizeModes[currentSizeModeIndex].Size
mainFrame.Position = UDim2.new(0.5, -220, 0.5, -240)
mainFrame.BackgroundColor3 = currentTheme.MainBg
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui
table.insert(registeredFrames, mainFrame)

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = mainFrame

local stroke = Instance.new("UIStroke")
stroke.Color = currentTheme.BorderColour
stroke.Thickness = 1
stroke.Parent = mainFrame
table.insert(registeredStrokes, stroke)

--------------------------------------------------------------------------------
-- Settings / Custom Modal Pop-up Frame
--------------------------------------------------------------------------------
local customModal = Instance.new("Frame")
customModal.Name = "CustomModal"
customModal.Size = UDim2.new(0, 240, 0, 260)
customModal.Position = UDim2.new(1, 12, 0, 0)
customModal.BackgroundColor3 = currentTheme.MainBg
customModal.BorderSizePixel = 0
customModal.Visible = false
customModal.ZIndex = 10
customModal.Parent = mainFrame
table.insert(registeredFrames, customModal)

local modalCorner = Instance.new("UICorner")
modalCorner.CornerRadius = UDim.new(0, 10)
modalCorner.Parent = customModal

local modalStroke = Instance.new("UIStroke")
modalStroke.Color = currentTheme.BorderColour
modalStroke.Thickness = 1
modalStroke.Parent = customModal
table.insert(registeredStrokes, modalStroke)

local modalTitle = Instance.new("TextLabel")
modalTitle.Name = "ModalTitle"
modalTitle.Size = UDim2.new(1, -35, 0, 35)
modalTitle.Position = UDim2.new(0, 12, 0, 0)
modalTitle.BackgroundTransparency = 1
modalTitle.Text = "Options"
modalTitle.Font = currentFont
modalTitle.TextSize = 13
modalTitle.TextColor3 = currentTheme.TextColour
modalTitle.TextXAlignment = Enum.TextXAlignment.Left
modalTitle.ZIndex = 11
modalTitle.Parent = customModal
table.insert(registeredLabels, modalTitle)

local modalClose = Instance.new("TextButton")
modalClose.Size = UDim2.new(0, 22, 0, 22)
modalClose.Position = UDim2.new(1, -28, 0, 7)
modalClose.BackgroundColor3 = Color3.fromRGB(235, 50, 50)
modalClose.Text = "X"
modalClose.Font = Enum.Font.GothamBold
modalClose.TextSize = 12
modalClose.TextColor3 = Color3.fromRGB(255, 255, 255)
modalClose.ZIndex = 11
modalClose.Parent = customModal

local modalCloseCorner = Instance.new("UICorner")
modalCloseCorner.CornerRadius = UDim.new(0, 6)
modalCloseCorner.Parent = modalClose

local modalCloseStroke = Instance.new("UIStroke")
modalCloseStroke.Color = Color3.fromRGB(0, 0, 0)
modalCloseStroke.Thickness = 1.5
modalCloseStroke.Parent = modalClose

local modalScroll = Instance.new("ScrollingFrame")
modalScroll.Name = "ModalScroll"
modalScroll.Size = UDim2.new(1, -20, 1, -45)
modalScroll.Position = UDim2.new(0, 10, 0, 38)
modalScroll.BackgroundTransparency = 1
modalScroll.BorderSizePixel = 0
modalScroll.ScrollBarThickness = 3
modalScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
modalScroll.ZIndex = 11
modalScroll.Parent = customModal

local modalList = Instance.new("UIListLayout")
modalList.Padding = UDim.new(0, 6)
modalList.Parent = modalScroll

modalList:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	modalScroll.CanvasSize = UDim2.new(0, 0, 0, modalList.AbsoluteContentSize.Y + 10)
end)

modalClose.MouseButton1Click:Connect(function()
	customModal.Visible = false
end)

--------------------------------------------------------------------------------
-- Theme Application Function
--------------------------------------------------------------------------------
local function applyTheme(newTheme)
	currentTheme = newTheme
	
	for _, f in ipairs(registeredFrames) do f.BackgroundColor3 = currentTheme.MainBg end
	for _, h in ipairs(registeredHeaders) do h.BackgroundColor3 = currentTheme.HeaderBg end
	for _, s in ipairs(registeredSections) do s.BackgroundColor3 = currentTheme.SectionBg end
	for _, st in ipairs(registeredStrokes) do st.Color = currentTheme.BorderColour end
	for _, lbl in ipairs(registeredLabels) do lbl.TextColor3 = currentTheme.TextColour end
	
	for _, btn in ipairs(registeredButtons) do
		btn.BackgroundColor3 = currentTheme.ButtonBg
		btn.TextColor3 = currentTheme.TextColour
	end
	
	for _, slider in ipairs(registeredSliders) do
		slider.track.BackgroundColor3 = currentTheme.SliderBg
		slider.fill.BackgroundColor3 = currentTheme.SliderFill
	end
end

--------------------------------------------------------------------------------
-- Dynamic Script Unloader wallubo made this
--------------------------------------------------------------------------------
local function unloadScript()
	for key, conn in pairs(activeConnections) do
		if typeof(conn) == "RBXScriptConnection" then
			conn:Disconnect()
		elseif typeof(conn) == "thread" then
			task.cancel(conn)
		end
		activeConnections[key] = nil
	end

	for cat, group in pairs(activeHighlights) do
		for target, hl in pairs(group) do
			if hl and hl.Parent then hl:Destroy() end
		end
	end

	if character then
		local hum = character:FindFirstChildOfClass("Humanoid")
		local hrp = character:FindFirstChild("HumanoidRootPart")
		if hum then
			hum.WalkSpeed = 16
			hum.JumpPower = 50
			hum.UseJumpPower = false
			hum.PlatformStand = false
		end
		if hrp then
			if hrp:FindFirstChild("SmoothFlyVelocity") then hrp.SmoothFlyVelocity:Destroy() end
			if hrp:FindFirstChild("SmoothFlyGyro") then hrp.SmoothFlyGyro:Destroy() end
			hrp.CustomPhysicalProperties = PhysicalProperties.new(0.7, 0.3, 0.5)
		end
	end

	Lighting.FogEnd = defaultLightingSettings.FogEnd
	Lighting.FogStart = defaultLightingSettings.FogStart
	Lighting.Ambient = defaultLightingSettings.Ambient
	Lighting.OutdoorAmbient = defaultLightingSettings.OutdoorAmbient
	Lighting.Brightness = defaultLightingSettings.Brightness
	if defaultLightingSettings.Atmosphere then
		defaultLightingSettings.Atmosphere.Parent = Lighting
	end
	workspace.Gravity = 196.2

	screenGui:Destroy()
end

--------------------------------------------------------------------------------
-- Top Header Bar
--------------------------------------------------------------------------------
local header = Instance.new("Frame")
header.Name = "Header"
header.Size = UDim2.new(1, 0, 0, 42)
header.BackgroundColor3 = currentTheme.HeaderBg
header.BorderSizePixel = 0
header.Parent = mainFrame
table.insert(registeredHeaders, header)

local headerCorner = Instance.new("UICorner")
headerCorner.CornerRadius = UDim.new(0, 12)
headerCorner.Parent = header

local headerCover = Instance.new("Frame")
headerCover.Size = UDim2.new(1, 0, 0, 6)
headerCover.Position = UDim2.new(0, 0, 1, -6)
headerCover.BackgroundColor3 = currentTheme.HeaderBg
headerCover.BorderSizePixel = 0
headerCover.Parent = header
table.insert(registeredHeaders, headerCover)

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(0, 220, 0, 20)
titleLabel.Position = UDim2.new(0, 14, 0, 4)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "W - Scripts Hub <font color='#3b82f6'>[" .. SCRIPT_VERSION .. "]</font>"
titleLabel.RichText = true
titleLabel.Font = currentFont
titleLabel.TextSize = 12
titleLabel.TextColor3 = currentTheme.TextColour
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = header
table.insert(registeredLabels, titleLabel)
-- wallubo made this
local statsLabel = Instance.new("TextLabel")
statsLabel.Size = UDim2.new(0, 200, 0, 14)
statsLabel.Position = UDim2.new(0, 14, 0, 22)
statsLabel.BackgroundTransparency = 1
statsLabel.Text = "FPS: -- | Ping: -- ms"
statsLabel.Font = currentFont
statsLabel.TextSize = 10
statsLabel.TextColor3 = currentTheme.SubTextColour
statsLabel.TextXAlignment = Enum.TextXAlignment.Left
statsLabel.Parent = header

local frameCount = 0
local lastCheck = os.clock()
activeConnections["StatsTracker"] = RunService.Heartbeat:Connect(function()
	frameCount = frameCount + 1
	local now = os.clock()
	if now - lastCheck >= 1 then
		local fps = math.floor(frameCount / (now - lastCheck))
		local ping = 0
		pcall(function()
			ping = math.floor(Stats.Network.ServerStatsItem["Data Ping"]:GetValue())
		end)
		statsLabel.Text = string.format("FPS: %d | Ping: %d ms", fps, ping)
		frameCount = 0
		lastCheck = now
	end
end)

local controlsHolder = Instance.new("Frame")
controlsHolder.Size = UDim2.new(0, 100, 1, 0)
controlsHolder.Position = UDim2.new(1, -105, 0, 0)
controlsHolder.BackgroundTransparency = 1
controlsHolder.Parent = header

local layoutControls = Instance.new("UIListLayout")
layoutControls.FillDirection = Enum.FillDirection.Horizontal
layoutControls.HorizontalAlignment = Enum.HorizontalAlignment.Right
layoutControls.VerticalAlignment = Enum.VerticalAlignment.Center
layoutControls.Padding = UDim.new(0, 6)
layoutControls.Parent = controlsHolder

local function createHeaderButton(text, bgOverride, isClose)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(0, 24, 0, 24)
	btn.BackgroundColor3 = bgOverride or currentTheme.ButtonBg
	btn.Text = text
	btn.Font = isClose and Enum.Font.GothamBold or currentFont
	btn.TextSize = 12
	btn.TextColor3 = isClose and Color3.fromRGB(255, 255, 255) or currentTheme.TextColour
	btn.Parent = controlsHolder
	
	local btnCorner = Instance.new("UICorner")
	btnCorner.CornerRadius = UDim.new(0, 6)
	btnCorner.Parent = btn

	if isClose then
		local btnStroke = Instance.new("UIStroke")
		btnStroke.Color = Color3.fromRGB(0, 0, 0)
		btnStroke.Thickness = 1.5
		btnStroke.Parent = btn
	else
		table.insert(registeredButtons, btn)
	end
	return btn
end

local resizeBtn = createHeaderButton("📐")
local minimizeBtn = createHeaderButton("—")
local closeScriptBtn = createHeaderButton("X", Color3.fromRGB(235, 50, 50), true)

closeScriptBtn.MouseButton1Click:Connect(unloadScript)

--------------------------------------------------------------------------------
-- Grid Layout & Sections Container wallubo made this
--------------------------------------------------------------------------------
local contentContainer = Instance.new("Frame")
contentContainer.Name = "ContentContainer"
contentContainer.Size = UDim2.new(1, -20, 1, -54)
contentContainer.Position = UDim2.new(0, 10, 0, 48)
contentContainer.BackgroundTransparency = 1
contentContainer.Parent = mainFrame

local sectionLayout = Instance.new("UIGridLayout")
sectionLayout.CellSize = UDim2.new(0.5, -6, 1, 0)
sectionLayout.CellPadding = UDim2.new(0, 12, 0, 0)
sectionLayout.Parent = contentContainer

local function createSection(title)
	local sectionFrame = Instance.new("Frame")
	sectionFrame.BackgroundColor3 = currentTheme.SectionBg
	sectionFrame.BorderSizePixel = 0
	sectionFrame.Parent = contentContainer
	table.insert(registeredSections, sectionFrame)
	
	local secCorner = Instance.new("UICorner")
	secCorner.CornerRadius = UDim.new(0, 10)
	secCorner.Parent = sectionFrame
	
	local secStroke = Instance.new("UIStroke")
	secStroke.Color = currentTheme.BorderColour
	secStroke.Thickness = 1
	secStroke.Parent = sectionFrame
	table.insert(registeredStrokes, secStroke)

	local secTitle = Instance.new("TextLabel")
	secTitle.Size = UDim2.new(1, -16, 0, 32)
	secTitle.Position = UDim2.new(0, 8, 0, 4)
	secTitle.BackgroundTransparency = 1
	secTitle.Text = title
	secTitle.Font = currentFont
	secTitle.TextSize = 12
	secTitle.TextColor3 = currentTheme.TextColour
	secTitle.TextXAlignment = Enum.TextXAlignment.Left
	secTitle.Parent = sectionFrame
	table.insert(registeredLabels, secTitle)

	local scrollFrame = Instance.new("ScrollingFrame")
	scrollFrame.Size = UDim2.new(1, -8, 1, -38)
	scrollFrame.Position = UDim2.new(0, 4, 0, 36)
	scrollFrame.BackgroundTransparency = 1
	scrollFrame.BorderSizePixel = 0
	scrollFrame.ScrollBarThickness = 2
	scrollFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
	scrollFrame.Parent = sectionFrame

	local listLayout = Instance.new("UIListLayout")
	listLayout.Padding = UDim.new(0, 5)
	listLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
	listLayout.Parent = scrollFrame

	listLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
		scrollFrame.CanvasSize = UDim2.new(0, 0, 0, listLayout.AbsoluteContentSize.Y + 8)
	end)

	return scrollFrame
end

local usefulScroll = createSection("Utility & Movement")
local visualScroll = createSection("Visuals & World")

--------------------------------------------------------------------------------
-- Component Builders & UI Helpers Definition
--------------------------------------------------------------------------------
local currentActiveModalKey = nil
local openCustomModal

openCustomModal = function(modalKey, titleText, builderCallback)
	currentActiveModalKey = modalKey
	-- wallubo made this
	for _, child in ipairs(modalScroll:GetChildren()) do
		if not child:IsA("UIListLayout") then child:Destroy() end
	end

	modalTitle.Text = titleText
	if builderCallback then builderCallback(modalScroll) end
	customModal.Visible = true
end

local function createFeatureRow(parent, modalKey, featureName, toggleCallback, openSettingsCallback)
	local rowFrame = Instance.new("Frame")
	rowFrame.Size = UDim2.new(0.96, 0, 0, 30)
	rowFrame.BackgroundTransparency = 1
	rowFrame.Parent = parent

	local buttonWidth = openSettingsCallback and -32 or 0

	local toggleBtn = Instance.new("TextButton")
	toggleBtn.Size = UDim2.new(1, buttonWidth, 1, 0)
	toggleBtn.BackgroundColor3 = currentTheme.ButtonBg
	toggleBtn.Text = "  " .. featureName
	toggleBtn.Font = currentFont
	toggleBtn.TextSize = 11
	toggleBtn.TextColor3 = currentTheme.TextColour
	toggleBtn.TextXAlignment = Enum.TextXAlignment.Left
	toggleBtn.ZIndex = 2
	toggleBtn.Parent = rowFrame

	local btnCorner = Instance.new("UICorner")
	btnCorner.CornerRadius = UDim.new(0, 6)
	btnCorner.Parent = toggleBtn

	local gearBtn
	if openSettingsCallback then
		gearBtn = Instance.new("TextButton")
		gearBtn.Size = UDim2.new(0, 26, 1, 0)
		gearBtn.Position = UDim2.new(1, -26, 0, 0)
		gearBtn.BackgroundColor3 = currentTheme.ButtonBg
		gearBtn.Text = "⚙"
		gearBtn.Font = currentFont
		gearBtn.TextSize = 12
		gearBtn.TextColor3 = currentTheme.TextColour
		gearBtn.ZIndex = 3
		gearBtn.Parent = rowFrame

		local gearCorner = Instance.new("UICorner")
		gearCorner.CornerRadius = UDim.new(0, 6)
		gearCorner.Parent = gearBtn
		table.insert(registeredButtons, gearBtn)
	end

	local isActive = false

	toggleBtn.MouseButton1Click:Connect(function()
		isActive = not isActive
		toggleBtn.BackgroundColor3 = isActive and currentTheme.ButtonActiveBg or currentTheme.ButtonBg
		toggleCallback(isActive)
	end)

	if gearBtn then
		gearBtn.MouseButton1Click:Connect(function()
			openCustomModal(modalKey, featureName .. " Settings", openSettingsCallback)
		end)
	end

	table.insert(registeredButtons, toggleBtn)
	return rowFrame
end

local function addActionButton(parent, buttonText, onClick)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(0.96, 0, 0, 30)
	btn.BackgroundColor3 = currentTheme.ButtonBg
	btn.Text = buttonText
	btn.Font = currentFont
	btn.TextSize = 11
	btn.TextColor3 = currentTheme.TextColour
	btn.ZIndex = 2
	btn.Parent = parent

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 6)
	corner.Parent = btn

	btn.MouseButton1Click:Connect(onClick)
	table.insert(registeredButtons, btn)
	return btn
end
-- wallubo made this
local function addModalSlider(parent, labelText, minVal, maxVal, currentVal, onChange)
	local container = Instance.new("Frame")
	container.Size = UDim2.new(1, 0, 0, 42)
	container.BackgroundTransparency = 1
	container.ZIndex = 11
	container.Parent = parent

	local lbl = Instance.new("TextLabel")
	lbl.Size = UDim2.new(1, 0, 0, 16)
	lbl.BackgroundTransparency = 1
	lbl.Text = labelText .. ": " .. tostring(math.floor(currentVal))
	lbl.Font = currentFont
	lbl.TextSize = 11
	lbl.TextColor3 = currentTheme.TextColour
	lbl.TextXAlignment = Enum.TextXAlignment.Left
	lbl.ZIndex = 11
	lbl.Parent = container
	table.insert(registeredLabels, lbl)

	local sliderTrack = Instance.new("Frame")
	sliderTrack.Size = UDim2.new(1, 0, 0, 8)
	sliderTrack.Position = UDim2.new(0, 0, 0, 22)
	sliderTrack.BackgroundColor3 = currentTheme.SliderBg
	sliderTrack.ZIndex = 11
	sliderTrack.Parent = container

	local trackCorner = Instance.new("UICorner")
	trackCorner.CornerRadius = UDim.new(0, 4)
	trackCorner.Parent = sliderTrack

	local fill = Instance.new("Frame")
	fill.Size = UDim2.new(math.clamp((currentVal - minVal) / (maxVal - minVal), 0, 1), 0, 1, 0)
	fill.BackgroundColor3 = currentTheme.SliderFill
	fill.ZIndex = 11
	fill.Parent = sliderTrack

	local fillCorner = Instance.new("UICorner")
	fillCorner.CornerRadius = UDim.new(0, 4)
	fillCorner.Parent = fill

	local isSliding = false
	local function updateSlider(input)
		local scale = math.clamp((input.Position.X - sliderTrack.AbsolutePosition.X) / sliderTrack.AbsoluteSize.X, 0, 1)
		local val = math.floor(minVal + (maxVal - minVal) * scale)
		fill.Size = UDim2.new(scale, 0, 1, 0)
		lbl.Text = labelText .. ": " .. tostring(val)
		onChange(val)
	end

	sliderTrack.InputBegan:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
			isSliding = true
			updateSlider(input)
		end
	end)

	sliderTrack.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then 
			isSliding = false 
		end
	end)

	activeConnections["SliderMove_" .. labelText] = UserInputService.InputChanged:Connect(function(input)
		if isSliding and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then 
			updateSlider(input) 
		end
	end)

	table.insert(registeredSliders, {track = sliderTrack, fill = fill})
end
-- wallubo made this
--------------------------------------------------------------------------------
-- Universal Workspace Scanning Engine
--------------------------------------------------------------------------------
local function getAllNPCs()
	local list = {}
	for _, obj in ipairs(workspace:GetDescendants()) do
		pcall(function()
			if obj:IsA("Model") and obj ~= character and not Players:GetPlayerFromCharacter(obj) then
				local hum = obj:FindFirstChildOfClass("Humanoid")
				if hum then
					local name = string.lower(obj.Name)
					local hasPrompt = obj:FindFirstChildWhichIsA("ProximityPrompt", true) ~= nil
					local hasClick = obj:FindFirstChildWhichIsA("ClickDetector", true) ~= nil
					local hasDialog = obj:FindFirstChildWhichIsA("Dialog", true) ~= nil
					local isTagged = string.find(name, "npc") or string.find(name, "quest") or string.find(name, "shop") or string.find(name, "merchant")
					
					if hasPrompt or hasClick or hasDialog or isTagged then
						table.insert(list, obj)
					end
				end
			end
		end)
	end
	return list
end

--------------------------------------------------------------------------------
-- TAB 1: USEFUL STUFF
--------------------------------------------------------------------------------

-- Infinite Jump
createFeatureRow(usefulScroll, "InfJump", "Infinite Jump", function(active)
	if active then
		activeConnections["InfJump"] = UserInputService.JumpRequest:Connect(function()
			local hum = character and character:FindFirstChildOfClass("Humanoid")
			if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
		end)
	else
		if activeConnections["InfJump"] then activeConnections["InfJump"]:Disconnect() end
	end
end)

-- No Fall Damage
createFeatureRow(usefulScroll, "NoFall", "No Fall Damage", function(active)
	if active then
		activeConnections["NoFall"] = RunService.Heartbeat:Connect(function()
			local hum = character and character:FindFirstChildOfClass("Humanoid")
			if hum then
				local root = character:FindFirstChild("HumanoidRootPart")
				if root and root.AssemblyLinearVelocity.Y < -75 then
					root.AssemblyLinearVelocity = Vector3.new(root.AssemblyLinearVelocity.X, -20, root.AssemblyLinearVelocity.Z)
				end
			end
		end)
	else
		if activeConnections["NoFall"] then activeConnections["NoFall"]:Disconnect() end
	end
end)

-- Gravity Control Feature
local gravityVal = 196.2
createFeatureRow(usefulScroll, "Gravity", "Gravity Control", function(active)
	if active then
		activeConnections["GravityLoop"] = RunService.RenderStepped:Connect(function()
			workspace.Gravity = gravityVal
		end)
	else
		if activeConnections["GravityLoop"] then activeConnections["GravityLoop"]:Disconnect() end
		workspace.Gravity = 196.2
	end
end, function(modal)
	addModalSlider(modal, "Gravity Force", 0, 500, gravityVal, function(v) gravityVal = v end)
end)
-- wallubo made this
-- Click Teleport
local clickTpActive = false
local maxTpDistance = 500
createFeatureRow(usefulScroll, "ClickTp", "Click Teleport", function(active)
	clickTpActive = active
	if clickTpActive then
		local mouse = player:GetMouse()
		activeConnections["ClickTp"] = mouse.Button1Down:Connect(function()
			if clickTpActive and mouse.Hit and character and character:FindFirstChild("HumanoidRootPart") then
				local targetPos = mouse.Hit.Position
				if (targetPos - character.HumanoidRootPart.Position).Magnitude <= maxTpDistance then
					character.HumanoidRootPart.CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))
				end
			end
		end)
	else
		if activeConnections["ClickTp"] then activeConnections["ClickTp"]:Disconnect() end
	end
end, function(modal)
	addModalSlider(modal, "Max Range", 50, 2000, maxTpDistance, function(v) maxTpDistance = v end)
end)

-- Omnidirectional Flight
local isFlying = false
local flySpeed = 75
local flyBoostAmount = 150
createFeatureRow(usefulScroll, "Flight", "Omnidirectional Flight", function(active)
	isFlying = active
	local hrp = character and character:FindFirstChild("HumanoidRootPart")
	local hum = character and character:FindFirstChild("Humanoid")
	local animator = hum and hum:FindFirstChildOfClass("Animator")

	if isFlying and hrp and hum then
		hum.PlatformStand = true

		local bodyVel = Instance.new("BodyVelocity")
		bodyVel.Name = "SmoothFlyVelocity"
		bodyVel.MaxForce = Vector3.new(1e9, 1e9, 1e9)
		bodyVel.Parent = hrp
-- wallubo made this
		local bodyGyro = Instance.new("BodyGyro")
		bodyGyro.Name = "SmoothFlyGyro"
		bodyGyro.MaxTorque = Vector3.new(1e9, 1e9, 1e9)
		bodyGyro.P = 20000
		bodyGyro.Parent = hrp

		local flyTrack = nil
		pcall(function()
			local anim = Instance.new("Animation")
			anim.AnimationId = "rbxassetid://507768375"
			flyTrack = animator:LoadAnimation(anim)
			flyTrack.Priority = Enum.AnimationPriority.Action4
			flyTrack:Play()
			flyTrack:AdjustSpeed(1)
		end)

		local currentTiltZ = 0
		local currentPitchX = 0
		
		activeConnections["FlyLoop"] = RunService.RenderStepped:Connect(function()
			if not isFlying or not hrp:FindFirstChild("SmoothFlyVelocity") then return end
			local cam = workspace.CurrentCamera
			local moveDir = Vector3.new()
			local strafeVal = 0
			local pitchVal = 0

			local currentSpeed = flySpeed
			if UserInputService:IsKeyDown(Enum.KeyCode.F) then
				currentSpeed = flySpeed + flyBoostAmount
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.W) then 
				moveDir = moveDir + cam.CFrame.LookVector 
				pitchVal = -90
			end
			if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - cam.CFrame.LookVector end
			if UserInputService:IsKeyDown(Enum.KeyCode.A) then 
				moveDir = moveDir - cam.CFrame.RightVector 
				strafeVal = 15
			end
			if UserInputService:IsKeyDown(Enum.KeyCode.D) then 
				moveDir = moveDir + cam.CFrame.RightVector 
				strafeVal = -15
			end
			if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0,1,0) end
			if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir = moveDir - Vector3.new(0,1,0) end
-- wallubo made this
			currentTiltZ = currentTiltZ + (strafeVal - currentTiltZ) * 0.1
			currentPitchX = currentPitchX + (pitchVal - currentPitchX) * 0.1

			bodyGyro.CFrame = cam.CFrame * CFrame.Angles(math.rad(currentPitchX), 0, math.rad(currentTiltZ))
			bodyVel.Velocity = bodyVel.Velocity:Lerp(moveDir * currentSpeed, 0.25)

			for _, part in ipairs(character:GetDescendants()) do
				if part:IsA("BasePart") then part.CanCollide = false end
			end
		end)
	else
		if activeConnections["FlyLoop"] then activeConnections["FlyLoop"]:Disconnect() end
		if hum then hum.PlatformStand = false end
		if hrp then
			if hrp:FindFirstChild("SmoothFlyVelocity") then hrp.SmoothFlyVelocity:Destroy() end
			if hrp:FindFirstChild("SmoothFlyGyro") then hrp.SmoothFlyGyro:Destroy() end
		end
	end
end, function(modal)
	addModalSlider(modal, "Flight Speed", 20, 300, flySpeed, function(v) flySpeed = v end)
	addModalSlider(modal, "Boost Amount", 50, 400, flyBoostAmount, function(v) flyBoostAmount = v end)
end)

-- Super Walk & Jump
local walkSpeedVal, jumpPowerVal = 50, 120
createFeatureRow(usefulScroll, "SuperWalk", "Super Walk", function(active)
	if active then
		activeConnections["SuperWalk"] = RunService.RenderStepped:Connect(function()
			if character and character:FindFirstChild("Humanoid") then character.Humanoid.WalkSpeed = walkSpeedVal end
		end)
	else
		if activeConnections["SuperWalk"] then activeConnections["SuperWalk"]:Disconnect() end
		if character and character:FindFirstChild("Humanoid") then character.Humanoid.WalkSpeed = 16 end
	end
end, function(modal)
	addModalSlider(modal, "Walk Speed", 16, 250, walkSpeedVal, function(v) walkSpeedVal = v end)
end)

createFeatureRow(usefulScroll, "SuperJump", "Super Jump", function(active)
	if active then
		activeConnections["SuperJump"] = RunService.RenderStepped:Connect(function()
			if character and character:FindFirstChild("Humanoid") then
				character.Humanoid.UseJumpPower = true
				character.Humanoid.JumpPower = jumpPowerVal
			end
		end)
	else
		if activeConnections["SuperJump"] then activeConnections["SuperJump"]:Disconnect() end
		if character and character:FindFirstChild("Humanoid") then character.Humanoid.JumpPower = 50 end
	end
end, function(modal)
	addModalSlider(modal, "Jump Power", 50, 350, jumpPowerVal, function(v) jumpPowerVal = v end)
end)

--------------------------------------------------------------------------------
-- TAB 2: VISUALS & WORLD
--------------------------------------------------------------------------------
-- wallubo made this
local function attachHighlight(targetModel, colour, category)
	if not targetModel or activeHighlights[category][targetModel] then return end

	local hl = Instance.new("Highlight")
	hl.Name = "OptimizedESP"
	hl.FillColor = colour
	hl.FillTransparency = 0.5
	hl.OutlineColor = Color3.fromRGB(255, 255, 255)
	hl.OutlineTransparency = 0.2
	hl.Adornee = targetModel
	hl.Parent = targetModel

	activeHighlights[category][targetModel] = hl
end

local function removeCategoryESP(category)
	for targetModel, hl in pairs(activeHighlights[category]) do
		if hl and hl.Parent then hl:Destroy() end
	end
	activeHighlights[category] = {}
end

local function startEspLoop(category, getTargetsFunc, color)
	activeConnections["ESP_Loop_" .. category] = task.spawn(function()
		while true do
			local hrp = character and character:FindFirstChild("HumanoidRootPart")
			if hrp then
				local maxRadius = espRadii[category] or 500
				local validTargets = getTargetsFunc()

				for target, hl in pairs(activeHighlights[category]) do
					if not target or not target.Parent then
						if hl and hl.Parent then hl:Destroy() end
						activeHighlights[category][target] = nil
					else
						local targetPos = nil
						pcall(function()
							targetPos = target:IsA("Model") and target:GetPivot().Position or target.Position
						end)
						-- wallubo made this
						if not targetPos or (targetPos - hrp.Position).Magnitude > maxRadius then
							if hl and hl.Parent then hl:Destroy() end
							activeHighlights[category][target] = nil
						end
					end
				end

				for _, target in ipairs(validTargets) do
					if target and target.Parent then
						local targetPos = nil
						pcall(function()
							targetPos = target:IsA("Model") and target:GetPivot().Position or target.Position
						end)
						
						if targetPos then
							local dist = (targetPos - hrp.Position).Magnitude
							if dist <= maxRadius then
								if not activeHighlights[category][target] then
									attachHighlight(target, color, category)
								end
							end
						end
					end
				end
			end
			task.wait(0.4)
		end
	end)
end
-- wallubo made this
-- Player ESP
createFeatureRow(visualScroll, "PlayerESP", "Player ESP", function(active)
	if active then
		startEspLoop("Players", function()
			local t = {}
			for _, p in ipairs(Players:GetPlayers()) do
				if p ~= player and p.Character then table.insert(t, p.Character) end
			end
			return t
		end, Color3.fromRGB(0, 180, 255))
	else
		if activeConnections["ESP_Loop_Players"] then task.cancel(activeConnections["ESP_Loop_Players"]) end
		removeCategoryESP("Players")
	end
end, function(modal)
	addModalSlider(modal, "Player Radius", 100, 3000, espRadii.Players, function(v) espRadii.Players = v end)
end)

-- Interactable NPC ESP
createFeatureRow(visualScroll, "NPCESP", "NPC ESP", function(active)
	if active then
		startEspLoop("NPCs", getAllNPCs, Color3.fromRGB(40, 230, 110))
	else
		if activeConnections["ESP_Loop_NPCs"] then task.cancel(activeConnections["ESP_Loop_NPCs"]) end
		removeCategoryESP("NPCs")
	end
end, function(modal)
	addModalSlider(modal, "NPC Radius", 100, 3000, espRadii.NPCs, function(v) espRadii.NPCs = v end)
end)
-- wallubo made this
-- Fullbright
createFeatureRow(visualScroll, "Fullbright", "Fullbright", function(active)
	if active then
		activeConnections["FB"] = RunService.RenderStepped:Connect(function()
			Lighting.Ambient = Color3.fromRGB(255, 255, 255)
			Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
			Lighting.Brightness = 2
		end)
	else
		if activeConnections["FB"] then activeConnections["FB"]:Disconnect() end
		Lighting.Ambient = defaultLightingSettings.Ambient
		Lighting.OutdoorAmbient = defaultLightingSettings.OutdoorAmbient
		Lighting.Brightness = defaultLightingSettings.Brightness
	end
end)

-- Theme Selection Menu Button
addActionButton(visualScroll, "Theme Settings", function()
	openCustomModal("ThemeSettings", "Select Theme", function(scrollFrame)
		for _, themeData in ipairs(ThemePalettes) do
			local tBtn = Instance.new("TextButton")
			tBtn.Size = UDim2.new(1, 0, 0, 30)
			tBtn.BackgroundColor3 = themeData.ButtonBg
			tBtn.Text = themeData.Name
			tBtn.Font = currentFont
			tBtn.TextSize = 11
			tBtn.TextColor3 = themeData.TextColour
			tBtn.ZIndex = 11
			tBtn.Parent = scrollFrame
			table.insert(registeredButtons, tBtn)

			local corner = Instance.new("UICorner")
			corner.CornerRadius = UDim.new(0, 6)
			corner.Parent = tBtn
-- wallubo made this
			tBtn.MouseButton1Click:Connect(function()
				applyTheme(themeData)
				customModal.Visible = false
			end)
		end
	end)
end)

--------------------------------------------------------------------------------
-- Window Dragging & Window Controls
--------------------------------------------------------------------------------
local dragging, dragStart, startPos
header.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = mainFrame.Position
	end
end)

header.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then 
		dragging = false 
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = input.Position - dragStart
		mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

resizeBtn.MouseButton1Click:Connect(function()
	currentSizeModeIndex = (currentSizeModeIndex % #sizeModes) + 1
	local targetMode = sizeModes[currentSizeModeIndex]
-- wallubo made this
	TweenService:Create(mainFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
		Size = targetMode.Size
	}):Play()
end)

minimizeBtn.MouseButton1Click:Connect(function()
	isMinimized = not isMinimized

	local currentMode = sizeModes[currentSizeModeIndex]
	local targetWidth = isMinimized and 160 or currentMode.Size.X.Offset
	local targetHeight = isMinimized and 42 or currentMode.Size.Y.Offset

	contentContainer.Visible = not isMinimized
	statsLabel.Visible = not isMinimized
	customModal.Visible = false

	titleLabel.Text = isMinimized and "W - SCRIPTS" or ("W - Scripts Hub <font color='#3b82f6'>[" .. SCRIPT_VERSION .. "]</font>")
	minimizeBtn.Text = isMinimized and "+" or "—"

	TweenService:Create(mainFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
		Size = UDim2.new(0, targetWidth, 0, targetHeight)
	}):Play()
end)