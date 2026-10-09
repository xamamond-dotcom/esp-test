--// Universal Mobile-Optimized ESP with Collapsible Control Panel
--// Works on: PC, Mobile, Tablet | Executors: Synapse, Delta, Arceus X, Fluxus, Codex, etc.

--// ============ SERVICES ============
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

--// ============ MOBILE DETECTION ============
local IS_MOBILE = UserInputService.TouchEnabled and not UserInputService.KeyboardEnabled
local IS_LOW_END = IS_MOBILE or (UserInputService.GamepadEnabled and not UserInputService.KeyboardEnabled)

--// ============ CONFIG ============
local CONFIG = {
    Enabled = true,
    TeamCheck = true,
    MaxDistance = IS_MOBILE and 500 or 1000,
    ShowName = not IS_MOBILE,
    ShowDistance = true,
    ShowHealth = true,
    ShowBox = true,
    ShowHealthBar = true,
    UpdateInterval = IS_MOBILE and (1/30) or (1/60),
    BoxColor = Color3.fromRGB(255, 255, 255),
    HealthBarBg = Color3.fromRGB(0, 0, 0),
    TextColor = Color3.fromRGB(255, 255, 255),
    HealthBarWidth = IS_MOBILE and 4 or 3,
    HealthBarGap = IS_MOBILE and 3 or 4,
    FontSize = IS_MOBILE and 12 or 14,
    TextSize = IS_MOBILE and 11 or 13,
    WallCheck = false,
    MaxRenderCount = IS_MOBILE and 10 or 30,
}

--// ============ RENDERER DETECTION ============
local RENDERER = "none"
local Drawing_new = nil

do
    if typeof(Drawing) == "table" and typeof(Drawing.new) == "function" then
        Drawing_new = Drawing.new
        RENDERER = "drawing"
    elseif typeof(syn) == "table" and syn.Drawing then
        Drawing_new = syn.Drawing.new
        RENDERER = "drawing"
    elseif typeof(cleardrawcache) == "function" and Drawing then
        Drawing_new = Drawing.new
        RENDERER = "drawing"
    end
end

local USE_BILLBOARD = (RENDERER == "none")

print(string.format("[ESP] Renderer: %s | Mobile: %s",
    USE_BILLBOARD and "BillboardGui" or "Drawing",
    tostring(IS_MOBILE)
))

--// ============ DRAWING OBJECTS ============
local function createDrawingObject()
    if not Drawing_new then return nil end
    return {
        Box = Drawing_new("Square"),
        HealthBarBg = Drawing_new("Square"),
        HealthBarFill = Drawing_new("Square"),
        Name = Drawing_new("Text"),
        Distance = Drawing_new("Text"),
        HealthText = Drawing_new("Text"),
        _initialized = false,
    }
end

local function initDrawing(d)
    if d._initialized then return end
    d.Box.Thickness = IS_MOBILE and 2 or 1
    d.Box.Filled = false
    d.Box.Transparency = 1
    d.HealthBarBg.Filled = true
    d.HealthBarBg.Transparency = 0.6
    d.HealthBarFill.Filled = true
    d.HealthBarFill.Transparency = 0.9
    d.Name.Size = CONFIG.FontSize
    d.Name.Center = true
    d.Name.Outline = true
    d.Name.Font = 2
    d.Distance.Size = CONFIG.TextSize
    d.Distance.Center = true
    d.Distance.Outline = true
    d.Distance.Font = 2
    d.HealthText.Size = CONFIG.TextSize
    d.HealthText.Center = true
    d.HealthText.Outline = true
    d.HealthText.Font = 2
    d._initialized = true
end

local function destroyDrawing(d)
    if not d then return end
    for k, v in pairs(d) do
        if k ~= "_initialized" and v and typeof(v) == "userdata" then
            pcall(function() v:Remove() end)
        end
    end
end

local function setDrawingVisible(d, visible)
    d.Box.Visible = visible and CONFIG.ShowBox
    d.HealthBarBg.Visible = visible and CONFIG.ShowHealthBar
    d.HealthBarFill.Visible = visible and CONFIG.ShowHealthBar
    d.Name.Visible = visible and CONFIG.ShowName
    d.Distance.Visible = visible and CONFIG.ShowDistance
    d.HealthText.Visible = visible and CONFIG.ShowHealth
