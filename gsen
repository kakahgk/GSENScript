loadstring([[
-- GSEN辅助V22（任务栏集成飞行/移速/自瞄按钮，自瞄按钮不可拖动）
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local Camera = workspace.CurrentCamera
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

-- 自瞄设置
local aimbotEnabled = false
local aimbotSettings = {
    targetPart = "Head",
    wallCheck = true,
    smoothness = 1.0
}
local aimbotWindow = nil
local aimbotConnection = nil

-- 玩家数量显示
local playerCountEnabled = false
local playerCountGui = nil
local playerCountConnections = {}

-- ========== 任务栏（统一白色背景，固定最小化按钮 + 水平滚动区） ==========
local taskbarGui = Instance.new("ScreenGui")
taskbarGui.Name = "TaskbarGUI"
taskbarGui.ResetOnSpawn = false
taskbarGui.Parent = CoreGui
taskbarGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
taskbarGui.DisplayOrder = 1000001

-- 任务栏总容器（白色背景，圆角，底部左对齐，宽度625）
local taskbarContainer = Instance.new("Frame")
taskbarContainer.Size = UDim2.new(0, 625, 0, 50)
taskbarContainer.Position = UDim2.new(0, 0, 1, -50)
taskbarContainer.BackgroundColor3 = Color3.new(1, 1, 1)
taskbarContainer.BorderSizePixel = 0
taskbarContainer.Parent = taskbarGui

local containerCorner = Instance.new("UICorner")
containerCorner.CornerRadius = UDim.new(0.1, 0) -- 10%圆角
containerCorner.Parent = taskbarContainer

-- 固定最小化按钮（位于容器最左侧）
local taskbarMinBtn = Instance.new("TextButton")
taskbarMinBtn.Size = UDim2.new(0, 40, 0, 40)
taskbarMinBtn.Position = UDim2.new(0, 5, 0.5, -20)
taskbarMinBtn.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
taskbarMinBtn.Text = "-"
taskbarMinBtn.TextColor3 = Color3.new(0, 0, 0)
taskbarMinBtn.Font = Enum.Font.SourceSansBold
taskbarMinBtn.TextSize = 24
taskbarMinBtn.ZIndex = 20
taskbarMinBtn.Parent = taskbarContainer

local minBtnCorner = Instance.new("UICorner")
minBtnCorner.CornerRadius = UDim.new(0.2, 0)
minBtnCorner.Parent = taskbarMinBtn

-- 水平滚动区域（背景透明，宽度570，位置X=50）
local taskbarScroller = Instance.new("ScrollingFrame")
taskbarScroller.Size = UDim2.new(0, 570, 0, 50)
taskbarScroller.Position = UDim2.new(0, 50, 0, 0)
taskbarScroller.BackgroundTransparency = 1
taskbarScroller.BorderSizePixel = 0
taskbarScroller.ScrollBarThickness = 8
taskbarScroller.ScrollBarImageColor3 = Color3.fromRGB(150, 150, 150)
taskbarScroller.ScrollBarImageTransparency = 0.3
taskbarScroller.ScrollingDirection = Enum.ScrollingDirection.X
taskbarScroller.ElasticBehavior = Enum.ElasticBehavior.Never
taskbarScroller.CanvasSize = UDim2.new(0, 400, 0, 0) -- 内容宽度设为400，容纳三个按钮
taskbarScroller.Parent = taskbarContainer

-- 滚动内容区
local taskbarContent = Instance.new("Frame")
taskbarContent.Size = UDim2.new(0, 400, 1, 0)
taskbarContent.BackgroundColor3 = Color3.new(1, 1, 1)
taskbarContent.BorderSizePixel = 0
taskbarContent.Parent = taskbarScroller

-- 飞行控制按钮（任务栏）
local taskbarFlyBtn = Instance.new("TextButton")
taskbarFlyBtn.Size = UDim2.new(0, 80, 0, 40)
taskbarFlyBtn.Position = UDim2.new(0, 5, 0.5, -20)
taskbarFlyBtn.BackgroundColor3 = Color3.fromRGB(100, 150, 200)
taskbarFlyBtn.Text = "飞行"
taskbarFlyBtn.TextColor3 = Color3.new(1, 1, 1)
taskbarFlyBtn.Font = Enum.Font.SourceSansBold
taskbarFlyBtn.TextSize = 16
taskbarFlyBtn.Parent = taskbarContent
local flyBtnCorner = Instance.new("UICorner")
flyBtnCorner.CornerRadius = UDim.new(0.2, 0)
flyBtnCorner.Parent = taskbarFlyBtn

-- 移速控制按钮（任务栏）
local taskbarWalkBtn = Instance.new("TextButton")
taskbarWalkBtn.Size = UDim2.new(0, 80, 0, 40)
taskbarWalkBtn.Position = UDim2.new(0, 95, 0.5, -20)
taskbarWalkBtn.BackgroundColor3 = Color3.fromRGB(100, 150, 200)
taskbarWalkBtn.Text = "移速"
taskbarWalkBtn.TextColor3 = Color3.new(1, 1, 1)
taskbarWalkBtn.Font = Enum.Font.SourceSansBold
taskbarWalkBtn.TextSize = 16
taskbarWalkBtn.Parent = taskbarContent
local walkBtnCorner = Instance.new("UICorner")
walkBtnCorner.CornerRadius = UDim.new(0.2, 0)
walkBtnCorner.Parent = taskbarWalkBtn

-- 自瞄控制按钮（任务栏，红色，不可拖动）
local taskbarAimBtn = Instance.new("TextButton")
taskbarAimBtn.Size = UDim2.new(0, 80, 0, 40)
taskbarAimBtn.Position = UDim2.new(0, 185, 0.5, -20)
taskbarAimBtn.BackgroundColor3 = Color3.fromRGB(220, 80, 80) -- 红色
taskbarAimBtn.Text = "自瞄"
taskbarAimBtn.TextColor3 = Color3.new(1, 1, 1)
taskbarAimBtn.Font = Enum.Font.SourceSansBold
taskbarAimBtn.TextSize = 16
taskbarAimBtn.Parent = taskbarContent
-- 注意：没有添加拖动逻辑，因此不可拖动
local aimBtnCorner = Instance.new("UICorner")
aimBtnCorner.CornerRadius = UDim.new(0.2, 0)
aimBtnCorner.Parent = taskbarAimBtn

-- 迷你任务栏按钮（最小化时显示）
local taskbarMini = Instance.new("TextButton")
taskbarMini.Size = UDim2.new(0, 40, 0, 40)
taskbarMini.Position = UDim2.new(0, 10, 1, -50)
taskbarMini.BackgroundColor3 = Color3.fromRGB(200, 200, 200)
taskbarMini.Text = "□"
taskbarMini.TextColor3 = Color3.new(0, 0, 0)
taskbarMini.Font = Enum.Font.SourceSansBold
taskbarMini.TextSize = 24
taskbarMini.Visible = false
taskbarMini.ZIndex = 1000
taskbarMini.Parent = taskbarGui

local miniCorner = Instance.new("UICorner")
miniCorner.CornerRadius = UDim.new(0.2, 0)
miniCorner.Parent = taskbarMini

-- 迷你按钮拖动
local miniDragging = false
local miniDragStart, miniPanelStartPos
taskbarMini.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        miniDragging = true
        miniDragStart = input.Position
        miniPanelStartPos = taskbarMini.Position
    end
end)
taskbarMini.InputChanged:Connect(function(input)
    if miniDragging and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement) then
        local delta = input.Position - miniDragStart
        taskbarMini.Position = UDim2.new(
            miniPanelStartPos.X.Scale,
            miniPanelStartPos.X.Offset + delta.X,
            miniPanelStartPos.Y.Scale,
            miniPanelStartPos.Y.Offset + delta.Y
        )
    end
end)
taskbarMini.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        miniDragging = false
    end
end)

