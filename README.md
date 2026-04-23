local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local root = character:WaitForChild("HumanoidRootPart")
local vim = game:GetService("VirtualInputManager")

-- // CONFIGURAÇÕES DO FRED
local FredConfig = {
    Ativo = true,
    DistanciaBase = 11,
    AguardarDrible = true,
    ReacaoMin = 0.08,
    ReacaoMax = 0.22,
    Cooldown = 1.9,
    UltimoTackle = 0,
    TeclaTackle = Enum.KeyCode.E
}

-- // CRIAÇÃO DO MOD MENU VISUAL
local ScreenGui = Instance.new("ScreenGui", player.PlayerGui)
ScreenGui.Name = "FredUltraMenu"

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 220, 0, 180)
MainFrame.Position = UDim2.new(0.1, 0, 0.4, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true -- Permite arrastar o menu com o mouse

-- Bordas Arredondadas (UICorner)
local Corner = Instance.new("UICorner", MainFrame)
Corner.CornerRadius = UDim.new(0, 8)

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 35)
Title.Text = "⚽ FRED SOCCER V2"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 14
local TitleCorner = Instance.new("UICorner", Title)

-- Botão 1: Ativar/Desativar
local ToggleBtn = Instance.new("TextButton", MainFrame)
ToggleBtn.Size = UDim2.new(0.9, 0, 0, 40)
ToggleBtn.Position = UDim2.new(0.05, 0, 0.25, 0)
ToggleBtn.Text = "AUTO-TACKLE: LIGADO"
ToggleBtn.Font = Enum.Font.Gotham
ToggleBtn.TextSize = 12
ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 100)
Instance.new("UICorner", ToggleBtn)

-- Botão 2: Modo de Espera
local ModeBtn = Instance.new("TextButton", MainFrame)
ModeBtn.Size = UDim2.new(0.9, 0, 0, 40)
ModeBtn.Position = UDim2.new(0.05, 0, 0.55, 0)
ModeBtn.Text = "ESPERAR DRIBLE: SIM"
ModeBtn.Font = Enum.Font.Gotham
ModeBtn.TextSize = 12
ModeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ModeBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
Instance.new("UICorner", ModeBtn)

-- Créditos
local Credits = Instance.new("TextLabel", MainFrame)
Credits.Size = UDim2.new(1, 0, 0, 20)
Credits.Position = UDim2.new(0, 0, 0.85, 0)
Credits.Text = "By Fred - Stealth Mode Active"
Credits.BackgroundTransparency = 1
Credits.TextColor3 = Color3.fromRGB(100, 100, 100)
Credits.TextSize = 10

-- // LÓGICA DE DETECÇÃO
local function estaDriblando(adversario)
    local humanoid = adversario:FindFirstChild("Humanoid")
    if not humanoid then return false end
    for _, anim in pairs(humanoid:GetPlayingAnimationTracks()) do
        local n = anim.Name:lower()
        if n:find("dribble") or n:find("skill") or n:find("trick") or n:find("kick") then
            return true
        end
    end
    return adversario.HumanoidRootPart.AssemblyLinearVelocity.Magnitude > 23
end

local function executarCarrinhoSafe()
    local d = math.random(50, 100) / 1000
    vim:SendKeyEvent(true, FredConfig.TeclaTackle, false, game)
    task.wait(d)
    vim:SendKeyEvent(false, FredConfig.TeclaTackle, false, game)
end

-- // LOOP DO SCRIPT
task.spawn(function()
    while true do
        task.wait(0.05)
        if FredConfig.Ativo and tick() - FredConfig.UltimoTackle > (FredConfig.Cooldown + math.random()) then
            for _, outro in pairs(game.Players:GetPlayers()) do
                if outro ~= player and outro.Character and outro.Character:FindFirstChild("HumanoidRootPart") then
                    local eRoot = outro.Character.HumanoidRootPart
                    local dReal = (root.Position - eRoot.Position).Magnitude
                    local dRand = FredConfig.DistanciaBase + math.random(-2, 2)
                    
                    if dReal <= dRand then
                        if not FredConfig.AguardarDrible or estaDriblando(outro.Character) then
                            task.wait(math.random(FredConfig.ReacaoMin * 100, FredConfig.ReacaoMax * 100) / 100)
                            executarCarrinhoSafe()
                            FredConfig.UltimoTackle = tick()
                        end
                    end
                end
            end
        end
    end
end)

-- // FUNÇÕES DOS BOTÕES
ToggleBtn.MouseButton1Click:Connect(function()
    FredConfig.Ativo = not FredConfig.Ativo
    ToggleBtn.Text = FredConfig.Ativo and "AUTO-TACKLE: LIGADO" or "AUTO-TACKLE: DESLIGADO"
    ToggleBtn.BackgroundColor3 = FredConfig.Ativo and Color3.fromRGB(0, 170, 100) or Color3.fromRGB(170, 0, 50)
end)

ModeBtn.MouseButton1Click:Connect(function()
    FredConfig.AguardarDrible = not FredConfig.AguardarDrible
    ModeBtn.Text = FredConfig.AguardarDrible and "ESPERAR DRIBLE: SIM" or "ESPERAR DRIBLE: NÃO"
    ModeBtn.BackgroundColor3 = FredConfig.AguardarDrible and Color3.fromRGB(0, 120, 255) or Color3.fromRGB(100, 100, 100)
end)
