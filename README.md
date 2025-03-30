local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

-- silly gui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "FollowPlayerUI"
screenGui.Parent = game:GetService("CoreGui") -- gui

-- Made by chase thingy buton
local madeByLabel = Instance.new("TextLabel")
madeByLabel.Name = "MadeByLabel"
madeByLabel.Size = UDim2.new(0.4, 0, 0.1, 0)
madeByLabel.Position = UDim2.new(0.3, 0, 0.45, 0)
madeByLabel.Text = "made by chase :3"
madeByLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
madeByLabel.Font = Enum.Font.GothamBold -- awesome font i lieik
madeByLabel.TextSize = 48
madeByLabel.TextScaled = true
madeByLabel.BackgroundTransparency = 0.4
madeByLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
madeByLabel.TextStrokeTransparency = 0.8
madeByLabel.TextStrokeColor3 = Color3.fromRGB(255, 255, 255)
madeByLabel.Parent = screenGui

-- uigradi
local gradient = Instance.new("UIGradient")
gradient.Parent = madeByLabel
gradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 0, 255)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(0, 255, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 0, 255))
})
gradient.Rotation = 45

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0.4, 0)
corner.Parent = madeByLabel

local shadow = Instance.new("Frame")
shadow.Name = "Shadow"
shadow.Size = madeByLabel.Size + UDim2.new(0, 15, 0, 15)
shadow.Position = madeByLabel.Position + UDim2.new(0, 7, 0, 7)
shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
shadow.BackgroundTransparency = 0.5
shadow.ZIndex = madeByLabel.ZIndex - 1
shadow.Parent = screenGui

local shadowCorner = Instance.new("UICorner")
shadowCorner.CornerRadius = UDim.new(0.4, 0)
shadowCorner.Parent = shadow

-- tween/easing
local tweenService = game:GetService("TweenService")
local tweenInfo = TweenInfo.new(1, Enum.EasingStyle.Bounce, Enum.EasingDirection.Out, 0, false, 0)
local goal = {Size = UDim2.new(0.4, 0, 0.1, 0), TextTransparency = 0}
local tween = tweenService:Create(madeByLabel, tweenInfo, goal)
tween:Play()


local fadeOutTweenInfo = TweenInfo.new(2, Enum.EasingStyle.Linear, Enum.EasingDirection.Out, 0, false, 0)
local fadeOutGoal = {TextTransparency = 1}
local fadeOutTween = tweenService:Create(madeByLabel, fadeOutTweenInfo, fadeOutGoal)
local fadeOutGoalBackground = {BackgroundTransparency = 1}
local fadeOutTweenBackground = tweenService:Create(madeByLabel, fadeOutTweenInfo, fadeOutGoalBackground)

wait(1)
fadeOutTween:Play()
fadeOutTweenBackground:Play()

fadeOutTween.Completed:Connect(function()
    madeByLabel:Destroy()
end)

fadeOutTweenBackground.Completed:Connect(function()
    shadow:Destroy()
end)

-- button to follow head
local followButton = Instance.new("TextButton")
followButton.Name = "FollowButton"
followButton.Size = UDim2.new(0.2, 0, 0.1, 0)
followButton.Position = UDim2.new(0.01, 0, 0.85, 0)
followButton.Text = "pewww :333"
followButton.TextColor3 = Color3.fromRGB(255, 255, 255)
followButton.Font = Enum.Font.GothamBold -- rahhh i love this fony
followButton.TextScaled = true
followButton.TextStrokeTransparency = 0.5
followButton.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
followButton.Parent = screenGui

-- wild guess but ui gradi agin
local gradientButton = Instance.new("UIGradient")
gradientButton.Parent = followButton
gradientButton.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 150, 255)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 75, 135))
})
gradientButton.Rotation = 45

local cornerButton = Instance.new("UICorner")
cornerButton.CornerRadius = UDim.new(0.3, 0)
cornerButton.Parent = followButton

local shadowButton = Instance.new("Frame")
shadowButton.Name = "Shadow"
shadowButton.Size = followButton.Size + UDim2.new(0, 10, 0, 10)
shadowButton.Position = followButton.Position + UDim2.new(0, 5, 0, 5)
shadowButton.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
shadowButton.BackgroundTransparency = 0.5
shadowButton.ZIndex = followButton.ZIndex - 1
shadowButton.Parent = screenGui