end

--// ============ BILLBOARD FALLBACK ============
local function createBillboard(player, character)
    local head = character:FindFirstChild("Head")
    if not head then return nil end
    
    local gui = Instance.new("BillboardGui")
    gui.Name = "ESP_Billboard"
    gui.Adornee = head
    gui.AlwaysOnTop = true
    gui.LightInfluence = 0
    gui.Size = UDim2.new(0, 100, 0, 60)
    gui.StudsOffsetWorldSpace = Vector3.new(0, 2.5, 0)
    gui.MaxDistance = CONFIG.MaxDistance
    
    local container = Instance.new("Frame")
    container.BackgroundTransparency = 1
    container.Size = UDim2.fromScale(1, 1)
    container.Parent = gui
    
    local barBg = Instance.new("Frame")
    barBg.Name = "BarBg"
    barBg.BackgroundColor3 = CONFIG.HealthBarBg
    barBg.BackgroundTransparency = 0.4
    barBg.BorderSizePixel = 0
    barBg.Size = UDim2.new(0, 4, 0.9, 0)
    barBg.Position = UDim2.new(0, 0, 0.05, 0)
    barBg.Parent = container
    
    local barFill = Instance.new("Frame")
    barFill.Name = "BarFill"
    barFill.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    barFill.BorderSizePixel = 0
    barFill.AnchorPoint = Vector2.new(0, 1)
    barFill.Size = UDim2.new(1, 0, 1, 0)
    barFill.Position = UDim2.new(0, 0, 1, 0)
    barFill.Parent = barBg
    
    local nameLabel = Instance.new("TextLabel")
    nameLabel.Name = "NameLabel"
    nameLabel.BackgroundTransparency = 1
    nameLabel.TextColor3 = CONFIG.TextColor
    nameLabel.TextStrokeTransparency = 0
    nameLabel.TextScaled = true
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.Size = UDim2.new(0.8, 0, 0.3, 0)
    nameLabel.Position = UDim2.new(0.2, 0, 0, 0)
    nameLabel.Text = player.Name
    nameLabel.Parent = container
    
    local healthLabel = Instance.new("TextLabel")
    healthLabel.Name = "HealthLabel"
    healthLabel.BackgroundTransparency = 1
    healthLabel.TextColor3 = CONFIG.TextColor
    healthLabel.TextStrokeTransparency = 0
    healthLabel.TextScaled = true
    healthLabel.Font = Enum.Font.Gotham
    healthLabel.Size = UDim2.new(0.8, 0, 0.25, 0)
    healthLabel.Position = UDim2.new(0.2, 0, 0.7, 0)
    healthLabel.Text = "100"
    healthLabel.Parent = container
    
    gui.Parent = PlayerGui
    
    return { Gui = gui, BarBg = barBg, BarFill = barFill, NameLabel = nameLabel, HealthLabel = healthLabel }
end

local function destroyBillboard(b)
    if not b then return end
    pcall(function() b.Gui:Destroy() end)
end

local function updateBillboard(b, player, humanoid)
    if not b or not humanoid then return end
    local hp = math.clamp(humanoid.Health / humanoid.MaxHealth, 0, 1)
    b.BarFill.Size = UDim2.new(1, 0, hp, 0)
    local r, g
    if hp > 0.5 then
        r = math.floor(255 * (1 - hp) * 2); g = 255
    else
        r = 255; g = math.floor(255 * hp * 2)
    end
    b.BarFill.BackgroundColor3 = Color3.fromRGB(r, g, 0)
    b.HealthLabel.Text = tostring(math.floor(humanoid.Health))
    b.NameLabel.Text = player.Name
    b.Gui.Enabled = CONFIG.Enabled
end

--// ============ HELPERS ============
local function getHealthColor(percent)
    if percent > 0.5 then
        return Color3.fromRGB(math.floor(255 * (1 - percent) * 2), 255, 0)
    else
        return Color3.fromRGB(255, math.floor(255 * percent * 2), 0)
    end
end