-- 任务栏最小化/恢复
taskbarMinBtn.MouseButton1Click:Connect(function()
    taskbarContainer.Visible = false
    taskbarMini.Visible = true
end)
taskbarMini.MouseButton1Click:Connect(function()
    taskbarContainer.Visible = true
    taskbarMini.Visible = false
end)

-- ========== 创建主GUI ==========
local gui = Instance.new("ScreenGui")
gui.Name = "MultiToolGUI"
gui.ResetOnSpawn = false
gui.Parent = CoreGui
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.DisplayOrder = 999999

-- 主窗口（350x340）
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 350, 0, 340)
mainFrame.Position = UDim2.new(0.5, -175, 0.5, -170)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = false
mainFrame.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 8)
corner.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 35)
title.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
title.Text = "GSEN辅助V22"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 22
title.Parent = mainFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 8)
titleCorner.Parent = title

-- 标题条拖动区域
local titleDragBar = Instance.new("Frame")
titleDragBar.Size = UDim2.new(1, -70, 0, 35)
titleDragBar.Position = UDim2.new(0, 0, 0, 0)
titleDragBar.BackgroundTransparency = 1
titleDragBar.Active = true
titleDragBar.Parent = mainFrame

local titleDragging = false
local titleDragStart, titlePanelStartPos
titleDragBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        titleDragging = true
        titleDragStart = input.Position
        titlePanelStartPos = mainFrame.Position
    end
end)
titleDragBar.InputChanged:Connect(function(input)
    if titleDragging and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement) then
        local delta = input.Position - titleDragStart
        mainFrame.Position = UDim2.new(
            titlePanelStartPos.X.Scale,
            titlePanelStartPos.X.Offset + delta.X,
            titlePanelStartPos.Y.Scale,
            titlePanelStartPos.Y.Offset + delta.Y
        )
    end
end)
titleDragBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        titleDragging = false
    end
end)

-- 主GUI最小化按钮
local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Size = UDim2.new(0, 35, 0, 35)
minimizeBtn.Position = UDim2.new(1, -70, 0, -1)
minimizeBtn.BackgroundColor3 = Color3.new(255, 255, 255)
minimizeBtn.BackgroundTransparency = 1
minimizeBtn.BorderSizePixel = 0
minimizeBtn.Text = "-"
minimizeBtn.TextColor3 = Color3.new(1, 1, 1)
minimizeBtn.Font = Enum.Font.SourceSansBold
minimizeBtn.TextSize = 28
minimizeBtn.ZIndex = 5
minimizeBtn.Parent = mainFrame

-- 关闭按钮
local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 35, 0, 35)
closeBtn.Position = UDim2.new(1, -35, 0, 0)
closeBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.BackgroundTransparency = 1
closeBtn.Text = "X"
closeBtn.TextColor3 = Color3.new(1, 1, 1)
closeBtn.Font = Enum.Font.SourceSansBold
closeBtn.TextSize = 22
closeBtn.ZIndex = 5
closeBtn.Parent = mainFrame

-- 迷你GSEN按钮
local gsenBtn = Instance.new("TextButton")
gsenBtn.Size = UDim2.new(0, 80, 0, 80)
gsenBtn.Position = UDim2.new(1, -90, 1, -90)
gsenBtn.BackgroundColor3 = Color3.new(0, 0, 0)
gsenBtn.BackgroundTransparency = 0
gsenBtn.Text = "GSEN"
gsenBtn.TextColor3 = Color3.new(1, 1, 1)
gsenBtn.Font = Enum.Font.SourceSansBold
gsenBtn.TextSize = 20
gsenBtn.Visible = false
gsenBtn.ZIndex = 999
gsenBtn.Parent = gui

local gsenCorner = Instance.new("UICorner")
gsenCorner.CornerRadius = UDim.new(0.25, 0)
gsenCorner.Parent = gsenBtn

-- GSEN迷你按钮拖动
local gsenDragging = false
local gsenDragStart, gsenPanelStartPos
gsenBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        gsenDragging = true
        gsenDragStart = input.Position
        gsenPanelStartPos = gsenBtn.Position
    end
end)
gsenBtn.InputChanged:Connect(function(input)
    if gsenDragging and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement) then
        local delta = input.Position - gsenDragStart
        gsenBtn.Position = UDim2.new(
            gsenPanelStartPos.X.Scale,
            gsenPanelStartPos.X.Offset + delta.X,
            gsenPanelStartPos.Y.Scale,
            gsenPanelStartPos.Y.Offset + delta.Y
        )
    end
end)
gsenBtn.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        gsenDragging = false
    end
end)

-- 主GUI最小化/恢复
minimizeBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
    gsenBtn.Visible = true
end)
gsenBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = true
    gsenBtn.Visible = false
end)

-- 关闭按钮（安全销毁）
closeBtn.MouseButton1Click:Connect(function()
    pcall(function()
        if disableESP then disableESP() end
        if disableAntenna then disableAntenna() end
        if aimbotConnection then aimbotConnection:Disconnect() end
        if aimbotWindow then aimbotWindow:Destroy() end
        if playerCountGui then playerCountGui:Destroy() end
        if gsenBtn then gsenBtn:Destroy() end
        if taskbarGui then taskbarGui:Destroy() end
        if gui then gui:Destroy() end
    end)
end)

-- 内容区域（滚动容器）
local content = Instance.new("Frame")
content.Size = UDim2.new(1, -20, 1, -45)
content.Position = UDim2.new(0, 10, 0, 40)
content.BackgroundTransparency = 1
content.Parent = mainFrame

local scrollingFrame = Instance.new("ScrollingFrame")
scrollingFrame.Size = UDim2.new(1, 0, 1, 0)
scrollingFrame.BackgroundTransparency = 1
scrollingFrame.ScrollBarThickness = 6
scrollingFrame.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)
scrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
scrollingFrame.ScrollingDirection = Enum.ScrollingDirection.Y
scrollingFrame.ElasticBehavior = Enum.ElasticBehavior.Always
scrollingFrame.Parent = content

local listFrame = Instance.new("Frame")
listFrame.Size = UDim2.new(1, -40, 0, 0)
listFrame.BackgroundTransparency = 1
listFrame.Parent = scrollingFrame

local uiListLayout = Instance.new("UIListLayout")
uiListLayout.SortOrder = Enum.SortOrder.LayoutOrder
uiListLayout.Padding = UDim.new(0, 0)
uiListLayout.Parent = listFrame

local function updateCanvasSize()
    scrollingFrame.CanvasSize = UDim2.new(0, 0, 0, uiListLayout.AbsoluteContentSize.Y)
end
uiListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(updateCanvasSize)
task.spawn(updateCanvasSize)

