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

local function CheckCredentials()
    local rawData = game:HttpGet("https://bot-gerenciar-menuv3.up.railway.app/keys.json")
    -- Tenta decodificar o arquivo JSON (em string)
    local success, data = pcall(function() return HttpService:JSONDecode(rawData) end)
    if not success then
        OrionLib:MakeNotification({
            Name = "❌ Erro!",
            Content = "Erro ao tentar carregar dados de acesso!",
            Image = "rbxassetid://4483345998",
            Time = 5
        })
        return false
    end

    -- Se o arquivo JSON foi lido corretamente, percorre as chaves
    for _, entry in ipairs(data) do
        local user, pass, rid = entry[1], entry[2], entry[3]
        if user and pass and rid then
            if userInput:lower() == user:lower() and passInput == pass and tostring(LocalPlayer.UserId) == rid then
                return true
            end
        end
    end
    return false
end

-- 🧾 Aba de Login
local LoginTab = Window:MakeTab({
    Name = "🔐 Login",
    Icon = "rbxassetid://4483345998",
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
    userTextbox:SetFocused(true)

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

-- 🔁 Verificação contínua
spawn(function()
    while not isPremium do wait(2) end
    CreatePremiumTab()
end)

OrionLib:Init()