local playerData = {}
local function getPlayerData(player)
    if not playerData[player] then
        playerData[player] = { Drawing = nil, Billboard = nil, LastUpdate = 0 }
    end
    return playerData[player]
end

--// ============ MAIN RENDER LOOP ============
local renderAccumulator = 0

local function onRender(dt)
    if not CONFIG.Enabled then
        for _, data in pairs(playerData) do
            if data.Drawing then setDrawingVisible(data.Drawing, false) end
            if data.Billboard then data.Billboard.Gui.Enabled = false end
        end
        return
    end
    
    renderAccumulator += dt
    if renderAccumulator < CONFIG.UpdateInterval then return end
    renderAccumulator = 0
    
    local cameraPos = Camera.CFrame.Position
    local localTeam = LocalPlayer.Team
    local candidates = {}
    
    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer then continue end
        local data = getPlayerData(player)
        local character = player.Character
        
        if CONFIG.TeamCheck and localTeam and player.Team == localTeam then
            if data.Drawing then setDrawingVisible(data.Drawing, false) end
            if data.Billboard then data.Billboard.Gui.Enabled = false end
            continue
        end
        
        if not character then
            if data.Drawing then setDrawingVisible(data.Drawing, false) end
            if data.Billboard then data.Billboard.Gui.Enabled = false end
            continue
        end
        
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        local hrp = character:FindFirstChild("HumanoidRootPart")
        
        if not humanoid or not hrp or humanoid.Health <= 0 then
            if data.Drawing then setDrawingVisible(data.Drawing, false) end
            if data.Billboard then data.Billboard.Gui.Enabled = false end
            continue
        end
        
        local distance = (cameraPos - hrp.Position).Magnitude
        if distance > CONFIG.MaxDistance then
            if data.Drawing then setDrawingVisible(data.Drawing, false) end
            if data.Billboard then data.Billboard.Gui.Enabled = false end
            continue
        end
        
        table.insert(candidates, {
            Player = player, Character = character, Humanoid = humanoid,
            HRP = hrp, Distance = distance, Data = data,
        })
    end
    
    table.sort(candidates, function(a, b) return a.Distance < b.Distance end)
    
    local renderedCount = 0
    for _, c in ipairs(candidates) do
        if renderedCount >= CONFIG.MaxRenderCount then
            if c.Data.Drawing then setDrawingVisible(c.Data.Drawing, false) end
            if c.Data.Billboard then c.Data.Billboard.Gui.Enabled = false end
            continue
        end
        
        local head = c.Character:FindFirstChild("Head")
        if not head then continue end
        
        if not USE_BILLBOARD and Drawing_new then
            local d = c.Data.Drawing
            if not d then
                d = createDrawingObject()
                c.Data.Drawing = d
            end
            initDrawing(d)
            
            local hrpPos, onScreen = Camera:WorldToViewportPoint(c.HRP.Position)
            local headPos = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))
            
            if not onScreen or headPos.Z < 0 then
                setDrawingVisible(d, false)
                continue
            end
            
            local height = math.abs(headPos.Y - hrpPos.Y) * 2
            local width = height * 0.6
            local posX = hrpPos.X - width / 2
            local posY = hrpPos.Y - height / 2
            local centerX = hrpPos.X
            
            d.Box.Size = Vector2.new(width, height)
            d.Box.Position = Vector2.new(posX, posY)
            d.Box.Color = CONFIG.BoxColor
            
            local hp = math.clamp(c.Humanoid.Health / c.Humanoid.MaxHealth, 0, 1)
            local barW = CONFIG.HealthBarWidth
            local barGap = CONFIG.HealthBarGap
            local barX = posX - barGap - barW
            
            d.HealthBarBg.Size = Vector2.new(barW, height)
            d.HealthBarBg.Position = Vector2.new(barX, posY)
            d.HealthBarBg.Color = CONFIG.HealthBarBg
            
            local fillH = height * hp
            d.HealthBarFill.Size = Vector2.new(barW, fillH)
            d.HealthBarFill.Position = Vector2.new(barX, posY + (height - fillH))
            d.HealthBarFill.Color = getHealthColor(hp)
            
            d.Name.Text = c.Player.Name
            d.Name.Position = Vector2.new(centerX, posY - 18)
            d.Name.Color = CONFIG.TextColor
            
            d.Distance.Text = string.format("[%d]", math.floor(c.Distance))
            d.Distance.Position = Vector2.new(centerX, posY + height + 2)
            d.Distance.Color = CONFIG.TextColor
            
            d.HealthText.Text = tostring(math.floor(c.Humanoid.Health))
            d.HealthText.Position = Vector2.new(barX + barW / 2, posY + height + 2)
            d.HealthText.Color = getHealthColor(hp)
            
            setDrawingVisible(d, true)
            renderedCount += 1
        else
            local b = c.Data.Billboard
            if not b or not b.Gui.Parent then
                b = createBillboard(c.Player, c.Character)
                c.Data.Billboard = b
            end
            if b then
                b.Gui.Enabled = true
                b.Gui.Adornee = head
                updateBillboard(b, c.Player, c.Humanoid)
                renderedCount += 1
            end
        end
    end