-- 主开关按钮函数
local function createToggle(name, desc, parent, callback)
    local bg = Instance.new("Frame")
    bg.Size = UDim2.new(1, -40, 0, 45)
    bg.BackgroundTransparency = 1
    bg.Parent = parent

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.65, 0, 1, 0)
    label.Position = UDim2.new(0.05, 0, 0, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.new(1, 1, 1)
    label.TextXAlignment = "Left"
    label.Font = Enum.Font.SourceSans
    label.TextSize = 16
    if desc then 
        label.TextSize = 14 
        label.RichText = true
        label.Text = name .. "\n<font size=\"12\">" .. desc .. "</font>"
    else
        label.Text = name
    end
    label.Parent = bg

    local toggle = Instance.new("TextButton")
    toggle.Size = UDim2.new(0, 50, 0, 22)
    toggle.Position = UDim2.new(1, -50, 0.5, -11)
    toggle.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    toggle.Text = "OFF"
    toggle.TextColor3 = Color3.new(1, 1, 1)
    toggle.Font = Enum.Font.SourceSansBold
    toggle.TextSize = 14
    toggle.Parent = bg

    local enabled = false
    toggle.MouseButton1Click:Connect(function()
        enabled = not enabled
        toggle.BackgroundColor3 = enabled and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(80, 80, 80)
        toggle.Text = enabled and "ON" or "OFF"
        callback(enabled)
    end)

    return bg
end

-- ========== 信息按钮 ==========
local infoBg = Instance.new("Frame")
infoBg.Size = UDim2.new(1, -40, 0, 45)
infoBg.BackgroundTransparency = 1
infoBg.Parent = listFrame
infoBg.LayoutOrder = 0

local infoLabel = Instance.new("TextLabel")
infoLabel.Size = UDim2.new(0.65, 0, 1, 0)
infoLabel.Position = UDim2.new(0.05, 0, 0, 0)
infoLabel.BackgroundTransparency = 1
infoLabel.Text = "信息"
infoLabel.TextColor3 = Color3.new(1, 1, 1)
infoLabel.TextXAlignment = "Left"
infoLabel.Font = Enum.Font.SourceSans
infoLabel.TextSize = 16
infoLabel.Parent = infoBg

local infoBtn = Instance.new("TextButton")
infoBtn.Size = UDim2.new(0, 50, 0, 22)
infoBtn.Position = UDim2.new(1, -50, 0.5, -11)
infoBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
infoBtn.Text = "信息"
infoBtn.TextColor3 = Color3.new(1, 1, 1)
infoBtn.Font = Enum.Font.SourceSansBold
infoBtn.TextSize = 14
infoBtn.ZIndex = 5
infoBtn.Parent = infoBg
local infoCorner = Instance.new("UICorner")
infoCorner.CornerRadius = UDim.new(0, 6)
infoCorner.Parent = infoBtn

local infoWindow = nil
infoBtn.MouseButton1Click:Connect(function()
    if infoWindow and infoWindow.Parent then return end
    local infoGui = Instance.new("ScreenGui")
    infoGui.Name = "PlayerInfoGUI"
    infoGui.ResetOnSpawn = false
    infoGui.Parent = CoreGui
    infoGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    infoGui.DisplayOrder = 999999

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 280, 0, 280)
    frame.Position = UDim2.new(0.5, -140, 0.5, -140)
    frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    frame.BorderSizePixel = 0
    frame.Active = true
    frame.Draggable = true
    frame.Parent = infoGui
    local fCorner = Instance.new("UICorner")
    fCorner.CornerRadius = UDim.new(0, 10)
    fCorner.Parent = frame

    local titleBar = Instance.new("Frame")
    titleBar.Size = UDim2.new(1, -35, 0, 30)
    titleBar.Position = UDim2.new(0, 0, 0, 0)
    titleBar.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    titleBar.BorderSizePixel = 0
    titleBar.Parent = frame
    local titleBarCorner = Instance.new("UICorner")
    titleBarCorner.CornerRadius = UDim.new(0, 10)
    titleBarCorner.Parent = titleBar

    local titleText = Instance.new("TextLabel")
    titleText.Size = UDim2.new(1, -10, 1, 0)
    titleText.Position = UDim2.new(0, 5, 0, 0)
    titleText.BackgroundTransparency = 1
    titleText.Text = "玩家信息"
    titleText.TextColor3 = Color3.new(1, 1, 1)
    titleText.Font = Enum.Font.SourceSansBold
    titleText.TextSize = 18
    titleText.TextXAlignment = "Left"
    titleText.Parent = titleBar

    local closeInfoBtn = Instance.new("TextButton")
    closeInfoBtn.Size = UDim2.new(0, 30, 0, 30)
    closeInfoBtn.Position = UDim2.new(1, -30, 0, 0)
    closeInfoBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    closeInfoBtn.BackgroundTransparency = 1
    closeInfoBtn.Text = "X"
    closeInfoBtn.TextColor3 = Color3.new(1, 1, 1)
    closeInfoBtn.Font = Enum.Font.SourceSansBold
    closeInfoBtn.TextSize = 22
    closeInfoBtn.Parent = frame
    closeInfoBtn.MouseButton1Click:Connect(function()
        infoGui:Destroy()
        infoWindow = nil
    end)

    local avatarFrame = Instance.new("Frame")
    avatarFrame.Size = UDim2.new(0, 80, 0, 80)
    avatarFrame.Position = UDim2.new(0.5, -40, 0, 50)
    avatarFrame.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    avatarFrame.BorderSizePixel = 0
    avatarFrame.Parent = frame
    local avatarCorner = Instance.new("UICorner")
    avatarCorner.CornerRadius = UDim.new(1, 0)
    avatarCorner.Parent = avatarFrame

    local avatarImage = Instance.new("ImageLabel")
    avatarImage.Size = UDim2.new(1, 0, 1, 0)
    avatarImage.BackgroundTransparency = 1
    avatarImage.Parent = avatarFrame
    avatarImage.Image = "rbxasset://textures/ui/avatar/emptyavatar.png"

    spawn(function()
        local success, image = pcall(function()
            return Players:GetUserThumbnailAsync(player.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size420x420)
        end)
        if success and image then
            avatarImage.Image = image
        end
    end)

    local displayNameLabel = Instance.new("TextLabel")
    displayNameLabel.Size = UDim2.new(1, -20, 0, 30)
    displayNameLabel.Position = UDim2.new(0, 10, 0, 140)
    displayNameLabel.BackgroundTransparency = 1
    displayNameLabel.Text = "昵称: " .. (player.DisplayName or "未知")
    displayNameLabel.TextColor3 = Color3.new(1, 1, 1)
    displayNameLabel.Font = Enum.Font.SourceSans
    displayNameLabel.TextSize = 18
    displayNameLabel.TextXAlignment = "Left"
    displayNameLabel.Parent = frame

    local userNameLabel = Instance.new("TextLabel")
    userNameLabel.Size = UDim2.new(1, -20, 0, 30)
    userNameLabel.Position = UDim2.new(0, 10, 0, 170)
    userNameLabel.BackgroundTransparency = 1
    userNameLabel.Text = "用户名: @" .. (player.Name or "未知")
    userNameLabel.TextColor3 = Color3.new(1, 1, 1)
    userNameLabel.Font = Enum.Font.SourceSans
    userNameLabel.TextSize = 18
    userNameLabel.TextXAlignment = "Left"
    userNameLabel.Parent = frame

    local thanks1 = Instance.new("TextLabel")
    thanks1.Size = UDim2.new(1, -20, 0, 30)
    thanks1.Position = UDim2.new(0, 10, 0, 210)
    thanks1.BackgroundTransparency = 1
    thanks1.Text = "感谢支持我们脚本"
    thanks1.TextColor3 = Color3.new(1, 1, 1)
    thanks1.Font = Enum.Font.SourceSans
    thanks1.TextSize = 16
    thanks1.TextXAlignment = "Left"
    thanks1.Parent = frame

    local thanks2 = Instance.new("TextLabel")
    thanks2.Size = UDim2.new(1, -20, 0, 30)
    thanks2.Position = UDim2.new(0, 10, 0, 235)
    thanks2.BackgroundTransparency = 1
    thanks2.Text = "作者 GSEN"
    thanks2.TextColor3 = Color3.new(1, 1, 1)
    thanks2.Font = Enum.Font.SourceSans
    thanks2.TextSize = 16
    thanks2.TextXAlignment = "Left"
    thanks2.Parent = frame

    local thanks3 = Instance.new("TextLabel")
    thanks3.Size = UDim2.new(1, -20, 0, 30)
    thanks3.Position = UDim2.new(0, 10, 0, 260)
    thanks3.BackgroundTransparency = 1
    thanks3.Text = "技术支持 DeepSeek"
    thanks3.TextColor3 = Color3.new(1, 1, 1)
    thanks3.Font = Enum.Font.SourceSans
    thanks3.TextSize = 16
    thanks3.TextXAlignment = "Left"
    thanks3.Parent = frame

    infoWindow = infoGui
end)

