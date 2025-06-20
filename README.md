local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

_G.HeadSize = 10
_G.HitboxEnabled = false
_G.ESPEnabled = false
_G.HighlightEnabled = false

local function GetTargets()
    local targets = {}
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            table.insert(targets, player)
        end
    end
    return targets
end

-- Criar GUI
local gui = Instance.new("ScreenGui")
gui.Name = "PainelSimples"
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = game.CoreGui

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 260, 0, 190)
frame.Position = UDim2.new(0.5, -130, 0.5, -95)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

-- Botão Toggle do Painel
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 120, 0, 40)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.Text = "Esconder Painel"
toggleButton.Font = Enum.Font.GothamBold
toggleButton.TextSize = 14
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.BackgroundColor3 = Color3.fromRGB(44, 62, 80)
Instance.new("UICorner", toggleButton).CornerRadius = UDim.new(0, 8)
toggleButton.Parent = gui

local painelVisivel = true
toggleButton.MouseButton1Click:Connect(function()
    painelVisivel = not painelVisivel
    frame.Visible = painelVisivel
    toggleButton.Text = painelVisivel and "Esconder Painel" or "Mostrar Painel"
end)

-- Tornar botão móvel
local dragging, dragInput, dragStart, startPos
local function update(input)
    if not dragging then return end
    local delta = input.Position - dragStart
    toggleButton.Position = UDim2.new(0, startPos.X.Offset + delta.X, 0, startPos.Y.Offset + delta.Y)
end

toggleButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = toggleButton.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

toggleButton.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput then update(input) end
end)

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.Text = "Painel de Funções"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.BackgroundTransparency = 1

-- Caixa de tamanho
local sizeBox = Instance.new("TextBox", frame)
sizeBox.Size = UDim2.new(1, -30, 0, 30)
sizeBox.Position = UDim2.new(0, 15, 0, 40)
sizeBox.PlaceholderText = "Tamanho da Hitbox"
sizeBox.Text = tostring(_G.HeadSize)
sizeBox.Font = Enum.Font.Gotham
sizeBox.TextSize = 14
sizeBox.TextColor3 = Color3.fromRGB(255, 255, 255)
sizeBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Instance.new("UICorner", sizeBox).CornerRadius = UDim.new(0, 6)

-- Botão Hitbox
local hitboxBtn = Instance.new("TextButton", frame)
hitboxBtn.Size = UDim2.new(1, -30, 0, 30)
hitboxBtn.Position = UDim2.new(0, 15, 0, 75)
hitboxBtn.Text = "Ativar Hitbox"
hitboxBtn.Font = Enum.Font.GothamBold
hitboxBtn.TextSize = 14
hitboxBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
hitboxBtn.BackgroundColor3 = Color3.fromRGB(52, 152, 219)
Instance.new("UICorner", hitboxBtn).CornerRadius = UDim.new(0, 6)

-- Botão ESP
local espBtn = Instance.new("TextButton", frame)
espBtn.Size = UDim2.new(1, -30, 0, 30)
espBtn.Position = UDim2.new(0, 15, 0, 110)
espBtn.Text = "Ativar ESP"
espBtn.Font = Enum.Font.GothamBold
espBtn.TextSize = 14
espBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
espBtn.BackgroundColor3 = Color3.fromRGB(231, 76, 60)
Instance.new("UICorner", espBtn).CornerRadius = UDim.new(0, 6)

-- Botão Highlight
local highlightBtn = Instance.new("TextButton", frame)
highlightBtn.Size = UDim2.new(1, -30, 0, 30)
highlightBtn.Position = UDim2.new(0, 15, 0, 145)
highlightBtn.Text = "Ativar Highlight"
highlightBtn.Font = Enum.Font.GothamBold
highlightBtn.TextSize = 14
highlightBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
highlightBtn.BackgroundColor3 = Color3.fromRGB(155, 89, 182)
Instance.new("UICorner", highlightBtn).CornerRadius = UDim.new(0, 6)

-- Funções dos botões
sizeBox.FocusLost:Connect(function()
    local n = tonumber(sizeBox.Text)
    if n then
        _G.HeadSize = n
    else
        sizeBox.Text = tostring(_G.HeadSize)
    end
end)

hitboxBtn.MouseButton1Click:Connect(function()
    _G.HitboxEnabled = not _G.HitboxEnabled
    hitboxBtn.Text = _G.HitboxEnabled and "Desativar Hitbox" or "Ativar Hitbox"
end)

espBtn.MouseButton1Click:Connect(function()
    _G.ESPEnabled = not _G.ESPEnabled
    espBtn.Text = _G.ESPEnabled and "Desativar ESP" or "Ativar ESP"
end)

highlightBtn.MouseButton1Click:Connect(function()
    _G.HighlightEnabled = not _G.HighlightEnabled
    highlightBtn.Text = _G.HighlightEnabled and "Desativar Highlight" or "Ativar Highlight"
end)

-- Execução da Hitbox + Highlight
RunService.RenderStepped:Connect(function()
    for _, p in ipairs(GetTargets()) do
        local root = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
        if root then
            if _G.HitboxEnabled then
                root.Size = Vector3.new(_G.HeadSize, _G.HeadSize, _G.HeadSize)
                root.Transparency = 0.5
                root.Material = Enum.Material.Neon
                root.BrickColor = BrickColor.new("Bright blue")
                root.CanCollide = false

                if _G.HighlightEnabled and not p.Character:FindFirstChild("HitboxHighlight") then
                    local highlight = Instance.new("Highlight")
                    highlight.Name = "HitboxHighlight"
                    highlight.Adornee = root
                    highlight.FillColor = Color3.fromRGB(0, 170, 255)
                    highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
                    highlight.FillTransparency = 0.2
                    highlight.OutlineTransparency = 0
                    highlight.Parent = p.Character
                elseif not _G.HighlightEnabled then
                    local oldHighlight = p.Character:FindFirstChild("HitboxHighlight")
                    if oldHighlight then oldHighlight:Destroy() end
                end
            else
                root.Size = Vector3.new(2, 2, 1)
                root.Transparency = 1
                root.Material = Enum.Material.Plastic
                root.BrickColor = BrickColor.new("Medium stone grey")
                root.CanCollide = true

                local oldHighlight = p.Character:FindFirstChild("HitboxHighlight")
                if oldHighlight then oldHighlight:Destroy() end
            end
        end
    end
end)

-- ESP fixo (sem duplicação)
local espLines = {}

RunService.RenderStepped:Connect(function()
    for _, line in ipairs(espLines) do
        line:Remove()
    end
    table.clear(espLines)

    if _G.ESPEnabled then
        local hue = tick() % 5 / 5
        for _, p in ipairs(GetTargets()) do
            local root = p.Character and p.Character:FindFirstChild("HumanoidRootPart")
            if root then
                local pos, onScreen = Camera:WorldToViewportPoint(root.Position)
                if onScreen then
                    local line = Drawing.new("Line")
                    line.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                    line.To = Vector2.new(pos.X, pos.Y)
                    line.Color = Color3.fromHSV(hue, 1, 1)
                    line.Thickness = 2
                    line.Transparency = 1
                    table.insert(espLines, line)
                end
            end
        end
    end
end)