end

local connection
if IS_MOBILE then
    connection = RunService.Heartbeat:Connect(function(dt) pcall(onRender, dt) end)
else
    connection = RunService.RenderStepped:Connect(function(dt) pcall(onRender, dt) end)
end

--// ============ CLEANUP ============
local function cleanupPlayer(player)
    local data = playerData[player]
    if not data then return end
    if data.Drawing then destroyDrawing(data.Drawing) end
    if data.Billboard then destroyBillboard(data.Billboard) end
    playerData[player] = nil
end

Players.PlayerRemoving:Connect(cleanupPlayer)

local function fullCleanup()
    if connection then connection:Disconnect() end
    for player, _ in pairs(playerData) do cleanupPlayer(player) end
    playerData = {}
end

-- ═══════════════════════════════════════════════════════════
-- ██  COLLAPSIBLE CONTROL PANEL                              ██
-- ═══════════════════════════════════════════════════════════

local PANEL_SIZE = IS_MOBILE and UDim2.new(0, 180, 0, 36) or UDim2.new(0, 200, 0, 32)
local HEADER_HEIGHT = IS_MOBILE and 36 or 32
local EXPANDED_HEIGHT = IS_MOBILE and 320 or 290
local ACCENT = Color3.fromRGB(0, 200, 255)
local BG_COLOR = Color3.fromRGB(20, 20, 25)
local SUB_BG = Color3.fromRGB(30, 30, 38)

-- Container
local panelGui = Instance.new("ScreenGui")
panelGui.Name = "ESP_ControlPanel"
panelGui.ResetOnSpawn = false
panelGui.IgnoreGuiInset = true
panelGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
panelGui.DisplayOrder = 999
panelGui.Parent = PlayerGui

local main = Instance.new("Frame")
main.Name = "Main"
main.Size = PANEL_SIZE
main.Position = UDim2.new(1, -PANEL_SIZE.X.Offset - 15, 0, 80)
main.BackgroundColor3 = BG_COLOR
main.BackgroundTransparency = 0.15
main.BorderSizePixel = 0
main.Active = true
main.ClipsDescendants = true
main.Parent = panelGui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 8)
mainCorner.Parent = main

local mainStroke = Instance.new("UIStroke")
mainStroke.Color = ACCENT
mainStroke.Thickness = 1
mainStroke.Transparency = 0.5
mainStroke.Parent = main

-- Header (clickable to collapse)
local header = Instance.new("TextButton")
header.Name = "Header"
header.Size = UDim2.new(1, 0, 0, HEADER_HEIGHT)
header.Position = UDim2.new(0, 0, 0, 0)
header.BackgroundColor3 = SUB_BG
header.BackgroundTransparency = 0.3
header.BorderSizePixel = 0
header.Text = ""
header.AutoButtonColor = false
header.Parent = main

local headerCorner = Instance.new("UICorner")
headerCorner.CornerRadius = UDim.new(0, 8)
headerCorner.Parent = header

-- Header icon (chevron)
local chevron = Instance.new("TextLabel")
chevron.Name = "Chevron"
chevron.Size = UDim2.new(0, HEADER_HEIGHT, 0, HEADER_HEIGHT)
chevron.Position = UDim2.new(0, 0, 0, 0)
chevron.BackgroundTransparency = 1
chevron.Text = "▼"
chevron.TextColor3 = ACCENT
chevron.TextScaled = true
chevron.Font = Enum.Font.GothamBold
chevron.Parent = header