-- ========== 移速控制面板 ==========
local walkSpeed = 16
if character then
    local hum = character:FindFirstChildOfClass("Humanoid")
    if hum then walkSpeed = hum.WalkSpeed end
end
local walkPanelVisible = false
local walkPanel = Instance.new("Frame")
walkPanel.Size = UDim2.new(0, 240, 0, 140)
walkPanel.Position = UDim2.new(1, -250, 1, -150)
walkPanel.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
walkPanel.BorderSizePixel = 0
walkPanel.Visible = false
walkPanel.ZIndex = 10
walkPanel.Parent = gui
local wpCorner = Instance.new("UICorner")
wpCorner.CornerRadius = UDim.new(0, 10)
wpCorner.Parent = walkPanel

local wpDragBar = Instance.new("Frame")
wpDragBar.Size = UDim2.new(1, 0, 0, 20)
wpDragBar.Position = UDim2.new(0, 0, 0, 0)
wpDragBar.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
wpDragBar.BackgroundTransparency = 0
wpDragBar.BorderSizePixel = 0
wpDragBar.ZIndex = 11
wpDragBar.Parent = walkPanel
local wpDragBarCorner = Instance.new("UICorner")
wpDragBarCorner.CornerRadius = UDim.new(0, 6)
wpDragBarCorner.Parent = wpDragBar

local wpTitle = Instance.new("TextLabel")
wpTitle.Size = UDim2.new(1, -30, 0, 20)
wpTitle.Position = UDim2.new(0, 0, 0, 0)
wpTitle.BackgroundTransparency = 1
wpTitle.Text = "移速控制"
wpTitle.TextColor3 = Color3.new(0, 0, 0)
wpTitle.Font = Enum.Font.SourceSansBold
wpTitle.TextSize = 16
wpTitle.ZIndex = 12
wpTitle.Parent = wpDragBar

local wpMinimizeBtn = Instance.new("TextButton")
wpMinimizeBtn.Size = UDim2.new(0, 30, 0, 20)
wpMinimizeBtn.Position = UDim2.new(1, -30, -0.1, 0)
wpMinimizeBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
wpMinimizeBtn.BackgroundTransparency = 1
wpMinimizeBtn.Text = "-"
wpMinimizeBtn.TextColor3 = Color3.new(0, 0, 0)
wpMinimizeBtn.Font = Enum.Font.SourceSansBold
wpMinimizeBtn.TextSize = 20
wpMinimizeBtn.ZIndex = 13
wpMinimizeBtn.Parent = wpDragBar
local wpMinCorner = Instance.new("UICorner")
wpMinCorner.CornerRadius = UDim.new(0, 4)
wpMinCorner.Parent = wpMinimizeBtn

local wpDragging = false
local wpDragStart, wpPanelStartPos
wpDragBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        wpDragging = true
        wpDragStart = input.Position
        wpPanelStartPos = walkPanel.Position
    end
end)
wpDragBar.InputChanged:Connect(function(input)
    if wpDragging and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement) then
        local delta = input.Position - wpDragStart
        walkPanel.Position = UDim2.new(
            wpPanelStartPos.X.Scale,
            wpPanelStartPos.X.Offset + delta.X,
            wpPanelStartPos.Y.Scale,
            wpPanelStartPos.Y.Offset + delta.Y
        )
    end
end)
wpDragBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        wpDragging = false
    end
end)

local wpSpeedValue = Instance.new("TextBox")
wpSpeedValue.Size = UDim2.new(0, 80, 0, 30)
wpSpeedValue.Position = UDim2.new(0.5, -40, 0, 40)
wpSpeedValue.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
wpSpeedValue.Text = tostring(walkSpeed)
wpSpeedValue.TextColor3 = Color3.new(1, 1, 1)
wpSpeedValue.Font = Enum.Font.SourceSansBold
wpSpeedValue.TextSize = 18
wpSpeedValue.ZIndex = 12
wpSpeedValue.ClearTextOnFocus = false
wpSpeedValue.Parent = walkPanel
local wpValCorner = Instance.new("UICorner")
wpValCorner.CornerRadius = UDim.new(0, 6)
wpValCorner.Parent = wpSpeedValue

local function changeWalkSpeed(delta)
    walkSpeed = math.clamp(walkSpeed + delta, 1, 10000)
    wpSpeedValue.Text = tostring(walkSpeed)
    local char = player.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then hum.WalkSpeed = walkSpeed end
    end
end

local wpPlus10 = Instance.new("TextButton")
wpPlus10.Size = UDim2.new(0, 40, 0, 30)
wpPlus10.Position = UDim2.new(0, 20, 0, 80)
wpPlus10.BackgroundColor3 = Color3.fromRGB(0, 120, 200)
wpPlus10.Text = "+10"
wpPlus10.TextColor3 = Color3.new(1, 1, 1)
wpPlus10.Font = Enum.Font.SourceSansBold
wpPlus10.TextSize = 18
wpPlus10.ZIndex = 12
wpPlus10.Parent = walkPanel
wpPlus10.MouseButton1Click:Connect(function() changeWalkSpeed(10) end)
local wpPlus10Corner = Instance.new("UICorner")
wpPlus10Corner.CornerRadius = UDim.new(0, 6)
wpPlus10Corner.Parent = wpPlus10

local wpMinus10 = Instance.new("TextButton")
wpMinus10.Size = UDim2.new(0, 40, 0, 30)
wpMinus10.Position = UDim2.new(0, 70, 0, 80)
wpMinus10.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
wpMinus10.Text = "-10"
wpMinus10.TextColor3 = Color3.new(1, 1, 1)
wpMinus10.Font = Enum.Font.SourceSansBold
wpMinus10.TextSize = 18
wpMinus10.ZIndex = 12
wpMinus10.Parent = walkPanel
wpMinus10.MouseButton1Click:Connect(function() changeWalkSpeed(-10) end)
local wpMinus10Corner = Instance.new("UICorner")
wpMinus10Corner.CornerRadius = UDim.new(0, 6)
wpMinus10Corner.Parent = wpMinus10