local shadowButtonCorner = Instance.new("UICorner")
shadowButtonCorner.CornerRadius = UDim.new(0.3, 0)
shadowButtonCorner.Parent = shadowButton

-- hardest line of code ever
local isFollowingEnabled = false

followButton.MouseButton1Click:Connect(function()
    isFollowingEnabled = not isFollowingEnabled
    if isFollowingEnabled then
        followButton.Text = "must have bin the wind....."
        followButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0) -- color changeeerrrr
    else
        followButton.Text = "pewww :333"
        followButton.BackgroundColor3 = Color3.fromRGB(0, 150, 255) -- ^^^^^^
    end
end)

-- checkrightmouse
local isRightMouseHeld = false
local targetPlayer = nil

-- this took me 2 hours because im sped
UserInputService.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 and isFollowingEnabled then
        isRightMouseHeld = true

        -- blah blach bluh bluh bluh vluh bluh bluh
        local closestPlayer = nil
        local closestDistance = math.huge

        for _, player in pairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
                local head = player.Character.Head
                local headScreenPosition, onScreen = Camera:WorldToScreenPoint(head.Position)

                if onScreen then
                    local mousePosition = UserInputService:GetMouseLocation()
                    local distance = (Vector2.new(headScreenPosition.X, headScreenPosition.Y) - mousePosition).Magnitude
                    if distance < closestDistance then
                        closestDistance = distance
                        closestPlayer = player
                    end
                end
            end
        end

        targetPlayer = closestPlayer
    end
end)

-- lockonkeybind :3 (can change)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        isRightMouseHeld = false
        targetPlayer = nil
    end
end)

-- renderstep
game:GetService("RunService").RenderStepped:Connect(function()
    if isRightMouseHeld and targetPlayer and targetPlayer.Character then
        local head = targetPlayer.Character:FindFirstChild("Head")
        if head then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, head.Position)
        else
            targetPlayer = nil
        end
    end
end)
-- i spent like 2 days on this wehha










-- eeeeesoppp
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

-- Cache money
local LocalPlayer = Players.LocalPlayer
local LocalCharacter = LocalPlayer and LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local LocalHumanoidRootPart = LocalCharacter:WaitForChild("HumanoidRootPart")

local ESP = {}
ESP.__index = ESP
--ramadeapakak pala
function ESP.new()
    local self = setmetatable({}, ESP)
    self.espCache = {}
    return self
end

function ESP:createDrawing(type, properties)
    local drawing = Drawing.new(type)
    for prop, val in pairs(properties) do
        drawing[prop] = val
    end
    return drawing
end

function ESP:createComponents()
    return {
        Box = self:createDrawing("Square", {
            Thickness = 1,
            Transparency = 1,
            Color = Color3.fromRGB(255, 255, 255),
            Filled = false
        }),
        DistanceLabel = self:createDrawing("Text", {
            Size = 10,
            Center = true,
            Outline = true,
            Color = Color3.fromRGB(255, 255, 255),
            OutlineColor = Color3.fromRGB(0, 0, 0)
        }),
        NameLabel = self:createDrawing("Text", {
            Size = 18,
            Center = true,
            Outline = true,
            Color = Color3.fromRGB(255, 255, 255),
            OutlineColor = Color3.fromRGB(0, 0, 0)
        }),
        HealthBar = {
            Outline = self:createDrawing("Square", {
                Thickness = 1,
                Transparency = 1,
                Color = Color3.fromRGB(0, 0, 0),
                Filled = false
            }),
            Health = self:createDrawing("Square", {
                Thickness = 1,
                Transparency = 1,
Color = Color3.fromRGB(0, 255, 0),
                Filled = true
            })
        },
        ItemLabel = self:createDrawing("Text", {
            Size = 18,
            Center = true,
            Outline = true,
            Color = Color3.fromRGB(255, 255, 255),
            OutlineColor = Color3.fromRGB(0, 0, 0)
        }),
        SkeletonLines = {}
    }
end

