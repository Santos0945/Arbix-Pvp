-- [[ ARBIX PVP - V27 UNIFIED & ANIMATED (HITBOX AZUL) ]] --

local Player = game.Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

-- Configurações e Estados
local CORRECT_KEY = "Arbix Pvp"
local Vector3_zero = Vector3.new(0, 0, 0)
local Vector3_new = Vector3.new
local states = { 
    InfiniteJump = false, SpeedBoost = false, Fly = false, 
    Hitbox = false, AntiRagdoll = false, SpinBot = false 
}
local isMinimized = false

-- Função Auxiliar para Animações (Tweens)
local function anim(obj, info, goal)
    local tween = TweenService:Create(obj, TweenInfo.new(info, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), goal)
    tween:Play()
    return tween
end

-- Criando a ScreenGui principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ArbixPvp_V27_Final"
ScreenGui.ResetOnSpawn = false 
ScreenGui.Parent = PlayerGui

local function addCorner(parent, cornerRadius)
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, cornerRadius)
    corner.Parent = parent
end

---------------------------------------------------------------------
-- JANELA DE KEY (SISTEMA DE ENTRADA)
---------------------------------------------------------------------
local KeyFrame = Instance.new("Frame")
KeyFrame.Name = "KeyFrame"
KeyFrame.Size = UDim2.new(0, 280, 0, 180)
KeyFrame.Position = UDim2.new(0.5, -140, 0.45, -90) -- Começa levemente acima
KeyFrame.BackgroundColor3 = Color3.fromRGB(5, 5, 15)
KeyFrame.Active = true
KeyFrame.Draggable = true
KeyFrame.BackgroundTransparency = 1
KeyFrame.Parent = ScreenGui
addCorner(KeyFrame, 12)

local KeyStroke = Instance.new("UIStroke")
KeyStroke.Color = Color3.fromRGB(0, 170, 255)
KeyStroke.Thickness = 0
KeyStroke.Parent = KeyFrame

-- Animação de Entrada da Key
task.spawn(function()
    anim(KeyFrame, 0.6, {Position = UDim2.new(0.5, -140, 0.5, -90), BackgroundTransparency = 0})
    anim(KeyStroke, 0.8, {Thickness = 1.8})
end)

local KeyTitle = Instance.new("TextLabel")
KeyTitle.Size = UDim2.new(1, 0, 0, 40)
KeyTitle.Text = "SISTEMA DE KEY 🔑"
KeyTitle.TextColor3 = Color3.fromRGB(0, 200, 255)
KeyTitle.BackgroundTransparency = 1
KeyTitle.Font = Enum.Font.GothamBold
KeyTitle.TextSize = 16
KeyTitle.Parent = KeyFrame

local KeyInput = Instance.new("TextBox")
KeyInput.Size = UDim2.new(0.8, 0, 0, 35)
KeyInput.Position = UDim2.new(0.1, 0, 0, 65)
KeyInput.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
KeyInput.Text = ""
KeyInput.PlaceholderText = "Insira a Key..."
KeyInput.TextColor3 = Color3.new(1, 1, 1)
KeyInput.Font = Enum.Font.Gotham
KeyInput.TextSize = 14
KeyInput.Parent = KeyFrame
addCorner(KeyInput, 6)

local CheckBtn = Instance.new("TextButton")
CheckBtn.Size = UDim2.new(0.8, 0, 0, 35)
CheckBtn.Position = UDim2.new(0.1, 0, 0, 115)
CheckBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
CheckBtn.Text = "Verificar Key"
CheckBtn.TextColor3 = Color3.new(1, 1, 1)
CheckBtn.Font = Enum.Font.GothamBold
CheckBtn.TextSize = 14
CheckBtn.Parent = KeyFrame
addCorner(CheckBtn, 6)

---------------------------------------------------------------------
-- JANELA PRINCIPAL (INVISÍVEL ATÉ A KEY)
---------------------------------------------------------------------
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 300, 0, 340)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -170)
MainFrame.BackgroundColor3 = Color3.fromRGB(5, 5, 15) 
MainFrame.Active = true
MainFrame.Draggable = true 
MainFrame.Visible = false
MainFrame.ClipsDescendants = true
MainFrame.Parent = ScreenGui
addCorner(MainFrame, 12)

local WindowStroke = Instance.new("UIStroke")
WindowStroke.Color = Color3.fromRGB(0, 170, 255) 
WindowStroke.Thickness = 1.8
WindowStroke.Parent = MainFrame

