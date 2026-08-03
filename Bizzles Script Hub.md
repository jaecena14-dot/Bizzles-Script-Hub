-- BIZZLES SCRIPT HUB V4.2 // INSTANT NO FOG & ENHANCED FULL BRIGHT
-- Place inside StarterPlayer -> StarterPlayerScripts as a LocalScript

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local TeleportService = game:GetService("TeleportService")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")

local player = Players.LocalPlayer
local mouse = player:GetMouse()
local camera = Workspace.CurrentCamera

local character, humanoid, rootPart, animator

local function updateCharacterReferences(newChar)
    character = newChar or player.Character or player.CharacterAdded:Wait()
    humanoid = character:WaitForChild("Humanoid", 5)
    rootPart = character:WaitForChild("HumanoidRootPart", 5)
    local anim = character:WaitForChild("Animate", 5)
    if anim then anim.Disabled = false end
    local animatorObj = humanoid:FindFirstChildOfClass("Animator")
    if not animatorObj then
        animatorObj = Instance.new("Animator")
        animatorObj.Parent = humanoid
    end
    animator = animatorObj
end
updateCharacterReferences(player.Character)

local toggleKey = Enum.KeyCode.RightControl
local isFlying = false
local flySpeed = 150
local currentFlightAnimation = nil

local isWalkSpeedActive = false
local currentWalkSpeed = 100

local isJumpBoostActive = false
local currentJumpPower = 120

local isInfiniteJumpActive = false
local isNoclipActive = false
local isClickTpActive = false
local isAntiAfkActive = true

-- Features
local isFullBrightActive = false
local isNoFogActive = false
local autoClickCps = 15
local isAutoClickerActive = false

-- Additional States
local isBtoolsActive = false
local isInfiniteZoomActive = false
local isAutoHealActive = false
local autoHealThreshold = 50
local isTimeFrozenActive = false
local customTime = 12
local isAutoCollectCoinsActive = false
local isNpcFreezeActive = false
local isSuperHumanoidStateActive = false
local isChatSpamActive = false
local chatSpamMessage = "Bizzles Script Hub V4 On Top!"

-- New Enhanced Features
local isXrayActive = false
local isRainbowCharacterActive = false
local isTimeScaleActive = false
local customTimeScale = 2
local isPlatformGunActive = false
local isProximityAuraActive = false

-- Version 4 Expanded Features
local isGodModeActive = false
local isInfiniteStaminaActive = false
local isAutoFarmGemsActive = false
local isSuperJumpActive = false
local isAutoClickChestActive = false
local isInvisibleArmsActive = false
local isCustomGravityActive = false
local customGravityValue = 196.2
local isPlayerOrbitsActive = false
local isAutoRebirthActive = false
local isAntiRagdollActive = false
local isAutoBuyBestActive = false

-- V4 Brand New Additions
local isAutoClickerHeadshotActive = false
local isAutoSellInventoryActive = false
local isAutoCollectGiftsActive = false
local isSuperSpeedBunnyHopActive = false
local isAlwaysDayActive = false
local isMuteAllOtherPlayersAudioActive = false
local isAntiVoidActive = false
local isInfiniteOxygenActive = false
local isInstantInteractActive = false
local isAutoOpenCasesActive = false
local isAutoFarmExpActive = false
local isAutoFishActive = false
local isAutoEquipBestToolsActive = false
local isAutoEatFoodActive = false

local isEspActive = false
local isPlayerEspActive = false
local isEspTracersActive = false
local isEspNamesActive = false
local isHitboxActive = false
local hitboxSize = 15
local mobTargetRadius = 500

local isMobFarmActive = false
local farmDistance = 6
local isMobMagnetActive = false
local isMobFreezeActive = false
local isAutoLootActive = false
local isSafeFarmActive = false
local safeHealthThreshold = 30
local isAutoEquipActive = false

local isFpsSaverActive = false
local isGhostModeActive = false
local isSpinbotActive = false

local isMinimised = false
local isGuiVisible = true
local activeTab = "Flight"

local uiFontFamily = Font.new("rbxasset://fonts/families/BuilderSans.json", Enum.FontWeight.Medium, Enum.FontStyle.Normal)
local uiFontBold = Font.new("rbxasset://fonts/families/BuilderSans.json", Enum.FontWeight.Bold, Enum.FontStyle.Normal)

