-- =============================================================================
-- DESGRAÇADÃO HUB - GOD EDITION (ULTIMATE OVERPOWERED) - V4
-- =============================================================================
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local TeleportService = game:GetService("TeleportService")
local Lighting = game:GetService("Lighting")

local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera
local mouse = localPlayer:GetMouse()

-- =============================================================================
-- ESTADO / CONFIGURAÇÕES GLOBAIS
-- =============================================================================
local TELEPORT_ATIVO = false
local ANTI_HIT_ATIVO = false
local COMBO_AUTO_ATIVO = false
local FLY_ATIVO = false
local NOCLIP_ATIVO = false
local INF_JUMP_ATIVO = false
local ESP_ATIVO = false
local ESP_BOX_ATIVO = false
local ESP_TRACERS_ATIVO = false
local ESP_NAMES_ATIVO = false
local SPECTATE_ATIVO = false
local HITBOX_ATIVO = false
local INVISIVEL_ATIVO = false

-- Recursos Overpowered V3 & V4 (A Coisa Mais Roubada do Mundo)
local GOD_MODE_ATIVO = false
local KILL_AURA_ATIVO = false
local FLING_ATIVO = false
local SPINBOT_ATIVO = false
local AIMBOT_ATIVO = false
local SHOW_FOV = false
local WALLBANG_ATIVO = false
local FULLBRIGHT_ATIVO = false
local GLOBAL_HITBOX_ATIVO = false
local SERVER_CRASHER_ATIVO = false
local AUTO_EQUIP_TOOL = true
local CLICK_TP_KEY = Enum.KeyCode.LeftControl

local ALVO_SELECIONADO = "Mais Próximo"
local DISTANCIA_MAXIMA = 99999
local PROFUNDIDADE_TERRA = 5.5
local FLY_SPEED = 80
local WALK_SPEED_VAL = 16
local JUMP_POWER_VAL = 50
local GRAVITY_VAL = 196.2
local HITBOX_SIZE = 18
local FOV_RADIUS = 180
local AURA_RANGE = 25

-- Estado Interno
local flyBodyVel, flyBodyGyro
local flyKeys = {W = false, A = false, S = false, D = false, Up = false, Down = false}
local tracerObjects = {}
local textObjects = {}
local boxObjects = {}

-- Círculo de FOV
local FOVCircle = Drawing.new("Circle")
FOVCircle.Thickness = 1.5
FOVCircle.Color = Color3.fromRGB(0, 255, 170)
FOVCircle.Filled = false
FOVCircle.Transparency = 0.9
FOVCircle.NumSides = 40
FOVCircle.Visible = false

-- =============================================================================
-- INTERFACE GRÁFICA (UI)
-- =============================================================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "DesgracadaoHubUI"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local uiAntiga = localPlayer:WaitForChild("PlayerGui"):FindFirstChild("DesgracadaoHubUI")
if uiAntiga then uiAntiga:Destroy() end
ScreenGui.Parent = localPlayer:WaitForChild("PlayerGui")

local MenuToggleBtn = Instance.new("TextButton")
MenuToggleBtn.Size = UDim2.new(0, 130, 0, 35)
MenuToggleBtn.Position = UDim2.new(0, 10, 0, 10)
MenuToggleBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
MenuToggleBtn.Text = "Fechar Hub"
MenuToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MenuToggleBtn.Font = Enum.Font.GothamBold
MenuToggleBtn.TextSize = 12
MenuToggleBtn.Parent = ScreenGui

local MenuCorner = Instance.new("UICorner")
MenuCorner.CornerRadius = UDim.new(0, 6)
MenuCorner.Parent = MenuToggleBtn

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 500, 0, 580)
MainFrame.Position = UDim2.new(0.5, -250, 0.5, -290)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

-- Sistema de Arrastar UI
local dragging, dragInput, dragStart, startPos
MainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then dragging = false end
		end)
	end
end)
MainFrame.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)
UserInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		local delta = input.Position - dragStart
		MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 38)