local wpPlus100 = Instance.new("TextButton")
wpPlus100.Size = UDim2.new(0, 40, 0, 30)
wpPlus100.Position = UDim2.new(0, 130, 0, 80)
wpPlus100.BackgroundColor3 = Color3.fromRGB(0, 120, 200)
wpPlus100.Text = "+100"
wpPlus100.TextColor3 = Color3.new(1, 1, 1)
wpPlus100.Font = Enum.Font.SourceSansBold
wpPlus100.TextSize = 18
wpPlus100.ZIndex = 12
wpPlus100.Parent = walkPanel
wpPlus100.MouseButton1Click:Connect(function() changeWalkSpeed(100) end)
local wpPlus100Corner = Instance.new("UICorner")
wpPlus100Corner.CornerRadius = UDim.new(0, 6)
wpPlus100Corner.Parent = wpPlus100

local wpMinus100 = Instance.new("TextButton")
wpMinus100.Size = UDim2.new(0, 40, 0, 30)
wpMinus100.Position = UDim2.new(0, 180, 0, 80)
wpMinus100.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
wpMinus100.Text = "-100"
wpMinus100.TextColor3 = Color3.new(1, 1, 1)
wpMinus100.Font = Enum.Font.SourceSansBold
wpMinus100.TextSize = 18
wpMinus100.ZIndex = 12
wpMinus100.Parent = walkPanel
wpMinus100.MouseButton1Click:Connect(function() changeWalkSpeed(-100) end)
local wpMinus100Corner = Instance.new("UICorner")
wpMinus100Corner.CornerRadius = UDim.new(0, 6)
wpMinus100Corner.Parent = wpMinus100

wpSpeedValue.FocusLost:Connect(function()
    local val = tonumber(wpSpeedValue.Text)
    if val then
        walkSpeed = math.clamp(val, 1, 10000)
        wpSpeedValue.Text = tostring(walkSpeed)
        local char = player.Character
        if char then
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hum then hum.WalkSpeed = walkSpeed end
        end
    else
        wpSpeedValue.Text = tostring(walkSpeed)
    end
end)

-- ========== 飞行控制面板 ==========
local controlPanel = Instance.new("Frame")
controlPanel.Size = UDim2.new(0, 240, 0, 160)
controlPanel.Position = UDim2.new(1, -250, 1, -170)
controlPanel.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
controlPanel.BorderSizePixel = 0
controlPanel.Visible = false
controlPanel.ZIndex = 10
controlPanel.Parent = gui
local cpCorner = Instance.new("UICorner")
cpCorner.CornerRadius = UDim.new(0, 10)
cpCorner.Parent = controlPanel

local dragBar = Instance.new("Frame")
dragBar.Size = UDim2.new(1, 0, 0, 20)
dragBar.Position = UDim2.new(0, 0, 0, 0)
dragBar.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dragBar.BackgroundTransparency = 0
dragBar.BorderSizePixel = 0
dragBar.ZIndex = 11
dragBar.Parent = controlPanel
local dragBarCorner = Instance.new("UICorner")
dragBarCorner.CornerRadius = UDim.new(0, 6)
dragBarCorner.Parent = dragBar

local cpTitle = Instance.new("TextLabel")
cpTitle.Size = UDim2.new(1, -30, 0, 20)
cpTitle.Position = UDim2.new(0, 0, 0, 0)
cpTitle.BackgroundTransparency = 1
cpTitle.Text = "飞行控制"
cpTitle.TextColor3 = Color3.new(0, 0, 0)
cpTitle.Font = Enum.Font.SourceSansBold
cpTitle.TextSize = 16
cpTitle.ZIndex = 12
cpTitle.Parent = dragBar

local minimizeBtnFly = Instance.new("TextButton")
minimizeBtnFly.Size = UDim2.new(0, 30, 0, 20)
minimizeBtnFly.Position = UDim2.new(1, -30, -0.1, 0)
minimizeBtnFly.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
minimizeBtnFly.BackgroundTransparency = 1
minimizeBtnFly.Text = "-"
minimizeBtnFly.TextColor3 = Color3.new(0, 0, 0)
minimizeBtnFly.Font = Enum.Font.SourceSansBold
minimizeBtnFly.TextSize = 20
minimizeBtnFly.ZIndex = 13
minimizeBtnFly.Parent = dragBar
local minCornerFly = Instance.new("UICorner")
minCornerFly.CornerRadius = UDim.new(0, 4)
minCornerFly.Parent = minimizeBtnFly

local draggingFly = false
local dragStartFly, panelStartPosFly
dragBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        draggingFly = true
        dragStartFly = input.Position
        panelStartPosFly = controlPanel.Position
    end
end)
dragBar.InputChanged:Connect(function(input)
    if draggingFly and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement) then
        local delta = input.Position - dragStartFly
        controlPanel.Position = UDim2.new(
            panelStartPosFly.X.Scale,
            panelStartPosFly.X.Offset + delta.X,
            panelStartPosFly.Y.Scale,
            panelStartPosFly.Y.Offset + delta.Y
        )
    end
end)
dragBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        draggingFly = false
    end
end)

local speedLabel = Instance.new("TextLabel")
speedLabel.Size = UDim2.new(0, 60, 0, 30)
speedLabel.Position = UDim2.new(0, 10, 0, 30)
speedLabel.BackgroundTransparency = 1
speedLabel.Text = "速度"
speedLabel.TextColor3 = Color3.new(1, 1, 1)
speedLabel.TextSize = 16
speedLabel.ZIndex = 11
speedLabel.Parent = controlPanel

local speedValue = Instance.new("TextLabel")
speedValue.Size = UDim2.new(0, 50, 0, 30)
speedValue.Position = UDim2.new(0, 70, 0, 30)
speedValue.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
speedValue.Text = "50"
speedValue.TextColor3 = Color3.new(1, 1, 1)
speedValue.TextSize = 18
speedValue.ZIndex = 11
speedValue.Parent = controlPanel
local valCorner = Instance.new("UICorner")
valCorner.CornerRadius = UDim.new(0, 6)
valCorner.Parent = speedValue

local speed = 50
local function changeSpeed(delta)
    speed = math.clamp(speed + delta, 10, 10000)
    speedValue.Text = tostring(speed)
end

local plus10Btn = Instance.new("TextButton")
plus10Btn.Size = UDim2.new(0, 40, 0, 30)
plus10Btn.Position = UDim2.new(0, 135, 0, 30)
plus10Btn.BackgroundColor3 = Color3.fromRGB(0, 120, 200)
plus10Btn.Text = "+10"
plus10Btn.TextColor3 = Color3.new(1, 1, 1)
plus10Btn.Font = Enum.Font.SourceSansBold
plus10Btn.TextSize = 18
plus10Btn.ZIndex = 12
plus10Btn.Parent = controlPanel
plus10Btn.MouseButton1Click:Connect(function() changeSpeed(10) end)
local plus10Corner = Instance.new("UICorner")
plus10Corner.CornerRadius = UDim.new(0, 6)
plus10Corner.Parent = plus10Btn

local minus10Btn = Instance.new("TextButton")
minus10Btn.Size = UDim2.new(0, 40, 0, 30)
minus10Btn.Position = UDim2.new(0, 185, 0, 30)
minus10Btn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
minus10Btn.Text = "-10"
minus10Btn.TextColor3 = Color3.new(1, 1, 1)
minus10Btn.Font = Enum.Font.SourceSansBold
minus10Btn.TextSize = 18
minus10Btn.ZIndex = 12
minus10Btn.Parent = controlPanel
minus10Btn.MouseButton1Click:Connect(function() changeSpeed(-10) end)
local minus10Corner = Instance.new("UICorner")
minus10Corner.CornerRadius = UDim.new(0, 6)
minus10Corner.Parent = minus10Btn