local Themes = {
    CyberNeon = {
        BG = Color3.fromRGB(8, 9, 14),
        Panel = Color3.fromRGB(14, 16, 24),
        Accent = Color3.fromRGB(0, 255, 170),
        Text = Color3.fromRGB(250, 252, 255),
        Subtext = Color3.fromRGB(160, 175, 205),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    MidnightViolet = {
        BG = Color3.fromRGB(12, 8, 18),
        Panel = Color3.fromRGB(20, 14, 30),
        Accent = Color3.fromRGB(175, 75, 255),
        Text = Color3.fromRGB(252, 245, 255),
        Subtext = Color3.fromRGB(180, 160, 215),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    CrimsonRed = {
        BG = Color3.fromRGB(14, 8, 10),
        Panel = Color3.fromRGB(24, 12, 16),
        Accent = Color3.fromRGB(255, 40, 70),
        Text = Color3.fromRGB(255, 245, 245),
        Subtext = Color3.fromRGB(210, 160, 170),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    ElectricCyan = {
        BG = Color3.fromRGB(6, 12, 18),
        Panel = Color3.fromRGB(12, 20, 30),
        Accent = Color3.fromRGB(0, 210, 255),
        Text = Color3.fromRGB(240, 252, 255),
        Subtext = Color3.fromRGB(150, 195, 225),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    SunsetOrange = {
        BG = Color3.fromRGB(16, 10, 8),
        Panel = Color3.fromRGB(28, 16, 13),
        Accent = Color3.fromRGB(255, 110, 30),
        Text = Color3.fromRGB(255, 242, 235),
        Subtext = Color3.fromRGB(220, 170, 150),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    MatrixEmerald = {
        BG = Color3.fromRGB(6, 14, 10),
        Panel = Color3.fromRGB(10, 24, 16),
        Accent = Color3.fromRGB(30, 255, 90),
        Text = Color3.fromRGB(238, 255, 242),
        Subtext = Color3.fromRGB(150, 220, 170),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    RoyalGold = {
        BG = Color3.fromRGB(15, 13, 8),
        Panel = Color3.fromRGB(25, 21, 13),
        Accent = Color3.fromRGB(255, 205, 0),
        Text = Color3.fromRGB(255, 248, 225),
        Subtext = Color3.fromRGB(220, 210, 160),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    BubblegumPink = {
        BG = Color3.fromRGB(16, 8, 13),
        Panel = Color3.fromRGB(28, 14, 23),
        Accent = Color3.fromRGB(255, 90, 190),
        Text = Color3.fromRGB(255, 238, 248),
        Subtext = Color3.fromRGB(220, 160, 205),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    ToxicLime = {
        BG = Color3.fromRGB(8, 15, 8),
        Panel = Color3.fromRGB(13, 25, 13),
        Accent = Color3.fromRGB(110, 255, 0),
        Text = Color3.fromRGB(242, 255, 238),
        Subtext = Color3.fromRGB(170, 215, 160),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    PlasmaPurple = {
        BG = Color3.fromRGB(12, 8, 20),
        Panel = Color3.fromRGB(20, 13, 32),
        Accent = Color3.fromRGB(210, 0, 255),
        Text = Color3.fromRGB(248, 238, 255),
        Subtext = Color3.fromRGB(190, 160, 225),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    SolarFlare = {
        BG = Color3.fromRGB(18, 10, 6),
        Panel = Color3.fromRGB(30, 16, 10),
        Accent = Color3.fromRGB(255, 80, 0),
        Text = Color3.fromRGB(255, 242, 230),
        Subtext = Color3.fromRGB(220, 170, 150),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    DeepOcean = {
        BG = Color3.fromRGB(6, 10, 18),
        Panel = Color3.fromRGB(10, 17, 30),
        Accent = Color3.fromRGB(0, 120, 255),
        Text = Color3.fromRGB(238, 245, 255),
        Subtext = Color3.fromRGB(150, 180, 225),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    HackerMatrix = {
        BG = Color3.fromRGB(4, 7, 4),
        Panel = Color3.fromRGB(8, 14, 8),
        Accent = Color3.fromRGB(0, 255, 55),
        Text = Color3.fromRGB(230, 255, 230),
        Subtext = Color3.fromRGB(130, 195, 130),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    AmethystGlow = {
        BG = Color3.fromRGB(13, 10, 18),
        Panel = Color3.fromRGB(21, 15, 30),
        Accent = Color3.fromRGB(190, 120, 255),
        Text = Color3.fromRGB(248, 242, 255),
        Subtext = Color3.fromRGB(190, 170, 215),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    NeonTeal = {
        BG = Color3.fromRGB(6, 15, 15),
        Panel = Color3.fromRGB(11, 24, 24),
        Accent = Color3.fromRGB(0, 245, 210),
        Text = Color3.fromRGB(238, 255, 255),
        Subtext = Color3.fromRGB(150, 215, 215),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    },
    BloodMoon = {
        BG = Color3.fromRGB(12, 6, 6),
        Panel = Color3.fromRGB(21, 10, 10),
        Accent = Color3.fromRGB(255, 15, 35),
        Text = Color3.fromRGB(255, 238, 238),
        Subtext = Color3.fromRGB(210, 150, 150),
        Danger = Color3.fromRGB(255, 55, 80),
        Success = Color3.fromRGB(0, 240, 110)
    }
}

local currentTheme = Themes.CyberNeon
local espColor = currentTheme.Accent

local function tween(obj, t, props)
    TweenService:Create(obj, TweenInfo.new(t, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), props):Play()
end

local cachedMobs = {}
local lastMobScan = 0

local function refreshMobCache()
    if os.clock() - lastMobScan < 0.5 then return cachedMobs end
    lastMobScan = os.clock()
    
    table.clear(cachedMobs)
    if not rootPart then return cachedMobs end

    for _, obj in ipairs(Workspace:GetChildren()) do
        if obj:IsA("Model") and obj ~= character and not Players:GetPlayerFromCharacter(obj) then
            local hum = obj:FindFirstChildOfClass("Humanoid")
            local hrp = obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChild("Torso") or obj.PrimaryPart
            if hum and hrp and hum.Health > 0 then
                if (rootPart.Position - hrp.Position).Magnitude <= mobTargetRadius then
                    table.insert(cachedMobs, {Model = obj, Humanoid = hum, RootPart = hrp})
                end
            end
        end
    end
    return cachedMobs
end

local playerGui = player:WaitForChild("PlayerGui")
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "BizzlesScriptHubV4"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.Parent = playerGui

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 720, 0, 500)
mainFrame.Position = UDim2.new(0.5, -360, 0.5, -250)
mainFrame.BackgroundColor3 = currentTheme.BG
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.ClipsDescendants = false
mainFrame.Parent = screenGui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 16)
mainCorner.Parent = mainFrame

local mainStroke = Instance.new("UIStroke")
mainStroke.Color = currentTheme.Accent
mainStroke.Thickness = 2
mainStroke.Transparency = 0.2
mainStroke.Parent = mainFrame

local dropShadow = Instance.new("ImageLabel")
dropShadow.Name = "GlowShadow"
dropShadow.AnchorPoint = Vector2.new(0.5, 0.5)
dropShadow.BackgroundTransparency = 1
dropShadow.Position = UDim2.new(0.5, 0, 0.5, 0)
dropShadow.Size = UDim2.new(1, 44, 1, 44)
dropShadow.ZIndex = -1
dropShadow.Image = "rbxassetid://6015897843"
dropShadow.ImageColor3 = currentTheme.Accent
dropShadow.ImageTransparency = 0.5
dropShadow.ScaleType = Enum.ScaleType.Slice
dropShadow.SliceCenter = Rect.new(49, 49, 450, 450)
dropShadow.Parent = mainFrame

local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 56)
header.BackgroundTransparency = 1
header.Parent = mainFrame

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(0, 280, 1, 0)
titleLabel.Position = UDim2.new(0, 20, 0, 0)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "BIZZLES SCRIPT HUB V4.2"
titleLabel.TextColor3 = currentTheme.Accent
titleLabel.TextSize = 14
titleLabel.FontFace = uiFontBold
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = header

local searchBox = Instance.new("TextBox")
searchBox.Size = UDim2.new(0, 150, 0, 32)
searchBox.Position = UDim2.new(1, -380, 0, 12)
searchBox.BackgroundColor3 = currentTheme.Panel
searchBox.PlaceholderText = "Search features..."
searchBox.Text = ""
searchBox.TextColor3 = currentTheme.Text
searchBox.PlaceholderColor3 = currentTheme.Subtext
searchBox.FontFace = uiFontFamily
searchBox.TextSize = 12
searchBox.Parent = header

local searchCorner = Instance.new("UICorner")
searchCorner.CornerRadius = UDim.new(0, 8)
searchCorner.Parent = searchBox

local searchStroke = Instance.new("UIStroke")
searchStroke.Color = currentTheme.Accent
searchStroke.Thickness = 1
searchStroke.Transparency = 0.7
searchStroke.Parent = searchBox

local fpsLabel = Instance.new("TextLabel")
fpsLabel.Size = UDim2.new(0, 80, 1, 0)
fpsLabel.Position = UDim2.new(1, -215, 0, 0)
fpsLabel.BackgroundTransparency = 1
fpsLabel.Text = "60 FPS"
fpsLabel.TextColor3 = currentTheme.Subtext
fpsLabel.TextSize = 12
fpsLabel.FontFace = uiFontBold
fpsLabel.TextXAlignment = Enum.TextXAlignment.Right
fpsLabel.Parent = header

local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 32, 0, 32)
minBtn.Position = UDim2.new(1, -78, 0, 12)
minBtn.BackgroundColor3 = currentTheme.Panel
minBtn.Text = "—"
minBtn.TextColor3 = currentTheme.Text
minBtn.FontFace = uiFontBold
minBtn.TextSize = 14
minBtn.Parent = header

local minCorner = Instance.new("UICorner")
minCorner.CornerRadius = UDim.new(0, 8)
minCorner.Parent = minBtn

local minStroke = Instance.new("UIStroke")
minStroke.Color = currentTheme.Accent
minStroke.Thickness = 1
minStroke.Transparency = 0.6
minStroke.Parent = minBtn

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 32, 0, 32)
closeBtn.Position = UDim2.new(1, -38, 0, 12)
closeBtn.BackgroundColor3 = currentTheme.Danger
closeBtn.Text = "✕"
closeBtn.TextColor3 = currentTheme.Text
closeBtn.FontFace = uiFontBold
closeBtn.TextSize = 14
closeBtn.Parent = header

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 8)
closeCorner.Parent = closeBtn

local sidebarFrame = Instance.new("Frame")
sidebarFrame.Size = UDim2.new(0, 160, 1, -70)
sidebarFrame.Position = UDim2.new(0, 14, 0, 56)
sidebarFrame.BackgroundColor3 = currentTheme.Panel
sidebarFrame.Parent = mainFrame

local sidebarCorner = Instance.new("UICorner")
sidebarCorner.CornerRadius = UDim.new(0, 12)
sidebarCorner.Parent = sidebarFrame

local sidebarStroke = Instance.new("UIStroke")
sidebarStroke.Color = currentTheme.Accent
sidebarStroke.Thickness = 1
sidebarStroke.Transparency = 0.8
sidebarStroke.Parent = sidebarFrame

local sidebarScroll = Instance.new("ScrollingFrame")
sidebarScroll.Size = UDim2.new(1, 0, 1, -12)
sidebarScroll.Position = UDim2.new(0, 0, 0, 6)
sidebarScroll.BackgroundTransparency = 1
sidebarScroll.ScrollBarThickness = 2
sidebarScroll.ScrollBarImageColor3 = currentTheme.Subtext
sidebarScroll.BorderSizePixel = 0
sidebarScroll.Parent = sidebarFrame

local tabListLayout = Instance.new("UIListLayout")
tabListLayout.Padding = UDim.new(0, 6)
tabListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
tabListLayout.SortOrder = Enum.SortOrder.LayoutOrder
tabListLayout.Parent = sidebarScroll

local tabCount = 0
local function createTabBtn(name)
    tabCount = tabCount + 1
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -12, 0, 34)
    btn.BackgroundColor3 = currentTheme.BG
    btn.BackgroundTransparency = 1
    btn.Text = name
    btn.TextColor3 = currentTheme.Subtext
    btn.FontFace = uiFontFamily
    btn.TextSize = 12
    btn.LayoutOrder = tabCount
    btn.Parent = sidebarScroll
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = btn

    sidebarScroll.CanvasSize = UDim2.new(0, 0, 0, tabCount * 40 + 10)
    return btn
end

local tabFlightBtn = createTabBtn("Flight Engine")
local tabSpeedBtn = createTabBtn("Speed & Jump")
local tabUtilsBtn = createTabBtn("Movement Utils")
local tabGodBtn = createTabBtn("God & Combat")
local tabPlayerTpBtn = createTabBtn("Player TP")
local tabMobFarmBtn = createTabBtn("Mob Auto-Farm")
local tabMobVisualsBtn = createTabBtn("ESP & Visuals")
local tabExtra1Btn = createTabBtn("Extra Perks I")
local tabExtra2Btn = createTabBtn("Extra Perks II")
local tabExtra3Btn = createTabBtn("Extra Perks III")
local tabExtra4Btn = createTabBtn("Extra Perks IV")
local tabWorldBtn = createTabBtn("World & Themes")

tabFlightBtn.BackgroundTransparency = 0
tabFlightBtn.BackgroundColor3 = currentTheme.BG
tabFlightBtn.TextColor3 = currentTheme.Accent
tabFlightBtn.FontFace = uiFontBold

local contentArea = Instance.new("Frame")
contentArea.Size = UDim2.new(1, -196, 1, -70)
contentArea.Position = UDim2.new(0, 184, 0, 56)
contentArea.BackgroundTransparency = 1
contentArea.Parent = mainFrame

local allCards = {}

local function createPage()
    local page = Instance.new("ScrollingFrame")
    page.Size = UDim2.new(1, 0, 1, 0)
    page.BackgroundTransparency = 1
    page.ScrollBarThickness = 4
    page.ScrollBarImageColor3 = currentTheme.Accent
    page.BorderSizePixel = 0
    page.CanvasSize = UDim2.new(0, 0, 0, 0)
    page.Visible = false
    page.Parent = contentArea

    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 10)
    layout.SortOrder = Enum.SortOrder.LayoutOrder
    layout.Parent = page

    layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        page.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 16)
    end)

    return page
end

local pageFlight = createPage()
local pageSpeed = createPage()
local pageUtils = createPage()
local pageGod = createPage()
local pagePlayerTp = createPage()
local pageMobFarm = createPage()
local pageMobVisuals = createPage()
local pageExtra1 = createPage()
local pageExtra2 = createPage()
local pageExtra3 = createPage()
local pageExtra4 = createPage()
local pageWorld = createPage()

pageFlight.Visible = true

local tabs = {
    {btn = tabFlightBtn, page = pageFlight, name = "Flight"},
    {btn = tabSpeedBtn, page = pageSpeed, name = "Speed"},
    {btn = tabUtilsBtn, page = pageUtils, name = "Utils"},
    {btn = tabGodBtn, page = pageGod, name = "God"},
    {btn = tabPlayerTpBtn, page = pagePlayerTp, name = "PlayerTp"},
    {btn = tabMobFarmBtn, page = pageMobFarm, name = "MobFarm"},
    {btn = tabMobVisualsBtn, page = pageMobVisuals, name = "MobVisuals"},
    {btn = tabExtra1Btn, page = pageExtra1, name = "Extra1"},
    {btn = tabExtra2Btn, page = pageExtra2, name = "Extra2"},
    {btn = tabExtra3Btn, page = pageExtra3, name = "Extra3"},
    {btn = tabExtra4Btn, page = pageExtra4, name = "Extra4"},
    {btn = tabWorldBtn, page = pageWorld, name = "World"}
}

local function setTab(selectedTab)
    for _, item in ipairs(tabs) do
        if item.name == selectedTab then
            activeTab = item.name
            item.page.Visible = true
            tween(item.btn, 0.2, {BackgroundTransparency = 0, BackgroundColor3 = currentTheme.BG, TextColor3 = currentTheme.Accent})
            item.btn.FontFace = uiFontBold
        else
            item.page.Visible = false
            tween(item.btn, 0.2, {BackgroundTransparency = 1, TextColor3 = currentTheme.Subtext})
            item.btn.FontFace = uiFontFamily
        end
    end
end

for _, tabData in ipairs(tabs) do
    tabData.btn.MouseButton1Click:Connect(function() setTab(tabData.name) end)
end

local function buildToggleCard(parent, title, callback)
    local card = Instance.new("Frame")
    card.Size = UDim2.new(1, -10, 0, 48)
    card.BackgroundColor3 = currentTheme.Panel
    card.Parent = parent

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = card

    local stroke = Instance.new("UIStroke")
    stroke.Color = currentTheme.Accent
    stroke.Thickness = 1
    stroke.Transparency = 0.85
    stroke.Parent = card

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0.65, 0, 1, 0)
    lbl.Position = UDim2.new(0, 16, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = title
    lbl.TextColor3 = currentTheme.Text
    lbl.TextSize = 12
    lbl.FontFace = uiFontFamily
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = card

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 68, 0, 26)
    btn.Position = UDim2.new(1, -78, 0.5, -13)
    btn.BackgroundColor3 = currentTheme.Danger
    btn.Text = "OFF"
    btn.TextColor3 = currentTheme.Text
    btn.FontFace = uiFontBold
    btn.TextSize = 11
    btn.Parent = card

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 8)
    btnCorner.Parent = btn

    local state = false
    btn.MouseButton1Click:Connect(function()
        state = not state
        tween(btn, 0.2, {BackgroundColor3 = state and currentTheme.Success or currentTheme.Danger})
        btn.Text = state and "ON" or "OFF"
        callback(state)
    end)

    table.insert(allCards, {Frame = card, Title = title:lower()})
    return card
end

local function buildButtonCard(parent, title, btnText, callback)
    local card = Instance.new("Frame")
    card.Size = UDim2.new(1, -10, 0, 48)
    card.BackgroundColor3 = currentTheme.Panel
    card.Parent = parent

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = card

    local stroke = Instance.new("UIStroke")
    stroke.Color = currentTheme.Accent
    stroke.Thickness = 1
    stroke.Transparency = 0.85
    stroke.Parent = card

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0.6, 0, 1, 0)
    lbl.Position = UDim2.new(0, 16, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = title
    lbl.TextColor3 = currentTheme.Text
    lbl.TextSize = 12
    lbl.FontFace = uiFontFamily
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = card

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 96, 0, 26)
    btn.Position = UDim2.new(1, -106, 0.5, -13)
    btn.BackgroundColor3 = currentTheme.Accent
    btn.Text = btnText
    btn.TextColor3 = currentTheme.BG
    btn.FontFace = uiFontBold
    btn.TextSize = 11
    btn.Parent = card

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 8)
    btnCorner.Parent = btn

    btn.MouseButton1Click:Connect(callback)
    table.insert(allCards, {Frame = card, Title = title:lower()})
