local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

local ESPEnabled = false
local ESPTransparency = 0.5

local ESPBoxColor = Color3.fromRGB(0, 255, 255)
local ESPTextColor = Color3.fromRGB(255, 255, 255)

local function getRoot(model)
    if model:IsA("Model") then
        return model.PrimaryPart or model:FindFirstChildWhichIsA("BasePart")
    end
    return model
end

local function createESP(plr)
    if not plr.Character then return end
    local character = plr.Character
    local rootPart = getRoot(character)

    if not rootPart then return end

    local espFolder = CoreGui:FindFirstChild(plr.Name .. "_ESP")
    if espFolder then espFolder:Destroy() end

    espFolder = Instance.new("Folder")
    espFolder.Name = plr.Name .. "_ESP"
    espFolder.Parent = CoreGui

    local function createBox(part)
        local box = Instance.new("BoxHandleAdornment")
        box.Adornee = part
        box.AlwaysOnTop = true
        box.Size = part.Size
        box.Transparency = ESPTransparency
        box.Color3 = ESPBoxColor
        box.Parent = espFolder
    end

    for _, part in ipairs(character:GetChildren()) do
        if part:IsA("BasePart") then createBox(part) end
    end

    local function updateESP()
        if not character or not rootPart then return end
        if Players.LocalPlayer.Character then
            local localRoot = getRoot(Players.LocalPlayer.Character)
            if localRoot then
                local distance = (localRoot.Position - rootPart.Position).Magnitude
            end
        end
    end

    local espLoop
    espLoop = RunService.RenderStepped:Connect(updateESP)

    plr.CharacterRemoving:Connect(function()
        espFolder:Destroy()
        espLoop:Disconnect()
    end)
end

local function toggleESP()
    ESPEnabled = not ESPEnabled
    for _, plr in ipairs(Players:GetPlayers()) do
        if ESPEnabled then
            createESP(plr)
        else
            local espFolder = CoreGui:FindFirstChild(plr.Name .. "_ESP")
            if espFolder then espFolder:Destroy() end
        end
    end
end

Players.PlayerAdded:Connect(function(plr)
    plr.CharacterAdded:Connect(function()
        if ESPEnabled then createESP(plr) end
    end)
end)

local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer.PlayerGui
screenGui.Name = "BAZUKA_HUB"

local frame = Instance.new("Frame")
frame.Parent = screenGui
frame.Size = UDim2.new(0, 320, 0, 220)
frame.Position = UDim2.new(0.5, -160, 0.5, -110)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BackgroundTransparency = 0.2
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = frame

local titleLabel = Instance.new("TextLabel")
titleLabel.Parent = frame
titleLabel.Size = UDim2.new(1, 0, 0, 30)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "BAZUKA HUB"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.TextSize = 20
titleLabel.Font = Enum.Font.GothamBold

local espButton = Instance.new("TextButton")
espButton.Parent = frame
espButton.Size = UDim2.new(0, 260, 0, 45)
espButton.Position = UDim2.new(0, 30, 0, 60)
espButton.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
espButton.Text = "Ativar ESP"
espButton.TextColor3 = Color3.fromRGB(255, 255, 255)
espButton.TextSize = 18
espButton.Font = Enum.Font.Gotham

local UICornerButton = Instance.new("UICorner")
UICornerButton.CornerRadius = UDim.new(0, 10)
UICornerButton.Parent = espButton

local closeButton = Instance.new("TextButton")
closeButton.Parent = frame
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -35, 0, 5)
closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
closeButton.Text = "X"
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.TextSize = 14
closeButton.Font = Enum.Font.GothamBold

local UICornerClose = Instance.new("UICorner")
UICornerClose.CornerRadius = UDim.new(0, 6)
UICornerClose.Parent = closeButton

local icon = Instance.new("ImageLabel")
icon.Parent = frame
icon.Size = UDim2.new(0, 60, 0, 60)
icon.Position = UDim2.new(0, 10, 0, 10)
icon.BackgroundTransparency = 1
icon.Image = "https://create.roblox.com/store/asset/10847744848/el-gato"

espButton.MouseButton1Click:Connect(function()
    toggleESP()
    if ESPEnabled then
        espButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
        espButton.Text = "Desativar ESP"
    else
        espButton.BackgroundColor3 = Color3.fromRGB(0, 200, 0)
        espButton.Text = "Ativar ESP"
    end
end)

closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

screenGui.Enabled = true
