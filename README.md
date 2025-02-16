local Players, LocalPlayer, Lighting, RunService, UserInputService = game:GetService("Players"), game:GetService("Players").LocalPlayer, game:GetService("Lighting"), game:GetService("RunService"), game:GetService("UserInputService")
local gui, frame, hideButton, showButton, antiLagButton, espToggleButton, espStatusLabel, titleLabel = Instance.new("ScreenGui"), Instance.new("Frame"), Instance.new("ImageButton"), Instance.new("ImageButton"), Instance.new("TextButton"), Instance.new("TextButton"), Instance.new("TextLabel"), Instance.new("TextLabel")

gui.Name, gui.Parent, frame.Size, frame.Position, frame.BackgroundColor3, frame.BackgroundTransparency, frame.BorderSizePixel, frame.AnchorPoint, frame.Parent = "AntiLagGUI", game.CoreGui, UDim2.new(0, 250, 0, 250), UDim2.new(0.5, -125, 0.5, -125), Color3.new(0, 0, 1), 0.1, 0, Vector2.new(0.5, 0.5), gui
Instance.new("UICorner", frame).CornerRadius = UDim.new(0.1, 0)
local gradient = Instance.new("UIGradient", frame)
gradient.Color = ColorSequence.new(Color3.new(0, 0, 1), Color3.new(0, 0, 0.5))

hideButton.Size, hideButton.Position, hideButton.Image, hideButton.Parent = UDim2.new(0, 20, 0, 20), UDim2.new(1, -30, 0, 10), "rbxassetid://6035047409", frame
showButton.Size, showButton.Position, showButton.BackgroundColor3, showButton.Image, showButton.Parent, showButton.Visible = UDim2.new(0, 50, 0, 50), UDim2.new(0, 10, 0, 10), Color3.new(0, 0, 0.5), "rbxassetid://6031068420", gui, false
antiLagButton.Size, antiLagButton.Position, antiLagButton.Text, antiLagButton.BackgroundColor3, antiLagButton.AnchorPoint, antiLagButton.Parent = UDim2.new(0, 200, 0, 50), UDim2.new(0.5, 0, 0.5, -40), "Ativar Anti-Lag", Color3.new(0.7, 0.7, 0.7), Vector2.new(0.5, 0.5), frame
espToggleButton.Size, espToggleButton.Position, espToggleButton.Text, espToggleButton.BackgroundColor3, espToggleButton.AnchorPoint, espToggleButton.Parent = UDim2.new(0, 200, 0, 50), UDim2.new(0.5, 0, 0.5, 10), "Ativar ESP", Color3.new(0.7, 0.7, 0.7), Vector2.new(0.5, 0.5), frame
espStatusLabel.Size, espStatusLabel.Position, espStatusLabel.Text, espStatusLabel.TextScaled, espStatusLabel.TextColor3, espStatusLabel.BackgroundTransparency, espStatusLabel.AnchorPoint, espStatusLabel.Parent = UDim2.new(0, 200, 0, 50), UDim2.new(0.5, 0, 0.5, 60), "ESP: Desativado", true, Color3.new(1, 1, 1), 1, Vector2.new(0.5, 0), frame

-- Alterando o título do Sonic Menu
titleLabel.TextColor3 = Color3.new(0, 0, 0)  -- Cor preta
titleLabel.Position = UDim2.new(0.5, 0, 0.5, -130)  -- Posição mais para cima

local antiLagEnabled = false
local function optimizeGame()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level02
    Lighting.GlobalShadows, Lighting.FogEnd, Lighting.Brightness = false, 8000, 1.5
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") then v.CastShadow, v.Material = false, Enum.Material.SmoothPlastic end
    end
end

local function resetGameSettings()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level10
    Lighting.GlobalShadows, Lighting.FogEnd, Lighting.Brightness = true, 10000, 2
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") then v.CastShadow, v.Material = true, Enum.Material.Plastic end
    end
end

antiLagButton.MouseButton1Click:Connect(function()
    antiLagEnabled = not antiLagEnabled
    antiLagButton.Text = antiLagEnabled and "Desativar Anti-Lag" or "Ativar Anti-Lag"
    if antiLagEnabled then optimizeGame() else resetGameSettings() end
end)

local espEnabled, espObjects = false, {}
local function createESP(player)
    local espFrame = Instance.new("BillboardGui", player.Character:WaitForChild("Head"))
    espFrame.Adornee, espFrame.Size, espFrame.StudsOffset, espFrame.AlwaysOnTop = player.Character:WaitForChild("Head"), UDim2.new(0, 200, 0, 50), Vector3.new(0, 3, 0), true
    local espLabel = Instance.new("TextLabel", espFrame)
    espLabel.Text, espLabel.TextColor3, espLabel.TextScaled, espLabel.BackgroundTransparency, espLabel.Size = player.Name, Color3.new(1, 0, 0), true, 1, UDim2.new(1, 0, 1, 0)
    table.insert(espObjects, espFrame)
end

local function removeESP(player)
    for i, espObject in ipairs(espObjects) do
        if espObject.Adornee == player.Character:FindFirstChild("Head") then
            espObject:Destroy()
            table.remove(espObjects, i)
            break
        end
    end
end

local function updateESP()
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if espEnabled then
                if player.Character and player.Character:FindFirstChild("Head") then
                    if not player.Character:FindFirstChild("Head"):FindFirstChild("BillboardGui") then createESP(player) end
                end
            else removeESP(player) end
        end
    end
end

RunService.Heartbeat:Connect(updateESP)

espToggleButton.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    espStatusLabel.Text = espEnabled and "ESP: Ativado" or "ESP: Desativado"
end)

local function updatePlayerColors()
    for _, player in pairs(Players:GetPlayers()) do
        if player.Character and player.Character:FindFirstChild("Head") then
            local character = player.Character
            if player.Team == LocalPlayer.Team then
                character.Head.BrickColor = BrickColor.new("Bright blue")
                for _, part in pairs(character:GetChildren()) do
                    if part:IsA("BasePart") then part.BrickColor = BrickColor.new("Bright blue") end
                end
            else
                character.Head.BrickColor = BrickColor.new("Bright red")
                for _, part in pairs(character:GetChildren()) do
                    if part:IsA("BasePart") then part.BrickColor = BrickColor.new("Bright red") end
                end
            end
        end
    end
end

RunService.Heartbeat:Connect(updatePlayerColors)

local function makeDraggable(frame)
    local dragging, dragInput, dragStart, startPos = false, nil, nil, nil
    local function update(input)
        local delta = input.Position - dragStart
        frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging, dragStart, startPos = true, input.Position, frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)

    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then dragInput = input end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then update(input) end
    end)
end

makeDraggable(frame)
makeDraggable(showButton)

hideButton.MouseButton1Click:Connect(function()
    frame.Visible, showButton.Visible = false, true
end)

showButton.MouseButton1Click:Connect(function()
    frame.Visible, showButton.Visible = true, false
end)