end

local function buildSliderCard(parent, title, minVal, maxVal, defaultVal, callback)
    local card = Instance.new("Frame")
    card.Size = UDim2.new(1, -10, 0, 60)
    card.BackgroundColor3 = currentTheme.Panel
    card.Parent = parent

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = card

    local stroke = Instance.new("UIStroke")
    stroke.Color = currentTheme.Accent
    stroke.Thickness = 1
    stroke.Transparency = 0.85
    stroke.Parent = card

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -32, 0, 22)
    lbl.Position = UDim2.new(0, 16, 0, 6)
    lbl.BackgroundTransparency = 1
    lbl.Text = title .. ": " .. tostring(defaultVal)
    lbl.TextColor3 = currentTheme.Subtext
    lbl.TextSize = 12
    lbl.FontFace = uiFontFamily
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = card

    local track = Instance.new("Frame")
    track.Size = UDim2.new(1, -32, 0, 6)
    track.Position = UDim2.new(0, 16, 0, 38)
    track.BackgroundColor3 = currentTheme.BG
    track.Parent = card

    local trackCorner = Instance.new("UICorner")
    trackCorner.CornerRadius = UDim.new(1, 0)
    trackCorner.Parent = track

    local pct = math.clamp((defaultVal - minVal) / (maxVal - minVal), 0, 1)

    local fill = Instance.new("Frame")
    fill.Size = UDim2.new(pct, 0, 1, 0)
    fill.BackgroundColor3 = currentTheme.Accent
    fill.BorderSizePixel = 0
    fill.Parent = track

    local fillCorner = Instance.new("UICorner")
    fillCorner.CornerRadius = UDim.new(1, 0)
    fillCorner.Parent = fill

    local knob = Instance.new("TextButton")
    knob.Size = UDim2.new(0, 16, 0, 16)
    knob.Position = UDim2.new(pct, -8, 0.5, -8)
    knob.BackgroundColor3 = currentTheme.Text
    knob.Text = ""
    knob.Parent = track

    local knobCorner = Instance.new("UICorner")
    knobCorner.CornerRadius = UDim.new(1, 0)
    knobCorner.Parent = knob

    local isDragging = false
    local moveConn, releaseConn

    knob.MouseButton1Down:Connect(function()
        isDragging = true
        moveConn = UserInputService.InputChanged:Connect(function(input)
            if isDragging and input.UserInputType == Enum.UserInputType.MouseMovement then
                local mouseLoc = UserInputService:GetMouseLocation()
                local relX = mouseLoc.X - track.AbsolutePosition.X
                local rawPct = math.clamp(relX / track.AbsoluteSize.X, 0, 1)
                local val = math.round(minVal + (rawPct * (maxVal - minVal)))
                
                lbl.Text = title .. ": " .. tostring(val)
                knob.Position = UDim2.new(rawPct, -8, 0.5, -8)
                fill.Size = UDim2.new(rawPct, 0, 1, 0)
                callback(val)
            end
        end)

        releaseConn = UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                isDragging = false
                if moveConn then moveConn:Disconnect() end
                if releaseConn then releaseConn:Disconnect() end
            end
        end)
    end)

    table.insert(allCards, {Frame = card, Title = title:lower()})
