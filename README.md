local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/jensonhirst/Orion/main/source')))()
local Window = OrionLib:MakeWindow({Name = "Japa Menu", HidePremium = true, SaveConfig = false})

local isPremium = false

-- Aba Pública
local PublicTab = Window:MakeTab({Name = "Pública", Icon = "rbxassetid://4483345998", PremiumOnly = false})

PublicTab:AddTextbox({
    Name = "Digite a chave:",
    Default = "",
    TextDisappear = true,
    Callback = function(Value)
        if Value == "Byjapamenu" then
            isPremium = true
            OrionLib:MakeNotification({
                Name = "Sucesso!",
                Content = "Você desbloqueou o acesso Premium!",
                Image = "rbxassetid://4483345998",
                Time = 5
            })
        else
            OrionLib:MakeNotification({
                Name = "Erro!",
                Content = "Chave incorreta!",
                Image = "rbxassetid://4483345998",
                Time = 5
            })
        end
    end
})

-- Aba Privada (Aparece apenas se for Premium)
local function CreatePremiumTab()
    if isPremium then
        local PrivateTab = Window:MakeTab({Name = "Privada", Icon = "rbxassetid://4483345998", PremiumOnly = false})

        PrivateTab:AddButton({
            Name = "Abrir Japa Menu V3     (Sem Time)",
            Callback = function()
                loadstring(game:HttpGet('https://raw.githubusercontent.com/japa777666/japa31/refs/heads/main/README.md'))()
            end
        })

        PrivateTab:AddButton({
            Name = "Abrir Japa Menu V2",
            Callback = function()
                loadstring(game:HttpGet(('https://raw.githubusercontent.com/japa777666/JapaHubIni/refs/heads/main/README.md')))()
            end
        })
    end
end

-- Verifica a cada 2 segundos se o usuário virou premium e cria a aba privada
spawn(function()
    while true do
        if isPremium then
            CreatePremiumTab()
            break
        end
        wait(2)
    end
end)

OrionLib:Init()