---------------------------------------------------------------------
-- LÓGICA DA HITBOX (QUADRADO AZUL)
---------------------------------------------------------------------
local function applyHitbox(v)
    if v ~= Player and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = v.Character.HumanoidRootPart
        local box = hrp:FindFirstChild("ArbixHitbox")
        
        if not box then
            box = Instance.new("SelectionBox")
            box.Name = "ArbixHitbox"
            box.Adornee = hrp
            box.Color3 = Color3.fromRGB(0, 170, 255) -- Azul Neon
            box.SurfaceColor3 = Color3.fromRGB(0, 170, 255)
            box.SurfaceTransparency = 0.75 -- Preenchimento leve
            box.LineThickness = 0.08
            box.Parent = hrp
        end
        hrp.Size = Vector3_new(18, 18, 18)
        hrp.Transparency = 0.8
        hrp.CanCollide = false
    end
end

local function removeHitbox(v)
    if v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = v.Character.HumanoidRootPart
        local box = hrp:FindFirstChild("ArbixHitbox")
        if box then box:Destroy() end
        hrp.Size = Vector3_new(2, 2, 1)
        hrp.Transparency = 1
        hrp.CanCollide = true
    end
end

---------------------------------------------------------------------
-- LISTA DE FUNÇÕES (UI CORPO)
---------------------------------------------------------------------
local Title = Instance.new("TextLabel")
Title.Text = "ARBIX PVP ⚔️" 
Title.Size = UDim2.new(0.6, 0, 0, 45)
Title.Position = UDim2.new(0, 15, 0, 0)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.fromRGB(0, 200, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = MainFrame

local Body = Instance.new("ScrollingFrame")
Body.Size = UDim2.new(1, -20, 1, -60)
Body.Position = UDim2.new(0, 10, 0, 50)
Body.BackgroundTransparency = 1
Body.ScrollBarThickness = 0
Body.CanvasSize = UDim2.new(0, 0, 0, 330)
Body.Parent = MainFrame

local function createToggle(id, text, emoji, posY)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -5, 0, 40)
    btn.Position = UDim2.new(0, 0, 0, posY)
    btn.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
    btn.Text = emoji .. " " .. text .. ": OFF"
    btn.TextColor3 = Color3.fromRGB(150, 150, 150)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 13
    btn.Parent = Body
    addCorner(btn, 8)
    
    local stroke = Instance.new("UIStroke")
    stroke.Color = Color3.fromRGB(45, 45, 65)
    stroke.Parent = btn
    
    -- Animação de Hover
    btn.MouseEnter:Connect(function()
        anim(btn, 0.3, {BackgroundColor3 = Color3.fromRGB(30, 30, 50)})
    end)
    btn.MouseLeave:Connect(function()
        local targetColor = states[id] and Color3.fromRGB(0, 50, 100) or Color3.fromRGB(20, 20, 35)
        anim(btn, 0.3, {BackgroundColor3 = targetColor})
    end)

    btn.MouseButton1Click:Connect(function()
        states[id] = not states[id]
        local active = states[id]
        
        anim(btn, 0.3, {
            BackgroundColor3 = active and Color3.fromRGB(0, 50, 100) or Color3.fromRGB(20, 20, 35),
            TextColor3 = active and Color3.fromRGB(0, 255, 255) or Color3.fromRGB(150, 150, 150)
        })
        anim(stroke, 0.3, {Color = active and Color3.fromRGB(0, 170, 255) or Color3.fromRGB(45, 45, 65)})
        btn.Text = emoji .. " " .. text .. (active and ": ON" or ": OFF")
    end)
end

createToggle("Hitbox", "Blue Hitbox", "🟦", 10)
createToggle("AntiRagdoll", "Anti-Knockback", "🛡️", 55)
createToggle("InfiniteJump", "Safe Jump", "🚀", 100)
createToggle("SpeedBoost", "Ultra Fast", "👟", 145)
createToggle("Fly", "Safe Fly", "☁️", 190)
createToggle("SpinBot", "Spin (Anti-Aim)", "🌪️", 235)

---------------------------------------------------------------------
-- BOTÕES DE TOPO (FECHAR/MINIMIZAR)
---------------------------------------------------------------------
local function createTopBtn(t, c, x)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 25, 0, 25)
    b.Position = UDim2.new(1, x, 0, 10)
    b.BackgroundColor3 = Color3.fromRGB(25, 25, 45)
    b.Text, b.TextColor3 = t, c
    b.Font, b.TextSize = Enum.Font.GothamBold, 14
    b.Parent = MainFrame
    addCorner(b, 6)
    return b