end

local function refreshPlayerTpTab()
    for _, child in ipairs(pagePlayerTp:GetChildren()) do
        if child:IsA("Frame") then child:Destroy() end
    end

    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player then
            local card = Instance.new("Frame")
            card.Size = UDim2.new(1, -10, 0, 48)
            card.BackgroundColor3 = currentTheme.Panel
            card.Parent = pagePlayerTp

            local corner = Instance.new("UICorner")
            corner.CornerRadius = UDim.new(0, 10)
            corner.Parent = card

            local stroke = Instance.new("UIStroke")
            stroke.Color = currentTheme.Accent
            stroke.Thickness = 1
            stroke.Transparency = 0.85
            stroke.Parent = card

            local lbl = Instance.new("TextLabel")
            lbl.Size = UDim2.new(0.65, 0, 1, 0)
            lbl.Position = UDim2.new(0, 16, 0, 0)
            lbl.BackgroundTransparency = 1
            lbl.Text = p.DisplayName .. " (@" .. p.Name .. ")"
            lbl.TextColor3 = currentTheme.Text
            lbl.TextSize = 12
            lbl.FontFace = uiFontFamily
            lbl.TextXAlignment = Enum.TextXAlignment.Left
            lbl.Parent = card

            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(0, 96, 0, 26)
            btn.Position = UDim2.new(1, -106, 0.5, -13)
            btn.BackgroundColor3 = currentTheme.Accent
            btn.Text = "TELEPORT"
            btn.TextColor3 = currentTheme.BG
            btn.FontFace = uiFontBold
            btn.TextSize = 11
            btn.Parent = card

            local btnCorner = Instance.new("UICorner")
            btnCorner.CornerRadius = UDim.new(0, 8)
            btnCorner.Parent = btn

            btn.MouseButton1Click:Connect(function()
                if p.Character and p.Character:FindFirstChild("HumanoidRootPart") and rootPart then
                    rootPart.CFrame = p.Character.HumanoidRootPart.CFrame * CFrame.new(0, 2, 3)
                end
            end)
        end
    end
end

Players.PlayerAdded:Connect(refreshPlayerTpTab)
Players.PlayerRemoving:Connect(refreshPlayerTpTab)
refreshPlayerTpTab()

searchBox:GetPropertyChangedSignal("Text"):Connect(function()
    local query = searchBox.Text:lower()
    for _, item in ipairs(allCards) do
        if query == "" or item.Title:find(query) then
            item.Frame.Visible = true
        else
            item.Frame.Visible = false
        end
    end
end)

local bgForce = nil
buildToggleCard(pageFlight, "Enable Flight Mode", function(state)
    isFlying = state
    if isFlying then
        if humanoid then humanoid.PlatformStand = true end
        if rootPart then
            if not rootPart:FindFirstChild("BizzlesFlightForce") then
                bgForce = Instance.new("BodyVelocity")
                bgForce.Name = "BizzlesFlightForce"
                bgForce.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bgForce.Velocity = Vector3.new(0, 0, 0)
                bgForce.Parent = rootPart
            end
        end
        pcall(function()
            if not currentFlightAnimation and animator then
                local anim = Instance.new("Animation")
                anim.AnimationId = "rbxassetid://507768375"
                currentFlightAnimation = animator:LoadAnimation(anim)
            end
            if currentFlightAnimation then
                currentFlightAnimation:Play()
                currentFlightAnimation:AdjustSpeed(1)
            end
        end)
    else
        if humanoid then humanoid.PlatformStand = false end
        if rootPart then
            local f = rootPart:FindFirstChild("BizzlesFlightForce")
            if f then f:Destroy() end
        end
        if currentFlightAnimation then
            pcall(function() currentFlightAnimation:Stop() end)
        end
        if rootPart then
            rootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            if character then
                for _, p in ipairs(character:GetDescendants()) do
                    if p:IsA("BasePart") then p.CanCollide = true end
                end
            end
        end
    end
end)
buildSliderCard(pageFlight, "Flight Speed Studs", 10, 1000, flySpeed, function(val) flySpeed = val end)