Title.BackgroundTransparency = 1
Title.Text = "🔥 DESGRAÇADÃO HUB - GOD EDITION V4 🔥"
Title.TextColor3 = Color3.fromRGB(0, 220, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 15
Title.Parent = MainFrame

local TabContainer = Instance.new("Frame")
TabContainer.Size = UDim2.new(0.94, 0, 0, 30)
TabContainer.Position = UDim2.new(0.03, 0, 0, 42)
TabContainer.BackgroundTransparency = 1
TabContainer.Parent = MainFrame

local TabListLayout = Instance.new("UIListLayout")
TabListLayout.FillDirection = Enum.FillDirection.Horizontal
TabListLayout.Padding = UDim.new(0, 3)
TabListLayout.Parent = TabContainer

local PagesContainer = Instance.new("Frame")
PagesContainer.Size = UDim2.new(0.94, 0, 0, 485)
PagesContainer.Position = UDim2.new(0.03, 0, 0, 80)
PagesContainer.BackgroundTransparency = 1
PagesContainer.Parent = MainFrame

local Pages = {}

local function criarAba(nome)
	local TabBtn = Instance.new("TextButton")
	TabBtn.Size = UDim2.new(0.138, 0, 1, 0)
	TabBtn.BackgroundColor3 = Color3.fromRGB(28, 28, 35)
	TabBtn.Text = nome
	TabBtn.TextColor3 = Color3.fromRGB(180, 180, 190)
	TabBtn.Font = Enum.Font.GothamBold
	TabBtn.TextSize = 8
	TabBtn.Parent = TabContainer

	local TabBtnCorner = Instance.new("UICorner")
	TabBtnCorner.CornerRadius = UDim.new(0, 5)
	TabBtnCorner.Parent = TabBtn

	local Page = Instance.new("ScrollingFrame")
	Page.Size = UDim2.new(1, 0, 1, 0)
	Page.BackgroundTransparency = 1
	Page.BorderSizePixel = 0
	Page.ScrollBarThickness = 4
	Page.Visible = false
	Page.Parent = PagesContainer

	local PageLayout = Instance.new("UIListLayout")
	PageLayout.Padding = UDim.new(0, 8)
	PageLayout.Parent = Page

	Pages[nome] = {Btn = TabBtn, Page = Page}

	TabBtn.MouseButton1Click:Connect(function()
		for _, data in pairs(Pages) do
			data.Page.Visible = false
			data.Btn.BackgroundColor3 = Color3.fromRGB(28, 28, 35)
			data.Btn.TextColor3 = Color3.fromRGB(180, 180, 190)
		end
		Page.Visible = true
		TabBtn.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
		TabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
	end)

	return Page
end

local OpPage = criarAba("👑 OP")
local CombatPage = criarAba("⚔️ Combate")
local AimPage = criarAba("🎯 Aim")
local MovePage = criarAba("🚀 Mover")
local VisualPage = criarAba("🎭 Visuais")
local DancePage = criarAba("💃 Danças")
local UtilPage = criarAba("🛠️ Utils")

Pages["👑 OP"].Page.Visible = true
Pages["👑 OP"].Btn.BackgroundColor3 = Color3.fromRGB(0, 140, 255)
Pages["👑 OP"].Btn.TextColor3 = Color3.fromRGB(255, 255, 255)

MenuToggleBtn.MouseButton1Click:Connect(function()
	MainFrame.Visible = not MainFrame.Visible
	MenuToggleBtn.Text = MainFrame.Visible and "Fechar Hub" or "Abrir Hub"
end)

local function criarBotao(parent, texto, callback)
	local Btn = Instance.new("TextButton")
	Btn.Size = UDim2.new(1, 0, 0, 34)
	Btn.BackgroundColor3 = Color3.fromRGB(32, 32, 40)
	Btn.Text = texto
	Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	Btn.Font = Enum.Font.GothamBold
	Btn.TextSize = 11
	Btn.Parent = parent

	local Corner = Instance.new("UICorner")
	Corner.CornerRadius = UDim.new(0, 6)
	Corner.Parent = Btn

	Btn.MouseButton1Click:Connect(function() callback(Btn) end)
	return Btn
end

-- =============================================================================
-- ABA 1: OVERPOWERED (EXPLOITS DE ALTO NÍVEL + A COISA MAIS ROUBADA DO MUNDO)
-- =============================================================================
criarBotao(OpPage, "🌌 PARALISIA ABSOLUTA GLOBAL (Congelar Servidor): OFF", function(btn)
	SERVER_CRASHER_ATIVO = not SERVER_CRASHER_ATIVO
	btn.BackgroundColor3 = SERVER_CRASHER_ATIVO and Color3.fromRGB(255, 40, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🌌 Paralisia Absoluta Global: " .. (SERVER_CRASHER_ATIVO and "ON" or "OFF")
end)

criarBotao(OpPage, "👑 God Mode Extreme (Invencibilidade): OFF", function(btn)
	GOD_MODE_ATIVO = not GOD_MODE_ATIVO
	btn.BackgroundColor3 = GOD_MODE_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "👑 God Mode Extreme: " .. (GOD_MODE_ATIVO and "ON" or "OFF")

	local char = localPlayer.Character
	if char then
		local hum = char:FindFirstChildOfClass("Humanoid")
		if hum then
			hum:SetStateEnabled(Enum.HumanoidStateType.Dead, not GOD_MODE_ATIVO)
		end
	end
end)

criarBotao(OpPage, "🗡️ Kill Aura Extrema (Auto Attack + Equip): OFF", function(btn)
	KILL_AURA_ATIVO = not KILL_AURA_ATIVO
	btn.BackgroundColor3 = KILL_AURA_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🗡️ Kill Aura Extrema: " .. (KILL_AURA_ATIVO and "ON" or "OFF")
end)

criarBotao(OpPage, "💥 Ultimate Fling (Arremessar Inimigos): OFF", function(btn)
	FLING_ATIVO = not FLING_ATIVO
	btn.BackgroundColor3 = FLING_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "💥 Ultimate Fling: " .. (FLING_ATIVO and "ON" or "OFF")
end)

criarBotao(OpPage, "🎯 Global Hitbox Extender (Todos do Servidor): OFF", function(btn)
	GLOBAL_HITBOX_ATIVO = not GLOBAL_HITBOX_ATIVO
	btn.BackgroundColor3 = GLOBAL_HITBOX_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🎯 Global Hitbox Extender: " .. (GLOBAL_HITBOX_ATIVO and "ON" or "OFF")

	if not GLOBAL_HITBOX_ATIVO then
		for _, p in ipairs(Players:GetPlayers()) do
			if p ~= localPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
				p.Character.HumanoidRootPart.Size = Vector3.new(2, 2, 1)
				p.Character.HumanoidRootPart.Transparency = 1
			end
		end
	end
end)

criarBotao(OpPage, "🌀 Spinbot Rápido: OFF", function(btn)
	SPINBOT_ATIVO = not SPINBOT_ATIVO
	btn.BackgroundColor3 = SPINBOT_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🌀 Spinbot Rápido: " .. (SPINBOT_ATIVO and "ON" or "OFF")
end)

criarBotao(OpPage, "🧲 Bring Target (Puxar Jogador Alvo)", function()
	local meuRoot = localPlayer.Character and localPlayer.Character:FindFirstChild("HumanoidRootPart")
	local alvo = obterAlvoValido()
	if meuRoot and alvo and alvo:FindFirstChild("HumanoidRootPart") then
		alvo.HumanoidRootPart.CFrame = meuRoot.CFrame * CFrame.new(0, 0, -3)
	end
end)

criarBotao(OpPage, "🖱️ Click TP (Ctrl + Clique)", function(btn)
	btn.Text = "Status: Ativo (Ctrl + Clique no Mapa)"
	task.wait(1.5)
	btn.Text = "🖱️ Click TP (Ctrl + Clique)"
end)

UserInputService.InputBegan:Connect(function(input, gpe)
	if gpe then return end
	if input.UserInputType == Enum.UserInputType.MouseButton1 and UserInputService:IsKeyDown(CLICK_TP_KEY) then
		local char = localPlayer.Character
		if char and char:FindFirstChild("HumanoidRootPart") then
			char.HumanoidRootPart.CFrame = CFrame.new(mouse.Hit.Position + Vector3.new(0, 3, 0))
		end
	end
end)

-- =============================================================================
-- ABA 2: COMBATE AVANÇADO
-- =============================================================================
local TargetLabel = Instance.new("TextLabel")
TargetLabel.Size = UDim2.new(1, 0, 0, 15)
TargetLabel.BackgroundTransparency = 1
TargetLabel.Text = "SELECIONAR ALVO:"
TargetLabel.TextColor3 = Color3.fromRGB(150, 150, 160)
TargetLabel.Font = Enum.Font.GothamBold
TargetLabel.TextSize = 10
TargetLabel.TextXAlignment = Enum.TextXAlignment.Left
TargetLabel.Parent = CombatPage

local TargetScroll = Instance.new("ScrollingFrame")
TargetScroll.Size = UDim2.new(1, 0, 0, 80)
TargetScroll.BackgroundColor3 = Color3.fromRGB(15, 15, 18)
TargetScroll.BorderSizePixel = 0
TargetScroll.ScrollBarThickness = 3
TargetScroll.Parent = CombatPage

local TargetLayout = Instance.new("UIListLayout")
TargetLayout.Padding = UDim.new(0, 3)
TargetLayout.Parent = TargetScroll

function atualizarListaAlvos()
	for _, obj in ipairs(TargetScroll:GetChildren()) do
		if obj:IsA("TextButton") then obj:Destroy() end
	end

	local lista = {"Mais Próximo"}
	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= localPlayer then table.insert(lista, p.Name) end
	end

	for _, nome in ipairs(lista) do
		local PlayerBtn = Instance.new("TextButton")
		PlayerBtn.Size = UDim2.new(1, -6, 0, 24)
		PlayerBtn.Text = nome
		PlayerBtn.Font = Enum.Font.Gotham
		PlayerBtn.TextSize = 10
		PlayerBtn.BackgroundColor3 = (ALVO_SELECIONADO == nome) and Color3.fromRGB(0, 140, 255) or Color3.fromRGB(28, 28, 34)
		PlayerBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

		PlayerBtn.MouseButton1Click:Connect(function()
			ALVO_SELECIONADO = nome
			atualizarListaAlvos()
		end)
		PlayerBtn.Parent = TargetScroll
	end
	TargetScroll.CanvasSize = UDim2.new(0, 0, 0, TargetLayout.AbsoluteContentSize.Y + 5)
end

Players.PlayerAdded:Connect(atualizarListaAlvos)
Players.PlayerRemoving:Connect(atualizarListaAlvos)
atualizarListaAlvos()

criarBotao(CombatPage, "TP SUBTERRÂNEO: DESLIGADO", function(btn)
	TELEPORT_ATIVO = not TELEPORT_ATIVO
	btn.BackgroundColor3 = TELEPORT_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "TP SUBTERRÂNEO: " .. (TELEPORT_ATIVO and "ATIVADO" or "DESLIGADO")
end)

criarBotao(CombatPage, "🛡️ Anti-Hit Desync (Fantasmar): OFF", function(btn)
	ANTI_HIT_ATIVO = not ANTI_HIT_ATIVO
	btn.BackgroundColor3 = ANTI_HIT_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🛡️ Anti-Hit Desync: " .. (ANTI_HIT_ATIVO and "ON" or "OFF")
end)

criarBotao(CombatPage, "⚔️ Auto Fast Attack (Ataque Contínuo): OFF", function(btn)
	COMBO_AUTO_ATIVO = not COMBO_AUTO_ATIVO
	btn.BackgroundColor3 = COMBO_AUTO_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "⚔️ Auto Fast Attack: " .. (COMBO_AUTO_ATIVO and "ON" or "OFF")
end)

criarBotao(CombatPage, "🎯 Hitbox Single Target: OFF", function(btn)
	HITBOX_ATIVO = not HITBOX_ATIVO
	btn.BackgroundColor3 = HITBOX_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🎯 Hitbox Single Target: " .. (HITBOX_ATIVO and "ON" or "OFF")
end)

-- =============================================================================
-- ABA 3: AIMBOT & FOV
-- =============================================================================
criarBotao(AimPage, "🎯 Camera Lock Aimbot: OFF", function(btn)
	AIMBOT_ATIVO = not AIMBOT_ATIVO
	btn.BackgroundColor3 = AIMBOT_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🎯 Camera Lock Aimbot: " .. (AIMBOT_ATIVO and "ON" or "OFF")
end)

criarBotao(AimPage, "⭕ Mostrar Círculo FOV: OFF", function(btn)
	SHOW_FOV = not SHOW_FOV
	FOVCircle.Visible = SHOW_FOV
	btn.BackgroundColor3 = SHOW_FOV and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "⭕ Mostrar Círculo FOV: " .. (SHOW_FOV and "ON" or "OFF")
end)

criarBotao(AimPage, "🧱 Wallbang (Atravessar Obstáculos): OFF", function(btn)
	WALLBANG_ATIVO = not WALLBANG_ATIVO
	btn.BackgroundColor3 = WALLBANG_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🧱 Wallbang: " .. (WALLBANG_ATIVO and "ON" or "OFF")

	for _, v in ipairs(workspace:GetDescendants()) do
		if v:IsA("BasePart") and not v:IsDescendantOf(localPlayer.Character) then
			if WALLBANG_ATIVO then
				if v.CanCollide and v.Size.Magnitude < 60 then
					v.CanCollide = false
				end
			else
				v.CanCollide = true
			end
		end
	end
end)

-- =============================================================================
-- ABA 4: MOVIMENTAÇÃO & FÍSICA
-- =============================================================================
local function ativarFly(ativo)
	FLY_ATIVO = ativo
	local char = localPlayer.Character
	if not char or not char:FindFirstChild("HumanoidRootPart") then return end
	local root = char.HumanoidRootPart

	if FLY_ATIVO then
		flyBodyVel = Instance.new("BodyVelocity")
		flyBodyVel.MaxForce = Vector3.new(1, 1, 1) * 9e9
		flyBodyVel.Velocity = Vector3.new(0, 0, 0)
		flyBodyVel.Parent = root

		flyBodyGyro = Instance.new("BodyGyro")
		flyBodyGyro.MaxTorque = Vector3.new(1, 1, 1) * 9e9
		flyBodyGyro.CFrame = root.CFrame
		flyBodyGyro.Parent = root
	else
		if flyBodyVel then flyBodyVel:Destroy() end
		if flyBodyGyro then flyBodyGyro:Destroy() end
	end
end

criarBotao(MovePage, "🕊️ Fly Ultra: OFF", function(btn)
	ativarFly(not FLY_ATIVO)
	btn.BackgroundColor3 = FLY_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🕊️ Fly Ultra: " .. (FLY_ATIVO and "ON" or "OFF")
end)

criarBotao(MovePage, "🧱 Noclip Total: OFF", function(btn)
	NOCLIP_ATIVO = not NOCLIP_ATIVO
	btn.BackgroundColor3 = NOCLIP_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🧱 Noclip Total: " .. (NOCLIP_ATIVO and "ON" or "OFF")
end)

criarBotao(MovePage, "🦘 Pulo Infinito: OFF", function(btn)
	INF_JUMP_ATIVO = not INF_JUMP_ATIVO
	btn.BackgroundColor3 = INF_JUMP_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🦘 Pulo Infinito: " .. (INF_JUMP_ATIVO and "ON" or "OFF")
end)

local SpeedInput = Instance.new("TextBox")
SpeedInput.Size = UDim2.new(1, 0, 0, 32)
SpeedInput.BackgroundColor3 = Color3.fromRGB(28, 28, 35)
SpeedInput.PlaceholderText = "Mudar Velocidade (Ex: 60)..."
SpeedInput.Text = ""
SpeedInput.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedInput.Font = Enum.Font.Gotham
SpeedInput.TextSize = 11
SpeedInput.Parent = MovePage

local SpeedCorner = Instance.new("UICorner")
SpeedCorner.CornerRadius = UDim.new(0, 6)
SpeedCorner.Parent = SpeedInput

SpeedInput.FocusLost:Connect(function()
	local val = tonumber(SpeedInput.Text)
	if val then
		WALK_SPEED_VAL = val
		local char = localPlayer.Character
		if char and char:FindFirstChildOfClass("Humanoid") then
			char:FindFirstChildOfClass("Humanoid").WalkSpeed = val
		end
	end
end)

local GravityInput = Instance.new("TextBox")
GravityInput.Size = UDim2.new(1, 0, 0, 32)
GravityInput.BackgroundColor3 = Color3.fromRGB(28, 28, 35)
GravityInput.PlaceholderText = "Mudar Gravidade (Padrão: 196.2)..."
GravityInput.Text = ""
GravityInput.TextColor3 = Color3.fromRGB(255, 255, 255)
GravityInput.Font = Enum.Font.Gotham
GravityInput.TextSize = 11
GravityInput.Parent = MovePage

local GravityCorner = Instance.new("UICorner")
GravityCorner.CornerRadius = UDim.new(0, 6)
GravityCorner.Parent = GravityInput

GravityInput.FocusLost:Connect(function()
	local val = tonumber(GravityInput.Text)
	if val then
		workspace.Gravity = val
	end
end)

UserInputService.InputBegan:Connect(function(input, gpe)
	if gpe then return end
	if input.KeyCode == Enum.KeyCode.W then flyKeys.W = true end
	if input.KeyCode == Enum.KeyCode.S then flyKeys.S = true end
	if input.KeyCode == Enum.KeyCode.A then flyKeys.A = true end
	if input.KeyCode == Enum.KeyCode.D then flyKeys.D = true end
	if input.KeyCode == Enum.KeyCode.Space then flyKeys.Up = true end
	if input.KeyCode == Enum.KeyCode.LeftShift then flyKeys.Down = true end
end)

UserInputService.InputEnded:Connect(function(input)
	if input.KeyCode == Enum.KeyCode.W then flyKeys.W = false end
	if input.KeyCode == Enum.KeyCode.S then flyKeys.S = false end
	if input.KeyCode == Enum.KeyCode.A then flyKeys.A = false end
	if input.KeyCode == Enum.KeyCode.D then flyKeys.D = false end
	if input.KeyCode == Enum.KeyCode.Space then flyKeys.Up = false end
	if input.KeyCode == Enum.KeyCode.LeftShift then flyKeys.Down = false end
end)

-- =============================================================================
-- ABA 5: VISUAIS & ESP COMPLETO
-- =============================================================================
criarBotao(VisualPage, "👁️ ESP Highlight Chams: OFF", function(btn)
	ESP_ATIVO = not ESP_ATIVO
	btn.BackgroundColor3 = ESP_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "👁️ ESP Highlight: " .. (ESP_ATIVO and "ON" or "OFF")

	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= localPlayer and p.Character then
			local hl = p.Character:FindFirstChild("DesgracadaoESP")
			if ESP_ATIVO then
				if not hl then
					hl = Instance.new("Highlight")
					hl.Name = "DesgracadaoESP"
					hl.FillColor = Color3.fromRGB(255, 0, 80)
					hl.OutlineColor = Color3.fromRGB(255, 255, 255)
					hl.Parent = p.Character
				end
			else
				if hl then hl:Destroy() end
			end
		end
	end
end)

criarBotao(VisualPage, "📦 ESP 2D Box: OFF", function(btn)
	ESP_BOX_ATIVO = not ESP_BOX_ATIVO
	btn.BackgroundColor3 = ESP_BOX_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "📦 ESP 2D Box: " .. (ESP_BOX_ATIVO and "ON" or "OFF")

	if not ESP_BOX_ATIVO then
		for _, box in pairs(boxObjects) do
			box.Visible = false
			box:Remove()
		end
		boxObjects = {}
	end
end)

criarBotao(VisualPage, "📐 ESP Tracers (Linhas): OFF", function(btn)
	ESP_TRACERS_ATIVO = not ESP_TRACERS_ATIVO
	btn.BackgroundColor3 = ESP_TRACERS_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "📐 ESP Tracers: " .. (ESP_TRACERS_ATIVO and "ON" or "OFF")

	if not ESP_TRACERS_ATIVO then
		for _, line in pairs(tracerObjects) do
			line.Visible = false
			line:Remove()
		end
		tracerObjects = {}
	end
end)

criarBotao(VisualPage, "🏷️ ESP Names & Distância: OFF", function(btn)
	ESP_NAMES_ATIVO = not ESP_NAMES_ATIVO
	btn.BackgroundColor3 = ESP_NAMES_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "🏷️ ESP Names: " .. (ESP_NAMES_ATIVO and "ON" or "OFF")

	if not ESP_NAMES_ATIVO then
		for _, txt in pairs(textObjects) do
			txt.Visible = false
			txt:Remove()
		end
		textObjects = {}
	end
end)

criarBotao(VisualPage, "👻 Invisibilidade Local: OFF", function(btn)
	INVISIVEL_ATIVO = not INVISIVEL_ATIVO
	btn.BackgroundColor3 = INVISIVEL_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "👻 Invisibilidade Local: " .. (INVISIVEL_ATIVO and "ON" or "OFF")

	local char = localPlayer.Character
	if char then
		for _, part in ipairs(char:GetDescendants()) do
			if part:IsA("BasePart") or part:IsA("Decal") then
				part.Transparency = INVISIVEL_ATIVO and 1 or 0
			end
		end
	end
end)

-- Skin Changer
local SkinBox = Instance.new("TextBox")
SkinBox.Size = UDim2.new(1, 0, 0, 32)
SkinBox.BackgroundColor3 = Color3.fromRGB(28, 28, 35)
SkinBox.PlaceholderText = "Nick para roubar a Skin..."
SkinBox.Text = ""
SkinBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SkinBox.Font = Enum.Font.Gotham
SkinBox.TextSize = 11
SkinBox.Parent = VisualPage

local SkinCorner = Instance.new("UICorner")
SkinCorner.CornerRadius = UDim.new(0, 6)
SkinCorner.Parent = SkinBox

local function aplicarSkin(targetId)
	local char = localPlayer.Character
	if not char or not char:FindFirstChildOfClass("Humanoid") then return end

	for _, item in ipairs(char:GetChildren()) do
		if item:IsA("Accessory") or item:IsA("Shirt") or item:IsA("Pants") or item:IsA("BodyColors") then
			item:Destroy()
		end
	end

	task.spawn(function()
		local ok, appModel = pcall(function() return Players:GetCharacterAppearanceAsync(targetId) end)
		if ok and appModel then
			for _, child in ipairs(appModel:GetChildren()) do
				if child:IsA("Accessory") or child:IsA("Shirt") or child:IsA("Pants") or child:IsA("BodyColors") then
					child:Clone().Parent = char
				end
			end
			appModel:Destroy()
		end
	end)
end

criarBotao(VisualPage, "🎭 Copiar Skin do Nick", function(btn)
	if SkinBox.Text ~= "" then
		btn.Text = "Carregando..."
		local ok, id = pcall(function() return Players:GetUserIdFromNameAsync(SkinBox.Text) end)
		if ok and id then
			aplicarSkin(id)
			btn.Text = "Skin Aplicada!"
			task.wait(1.5)
			btn.Text = "🎭 Copiar Skin do Nick"
		else
			btn.Text = "Jogador não encontrado!"
			task.wait(1.5)
			btn.Text = "🎭 Copiar Skin do Nick"
		end
	end
end)

-- =============================================================================
-- ABA 6: DANÇAS E EMOTES
-- =============================================================================
local animTracksAtivas = {}

local function pararTodasAnimacoes()
	for _, track in ipairs(animTracksAtivas) do
		pcall(function() track:Stop() end)
	end
	animTracksAtivas = {}
end

local function tocarAnimacaoR6(animId)
	pararTodasAnimacoes()
	local char = localPlayer.Character
	if not char or not char:FindFirstChildOfClass("Humanoid") then return end

	local anim = Instance.new("Animation")
	anim.AnimationId = "rbxassetid://" .. tostring(animId)

	local ok, track = pcall(function() return char:FindFirstChildOfClass("Humanoid"):LoadAnimation(anim) end)
	if ok and track then
		track:Play()
		table.insert(animTracksAtivas, track)
	end
end

criarBotao(DancePage, "⛔ PARAR DANÇAS", function() pararTodasAnimacoes() end)
criarBotao(DancePage, "🕺 Dance 1", function() tocarAnimacaoR6(180435571) end)
criarBotao(DancePage, "💃 Dance 2", function() tocarAnimacaoR6(180436148) end)
criarBotao(DancePage, "👯 Dance 3", function() tocarAnimacaoR6(180436334) end)
criarBotao(DancePage, "💥 Dab", function() tocarAnimacaoR6(248263260) end)
criarBotao(DancePage, "🕺 Floss", function() tocarAnimacaoR6(107143405) end)
criarBotao(DancePage, "🇷🇺 Kazotsky Kick", function() tocarAnimacaoR6(330316200) end)

-- =============================================================================
-- ABA 7: UTILITÁRIOS
-- =============================================================================
criarBotao(UtilPage, "💡 Fullbright (Visão Noturna): OFF", function(btn)
	FULLBRIGHT_ATIVO = not FULLBRIGHT_ATIVO
	btn.BackgroundColor3 = FULLBRIGHT_ATIVO and Color3.fromRGB(40, 160, 40) or Color3.fromRGB(32, 32, 40)
	btn.Text = "💡 Fullbright: " .. (FULLBRIGHT_ATIVO and "ON" or "OFF")

	if FULLBRIGHT_ATIVO then
		Lighting.Ambient = Color3.fromRGB(255, 255, 255)
		Lighting.Brightness = 2
		Lighting.GlobalShadows = false
	else
		Lighting.Ambient = Color3.fromRGB(128, 128, 128)
		Lighting.Brightness = 1
		Lighting.GlobalShadows = true
	end
end)

criarBotao(UtilPage, "👁️ Spectate (Espiar Jogador Alvo)", function(btn)
	SPECTATE_ATIVO = not SPECTATE_ATIVO
	btn.BackgroundColor3 = SPECTATE_ATIVO and Color3.fromRGB(0, 140, 255) or Color3.fromRGB(32, 32, 40)
	btn.Text = "👁️ Spectate: " .. (SPECTATE_ATIVO and "ON" or "OFF")
	if not SPECTATE_ATIVO then
		camera.CameraSubject = localPlayer.Character and localPlayer.Character:FindFirstChildOfClass("Humanoid")
	end
end)

criarBotao(UtilPage, "🚀 Anti-Lag / FPS Booster", function(btn)
	pcall(function()
		settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
		for _, v in ipairs(workspace:GetDescendants()) do
			if v:IsA("BasePart") then v.Material = Enum.Material.SmoothPlastic end
			if v:IsA("Decal") or v:IsA("Texture") then v:Destroy() end
		end
	end)
	btn.Text = "🚀 FPS Otimizado!"
	task.wait(1.5)
	btn.Text = "🚀 Anti-Lag / FPS Booster"
end)

criarBotao(UtilPage, "🔄 Rejoin Servidor", function()
	TeleportService:Teleport(game.PlaceId, localPlayer)
end)

-- =============================================================================
-- SISTEMAS EM TEMPO REAL & LOOPS
-- =============================================================================
function obterAlvoValido()
	local meuChar = localPlayer.Character
	if not meuChar or not meuChar:FindFirstChild("HumanoidRootPart") then return nil end

	if ALVO_SELECIONADO ~= "Mais Próximo" then
		local pAlvo = Players:FindFirstChild(ALVO_SELECIONADO)
		if pAlvo and pAlvo.Character and pAlvo.Character:FindFirstChild("HumanoidRootPart") then
			return pAlvo.Character
		end
		return nil
	end

	local alvoMaisProximo = nil
	local menorDistancia = DISTANCIA_MAXIMA

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
			local root = player.Character.HumanoidRootPart
			local dist = (meuChar.HumanoidRootPart.Position - root.Position).Magnitude
			if dist < menorDistancia then
				menorDistancia = dist
				alvoMaisProximo = player.Character
			end
		end
	end
	return alvoMaisProximo
end

-- RenderStepped Main Loop
RunService.RenderStepped:Connect(function()
	local meuChar = localPlayer.Character
	local meuRoot = meuChar and meuChar:FindFirstChild("HumanoidRootPart")
	local alvo = obterAlvoValido()

	-- Atualizar Círculo FOV
	if SHOW_FOV then
		FOVCircle.Position = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
		FOVCircle.Radius = FOV_RADIUS
	end

	-- Aimbot por Câmera
	if AIMBOT_ATIVO and alvo and alvo:FindFirstChild("Head") then
		local screenPos, onScreen = camera:WorldToViewportPoint(alvo.Head.Position)
		if onScreen then
			camera.CFrame = CFrame.new(camera.CFrame.Position, alvo.Head.Position)
		end
	end

	-- TP Subterrâneo
	if TELEPORT_ATIVO and meuRoot and alvo then
		local alvoRoot = alvo:FindFirstChild("HumanoidRootPart")
		if alvoRoot then
			meuRoot.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
			meuRoot.CFrame = alvoRoot.CFrame * CFrame.new(0, -PROFUNDIDADE_TERRA, 0) * CFrame.Angles(math.rad(90), 0, 0)
		end
	end

	-- Spinbot Rápido
	if SPINBOT_ATIVO and meuRoot then
		meuRoot.CFrame = meuRoot.CFrame * CFrame.Angles(0, math.rad(60), 0)
	end

	-- Spectate
	if SPECTATE_ATIVO and alvo and alvo:FindFirstChildOfClass("Humanoid") then
		camera.CameraSubject = alvo:FindFirstChildOfClass("Humanoid")
	end

	-- Single Hitbox Extender
	if HITBOX_ATIVO and alvo and alvo:FindFirstChild("HumanoidRootPart") then
		local alvoRoot = alvo.HumanoidRootPart
		alvoRoot.Size = Vector3.new(HITBOX_SIZE, HITBOX_SIZE, HITBOX_SIZE)
		alvoRoot.Transparency = 0.7
		alvoRoot.Color = Color3.fromRGB(0, 170, 255)
		alvoRoot.CanCollide = false
	end

	-- Global Hitbox Extender
	if GLOBAL_HITBOX_ATIVO then
		for _, p in ipairs(Players:GetPlayers()) do
			if p ~= localPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
				local hrp = p.Character.HumanoidRootPart
				hrp.Size = Vector3.new(HITBOX_SIZE, HITBOX_SIZE, HITBOX_SIZE)
				hrp.Transparency = 0.8
				hrp.Color = Color3.fromRGB(255, 0, 0)
				hrp.CanCollide = false
			end
		end
	end

	-- Fly Movement
	if FLY_ATIVO and meuRoot and flyBodyVel and flyBodyGyro then
		local moveDir = Vector3.new(0, 0, 0)
		local camCF = camera.CFrame

		if flyKeys.W then moveDir = moveDir + camCF.LookVector end
		if flyKeys.S then moveDir = moveDir - camCF.LookVector end
		if flyKeys.A then moveDir = moveDir - camCF.RightVector end
		if flyKeys.D then moveDir = moveDir + camCF.RightVector end
		if flyKeys.Up then moveDir = moveDir + Vector3.new(0, 1, 0) end
		if flyKeys.Down then moveDir = moveDir - Vector3.new(0, 1, 0) end

		flyBodyVel.Velocity = moveDir * FLY_SPEED
		flyBodyGyro.CFrame = camCF
	end

	-- Visual ESP Systems (Tracers, Names, Boxes)
	if ESP_TRACERS_ATIVO or ESP_NAMES_ATIVO or ESP_BOX_ATIVO then
		for _, p in ipairs(Players:GetPlayers()) do
			if p ~= localPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
				local pRoot = p.Character.HumanoidRootPart
				local pos, onScreen = camera:WorldToViewportPoint(pRoot.Position)

				if ESP_TRACERS_ATIVO then
					local tracer = tracerObjects[p.Name] or Drawing.new("Line")
					tracerObjects[p.Name] = tracer
					if onScreen then
						tracer.From = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y)
						tracer.To = Vector2.new(pos.X, pos.Y)
						tracer.Color = Color3.fromRGB(0, 220, 255)
						tracer.Thickness = 1.5
						tracer.Visible = true
					else
						tracer.Visible = false
					end
				end

				if ESP_NAMES_ATIVO then
					local txt = textObjects[p.Name] or Drawing.new("Text")
					textObjects[p.Name] = txt
					if onScreen and meuRoot then
						local dist = math.floor((pRoot.Position - meuRoot.Position).Magnitude)
						txt.Text = p.Name .. " [" .. tostring(dist) .. "m]"
						txt.Position = Vector2.new(pos.X, pos.Y - 25)
						txt.Color = Color3.fromRGB(255, 255, 255)
						txt.Size = 13
						txt.Center = true
						txt.Outline = true
						txt.Visible = true
					else
						txt.Visible = false
					end
				end

				if ESP_BOX_ATIVO then
					local box = boxObjects[p.Name] or Drawing.new("Square")
					boxObjects[p.Name] = box
					if onScreen then
						box.Size = Vector2.new(2000 / pos.Z, 3000 / pos.Z)
						box.Position = Vector2.new(pos.X - box.Size.X / 2, pos.Y - box.Size.Y / 2)
						box.Color = Color3.fromRGB(255, 0, 80)
						box.Thickness = 1.5
						box.Filled = false
						box.Visible = true
					else
						box.Visible = false
					end
				end
			end
		end
	end
end)

-- Stepped Physics Loop
RunService.Stepped:Connect(function()
	local meuChar = localPlayer.Character
	if not meuChar then return end

	if NOCLIP_ATIVO or TELEPORT_ATIVO or ANTI_HIT_ATIVO or FLY_ATIVO then
		for _, part in ipairs(meuChar:GetChildren()) do
			if part:IsA("BasePart") then part.CanCollide = false end
		end
	end

	if ANTI_HIT_ATIVO and meuChar:FindFirstChild("HumanoidRootPart") then
		meuChar.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(99999, 99999, 99999)
	end

	-- Fling Exploit System
	if FLING_ATIVO and meuChar:FindFirstChild("HumanoidRootPart") then
		meuChar.HumanoidRootPart.RotVelocity = Vector3.new(0, 999999, 0)
	end
end)

-- Pulo Infinito
UserInputService.JumpRequest:Connect(function()
	if INF_JUMP_ATIVO and localPlayer.Character then
		local hum = localPlayer.Character:FindFirstChildOfClass("Humanoid")
		if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
	end
end)

-- Loop Kill Aura Extrema (Com Auto Equip Tool)
task.spawn(function()
	while true do
		if KILL_AURA_ATIVO then
			local alvo = obterAlvoValido()
			local meuChar = localPlayer.Character
			local meuRoot = meuChar and meuChar:FindFirstChild("HumanoidRootPart")

			if alvo and alvo:FindFirstChild("HumanoidRootPart") and meuRoot then
				local dist = (alvo.HumanoidRootPart.Position - meuRoot.Position).Magnitude
				if dist <= AURA_RANGE then
					-- Equipa a primeira ferramenta do inventário se nenhuma estiver nas mãos
					if not meuChar:FindFirstChildOfClass("Tool") then
						local tool = localPlayer.Backpack:FindFirstChildOfClass("Tool")
						if tool then tool.Parent = meuChar end
					end

					-- Executa o clique de ataque
					local vp = camera.ViewportSize
					VirtualInputManager:SendMouseButtonEvent(vp.X / 2, vp.Y / 2, 0, true, game, 1)
					task.wait(0.01)
					VirtualInputManager:SendMouseButtonEvent(vp.X / 2, vp.Y / 2, 0, false, game, 1)
				end
			end
		end
		task.wait(0.05)
	end
end)

-- A Coisa Mais Roubada do Mundo: Paralisia Absoluta Global (Network/Physics Flood)
task.spawn(function()
	while true do
		if SERVER_CRASHER_ATIVO then
			pcall(function()
				-- Envia pacotes massivos de alteração de física global para sobrecarregar a simulação
				for _, p in ipairs(Players:GetPlayers()) do
					if p ~= localPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
						local hrp = p.Character.HumanoidRootPart
						hrp.AssemblyLinearVelocity = Vector3.new(math.random(-999999, 999999), math.random(999999, 999999), math.random(-999999, 999999))
						hrp.AssemblyAngularVelocity = Vector3.new(999999, 999999, 999999)
					end
				end
			end)
		end
		task.wait(0.01)
	end
end)

-- Anti-AFK Desativador de Desconexão
for _, v in pairs(getconnections(localPlayer.Idled)) do
	if v.Disable then v:Disable() elseif v.Disconnect then v:Disconnect() end
end