end

createTopBtn("✕", Color3.fromRGB(255, 60, 60), -35).MouseButton1Click:Connect(function() 
    anim(MainFrame, 0.4, {Size = UDim2.new(0,0,0,0), BackgroundTransparency = 1})
    task.wait(0.4)
    ScreenGui:Destroy() 
end)

local MinBtn = createTopBtn("-", Color3.new(1,1,1), -65)
MinBtn.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    local targetSize = isMinimized and UDim2.new(0, 300, 0, 45) or UDim2.new(0, 300, 0, 340)
    anim(MainFrame, 0.5, {Size = targetSize})
    Body.Visible = not isMinimized
    MinBtn.Text = isMinimized and "+" or "-"
end)

---------------------------------------------------------------------
-- LÓGICA DE VERIFICAÇÃO DE KEY E LOOPS
---------------------------------------------------------------------
CheckBtn.MouseButton1Click:Connect(function()
    if KeyInput.Text == CORRECT_KEY then
        CheckBtn.Text = "ACEITO!"
        anim(CheckBtn, 0.3, {BackgroundColor3 = Color3.fromRGB(0, 200, 100)})
        task.wait(0.5)
        anim(KeyFrame, 0.5, {Size = UDim2.new(0,0,0,0), BackgroundTransparency = 1})
        task.wait(0.4)
        KeyFrame:Destroy()
        
        MainFrame.Visible = true
        MainFrame.Size = UDim2.new(0, 300, 0, 0)
        anim(MainFrame, 0.6, {Size = UDim2.new(0, 300, 0, 340)})
    else
        CheckBtn.Text = "KEY INCORRETA"
        anim(CheckBtn, 0.3, {BackgroundColor3 = Color3.fromRGB(150, 0, 0)})
        task.wait(1)
        CheckBtn.Text = "Verificar Key"
        anim(CheckBtn, 0.3, {BackgroundColor3 = Color3.fromRGB(0, 120, 255)})
    end
end)

-- Loop de Física e Hitbox
RunService.PostSimulation:Connect(function()
    local char = Player.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local hrp, hum, cam = char.HumanoidRootPart, char:FindFirstChildOfClass("Humanoid"), workspace.CurrentCamera

    if states.SpinBot then hrp.CFrame = hrp.CFrame * CFrame.Angles(0, math.rad(60), 0) end
    if states.SpeedBoost and not states.Fly and hum and hum.MoveDirection.Magnitude > 0 then
        hrp.AssemblyLinearVelocity = Vector3_new(hum.MoveDirection.X * 35, hrp.AssemblyLinearVelocity.Y, hum.MoveDirection.Z * 35)
    end
    if states.Fly then
        hrp.AssemblyLinearVelocity = Vector3_zero
        local dir = Vector3_zero
        if UserInputService:IsKeyDown(Enum.KeyCode.W) then dir = dir + cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.S) then dir = dir - cam.CFrame.LookVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.A) then dir = dir - cam.CFrame.RightVector end
        if UserInputService:IsKeyDown(Enum.KeyCode.D) then dir = dir + cam.CFrame.RightVector end
        hrp.AssemblyLinearVelocity = dir * 35 + Vector3_new(0, 0.05, 0)
    end
end)

RunService.Stepped:Connect(function()
    for _, v in pairs(game.Players:GetPlayers()) do
        if v ~= Player then
            if states.Hitbox then applyHitbox(v) else removeHitbox(v) end
        end
    end
end)

UserInputService.JumpRequest:Connect(function()
    if states.InfiniteJump and Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
        Player.Character.HumanoidRootPart.AssemblyLinearVelocity = Vector3_new(Player.Character.HumanoidRootPart.AssemblyLinearVelocity.X, 50, Player.Character.HumanoidRootPart.AssemblyLinearVelocity.Z)
    end
end)

-- Tecla Q para mostrar/ocultar
UserInputService.InputBegan:Connect(function(i, p)
    if not p and i.KeyCode == Enum.KeyCode.Q then
        if MainFrame.Visible then
            anim(MainFrame, 0.4, {Size = UDim2.new(0,0,0,0)})
            task.wait(0.4)
            MainFrame.Visible = false
        else
            MainFrame.Visible = true
            anim(MainFrame, 0.4, {Size = UDim2.new(0, 300, 0, 340)})
        end
    end
end)