buildToggleCard(pageSpeed, "Enable Custom WalkSpeed Override", function(state)
    isWalkSpeedActive = state
    if not isWalkSpeedActive and humanoid then humanoid.WalkSpeed = 16 end
end)
buildSliderCard(pageSpeed, "WalkSpeed Multiplier", 16, 500, currentWalkSpeed, function(val) currentWalkSpeed = val end)

buildToggleCard(pageSpeed, "Enable Jump Power Boost", function(state)
    isJumpBoostActive = state
    if not isJumpBoostActive and humanoid then humanoid.JumpPower = 50 end
end)
buildSliderCard(pageSpeed, "Jump Power Multiplier", 50, 500, currentJumpPower, function(val) currentJumpPower = val end)
buildToggleCard(pageSpeed, "Super Speed Bunny Hop", function(state) isSuperSpeedBunnyHopActive = state end)

buildToggleCard(pageUtils, "Infinite Jump Engine", function(state) isInfiniteJumpActive = state end)
buildToggleCard(pageUtils, "Manual Noclip Pass", function(state) isNoclipActive = state end)
buildToggleCard(pageUtils, "Ctrl + Click / Crosshair TP", function(state) isClickTpActive = state end)
buildToggleCard(pageUtils, "Spinbot / Anti-Aim", function(state) isSpinbotActive = state end)
buildToggleCard(pageUtils, "Anti-Void Fall Protector", function(state) isAntiVoidActive = state end)
buildToggleCard(pageUtils, "Ghost Mode (Full Transparency)", function(state)
    isGhostModeActive = state
    if character then
        for _, p in ipairs(character:GetDescendants()) do
            if p:IsA("BasePart") or p:IsA("MeshPart") then
                p.Transparency = isGhostModeActive and 0.85 or (p.Name == "HumanoidRootPart" and 1 or 0)
            elseif p:IsA("Accessory") then
                local handle = p:FindFirstChild("Handle")
                if handle then handle.Transparency = isGhostModeActive and 0.85 or 0 end
            end
        end
    end
end)

buildToggleCard(pageGod, "God Mode (Infinite Health Loop)", function(state) isGodModeActive = state end)
buildToggleCard(pageGod, "Anti-Ragdoll & Force State Lock", function(state) isAntiRagdollActive = state end)
buildToggleCard(pageGod, "Infinite Stamina / Energy Bypass", function(state) isInfiniteStaminaActive = state end)
buildToggleCard(pageGod, "Super Jump Height Booster", function(state) isSuperJumpActive = state end)
buildToggleCard(pageGod, "Invisible Arms / Sleeves", function(state)
    isInvisibleArmsActive = state
    if character then
        for _, part in ipairs(character:GetChildren()) do
            if part:IsA("BasePart") and (part.Name:find("Arm") or part.Name:find("Hand")) then
                part.Transparency = isInvisibleArmsActive and 1 or 0
            end
        end
    end
end)

buildToggleCard(pageMobFarm, "Mob Auto-Farm (Killaura)", function(state) isMobFarmActive = state end)
buildSliderCard(pageMobFarm, "Hover Height Above Mob", 2, 20, farmDistance, function(val) farmDistance = val end)
buildToggleCard(pageMobFarm, "Mob Magnet (Pull Target)", function(state) isMobMagnetActive = state end)
buildToggleCard(pageMobFarm, "Freeze Mobs In Place", function(state) isMobFreezeActive = state end)
buildToggleCard(pageMobFarm, "Auto-Loot Item Vacuum", function(state) isAutoLootActive = state end)
buildToggleCard(pageMobFarm, "Safe Farm (Low HP Retreat)", function(state) isSafeFarmActive = state end)
buildToggleCard(pageMobFarm, "Auto-Equip Weapon Tool", function(state) isAutoEquipActive = state end)
buildSliderCard(pageMobFarm, "Max Mob Target Radius", 50, 1000, mobTargetRadius, function(val) mobTargetRadius = val end)

local activeEspObjects = {}

buildToggleCard(pageMobVisuals, "Mob ESP Highlight Boxes", function(state)
    isEspActive = state
    if not state then
        for model, data in pairs(activeEspObjects) do
            if data.Highlight and not data.IsPlayer then data.Highlight:Destroy(); data.Highlight = nil end
        end
    end
end)
buildToggleCard(pageMobVisuals, "Player ESP Highlights", function(state)
    isPlayerEspActive = state
    if not state then
        for model, data in pairs(activeEspObjects) do
            if data.Highlight and data.IsPlayer then data.Highlight:Destroy(); data.Highlight = nil end
        end
    end
end)
buildToggleCard(pageMobVisuals, "Mob & Player Tracing Lines", function(state)
    isEspTracersActive = state
    if not state then
        for _, data in pairs(activeEspObjects) do
            if data.Tracer then data.Tracer:Remove(); data.Tracer = nil end
        end
    end
end)
buildToggleCard(pageMobVisuals, "Mob & Player Name Labels", function(state)
    isEspNamesActive = state
    if not state then
        for _, data in pairs(activeEspObjects) do
            if data.Billboard then data.Billboard:Destroy(); data.Billboard = nil end
        end
    end
end)
buildToggleCard(pageMobVisuals, "Mob Hitbox Expander", function(state)
    isHitboxActive = state
    if not isHitboxActive then
        for _, mob in ipairs(refreshMobCache()) do
            mob.RootPart.Size = Vector3.new(2, 2, 1)
            mob.RootPart.Transparency = 1
        end
    end
end)
buildSliderCard(pageMobVisuals, "Hitbox Expander Scale", 5, 60, hitboxSize, function(val) hitboxSize = val end)

buildToggleCard(pageExtra1, "Btools (Give Classic Building Tools)", function(state)
    isBtoolsActive = state
    if isBtoolsActive then
        local bp = player:FindFirstChild("Backpack")
        if bp then
            for _, toolName in ipairs({"Hammer", "Clone", "Delete"}) do
                if not bp:FindFirstChild(toolName) and player.Character and not player.Character:FindFirstChild(toolName) then
                    local tool = Instance.new("HopperBin")
                    tool.BinType = Enum.BinType[toolName == "Delete" and "Destructive" or (toolName == "Clone" and "Clone" or "Hammer")]
                    tool.Name = toolName
                    tool.Parent = bp
                end
            end
        end
    end
end)

buildToggleCard(pageExtra1, "Infinite Camera Zoom Distance", function(state)
    isInfiniteZoomActive = state
    player.CameraMaxZoomDistance = isInfiniteZoomActive and 999999 or 400
end)

buildToggleCard(pageExtra1, "Auto-Heal / Regeneration Loop", function(state) isAutoHealActive = state end)
buildSliderCard(pageExtra1, "Auto-Heal Health Threshold %", 10, 90, autoHealThreshold, function(val) autoHealThreshold = val end)
buildToggleCard(pageExtra1, "Time Freeze (Lock Lighting Clock)", function(state)
    isTimeFrozenActive = state
    if isTimeFrozenActive then customTime = Lighting.ClockTime end
end)
buildToggleCard(pageExtra1, "X-Ray Wall Vision", function(state)
    isXrayActive = state
    for _, part in ipairs(Workspace:GetDescendants()) do
        if part:IsA("BasePart") and not part:IsDescendantOf(character) then
            if isXrayActive then
                if part.Transparency < 0.5 and part.Name ~= "HumanoidRootPart" then
                    part.LocalTransparencyModifier = 0.5
                end
            else
                part.LocalTransparencyModifier = 0
            end
        end
    end
end)
buildToggleCard(pageExtra1, "Rainbow Character Glow", function(state) isRainbowCharacterActive = state end)
buildToggleCard(pageExtra1, "Client-Side Time Scale Multiplier", function(state) isTimeScaleActive = state end)
buildSliderCard(pageExtra1, "Time Scale Speed Factor", 1, 5, customTimeScale, function(val) customTimeScale = val end)