local chevronPad = Instance.new("UIPadding")
chevronPad.PaddingTop = UDim.new(0, 8)
chevronPad.PaddingBottom = UDim.new(0, 8)
chevronPad.PaddingLeft = UDim.new(0, 10)
chevronPad.PaddingRight = UDim.new(0, 10)
chevronPad.Parent = chevron

-- Header title
local title = Instance.new("TextLabel")
title.Name = "Title"
title.Size = UDim2.new(1, -HEADER_HEIGHT - 60, 1, 0)
title.Position = UDim2.new(0, HEADER_HEIGHT, 0, 0)
title.BackgroundTransparency = 1
title.Text = "ESP"
title.TextColor3 = Color3.fromRGB(240, 240, 240)
title.TextXAlignment = Enum.TextXAlignment.Left
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.Parent = header

-- Status indicator (ON/OFF pill)
local statusPill = Instance.new("TextLabel")
statusPill.Name = "StatusPill"
statusPill.Size = UDim2.new(0, IS_MOBILE and 45 or 50, 0, HEADER_HEIGHT - 14)
statusPill.Position = UDim2.new(1, -(IS_MOBILE and 55 or 60), 0, 7)
statusPill.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
statusPill.BackgroundTransparency = 0.15
statusPill.BorderSizePixel = 0
statusPill.Text = "ON"
statusPill.TextColor3 = Color3.fromRGB(255, 255, 255)
statusPill.TextScaled = true
statusPill.Font = Enum.Font.GothamBold
statusPill.Parent = header

local pillCorner = Instance.new("UICorner")
pillCorner.CornerRadius = UDim.new(1, 0)
pillCorner.Parent = statusPill

local pillPad = Instance.new("UIPadding")
pillPad.PaddingLeft = UDim.new(0, 4)
pillPad.PaddingRight = UDim.new(0, 4)
pillPad.PaddingTop = UDim.new(0, 2)
pillPad.PaddingBottom = UDim.new(0, 2)
pillPad.Parent = statusPill

-- Scroll frame for body (mobile-friendly)
local body = Instance.new("ScrollingFrame")
body.Name = "Body"
body.Size = UDim2.new(1, 0, 0, 0)  -- collapsed by default
body.Position = UDim2.new(0, 0, 0, HEADER_HEIGHT)
body.BackgroundTransparency = 1
body.BorderSizePixel = 0
body.ScrollBarThickness = 3
body.ScrollBarImageColor3 = ACCENT
body.ScrollBarImageTransparency = 0.3
body.CanvasSize = UDim2.new(0, 0, 0, 0)
body.AutomaticCanvasSize = Enum.AutomaticSize.Y
body.Parent = main

local bodyLayout = Instance.new("UIListLayout")
bodyLayout.SortOrder = Enum.SortOrder.LayoutOrder
bodyLayout.Padding = UDim.new(0, 6)
bodyLayout.Parent = body

local bodyPad = Instance.new("UIPadding")
bodyPad.PaddingTop = UDim.new(0, 8)
bodyPad.PaddingBottom = UDim.new(0, 8)
bodyPad.PaddingLeft = UDim.new(0, 10)
bodyPad.PaddingRight = UDim.new(0, 10)
bodyPad.Parent = body

--// ============ UI BUILDERS ============
local function makeSectionLabel(text)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, 0, 0, 16)
    lbl.BackgroundTransparency = 1
    lbl.Text = string.upper(text)
    lbl.TextColor3 = ACCENT
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.TextSize = IS_MOBILE and 11 or 10
    lbl.Font = Enum.Font.GothamBold
    lbl.Parent = body
    return lbl
end

local function makeRow(height)
    local row = Instance.new("Frame")
    row.Size = UDim2.new(1, 0, 0, height or 26)
    row.BackgroundColor3 = SUB_BG
    row.BackgroundTransparency = 0.5
    row.BorderSizePixel = 0
    row.Parent = body
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 6)
    c.Parent = row
    return row
end