local plus100Btn = Instance.new("TextButton")
plus100Btn.Size = UDim2.new(0, 40, 0, 30)
plus100Btn.Position = UDim2.new(0, 135, 0, 80)
plus100Btn.BackgroundColor3 = Color3.fromRGB(0, 120, 200)
plus100Btn.Text = "+100"
plus100Btn.TextColor3 = Color3.new(1, 1, 1)
plus100Btn.Font = Enum.Font.SourceSansBold
plus100Btn.TextSize = 18
plus100Btn.ZIndex = 12
plus100Btn.Parent = controlPanel
plus100Btn.MouseButton1Click:Connect(function() changeSpeed(100) end)
local plus100Corner = Instance.new("UICorner")
plus100Corner.CornerRadius = UDim.new(0, 6)
plus100Corner.Parent = plus100Btn

local minus100Btn = Instance.new("TextButton")
minus100Btn.Size = UDim2.new(0, 40, 0, 30)
minus100Btn.Position = UDim2.new(0, 185, 0, 80)
minus100Btn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
minus100Btn.Text = "-100"
minus100Btn.TextColor3 = Color3.new(1, 1, 1)
minus100Btn.Font = Enum.Font.SourceSansBold
minus100Btn.TextSize = 18
minus100Btn.ZIndex = 12
minus100Btn.Parent = controlPanel
minus100Btn.MouseButton1Click:Connect(function() changeSpeed(-100) end)
local minus100Corner = Instance.new("UICorner")
minus100Corner.CornerRadius = UDim.new(0, 6)
minus100Corner.Parent = minus100Btn

-- ========== 飞行功能 ==========
local flying = false
local flyConnection

local function updateFly()
    if not flying then return end
    local char = player.Character
    if not char then return end
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    local root = char:FindFirstChild("HumanoidRootPart") or char.PrimaryPart
    if not humanoid or not root then return end

    if not humanoid.PlatformStand then
        humanoid.PlatformStand = true
    end
    humanoid.AutoRotate = false

    local cam = workspace.CurrentCamera
    local camCFrame = cam.CFrame
    root.CFrame = CFrame.new(root.Position) * (camCFrame - camCFrame.Position)

    local moveWorld = humanoid.MoveDirection
    local velocity = Vector3.new()

    if moveWorld.Magnitude > 0.1 then
        local localDir = camCFrame:VectorToObjectSpace(moveWorld)
        local forward = -localDir.Z
        local sideways = localDir.X
        velocity = (camCFrame.LookVector * forward + camCFrame.RightVector * sideways) * speed
    end

    root.Velocity = velocity
end

-- 飞行开关
createToggle("飞行", "摇杆控制全方向飞行", listFrame, function(v)
    flying = v
    local char = player.Character
    if not char then return end
    local humanoid = char:FindFirstChildOfClass("Humanoid")
    local root = char:FindFirstChild("HumanoidRootPart")
    if v then
        if humanoid then
            humanoid.AutoRotate = false
            humanoid.PlatformStand = true
        end
        if flyConnection then flyConnection:Disconnect() end
        flyConnection = RunService.Heartbeat:Connect(updateFly)
        controlPanel.Visible = true
    else
        if flyConnection then flyConnection:Disconnect() end
        if humanoid then
            humanoid.AutoRotate = true
            humanoid.PlatformStand = false
        end
        if root then root.Velocity = Vector3.new() end
        controlPanel.Visible = false
    end
    -- 更新任务栏飞行按钮可见性
    taskbarFlyBtn.Visible = flying
end)

-- 飞行面板最小化按钮：仅隐藏面板
minimizeBtnFly.MouseButton1Click:Connect(function()
    if flying then
        controlPanel.Visible = false
    end
end)

-- 任务栏飞行按钮：切换面板显示/隐藏（如果飞行功能开启）
taskbarFlyBtn.MouseButton1Click:Connect(function()
    if flying then
        controlPanel.Visible = not controlPanel.Visible
    end
end)

-- 移速开关
local walkToggleEnabled = false
createToggle("移速", "地面移动速度调节", listFrame, function(v)
    walkToggleEnabled = v
    if v then
        walkPanel.Visible = true
        local char = player.Character
        if char then
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hum then hum.WalkSpeed = walkSpeed end
        end
    else
        walkPanel.Visible = false
        local char = player.Character
        if char then
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hum then hum.WalkSpeed = 16 end
        end
    end
    -- 更新任务栏移速按钮可见性
    taskbarWalkBtn.Visible = walkToggleEnabled
end)

-- 移速面板最小化按钮：仅隐藏面板
wpMinimizeBtn.MouseButton1Click:Connect(function()
    if walkToggleEnabled then
        walkPanel.Visible = false
    end
end)

-- 任务栏移速按钮：切换面板显示/隐藏（如果移速功能开启）
taskbarWalkBtn.MouseButton1Click:Connect(function()
    if walkToggleEnabled then
        walkPanel.Visible = not walkPanel.Visible
    end
end)

-- 初始隐藏任务栏按钮（等待对应开关开启）
taskbarFlyBtn.Visible = false
taskbarWalkBtn.Visible = false