buildToggleCard(pageExtra2, "Auto-Collect Coins & Drops", function(state) isAutoCollectCoinsActive = state end)
buildToggleCard(pageExtra2, "Freeze All World NPCs & Dummies", function(state)
    isNpcFreezeActive = state
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("Model") and obj ~= character and not Players:GetPlayerFromCharacter(obj) then
            local hrp = obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChild("Torso")
            if hrp then hrp.Anchored = isNpcFreezeActive end
        end
    end
end)
buildToggleCard(pageExtra2, "Super Humanoid States (Never Fall Over)", function(state)
    isSuperHumanoidStateActive = state
    if humanoid then
        humanoid.BreakJointsOnDeath = not isSuperHumanoidStateActive
        humanoid:SetStateEnabled(Enum.HumanoidStateType.FallingDown, not isSuperHumanoidStateActive)
        humanoid:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, not isSuperHumanoidStateActive)
    end
end)
buildToggleCard(pageExtra2, "Chat Spammer Utility", function(state) isChatSpamActive = state end)
buildToggleCard(pageExtra2, "Platform Gun (Click to spawn platforms)", function(state) isPlatformGunActive = state end)
buildToggleCard(pageExtra2, "Proximity Prompt Instant Interactor", function(state) isProximityAuraActive = state end)

buildToggleCard(pageExtra3, "Auto-Clicker Tool Trigger", function(state) isAutoClickerActive = state end)
buildSliderCard(pageExtra3, "Auto-Clicker CPS", 1, 50, autoClickCps, function(val) autoClickCps = val end)
buildToggleCard(pageExtra3, "Auto-Farm Gems & Diamonds", function(state) isAutoFarmGemsActive = state end)
buildToggleCard(pageExtra3, "Auto-Open Chests / Crates", function(state) isAutoClickChestActive = state end)
buildToggleCard(pageExtra3, "Auto-Rebirth Loop Engine", function(state) isAutoRebirthActive = state end)
buildToggleCard(pageExtra3, "Auto-Buy Best Upgrades/Items", function(state) isAutoBuyBestActive = state end)
buildToggleCard(pageExtra3, "Player Orbit Circle Around Me", function(state) isPlayerOrbitsActive = state end)

buildToggleCard(pageExtra4, "Auto-Sell Inventory / Items", function(state) isAutoSellInventoryActive = state end)
buildToggleCard(pageExtra4, "Auto-Collect Free Gifts / Rewards", function(state) isAutoCollectGiftsActive = state end)
buildToggleCard(pageExtra4, "Mute All Other Players Audio", function(state)
    isMuteAllOtherPlayersAudioActive = state
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= player and p.Character then
            for _, sound in ipairs(p.Character:GetDescendants()) do
                if sound:IsA("Sound") then sound.Volume = isMuteAllOtherPlayersAudioActive and 0 or 0.5 end
            end
        end
    end
end)
buildToggleCard(pageExtra4, "Infinite Oxygen / Breath Under Water", function(state) isInfiniteOxygenActive = state end)
buildToggleCard(pageExtra4, "Instant Interact (Bypass Hold Times)", function(state) isInstantInteractActive = state end)
buildToggleCard(pageExtra4, "Auto-Open Cases / Mystery Boxes", function(state) isAutoOpenCasesActive = state end)
buildToggleCard(pageExtra4, "Auto-Farm Experience / XP Loop", function(state) isAutoFarmExpActive = state end)
buildToggleCard(pageExtra4, "Auto-Fish / Idle Fishing Loop", function(state) isAutoFishActive = state end)
buildToggleCard(pageExtra4, "Auto-Equip Best Gear / Tools", function(state) isAutoEquipBestToolsActive = state end)
buildToggleCard(pageExtra4, "Auto-Eat Food / Restore Hunger", function(state) isAutoEatFoodActive = state end)

buildButtonCard(pageWorld, "Rejoin Current Server", "REJOIN", function()
    TeleportService:Teleport(game.PlaceId, player)
end)

buildToggleCard(pageWorld, "Full Bright (Enhanced Night Vision)", function(state)
    isFullBrightActive = state
    if isFullBrightActive then
        Lighting.Brightness = 3
        Lighting.ClockTime = 14
        Lighting.GlobalShadows = false
        Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
        Lighting.Ambient = Color3.fromRGB(255, 255, 255)
    else
        Lighting.Brightness = 1
        Lighting.GlobalShadows = true
        Lighting.OutdoorAmbient = Color3.fromRGB(127, 127, 127)
        Lighting.Ambient = Color3.fromRGB(0, 0, 0)
    end
end)

buildToggleCard(pageWorld, "No Fog / Endless Clear Atmosphere", function(state)
    isNoFogActive = state
    if isNoFogActive then
        Lighting.FogEnd = 1000000
        Lighting.FogStart = 0
        for _, effect in ipairs(Lighting:GetChildren()) do
            if effect:IsA("Atmosphere") then
                effect.Density = 0
                effect.Haze = 0
                effect.Glare = 0
            end
        end
    end
end)

buildToggleCard(pageWorld, "Always Day (Lock Time to Noon)", function(state)
    isAlwaysDayActive = state
    if isAlwaysDayActive then Lighting.ClockTime = 14 end
end)

buildToggleCard(pageWorld, "FPS Saver (Low Graphics)", function(state)
    isFpsSaverActive = state
    Lighting.GlobalShadows = not isFpsSaverActive
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and isFpsSaverActive then
            obj.Material = Enum.Material.SmoothPlastic
        end
    end
end)

buildToggleCard(pageWorld, "Custom World Gravity Override", function(state)
    isCustomGravityActive = state
    if not isCustomGravityActive then Workspace.Gravity = 196.2 end
end)
buildSliderCard(pageWorld, "Gravity Value Scale", 0, 500, customGravityValue, function(val) customGravityValue = val end)

buildSliderCard(pageWorld, "Camera Field of View", 70, 120, 70, function(val)
    if camera then camera.FieldOfView = val end
end)

local themeCard = Instance.new("Frame")
themeCard.Size = UDim2.new(1, -10, 0, 310)
themeCard.BackgroundColor3 = currentTheme.Panel
themeCard.Parent = pageWorld

local themeCorner = Instance.new("UICorner")
themeCorner.CornerRadius = UDim.new(0, 10)
themeCorner.Parent = themeCard

local themeStroke = Instance.new("UIStroke")
themeStroke.Color = currentTheme.Accent
themeStroke.Thickness = 1
themeStroke.Transparency = 0.85
themeStroke.Parent = themeCard

local themeTitle = Instance.new("TextLabel")
themeTitle.Size = UDim2.new(1, -32, 0, 24)
themeTitle.Position = UDim2.new(0, 16, 0, 10)
themeTitle.BackgroundTransparency = 1
themeTitle.Text = "THEME ENGINE SELECTOR (16 STYLES)"
themeTitle.TextColor3 = currentTheme.Subtext
themeTitle.TextSize = 12
themeTitle.FontFace = uiFontBold
themeTitle.TextXAlignment = Enum.TextXAlignment.Left
themeTitle.Parent = themeCard