-- Toggle switch (mobile-friendly, tap anywhere on row)
local function makeToggle(labelText, getter, setter)
    local row = makeRow(IS_MOBILE and 32 or 26)
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -50, 1, 0)
    label.Position = UDim2.new(0, 10, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = labelText
    label.TextColor3 = Color3.fromRGB(220, 220, 220)
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextSize = IS_MOBILE and 13 or 12
    label.Font = Enum.Font.Gotham
    label.Parent = row
    
    local switch = Instance.new("Frame")
    switch.Size = UDim2.new(0, IS_MOBILE and 38 or 32, 0, IS_MOBILE and 20 or 16)
    switch.Position = UDim2.new(1, -(IS_MOBILE and 48 or 42), 0.5, -(IS_MOBILE and 10 or 8))
    switch.BackgroundColor3 = getter() and Color3.fromRGB(0, 200, 100) or Color3.fromRGB(60, 60, 70)
    switch.BorderSizePixel = 0
    switch.Parent = row
    
    local swCorner = Instance.new("UICorner")
    swCorner.CornerRadius = UDim.new(1, 0)
    swCorner.Parent = switch
    
    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, switch.AbsoluteSize.Y - 4, 1, -4)
    knob.Position = getter() and UDim2.new(1, -switch.AbsoluteSize.Y + 2, 0.5, 0) or UDim2.new(0, 2, 0.5, 0)
    knob.AnchorPoint = Vector2.new(0, 0.5)
    knob.Size = UDim2.new(0, IS_MOBILE and 16 or 12, 0, IS_MOBILE and 16 or 12)
    knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    knob.BorderSizePixel = 0
    knob.Parent = switch
    
    local knCorner = Instance.new("UICorner")
    knCorner.CornerRadius = UDim.new(1, 0)
    knCorner.Parent = knob
    
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 1, 0)
    btn.BackgroundTransparency = 1
    btn.Text = ""
    btn.Parent = row
    
    btn.MouseButton1Click:Connect(function()
        local newState = not getter()
        setter(newState)
        switch.BackgroundColor3 = newState and Color3.fromRGB(0, 200, 100) or Color3.fromRGB(60, 60, 70)
        knob.Position = newState and UDim2.new(1, -knob.AbsoluteSize.X - 2, 0.5, 0) or UDim2.new(0, 2, 0.5, 0)
    end)
    
    return { Row = row, Switch = switch, Knob = knob, SetState = function(v)
        switch.BackgroundColor3 = v and Color3.fromRGB(0, 200, 100) or Color3.fromRGB(60, 60, 70)
        knob.Position = v and UDim2.new(1, -knob.AbsoluteSize.X - 2, 0.5, 0) or UDim2.new(0, 2, 0.5, 0)
    end }
end

-- Slider
local function makeSlider(labelText, minVal, maxVal, getter, setter)
    local row = makeRow(IS_MOBILE and 44 or 38)
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -10, 0, 14)
    label.Position = UDim2.new(0, 10, 0, 4)
    label.BackgroundTransparency = 1
    label.Text = labelText .. ": " .. tostring(math.floor(getter()))
    label.TextColor3 = Color3.fromRGB(220, 220, 220)
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextSize = IS_MOBILE and 12 or 11
    label.Font = Enum.Font.Gotham
    label.Parent = row

    local track = Instance.new("Frame")
    track.Size = UDim2.new(1, -20, 0, 6)
    track.Position = UDim2.new(0, 10, 1, IS_MOBILE and -16 or -14)
    track.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
    track.BorderSizePixel = 0
    track.Parent = row
    
    local trackCorner = Instance.new("UICorner")
    trackCorner.CornerRadius = UDim.new(1, 0)
    trackCorner.Parent = track
    
    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((getter() - minVal) / (maxVal - minVal), 0, 1, 0)
    fill.BackgroundColor3 = ACCENT
    fill.BorderSizePixel = 0
    fill.Parent = track
    
    local fillCorner = Instance.new("UICorner")
    fillCorner.CornerRadius = UDim.new(1, 0)
    fillCorner.Parent = fill
    
    local trackBtn = Instance.new("TextButton")
    trackBtn.Size = UDim2.new(1, 0, 3, 0)
    trackBtn.Position = UDim2.new(0, 0, 0.5, 0)
    trackBtn.AnchorPoint = Vector2.new(0, 0.5)
    trackBtn.BackgroundTransparency = 1
    trackBtn.Text = ""
    trackBtn.Parent = track
    
    local dragging = false
    
    local function updateFromInput(input)
        local pos = input.Position.X - track.AbsolutePosition.X
        local pct = math.clamp(pos / track.AbsoluteSize.X, 0, 1)
        local val = math.floor(minVal + (maxVal - minVal) * pct)
        setter(val)
        fill.Size = UDim2.new(pct, 0, 1, 0)
        label.Text = labelText .. ": " .. tostring(val)
    end
    
    trackBtn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            updateFromInput(input)
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch) then
            updateFromInput(input)
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end)
    
    return row