-- ========== 自瞄功能（任务栏按钮控制，不可拖动） ==========
local function createAimbotWindow()
    if aimbotWindow and aimbotWindow.Parent then return end

    local winGui = Instance.new("ScreenGui")
    winGui.Name = "AimbotGUI"
    winGui.ResetOnSpawn = false
    winGui.Parent = CoreGui
    winGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    winGui.DisplayOrder = 999999

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 250, 0, 180)
    frame.Position = UDim2.new(0.5, -125, 0.5, -90)
    frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    frame.BorderSizePixel = 0
    frame.Active = true
    frame.Draggable = true
    frame.Parent = winGui
    local fCorner = Instance.new("UICorner")
    fCorner.CornerRadius = UDim.new(0, 10)
    fCorner.Parent = frame

    local titleBar = Instance.new("Frame")
    titleBar.Size = UDim2.new(1, -35, 0, 30)
    titleBar.Position = UDim2.new(0, 0, 0, 0)
    titleBar.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    titleBar.BorderSizePixel = 0
    titleBar.Parent = frame
    local titleBarCorner = Instance.new("UICorner")
    titleBarCorner.CornerRadius = UDim.new(0, 10)
    titleBarCorner.Parent = titleBar

    local titleText = Instance.new("TextLabel")
    titleText.Size = UDim2.new(1, -10, 1, 0)
    titleText.Position = UDim2.new(0, 5, 0, 0)
    titleText.BackgroundTransparency = 1
    titleText.Text = "自瞄设置"
    titleText.TextColor3 = Color3.new(1, 1, 1)
    titleText.Font = Enum.Font.SourceSansBold
    titleText.TextSize = 18
    titleText.TextXAlignment = "Left"
    titleText.Parent = titleBar

    local closeWinBtn = Instance.new("TextButton")
    closeWinBtn.Size = UDim2.new(0, 30, 0, 30)
    closeWinBtn.Position = UDim2.new(1, -30, 0, 0)
    closeWinBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    closeWinBtn.BackgroundTransparency = 1
    closeWinBtn.Text = "X"
    closeWinBtn.TextColor3 = Color3.new(1, 1, 1)
    closeWinBtn.Font = Enum.Font.SourceSansBold
    closeWinBtn.TextSize = 22
    closeWinBtn.Parent = frame
    closeWinBtn.MouseButton1Click:Connect(function()
        winGui:Destroy()
        aimbotWindow = nil
    end)

    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -20, 1, -40)
    contentFrame.Position = UDim2.new(0, 10, 0, 40)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = frame

    local partLabel = Instance.new("TextLabel")
    partLabel.Size = UDim2.new(1, 0, 0, 30)
    partLabel.Position = UDim2.new(0, 0, 0, 0)
    partLabel.BackgroundTransparency = 1
    partLabel.Text = "锁定部位:"
    partLabel.TextColor3 = Color3.new(1, 1, 1)
    partLabel.Font = Enum.Font.SourceSans
    partLabel.TextSize = 16
    partLabel.TextXAlignment = "Left"
    partLabel.Parent = contentFrame

    local headBtn = Instance.new("TextButton")
    headBtn.Size = UDim2.new(0.5, -5, 0, 30)
    headBtn.Position = UDim2.new(0, 0, 0, 30)
    headBtn.BackgroundColor3 = aimbotSettings.targetPart == "Head" and Color3.fromRGB(0, 120, 200) or Color3.fromRGB(80, 80, 80)
    headBtn.Text = "头部"
    headBtn.TextColor3 = Color3.new(1, 1, 1)
    headBtn.Font = Enum.Font.SourceSansBold
    headBtn.TextSize = 16
    headBtn.Parent = contentFrame
    local headCorner = Instance.new("UICorner")
    headCorner.CornerRadius = UDim.new(0, 6)
    headCorner.Parent = headBtn

    local bodyBtn = Instance.new("TextButton")
    bodyBtn.Size = UDim2.new(0.5, -5, 0, 30)
    bodyBtn.Position = UDim2.new(0.5, 5, 0, 30)
    bodyBtn.BackgroundColor3 = aimbotSettings.targetPart == "Body" and Color3.fromRGB(0, 120, 200) or Color3.fromRGB(80, 80, 80)
    bodyBtn.Text = "身体"
    bodyBtn.TextColor3 = Color3.new(1, 1, 1)
    bodyBtn.Font = Enum.Font.SourceSansBold
    bodyBtn.TextSize = 16
    bodyBtn.Parent = contentFrame
    local bodyCorner = Instance.new("UICorner")
    bodyCorner.CornerRadius = UDim.new(0, 6)
    bodyCorner.Parent = bodyBtn

    headBtn.MouseButton1Click:Connect(function()
        aimbotSettings.targetPart = "Head"
        headBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 200)
        bodyBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    end)
    bodyBtn.MouseButton1Click:Connect(function()
        aimbotSettings.targetPart = "Body"
        bodyBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 200)
        headBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 80)
    end)

    local wallCheckBtn = Instance.new("TextButton")
    wallCheckBtn.Size = UDim2.new(0, 30, 0, 30)
    wallCheckBtn.Position = UDim2.new(0, 0, 0, 70)
    wallCheckBtn.BackgroundColor3 = aimbotSettings.wallCheck and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(80, 80, 80)
    wallCheckBtn.Text = aimbotSettings.wallCheck and "✓" or ""
    wallCheckBtn.TextColor3 = Color3.new(1, 1, 1)
    wallCheckBtn.Font = Enum.Font.SourceSansBold
    wallCheckBtn.TextSize = 20
    wallCheckBtn.Parent = contentFrame
    local wallCorner = Instance.new("UICorner")
    wallCorner.CornerRadius = UDim.new(0, 6)
    wallCorner.Parent = wallCheckBtn

    local wallLabel = Instance.new("TextLabel")
    wallLabel.Size = UDim2.new(1, -40, 0, 30)
    wallLabel.Position = UDim2.new(0, 40, 0, 70)
    wallLabel.BackgroundTransparency = 1
    wallLabel.Text = "墙体检测 (不穿墙)"
    wallLabel.TextColor3 = Color3.new(1, 1, 1)
    wallLabel.Font = Enum.Font.SourceSans
    wallLabel.TextSize = 16
    wallLabel.TextXAlignment = "Left"
    wallLabel.Parent = contentFrame

    wallCheckBtn.MouseButton1Click:Connect(function()
        aimbotSettings.wallCheck = not aimbotSettings.wallCheck
        wallCheckBtn.BackgroundColor3 = aimbotSettings.wallCheck and Color3.fromRGB(0, 170, 0) or Color3.fromRGB(80, 80, 80)
        wallCheckBtn.Text = aimbotSettings.wallCheck and "✓" or ""
    end)

    aimbotWindow = winGui
end

local function aimbotUpdate()
    if not aimbotEnabled then return end
    local camPos = Camera.CFrame.Position
    local bestTargetPos = nil
    local bestDist = math.huge
    local screenCenter = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)

    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= player then
            local char = plr.Character
            if char then
                local part = aimbotSettings.targetPart == "Head" and char:FindFirstChild("Head") or char:FindFirstChild("HumanoidRootPart")
                if part then
                    local screenPos, onScreen = Camera:WorldToViewportPoint(part.Position)
                    if onScreen and screenPos.Z > 0 then
                        if aimbotSettings.wallCheck then
                            local ray = Ray.new(camPos, (part.Position - camPos).Unit * (part.Position - camPos).Magnitude)
                            local hit, hitPos, normal, material = workspace:FindPartOnRay(ray, player.Character, false, true)
                            if hit and not hit:IsDescendantOf(plr.Character) then
                                continue
                            end
                        end
                        local screenVec = Vector2.new(screenPos.X, screenPos.Y)
                        local dist = (screenVec - screenCenter).Magnitude
                        if dist < bestDist then
                            bestDist = dist
                            bestTargetPos = part.Position
                        end
                    end
                end
            end
        end
    end

    if bestTargetPos then
        Camera.CFrame = CFrame.lookAt(camPos, bestTargetPos)
    end
end

-- 自瞄开关
createToggle("自瞄", "开启自瞄设置（相机对准）", listFrame, function(v)
    aimbotEnabled = v
    if v then
        createAimbotWindow()
        if aimbotWindow then
            aimbotWindow.Visible = true
        end
        if not aimbotConnection then
            aimbotConnection = RunService.RenderStepped:Connect(aimbotUpdate)
        end
    else
        if aimbotWindow then
            aimbotWindow:Destroy()
            aimbotWindow = nil
        end
        if aimbotConnection then
            aimbotConnection:Disconnect()
            aimbotConnection = nil
        end
    end
    -- 更新任务栏自瞄按钮可见性
    taskbarAimBtn.Visible = aimbotEnabled
end)

-- 任务栏自瞄按钮：切换自瞄窗口显示/隐藏（如果自瞄功能开启）
taskbarAimBtn.MouseButton1Click:Connect(function()
    if aimbotEnabled then
        if aimbotWindow then
            aimbotWindow.Visible = not aimbotWindow.Visible
        else
            createAimbotWindow()
            if aimbotWindow then
                aimbotWindow.Visible = true
            end
        end
    end
end)

-- 初始隐藏自瞄按钮（等待开关开启）
taskbarAimBtn.Visible = false