local function applyTheme(themeObj)
    currentTheme = themeObj
    espColor = themeObj.Accent
    
    tween(mainFrame, 0.3, {BackgroundColor3 = themeObj.BG})
    tween(sidebarFrame, 0.3, {BackgroundColor3 = themeObj.Panel})
    tween(mainStroke, 0.3, {Color = themeObj.Accent})
    tween(titleLabel, 0.3, {TextColor3 = themeObj.Accent})
    dropShadow.ImageColor3 = themeObj.Accent
    
    for _, tabData in ipairs(tabs) do
        if tabData.name == activeTab then
            tween(tabData.btn, 0.3, {BackgroundColor3 = themeObj.BG, TextColor3 = themeObj.Accent})
        end
    end
end

local function buildThemeBtn(name, themeObj, xPos, yPos)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.46, 0, 0, 30)
    btn.Position = UDim2.new(xPos, 0, 0, yPos)
    btn.BackgroundColor3 = themeObj.BG
    btn.Text = name
    btn.TextColor3 = themeObj.Accent
    btn.FontFace = uiFontBold
    btn.TextSize = 11
    btn.Parent = themeCard
    
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 8)
    c.Parent = btn
    
    local s = Instance.new("UIStroke")
    s.Color = themeObj.Accent
    s.Thickness = 1
    s.Transparency = 0.4
    s.Parent = btn

    btn.MouseButton1Click:Connect(function() applyTheme(themeObj) end)
end

buildThemeBtn("Cyber Neon", Themes.CyberNeon, 0.03, 38)
buildThemeBtn("Midnight Violet", Themes.MidnightViolet, 0.51, 38)
buildThemeBtn("Crimson Red", Themes.CrimsonRed, 0.03, 74)
buildThemeBtn("Electric Cyan", Themes.ElectricCyan, 0.51, 74)
buildThemeBtn("SunsetOrange", Themes.SunsetOrange, 0.03, 110)
buildThemeBtn("Matrix Emerald", Themes.MatrixEmerald, 0.51, 110)
buildThemeBtn("Royal Gold", Themes.RoyalGold, 0.03, 146)
buildThemeBtn("Bubblegum Pink", Themes.BubblegumPink, 0.51, 146)
buildThemeBtn("Toxic Lime", Themes.ToxicLime, 0.03, 182)
buildThemeBtn("Plasma Purple", Themes.PlasmaPurple, 0.51, 182)
buildThemeBtn("Solar Flare", Themes.SolarFlare, 0.03, 218)
buildThemeBtn("Deep Ocean", Themes.DeepOcean, 0.51, 218)
buildThemeBtn("Hacker Matrix", Themes.HackerMatrix, 0.03, 254)
buildThemeBtn("Amethyst Glow", Themes.AmethystGlow, 0.51, 254)
buildThemeBtn("NeonTeal", Themes.NeonTeal, 0.03, 290)
buildThemeBtn("Blood Moon", Themes.BloodMoon, 0.51, 290)

minBtn.MouseButton1Click:Connect(function()
    isMinimised = not isMinimised
    if isMinimised then
        contentArea.Visible = false
        sidebarFrame.Visible = false
        tween(mainFrame, 0.2, {Size = UDim2.new(0, 720, 0, 56)})
        minBtn.Text = "+"
    else
        tween(mainFrame, 0.2, {Size = UDim2.new(0, 720, 0, 500)})
        task.delay(0.15, function()
            contentArea.Visible = true
            sidebarFrame.Visible = true
        end)
        minBtn.Text = "—"
    end
end)

closeBtn.MouseButton1Click:Connect(function()
    if isFlying and humanoid then humanoid.PlatformStand = false end
    screenGui:Destroy()
end)

player.CharacterAdded:Connect(function(newCharacter)
    task.wait(0.3)
    updateCharacterReferences(newCharacter)
    currentFlightAnimation = nil
end)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == toggleKey then
        isGuiVisible = not isGuiVisible
        mainFrame.Visible = isGuiVisible
    end
    if input.KeyCode == Enum.KeyCode.Space and isInfiniteJumpActive and humanoid then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
    if (input.KeyCode == Enum.KeyCode.V or input.UserInputType == Enum.UserInputType.MouseButton1) and isClickTpActive then
        if input.KeyCode == Enum.KeyCode.V or UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
            if mouse.Hit and rootPart then
                rootPart.CFrame = CFrame.new(mouse.Hit.Position + Vector3.new(0, 3, 0))
            end
        end
    end
    if isPlatformGunActive and input.UserInputType == Enum.UserInputType.MouseButton1 and mouse.Hit then
        local plat = Instance.new("Part")
        plat.Size = Vector3.new(6, 1, 6)
        plat.Position = mouse.Hit.Position + Vector3.new(0, 0.5, 0)
        plat.Anchored = true
        plat.BrickColor = BrickColor.random()
        plat.Parent = Workspace
        task.delay(10, function() plat:Destroy() end)
    end
end)

player.Idled:Connect(function()
    if isAntiAfkActive then
        local VirtualUser = game:GetService("VirtualUser")
        VirtualUser:CaptureController()
        VirtualUser:ClickButton2(Vector2.new())
    end
end)

task.spawn(function()
    while true do
        if isAutoClickerActive then
            mouse1click()
            task.wait(1 / autoClickCps)
        else
            task.wait(0.1)
        end
    end
end)

task.spawn(function()
    while true do
        if isChatSpamActive then
            pcall(function()
                game:GetService("ReplicatedStorage"):FindFirstChild("DefaultChatSystemChatEvents", true):FindFirstChild("SayMessageRequest"):FireServer(chatSpamMessage, "All")
            end)
            task.wait(4)
        else
            task.wait(1)
        end
    end
end)