end

--// ============ BUILD PANEL CONTENT ============
makeSectionLabel("Vision")
local toggleESP = makeToggle("ESP Enabled", function() return CONFIG.Enabled end, function(v)
    CONFIG.Enabled = v
    statusPill.Text = v and "ON" or "OFF"
    statusPill.BackgroundColor3 = v and Color3.fromRGB(0, 200, 100) or Color3.fromRGB(180, 50, 50)
end)

local toggleBox = makeToggle("Box", function() return CONFIG.ShowBox end, function(v) CONFIG.ShowBox = v end)
local toggleHealth = makeToggle("Health Bar", function() return CONFIG.ShowHealthBar end, function(v) CONFIG.ShowHealthBar = v end)
local toggleTeam = makeToggle("Team Check", function() return CONFIG.TeamCheck end, function(v) CONFIG.TeamCheck = v end)

makeSectionLabel("Text")
local toggleName = makeToggle("Name", function() return CONFIG.ShowName end, function(v) CONFIG.ShowName = v end)
local toggleDist = makeToggle("Distance", function() return CONFIG.ShowDistance end, function(v) CONFIG.ShowDistance = v end)
local toggleHPText = makeToggle("Health Value", function() return CONFIG.ShowHealth end, function(v) CONFIG.ShowHealth = v end)

makeSectionLabel("Performance")
makeSlider("Max Distance", 100, 2000, function() return CONFIG.MaxDistance end, function(v) CONFIG.MaxDistance = v end)
makeSlider("Max Render", 5, 30, function() return CONFIG.MaxRenderCount end, function(v) CONFIG.MaxRenderCount = v end)

--// ============ COLLAPSE / EXPAND ============
local isExpanded = false
local tweenInfo = TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)

local function expand()
    isExpanded = true
    chevron.Text = "▼"
    local tween = game:GetService("TweenService"):Create(
        body,
        tweenInfo,
        { Size = UDim2.new(1, 0, 0, EXPANDED_HEIGHT - HEADER_HEIGHT) }
    )
    tween:Play()
    body.Visible = true
end

local function collapse()
    isExpanded = false
    chevron.Text = "▶"
    local tween = game:GetService("TweenService"):Create(
        body,
        tweenInfo,
        { Size = UDim2.new(1, 0, 0, 0) }
    )
    tween:Play()
    tween.Completed:Connect(function()
        if not isExpanded then
            body.Visible = false
        end
    end)
end

body.Visible = false

-- Make header toggle collapse/expand on tap
local tapState = { startTime = 0, startPos = nil, moved = false }

header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
    or input.UserInputType == Enum.UserInputType.Touch then
        tapState.startTime = tick()
        tapState.startPos = input.Position
        tapState.moved = false
    end
end)

header.InputChanged:Connect(function(input)
    if not tapState.startPos then return end
    if input.UserInputType == Enum.UserInputType.MouseMovement
    or input.UserInputType == Enum.UserInputType.Touch then
        local delta = input.Position - tapState.startPos
        if math.abs(delta.X) > 8 or math.abs(delta.Y) > 8 then
            tapState.moved = true
        end
    end
end)

header.InputEnded:Connect(function(input)
    if input.UserInputType ~= Enum.UserInputType.MouseButton1
    and input.UserInputType ~= Enum.UserInputType.Touch then return end
    
    local duration = tick() - tapState.startTime
    if not tapState.moved and duration < 0.5 then
        if isExpanded then collapse()
