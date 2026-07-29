-- Script: União Blox™ Auto Hop v1.0 (Anti-Erro, Inicialização, RGB & Glitch)
-- Compatível com Delta Executor (Blox Fruits)

local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local VirtualInputManager = game:GetService("VirtualInputManager")
local GuiService = game:GetService("GuiService")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Remove interface anterior se já estiver aberta
if CoreGui:FindFirstChild("UniaoBloxUI") then
    CoreGui.UniaoBloxUI:Destroy()
end

-- ==========================================
-- CRIANDO A INTERFACE
-- ==========================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "UniaoBloxUI"
ScreenGui.Parent = CoreGui

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 280, 0, 90) 
MainFrame.Position = UDim2.new(0.5, -140, 0.08, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

-- Camadas do Efeito Glitch (Ficam atrás do texto principal)
local GlitchRed = Instance.new("TextLabel")
GlitchRed.Size = UDim2.new(1, 0, 0, 30)
GlitchRed.Position = UDim2.new(0, 0, 0, 5)
GlitchRed.BackgroundTransparency = 1
GlitchRed.Text = "União Blox™ Auto Hop v1.0"
GlitchRed.TextColor3 = Color3.fromRGB(255, 0, 50)
GlitchRed.TextSize = 16
GlitchRed.Font = Enum.Font.GothamBold
GlitchRed.Visible = false
GlitchRed.ZIndex = 1
GlitchRed.Parent = MainFrame

local GlitchBlue = GlitchRed:Clone()
GlitchBlue.TextColor3 = Color3.fromRGB(0, 255, 255)
GlitchBlue.Parent = MainFrame

-- Texto Principal (O que vai ficar RGB)
local Watermark = Instance.new("TextLabel")
Watermark.Size = UDim2.new(1, 0, 0, 30)
Watermark.Position = UDim2.new(0, 0, 0, 5)
Watermark.BackgroundTransparency = 1
Watermark.Text = "União Blox™ Auto Hop v1.0"
Watermark.TextColor3 = Color3.fromRGB(255, 255, 255)
Watermark.TextSize = 16
Watermark.Font = Enum.Font.GothamBold
Watermark.ZIndex = 2
Watermark.Parent = MainFrame

local TimerLabel = Instance.new("TextLabel")
TimerLabel.Size = UDim2.new(1, 0, 0, 40)
TimerLabel.Position = UDim2.new(0, 0, 0, 35)
TimerLabel.BackgroundTransparency = 1
TimerLabel.Text = "Inicializando..."
TimerLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
TimerLabel.TextSize = 14
TimerLabel.Font = Enum.Font.Gotham
TimerLabel.Parent = MainFrame

-- ==========================================
-- EFEITOS VISUAIS (RGB E GLITCH)
-- ==========================================
-- Loop do Efeito RGB
task.spawn(function()
    local hue = 0
    while task.wait() do
        hue = hue + 0.003 -- Velocidade da troca de cores
        if hue > 1 then hue = 0 end
        Watermark.TextColor3 = Color3.fromHSV(hue, 1, 1)
    end
end)

-- Loop do Efeito Glitch
task.spawn(function()
    local originPos = UDim2.new(0, 0, 0, 5)
    while true do
        task.wait(math.random(1, 4)) -- Aguarda de 1 a 4 segundos aleatoriamente
        
        -- Ativa o glitch por uma fração de segundo
        for i = 1, math.random(3, 6) do
            GlitchRed.Visible = true
            GlitchBlue.Visible = true
            
            -- Treme as camadas do glitch
            GlitchRed.Position = originPos + UDim2.new(0, math.random(-3, 3), 0, math.random(-2, 2))
            GlitchBlue.Position = originPos + UDim2.new(0, math.random(-3, 3), 0, math.random(-2, 2))
            
            -- Treme o texto principal bem de leve
            Watermark.Position = originPos + UDim2.new(0, math.random(-1, 1), 0, math.random(-1, 1))
            
            task.wait(0.04)
        end
        
        -- Desativa o glitch e reseta a posição
        GlitchRed.Visible = false
        GlitchBlue.Visible = false
        Watermark.Position = originPos
    end
end)

-- ==========================================
-- SISTEMA DE CLIQUE COM COMPENSAÇÃO
-- ==========================================
local function clickElement(guiElement, ajusteX, ajusteY)
    if guiElement and guiElement.AbsolutePosition then
        local inset = GuiService:GetGuiInset()
        
        local centerX = guiElement.AbsolutePosition.X + (guiElement.AbsoluteSize.X / 2) + (ajusteX or 0)
        local centerY = guiElement.AbsolutePosition.Y + (guiElement.AbsoluteSize.Y / 2) + inset.Y + (ajusteY or 0)
        
        VirtualInputManager:SendMouseButtonEvent(centerX, centerY, 0, true, game, 1)
        task.wait(0.1)
        VirtualInputManager:SendMouseButtonEvent(centerX, centerY, 0, false, game, 1)
    end
end

-- ==========================================
-- AUTOMAÇÃO: MENU -> SCROLL -> JOIN VISÍVEL
-- ==========================================
local function executeAutoHop()
    pcall(function()
        local serverBtn = nil
        local highestX = 0
        
        -- 1. ABRIR O MENU DE SERVIDORES
        for _, gui in ipairs(playerGui:GetDescendants()) do
            if gui:IsA("ImageButton") and gui.Visible and gui.AbsoluteSize.X >= 20 then
                if gui.AbsolutePosition.Y < 150 then
                    if gui.AbsolutePosition.X > highestX then
                        highestX = gui.AbsolutePosition.X
                        serverBtn = gui
                    end
                end
            end
        end

        if serverBtn then
            clickElement(serverBtn, 15, 0)
        end

        task.wait(2) 

        -- 2. ENCONTRAR A LISTA DE ROLAGEM E DESCER
        local scrollFrame = nil
        for _, frame in ipairs(playerGui:GetDescendants()) do
            if frame:IsA("ScrollingFrame") and frame.Visible and frame.AbsoluteSize.Y > 150 then
                scrollFrame = frame
                frame.CanvasPosition = Vector2.new(0, frame.CanvasPosition.Y + math.random(250, 400))
                break 
            end
        end

        task.wait(1.5) 

        -- 3. ACHAR UM BOTÃO "JOIN" QUE ESTEJA VISÍVEL NA TELA
        local joinButtons = {}
        if scrollFrame then
            local sfTop = scrollFrame.AbsolutePosition.Y
            local sfBottom = sfTop + scrollFrame.AbsoluteSize.Y

            for _, btn in ipairs(playerGui:GetDescendants()) do
                if btn:IsA("TextButton") and btn.Visible then
                    local txt = string.lower(btn.Text)
                    if txt == "join" or txt == "entrar" then
                        
                        local btnTop = btn.AbsolutePosition.Y
                        local btnBottom = btnTop + btn.AbsoluteSize.Y

                        if btnTop >= sfTop and btnBottom <= sfBottom then
                            table.insert(joinButtons, btn)
                        end
                        
                    end
                end
            end
        end

        if #joinButtons > 0 then
            local targetJoin = joinButtons[math.random(1, #joinButtons)]
            clickElement(targetJoin, 20, 0)
        end
    end)
end

-- ==========================================
-- LOOP DE TEMPO (INICIALIZAÇÃO + CRONÔMETRO)
-- ==========================================
task.spawn(function()
    -- Fase 1: Inicialização e Carregamento (10s)
    TimerLabel.Text = "Inicializando..."
    task.wait(1)
    
    for i = 10, 1, -1 do
        TimerLabel.Text = "Aguardando carregamento do jogo (" .. i .. "s)"
        task.wait(1)
    end
    
    -- Fase 2: Loop Principal do Auto Hop (20s)
    while true do
        for i = 20, 1, -1 do
            TimerLabel.Text = "Próximo Hop em: " .. i .. "s"
            task.wait(1)
        end
        TimerLabel.Text = "Escaneando e Entrando..."
        task.wait(1)
        executeAutoHop()
        task.wait(5)
    end
end)