task.spawn(function()
    while true do
        task.wait(0.5)

        if isTimeFrozenActive then
            Lighting.ClockTime = customTime
        elseif isAlwaysDayActive then
            Lighting.ClockTime = 14
        end

        if isCustomGravityActive then
            Workspace.Gravity = customGravityValue
        end

        if isGodModeActive and humanoid then
            humanoid.Health = humanoid.MaxHealth
        end

        if isAntiVoidActive and rootPart and rootPart.Position.Y < -50 then
            rootPart.CFrame = CFrame.new(rootPart.Position.X, 50, rootPart.Position.Z)
            rootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
        end

        if isInstantInteractActive then
            for _, prompt in ipairs(Workspace:GetDescendants()) do
                if prompt:IsA("ProximityPrompt") then
                    prompt.HoldDuration = 0
                end
            end
        end

        if isProximityAuraActive and rootPart then
            for _, prompt in ipairs(Workspace:GetDescendants()) do
                if prompt:IsA("ProximityPrompt") then
                    local parentPart = prompt.Parent
                    if parentPart and parentPart:IsA("BasePart") then
                        if (rootPart.Position - parentPart.Position).Magnitude <= (prompt.MaxActivationDistance or 32) then
                            fireproximityprompt(prompt)
                        end
                    end
                end
            end
        end

        local mobs = refreshMobCache()

        if isPlayerEspActive then
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                    local pChar = p.Character
                    if not activeEspObjects[pChar] then activeEspObjects[pChar] = {IsPlayer = true} end
                    if not activeEspObjects[pChar].Highlight then
                        local hl = Instance.new("Highlight")
                        hl.FillColor = Color3.fromRGB(255, 170, 0)
                        hl.FillTransparency = 0.5
                        hl.Parent = pChar
                        activeEspObjects[pChar].Highlight = hl
                    end
                end
            end
        end

        for _, mob in ipairs(mobs) do
            if mob.Humanoid.Health > 0 then
                if isHitboxActive then
                    mob.RootPart.Size = Vector3.new(hitboxSize, hitboxSize, hitboxSize)
                    mob.RootPart.Transparency = 0.7
                    mob.RootPart.CanCollide = false
                end

                if isMobFreezeActive then mob.RootPart.Anchored = true end

                if isMobMagnetActive and rootPart then
                    mob.RootPart.CFrame = rootPart.CFrame * CFrame.new(0, 0, -5)
                end

                if isEspActive or isEspNamesActive then
                    if not activeEspObjects[mob.Model] then
                        activeEspObjects[mob.Model] = {IsPlayer = false}
                    end

                    if isEspActive and not activeEspObjects[mob.Model].Highlight then
                        local hl = Instance.new("Highlight")
                        hl.FillColor = espColor
                        hl.FillTransparency = 0.5
                        hl.Parent = mob.Model
                        activeEspObjects[mob.Model].Highlight = hl
                    end

                    if isEspNamesActive then
                        if not activeEspObjects[mob.Model].Billboard then
                            local bb = Instance.new("BillboardGui")
                            bb.Size = UDim2.new(0, 180, 0, 36)
                            bb.StudsOffset = Vector3.new(0, 3, 0)
                            bb.AlwaysOnTop = true
                            bb.Parent = mob.RootPart

                            local lbl = Instance.new("TextLabel")
                            lbl.Size = UDim2.new(1, 0, 1, 0)
                            lbl.BackgroundTransparency = 1
                            lbl.TextColor3 = espColor
                            lbl.FontFace = uiFontBold
                            lbl.TextSize = 12
                            lbl.Parent = bb
                            activeEspObjects[mob.Model].Billboard = bb
                        end
                        
                        local dist = rootPart and math.round((rootPart.Position - mob.RootPart.Position).Magnitude) or 0
                        local txt = activeEspObjects[mob.Model].Billboard:FindFirstChildOfClass("TextLabel")
                        if txt then
                            txt.Text = mob.Model.Name .. "\n[" .. tostring(math.round(mob.Humanoid.Health)) .. " HP] • " .. tostring(dist) .. "m"
                        end
                    end
                end
            end
        end

        if isAutoHealActive and humanoid then
            local currentHPPct = (humanoid.Health / humanoid.MaxHealth) * 100
            if currentHPPct <= autoHealThreshold then
                humanoid.Health = humanoid.MaxHealth
            end
        end

        if isAutoEquipActive and character and player:FindFirstChild("Backpack") then
            if not character:FindFirstChildOfClass("Tool") then
                local tool = player.Backpack:FindFirstChildOfClass("Tool")
                if tool then humanoid:EquipTool(tool) end
            end
        end

        if isMobFarmActive and #mobs > 0 and rootPart and humanoid then
            if isSafeFarmActive and (humanoid.Health / humanoid.MaxHealth) * 100 < safeHealthThreshold then
                rootPart.CFrame = rootPart.CFrame + Vector3.new(0, 100, 0)
            else
                local targetMob = mobs[1]
                rootPart.CFrame = targetMob.RootPart.CFrame * CFrame.new(0, farmDistance, 0) * CFrame.Angles(math.rad(-90), 0, 0)
            end
        end

        if (isAutoLootActive or isAutoCollectCoinsActive or isAutoFarmGemsActive) and rootPart then
            for _, drop in ipairs(Workspace:GetChildren()) do
                if drop:IsA("BasePart") or drop:IsA("MeshPart") then
                    local nameL = drop.Name:lower()
                    if nameL:find("coin") or nameL:find("drop") or nameL:find("item") or nameL:find("gem") or nameL:find("gold") or nameL:find("diamond") then
                        drop.CFrame = rootPart.CFrame
                    end
                end
            end
        end
    end
end)

local frameCount = 0
local lastStatUpdate = os.clock()

RunService.RenderStepped:Connect(function()
    frameCount = frameCount + 1
    if os.clock() - lastStatUpdate >= 1 then
        fpsLabel.Text = tostring(frameCount) .. " FPS"
        frameCount = 0
        lastStatUpdate = os.clock()
    end

    -- Instant No Fog Enforcement on every single frame
    if isNoFogActive then
        Lighting.FogEnd = 1000000
        Lighting.FogStart = 0
        for _, effect in ipairs(Lighting:GetChildren()) do
            if effect:IsA("Atmosphere") then
                effect.Density = 0
                effect.Haze = 0
                effect.Glare = 0
            end
        end
    end

    -- Instant Full Bright Enforcement on every single frame
    if isFullBrightActive then
        Lighting.Brightness = 3
        Lighting.GlobalShadows = false
        Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
        Lighting.Ambient = Color3.fromRGB(255, 255, 255)
    end

    local pulseAlpha = (math.sin(tick() * 3) + 1) / 2
    mainStroke.Transparency = 0.1 + (0.5 * pulseAlpha)
    dropShadow.ImageTransparency = 0.3 + (0.4 * pulseAlpha)

    if isRainbowCharacterActive and character then
        local hue = tick() % 5 / 5
        local rainbowColor = Color3.fromHSV(hue, 1, 1)
        for _, part in ipairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.Color = rainbowColor
            end
        end
    end

    if isTimeScaleActive and character then
        for _, anim in ipairs(character:GetDescendants()) do
            if anim:IsA("AnimationTrack") then
                anim:AdjustSpeed(customTimeScale)
            end
        end
    end

    if (isFlying or isNoclipActive) and character then
        for _, p in ipairs(character:GetDescendants()) do
            if p:IsA("BasePart") then p.CanCollide = false end
        end
    end

    if isSpinbotActive and rootPart then
        rootPart.CFrame = rootPart.CFrame * CFrame.Angles(0, math.rad(25), 0)
    end

    if isWalkSpeedActive and humanoid and rootPart then
        local moveDir = humanoid.MoveDirection
        if moveDir.Magnitude > 0 then
            rootPart.AssemblyLinearVelocity = Vector3.new(moveDir.X * currentWalkSpeed, rootPart.AssemblyLinearVelocity.Y, moveDir.Z * currentWalkSpeed)
        else
            rootPart.AssemblyLinearVelocity = Vector3.new(0, rootPart.AssemblyLinearVelocity.Y, 0)
        end
    end

    if isJumpBoostActive and humanoid then
        humanoid.UseJumpPower = true
        humanoid.JumpPower = currentJumpPower
    end

    if isSuperJumpActive and humanoid then
        humanoid.JumpPower = 350
    end

    if isSuperSpeedBunnyHopActive and humanoid and rootPart then
        if humanoid.FloorMaterial ~= Enum.Material.Air then
            humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
        end
    end

    if isEspTracersActive and camera then
        for modelObj, data in pairs(activeEspObjects) do
            local hrp = modelObj:FindFirstChild("HumanoidRootPart") or (modelObj:IsA("Model") and modelObj.PrimaryPart)
            if hrp then
                if not data.Tracer then
                    local line = Drawing.new("Line")
                    line.Thickness = 1.5
                    line.Color = espColor
                    data.Tracer = line
                end
                local vec, onScreen = camera:WorldToViewportPoint(hrp.Position)
                if onScreen then
                    data.Tracer.From = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y)
                    data.Tracer.To = Vector2.new(vec.X, vec.Y)
                    data.Tracer.Visible = true
                else
                    data.Tracer.Visible = false
                end
            end
        end
    end

    if isFlying and rootPart and camera then
        local moveDir = Vector3.new(0, 0, 0)
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + camera.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - camera.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + camera.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - camera.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0, 1, 0) end
        if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then moveDir = moveDir - Vector3.new(0, 1, 0) end

        local f = rootPart:FindFirstChild("BizzlesFlightForce")
        if not f then
            f = Instance.new("BodyVelocity")
            f.Name = "BizzlesFlightForce"
            f.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
            f.Parent = rootPart
        end

        if moveDir.Magnitude > 0 then
            f.Velocity = moveDir.Unit * flySpeed
        else
            f.Velocity = Vector3.new(0, 0, 0)
        end
        rootPart.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
    end
end)