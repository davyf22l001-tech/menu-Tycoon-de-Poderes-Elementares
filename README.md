--[[
    SUPER MENU UNIFICADO - ELEMENTAL POWERS TYCOON
    Desenvolvido por: Manus AI
    
    Funcionalidades Integradas:
    1. Aba de Poderes: Desbloqueio de magias (Dark Flames, Cruel Sun, etc.)
    2. Aba de Velocidade: Ajuste de WalkSpeed persistente.
    3. Aba de Teleporte: Lista de jogadores para teletransporte instantâneo.
--]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

-- Configurações Iniciais
local VELOCIDADE_PADRAO = 100
local ATIVADO_SPEED = false
local PODERES_LISTA = {
    "Dark Flames", "Cruel Sun", "Halloween Sword", "Draedron's Tech", "Yoru", "Plasma Orbs", 
    "Undead Staff", "Sonic Barrage", "Rebound Blast", "sonic boom", 
    "Super Sonic Wave", "Tesseract", "Tesla Turret", "Twin-Photon Blast", 
    "Hyper Slash", "Photon Blast", "Sonic Twister", "Nuclear Spore", 
    "Pine Burst", "Nature's Wrath", "Warp Bomb", "Time Trap", "Tempo Beam", 
    "Fire Bomb", "Comet", "Combust", "Fire Shower", "Elysian Beam", 
    "Shadow Sword", "Dark Hold", "Frost Staff", "Ice Disk", 
    "Frost Fire Bomb", "Ultracold Aura", "Ice Spikes"
}

-- Função para obter o RemoteEvent
local function gRE()
    return ReplicatedStorage:WaitForChild("Remotes", 5):FindFirstChild("RemoteEvent") 
    or ReplicatedStorage:FindFirstChild("RemoteEvent")
end

-- Criando a Interface (GUI)
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SuperMenuManus"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -150)
MainFrame.Size = UDim2.new(0, 300, 0, 350)
MainFrame.Visible = false
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel")
Title.Parent = MainFrame
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "F22 MENU-Elementares"
Title.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 20

-- Botão de Abrir/Fechar
local ToggleButton = Instance.new("TextButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Parent = ScreenGui
ToggleButton.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
ToggleButton.Position = UDim2.new(0, 10, 0.5, -20)
ToggleButton.Size = UDim2.new(0, 100, 0, 40)
ToggleButton.Text = "Abrir Menu"
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 16

ToggleButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
    ToggleButton.Text = MainFrame.Visible and "Fechar Menu" or "Abrir Menu"
end)

-- Sistema de Abas
local TabButtons = Instance.new("Frame")
TabButtons.Parent = MainFrame
TabButtons.Position = UDim2.new(0, 0, 0, 40)
TabButtons.Size = UDim2.new(1, 0, 0, 30)
TabButtons.BackgroundColor3 = Color3.fromRGB(40, 40, 40)

local function criarAbaBtn(nome, pos)
    local btn = Instance.new("TextButton")
    btn.Parent = TabButtons
    btn.Size = UDim2.new(0.33, 0, 1, 0)
    btn.Position = UDim2.new(pos * 0.33, 0, 0, 0)
    btn.Text = nome
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.SourceSansBold
    return btn
end

local btnPoderes = criarAbaBtn("Poderes", 0)
local btnSpeed = criarAbaBtn("Velocidade", 1)
local btnTeleport = criarAbaBtn("Teleporte", 2)

local ContentFrame = Instance.new("Frame")
ContentFrame.Parent = MainFrame
ContentFrame.Position = UDim2.new(0, 0, 0, 70)
ContentFrame.Size = UDim2.new(1, 0, 1, -70)
ContentFrame.BackgroundTransparency = 1

local function limparConteudo()
    for _, child in pairs(ContentFrame:GetChildren()) do
        child:Destroy()
    end
end

-- --- ABA PODERES ---
local function showPoderes()
    limparConteudo()
    local scroll = Instance.new("ScrollingFrame")
    scroll.Parent = ContentFrame
    scroll.Size = UDim2.new(1, 0, 1, 0)
    scroll.CanvasSize = UDim2.new(0, 0, 0, #PODERES_LISTA * 35)
    scroll.ScrollBarThickness = 5
    
    local list = Instance.new("UIListLayout")
    list.Parent = scroll
    list.Padding = UDim.new(0, 5)
    
    local btnUnlockAll = Instance.new("TextButton")
    btnUnlockAll.Parent = scroll
    btnUnlockAll.Size = UDim2.new(1, -10, 0, 40)
    btnUnlockAll.Text = "DESBLOQUEAR TODOS"
    btnUnlockAll.BackgroundColor3 = Color3.fromRGB(0, 120, 0)
    btnUnlockAll.TextColor3 = Color3.new(1, 1, 1)
    btnUnlockAll.Font = Enum.Font.SourceSansBold
    
    btnUnlockAll.MouseButton1Click:Connect(function()
        local RE = gRE()
        if RE then
            for _, m in ipairs(PODERES_LISTA) do
                RE:FireServer("equip_mystery_spell", m)
            end
            print("Todos os poderes solicitados!")
        end
    end)
    
    for _, nome in ipairs(PODERES_LISTA) do
        local btn = Instance.new("TextButton")
        btn.Parent = scroll
        btn.Size = UDim2.new(1, -10, 0, 30)
        btn.Text = nome
        btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        btn.TextColor3 = Color3.new(1, 1, 1)
        
        btn.MouseButton1Click:Connect(function()
            local RE = gRE()
            if RE then
                RE:FireServer("equip_mystery_spell", nome)
            end
        end)
    end
end

-- --- ABA VELOCIDADE ---
local function showSpeed()
    limparConteudo()
    local label = Instance.new("TextLabel")
    label.Parent = ContentFrame
    label.Size = UDim2.new(1, 0, 0, 40)
    label.Text = "Velocidade Atual: " .. VELOCIDADE_PADRAO
    label.TextColor3 = Color3.new(1, 1, 1)
    label.BackgroundTransparency = 1
    
    local box = Instance.new("TextBox")
    box.Parent = ContentFrame
    box.Position = UDim2.new(0.1, 0, 0, 50)
    box.Size = UDim2.new(0.8, 0, 0, 40)
    box.Text = tostring(VELOCIDADE_PADRAO)
    box.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    box.TextColor3 = Color3.new(1, 1, 1)
    
    local btnSet = Instance.new("TextButton")
    btnSet.Parent = ContentFrame
    btnSet.Position = UDim2.new(0.1, 0, 0, 100)
    btnSet.Size = UDim2.new(0.8, 0, 0, 40)
    btnSet.Text = "APLICAR VELOCIDADE"
    btnSet.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    btnSet.TextColor3 = Color3.new(1, 1, 1)
    
    btnSet.MouseButton1Click:Connect(function()
        local val = tonumber(box.Text)
        if val then
            VELOCIDADE_PADRAO = val
            label.Text = "Velocidade Atual: " .. VELOCIDADE_PADRAO
        end
    end)
end

-- --- ABA TELEPORTE ---
local function showTeleport()
    limparConteudo()
    local scroll = Instance.new("ScrollingFrame")
    scroll.Parent = ContentFrame
    scroll.Size = UDim2.new(1, 0, 1, 0)
    scroll.ScrollBarThickness = 5
    
    local list = Instance.new("UIListLayout")
    list.Parent = scroll
    list.Padding = UDim.new(0, 5)
    
    local function atualizar()
        for _, child in pairs(scroll:GetChildren()) do
            if child:IsA("TextButton") then child:Destroy() end
        end
        
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer then
                local btn = Instance.new("TextButton")
                btn.Parent = scroll
                btn.Size = UDim2.new(1, -10, 0, 30)
                btn.Text = p.DisplayName or p.Name
                btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
                btn.TextColor3 = Color3.new(1, 1, 1)
                
                btn.MouseButton1Click:Connect(function()
                    if p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                        LocalPlayer.Character.HumanoidRootPart.CFrame = p.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
                    end
                end)
            end
        end
        scroll.CanvasSize = UDim2.new(0, 0, 0, list.AbsoluteContentSize.Y)
    end
    
    atualizar()
    Players.PlayerAdded:Connect(atualizar)
    Players.PlayerRemoving:Connect(atualizar)
end

-- Eventos de Clique nas Abas
btnPoderes.MouseButton1Click:Connect(showPoderes)
btnSpeed.MouseButton1Click:Connect(showSpeed)
btnTeleport.MouseButton1Click:Connect(showTeleport)

-- Lógica de Velocidade Persistente
local function aplicarSpeed(char)
    local hum = char:WaitForChild("Humanoid")
    task.spawn(function()
        while ATIVADO_SPEED and char.Parent do
            if hum.WalkSpeed ~= VELOCIDADE_PADRAO then
                hum.WalkSpeed = VELOCIDADE_PADRAO
            end
            task.wait(0.1)
        end
    end)
end

if LocalPlayer.Character then aplicarSpeed(LocalPlayer.Character) end
LocalPlayer.CharacterAdded:Connect(aplicarSpeed)

-- Inicialização
showPoderes()
print("[Manus AI] Super Menu Unificado Carregado com Sucesso!")