-- ========== 穿墙功能 ==========
local noclip = false
local noclipConnection
createToggle("穿墙", "穿过任何实体", listFrame, function(v)
    noclip = v
    local char = player.Character
    if noclipConnection then noclipConnection:Disconnect() end
    if v then
        noclipConnection = RunService.Stepped:Connect(function()
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    else
        if char then
            for _, part in ipairs(char:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end)

-- ========== 透视功能 ==========
local espEnabled = false
local espHighlights = {}
local espConnections = {}

local function addESP(plr)
    if plr == player then return end
    local char = plr.Character
    if not char then return end
    if espHighlights[plr] then
        espHighlights[plr]:Destroy()
        espHighlights[plr] = nil
    end
    local highlight = Instance.new("Highlight")
    highlight.FillColor = Color3.new(1, 0, 0)
    highlight.OutlineColor = Color3.new(1, 1, 1)
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0
    highlight.Parent = char
    espHighlights[plr] = highlight
end

local function removeESP(plr)
    if espHighlights[plr] then
        espHighlights[plr]:Destroy()
        espHighlights[plr] = nil
    end
end

local function enableESP()
    espEnabled = true
    for _, plr in ipairs(Players:GetPlayers()) do
        addESP(plr)
    end
    espConnections.PlayerAdded = Players.PlayerAdded:Connect(function(plr)
        if espEnabled then
            addESP(plr)
            espConnections[plr] = plr.CharacterAdded:Connect(function(char)
                addESP(plr)
            end)
            plr.CharacterRemoving:Connect(function()
                removeESP(plr)
            end)
        end
    end)
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= player then
            espConnections[plr] = plr.CharacterAdded:Connect(function(char)
                addESP(plr)
            end)
            plr.CharacterRemoving:Connect(function()
                removeESP(plr)
            end)
        end
    end
end

local function disableESP()
    espEnabled = false
    for _, conn in pairs(espConnections) do
        if conn and conn.Disconnect then
            conn:Disconnect()
        end
    end
    espConnections = {}
    for plr, highlight in pairs(espHighlights) do
        if highlight then
            highlight:Destroy()
        end
    end
    espHighlights = {}
end

createToggle("透视", "红色高亮显示其他玩家", listFrame, function(v)
    if v then
        enableESP()
    else
        disableESP()
    end
end)

-- ========== 天线功能 ==========
local antennaEnabled = false
local antennaRenderConnection = nil
local antennaDrawings = {}
local antennaConnections = {}

local function createAntennaForPlayer(plr)
    if plr == player then return end
    if antennaDrawings[plr] then return end
    local line = Drawing.new("Line")
    line.Thickness = 2
    line.Color = Color3.new(1, 0, 0)
    line.Transparency = 1
    line.Visible = false
    antennaDrawings[plr] = line
end

local function destroyAntennaForPlayer(plr)
    if antennaDrawings[plr] then
        antennaDrawings[plr]:Remove()
        antennaDrawings[plr] = nil
    end
end

local function updateAntennas()
    if not antennaEnabled then return end
    local viewportSize = Camera.ViewportSize
    local centerX = viewportSize.X / 2
    local topY = 0
    for plr, line in pairs(antennaDrawings) do
        local char = plr.Character
        local part = char and (char:FindFirstChild("Head") or char:FindFirstChild("HumanoidRootPart"))
        if part then
            local screenPos, onScreen = Camera:WorldToViewportPoint(part.Position)
            if screenPos.Z > 0 then
                line.Visible = true
                line.From = Vector2.new(screenPos.X, screenPos.Y)
                line.To = Vector2.new(centerX, topY)
            else
                line.Visible = false
            end
        else
            line.Visible = false
        end
    end
end

local function enableAntenna()
    antennaEnabled = true
    for _, plr in ipairs(Players:GetPlayers()) do
        createAntennaForPlayer(plr)
    end
    antennaConnections.PlayerAdded = Players.PlayerAdded:Connect(function(plr)
        createAntennaForPlayer(plr)
    end)
    antennaConnections.PlayerRemoving = Players.PlayerRemoving:Connect(function(plr)
        destroyAntennaForPlayer(plr)
    end)
    if not antennaRenderConnection then
        antennaRenderConnection = RunService.RenderStepped:Connect(updateAntennas)
    end
end

local function disableAntenna()
    antennaEnabled = false
    for _, conn in pairs(antennaConnections) do
        if conn and conn.Disconnect then conn:Disconnect() end
    end
    antennaConnections = {}
    for plr, line in pairs(antennaDrawings) do
        line:Remove()
    end
    antennaDrawings = {}
    if antennaRenderConnection then
        antennaRenderConnection:Disconnect()
        antennaRenderConnection = nil
    end
end

createToggle("天线", "玩家头顶红色天线指向屏幕上方", listFrame, function(v)
    if v then enableAntenna() else disableAntenna() end
end)

-- ========== 玩家数量显示功能 ==========
local function createPlayerCountDisplay()
    if playerCountGui and playerCountGui.Parent then return end

    local displayGui = Instance.new("ScreenGui")
    displayGui.Name = "PlayerCountGUI"
    displayGui.ResetOnSpawn = false
    displayGui.Parent = CoreGui
    displayGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    displayGui.DisplayOrder = 1000000

    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(0, 120, 0, 30)
    bar.Position = UDim2.new(0.5, -60, 0, -50)
    bar.BackgroundColor3 = Color3.new(1, 1, 1)
    bar.BorderSizePixel = 0
    bar.Parent = displayGui

    local barCorner = Instance.new("UICorner")
    barCorner.CornerRadius = UDim.new(0.5, 0)
    barCorner.Parent = bar

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.new(0, 0, 0)
    label.Font = Enum.Font.SourceSansBold
    label.TextSize = 18
    label.Text = "在线: " .. #Players:GetPlayers()
    label.Parent = bar

    task.wait(0.5)
    local absPos = bar.AbsolutePosition
    print("白条绝对位置 Y =", absPos.Y, "屏幕高度 =", Camera.ViewportSize.Y)

    local function updateCount()
        label.Text = "在线: " .. #Players:GetPlayers()
    end

    playerCountConnections.PlayerAdded = Players.PlayerAdded:Connect(updateCount)
    playerCountConnections.PlayerRemoving = Players.PlayerRemoving:Connect(updateCount)

    playerCountGui = displayGui
end

createToggle("玩家数量", "屏幕顶部显示在线人数", listFrame, function(v)
    playerCountEnabled = v
    if v then
        createPlayerCountDisplay()
    else
        if playerCountGui then
            playerCountGui:Destroy()
            playerCountGui = nil
        end
        for _, conn in pairs(playerCountConnections) do
            if conn and conn.Disconnect then
                conn:Disconnect()
            end
        end
        playerCountConnections = {}
    end
end)

-- ========== 角色重生处理 ==========
player.CharacterAdded:Connect(function(newChar)
    character = newChar
    if flying then
        local humanoid = newChar:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.AutoRotate = false
            humanoid.PlatformStand = true
        end
        -- 确保飞行面板可见性根据当前状态
        if controlPanel.Visible then
            controlPanel.Visible = true
        end
    end
    local hum = newChar:FindFirstChildOfClass("Humanoid")
    if hum then
        if walkToggleEnabled then
            hum.WalkSpeed = walkSpeed
        else
            hum.WalkSpeed = 16
        end
    end
end)

print("GSEN辅助V22（任务栏集成飞行/移速/自瞄按钮，自瞄按钮不可拖动）已加载！速度上限10000。")
]])()
