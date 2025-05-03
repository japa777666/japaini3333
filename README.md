local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

local OrionLib = loadstring(game:HttpGet('https://raw.githubusercontent.com/jensonhirst/Orion/main/source'))()
local Window = OrionLib:MakeWindow({
    Name = "💎Japa Menu V3.2",
    HidePremium = true,
    SaveConfig = false
})

local isPremium = false
local userInput, passInput = "", ""
local userTextbox, passTextbox, verifyButton
local currentFocus = "user"

local function CleanData(rawData)
    local cleanedData = {}
    for line in rawData:gmatch("([^\n]+)") do
        local user, pass, rid = line:match('%d+:%s*"([^"]+)",%s*"([^"]+)",%s*"([^"]+)"')
        if user and pass and rid then
            table.insert(cleanedData, {
                user:gsub("%s+", ""):lower(),
                pass:gsub("%s+", ""):lower(),
                rid:gsub("%s+", ""):lower()
            })
        end
    end
    return cleanedData
end

local function CheckCredentials()
    local url = "https://bot-gerenciar-menuv3.up.railway.app/keys.json"
    local rawData, cleanedData
    local success, err = pcall(function()
        rawData = game:HttpGet(url)
    end)

    if not success then
        warn("Falha ao obter dados:", err)
        return false
    end

    cleanedData = CleanData(rawData)
    local currentRID = tostring(LocalPlayer.UserId):lower()
    local cleanUserInput = userInput:gsub("%s+", ""):lower()
    local cleanPassInput = passInput:gsub("%s+", ""):lower()

    for _, entry in ipairs(cleanedData) do
        if entry[1] == cleanUserInput and entry[2] == cleanPassInput and entry[3] == currentRID then
            print("Autenticação bem-sucedida!")
            return true
        end
    end

    warn("Nenhuma correspondência encontrada")
    return false
end

-- 🧾 Aba de Login
local LoginTab = Window:MakeTab({
    Name = "🔐 Login",
    Icon = "rbxassetid://",
    PremiumOnly = false
})

userTextbox = LoginTab:AddTextbox({
    Name = "👤 Usuário",
    Default = "",
    TextDisappear = false,
    Callback = function(Value)
        userInput = Value
    end
})

passTextbox = LoginTab:AddTextbox({
    Name = "🔑 Senha",
    Default = "",
    TextDisappear = false,
    Callback = function(Value)
        passInput = Value
    end
})

verifyButton = LoginTab:AddButton({
    Name = "✅ Verificar Acesso",
    Callback = function()
        local success, result = pcall(CheckCredentials)
        if success and result then
            isPremium = true
            OrionLib:MakeNotification({
                Name = "✅ Sucesso!",
                Content = "Acesso Premium liberado para " .. userInput .. "!",
                Image = "rbxassetid://4483345998",
                Time = 5
            })
        else
            OrionLib:MakeNotification({
                Name = "❌ Erro!",
                Content = "Usuário, senha ou conta Roblox inválidos!",
                Image = "rbxassetid://4483345998",
                Time = 5
            })
        end
    end
})

-- 🎯 Foco automático + teclas de atalho
spawn(function()
    wait(1)

    UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then return end
        if input.KeyCode == Enum.KeyCode.Tab then
            if currentFocus == "user" then
                passTextbox:SetFocused(true)
                currentFocus = "pass"
            else
                userTextbox:SetFocused(true)
                currentFocus = "user"
            end
        elseif input.KeyCode == Enum.KeyCode.Return then
            verifyButton.Callback()
        end
    end)
end)

-- ⭐ Aba Privada
local function CreatePremiumTab()
    if isPremium then
        local PrivateTab = Window:MakeTab({
            Name = "🌟 Privada",
            Icon = "rbxassetid://4483345998",
            PremiumOnly = false
        })

        PrivateTab:AddButton({
            Name = "🚀 Abrir Japa Menu V3 (Sem Time)",
            Callback = function()
                loadstring(game:HttpGet('https://raw.githubusercontent.com/japa777666/japa31/refs/heads/main/README.md'))()
            end
        })

        PrivateTab:AddButton({
            Name = "🧠 Abrir Japa Menu V2",
            Callback = function()
                loadstring(game:HttpGet('https://raw.githubusercontent.com/japa777666/JapaHubIni/refs/heads/main/README.md'))()
            end
        })
    end
end

-- 🔓 Mouse começa destravado e tecla V trava/destrava
local mouseLocked = false
local UIS = game:GetService("UserInputService")
local lockKey = Enum.KeyCode.V

UIS.MouseBehavior = Enum.MouseBehavior.Default
UIS.MouseIconEnabled = true

local function resetCamera()
    local camera = workspace.CurrentCamera
    local player = game.Players.LocalPlayer
    if player and player.Character and player.Character:FindFirstChild("Humanoid") then
        camera.CameraSubject = player.Character:FindFirstChild("Humanoid")
        camera.CameraType = Enum.CameraType.Custom
        camera.CFrame = camera.CFrame * CFrame.new(0, 0, 3)
    end
end

local function toggleMouseLock()
    mouseLocked = not mouseLocked
    if mouseLocked then
        UIS.MouseBehavior = Enum.MouseBehavior.LockCenter
        UIS.MouseIconEnabled = false
    else
        UIS.MouseBehavior = Enum.MouseBehavior.Default
        UIS.MouseIconEnabled = true
        resetCamera()
    end
end

UIS.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == lockKey then
        toggleMouseLock()
    end
end)

-- 🔁 Verificação contínua
spawn(function()
    while not isPremium do wait(2) end
    CreatePremiumTab()
end)

OrionLib:Init()