local bodyConnections = {
    R15 = {
        {"Head", "UpperTorso"},
        {"UpperTorso", "LowerTorso"},
        {"LowerTorso", "LeftUpperLeg"},
        {"LowerTorso", "RightUpperLeg"},
        {"LeftUpperLeg", "LeftLowerLeg"},
        {"LeftLowerLeg", "LeftFoot"},
        {"RightUpperLeg", "RightLowerLeg"},
        {"RightLowerLeg", "RightFoot"},
        {"UpperTorso", "LeftUpperArm"},
        {"UpperTorso", "RightUpperArm"},
        {"LeftUpperArm", "LeftLowerArm"},
        {"LeftLowerArm", "LeftHand"},
        {"RightUpperArm", "RightLowerArm"},
        {"RightLowerArm", "RightHand"}
    },
    R6 = {
        {"Head", "Torso"},
        {"Torso", "Left Arm"},
        {"Torso", "Right Arm"},
        {"Torso", "Left Leg"},
        {"Torso", "Right Leg"}
    }
}

function ESP:updateComponents(components, character, player)
    local hrp = character:FindFirstChild("HumanoidRootPart")
    local humanoid = character:FindFirstChild("Humanoid")

    if hrp and humanoid then
        local hrpPosition, onScreen = Camera:WorldToViewportPoint(hrp.Position)
        local mousePosition = Camera:WorldToViewportPoint(LocalPlayer:GetMouse().Hit.p)

        if onScreen then
            local screenWidth, screenHeight = Camera.ViewportSize.X, Camera.ViewportSize.Y
            local factor = 1 / (hrpPosition.Z * math.tan(math.rad(Camera.FieldOfView * 0.5)) * 2) * 100
            local width, height = math.floor(screenHeight / 25 * factor), math.floor(screenWidth / 27 * factor)
            local distanceFromPlayer = math.floor((LocalHumanoidRootPart.Position - hrp.Position).magnitude)

            -- distance labe
            components.DistanceLabel.Text = string.format("[%dM]", distanceFromPlayer)
            components.DistanceLabel.Position = Vector2.new(hrpPosition.X, hrpPosition.Y + height / 2 + 15)
            components.DistanceLabel.Visible = true

            -- bel iykyk
            components.NameLabel.Text = string.format("[%s]", player.DisplayName)
            components.NameLabel.Position = Vector2.new(hrpPosition.X, hrpPosition.Y - height / 2 - 15)
            components.NameLabel.Visible = true

            -- SKETLETON SNO SPEOOKY
            local connections = bodyConnections[humanoid.RigType.Name] or {}
            for _, connection in ipairs(connections) do
                local partA = character:FindFirstChild(connection[1])
                local partB = character:FindFirstChild(connection[2])
                if partA and partB then
                    local line = components.SkeletonLines[connection[1] .. "-" .. connection[2]] or self:createDrawing("Line", {Thickness = 1, Color = Color3.fromRGB(255, 255, 255)})
                    local posA, onScreenA = Camera:WorldToViewportPoint(partA.Position)
                    local posB, onScreenB = Camera:WorldToViewportPoint(partB.Position)
                    if onScreenA and onScreenB then
                        line.From = Vector2.new(posA.X, posA.Y)
                        line.To = Vector2.new(posB.X, posB.Y)
                        line.Visible = true
                        components.SkeletonLines[connection[1] .. "-" .. connection[2]] = line
                    else
                        line.Visible = false
                    end
                end
            end
        else
            self:hideComponents(components)
        end
    else
        self:hideComponents(components)
    end
end
--vdv z neba privet
function ESP:hideComponents(components)
    components.Box.Visible = false
    components.DistanceLabel.Visible = false
    components.NameLabel.Visible = false

    for _, line in pairs(components.SkeletonLines) do
        line.Visible = false
    end
end
--t ralealala
function ESP:removeEsp(player)
    local components = self.espCache[player]
    if components then
        components.Box:Remove()
        components.DistanceLabel:Remove()
        components.NameLabel:Remove()
        for _, line in pairs(components.SkeletonLines) do
            line:Remove()
        end
        self.espCache[player] = nil
    end
end

local espInstance = ESP.new()
--sillyism silly sill
RunService.RenderStepped:Connect(function()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local character = player.Character
            if character then
                if not espInstance.espCache[player] then
                    espInstance.espCache[player] = espInstance:createComponents()
                end
                espInstance:updateComponents(espInstance.espCache[player], character, player)
            else
                if espInstance.espCache[player] then
                    espInstance:hideComponents(espInstance.espCache[player])
                end
            end
        end
    end
end)
-- im so sile!!
Players.PlayerRemoving:Connect(function(player)
    espInstance:removeEsp(player)
end)
-- the :33 







-- if your reading this dont mind the labels i was bored
