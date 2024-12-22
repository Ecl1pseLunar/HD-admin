# HD-admin
OrionLib: comando de admin

 -- Adiciona Biblioteca
local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/shlexware/Orion/main/source')))()

 -- Criando uma janela
local Window = OrionLib:MakeWindow({Name = "Admin Painel", HidePremium = false, SaveConfig = true, ConfigFolder = "OrionTest"})

 -- Criando uma aba
local Tab = Window:MakeTab({
	Name = "Painel de Admin",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = false
})

Tab:AddLabel("Comando de Admin")

-- Criando a aba e os elementos
Tab:AddTextbox({
    Name = "Digite o seu comando",
    Default = "None",
    TextDisappear = true,
    Callback = function(Value)
        -- Salva o valor digitado para uso no botão
        _G.ComandoDigitado = Value 
        print("Comando digitado: " .. Value)
    end
})

Tab:AddButton({
    Name = "Executar Comando!",
    Callback = function()
        -- Verifica se existe algum comando digitado
        if _G.ComandoDigitado and _G.ComandoDigitado ~= "" then
            -- Adiciona a barra "/" automaticamente
            local comandoExecutar = "/" .. _G.ComandoDigitado

            -- Envia o comando para o HD Admin
            local args = {
                [1] = comandoExecutar
            }
            game:GetService("ReplicatedStorage").HDAdminClient.Signals.RequestCommand:InvokeServer(unpack(args))
            
            -- Mensagem de feedback no console
            print("Comando enviado: " .. comandoExecutar)
        else
            print("Nenhum comando foi digitado!")
        end
    end    
}) 

Tab:AddLabel("Player Painel")

-- Variáveis Globais
local players = game:GetService("Players") -- Serviço para obter jogadores
local localPlayer = players.LocalPlayer -- Jogador que está executando o script
local commandToExecute = "" -- Armazena o comando digitado
local targetPlayer = "" -- Armazena o nome do jogador alvo
local running = false -- Controle do loop para alternar jogadores

-- Textbox: Digite o Comando
Tab:AddTextbox({
    Name = "Digite o Seu Comando",
    Default = "None",
    TextDisappear = true,
    Callback = function(Value)
        commandToExecute = "/" .. Value -- Adiciona automaticamente a barra '/' ao comando
        print("Comando recebido:", commandToExecute)
    end
})

-- Textbox: Digite o Nome do Jogador
Tab:AddTextbox({
    Name = "Digite o Nome do Jogador",
    Default = "Player",
    TextDisappear = true,
    Callback = function(Value)
        if Value:lower() == "all" then
            targetPlayer = "All" -- Ativa o sistema de alternância
        else
            targetPlayer = Value -- Armazena o nome do jogador específico
        end
        print("Alvo selecionado:", targetPlayer)
    end
})

-- Função para alternar jogadores
local function alternatePlayers()
    if targetPlayer:lower() == "all" then
        running = true
        while running do
            for _, player in pairs(players:GetPlayers()) do
                if player ~= localPlayer then -- Ignora o jogador que está executando
                    local args = {
                        [1] = commandToExecute .. " " .. player.Name
                    }
                    game:GetService("ReplicatedStorage").HDAdminClient.Signals.RequestCommand:InvokeServer(unpack(args))
                    wait(0.3) -- Espera 0.3 segundos antes de mudar o jogador
                end
            end
        end
    else
        -- Executa o comando em um jogador específico
        local args = {
            [1] = commandToExecute .. " " .. targetPlayer
        }
        game:GetService("ReplicatedStorage").HDAdminClient.Signals.RequestCommand:InvokeServer(unpack(args))
    end
end

-- Botão: Executar Comando
Tab:AddButton({
    Name = "Executar Comando!",
    Callback = function()
        print("Botão pressionado, executando comando...")
        if targetPlayer ~= "" and commandToExecute ~= "" then
            if targetPlayer:lower() == "all" then
                -- Inicia alternância entre jogadores
                alternatePlayers()
            else
                -- Executa o comando diretamente
                local args = {
                    [1] = commandToExecute .. " " .. targetPlayer
                }
                game:GetService("ReplicatedStorage").HDAdminClient.Signals.RequestCommand:InvokeServer(unpack(args))
                print("Comando executado para:", targetPlayer)
            end
        else
            warn("Por favor, digite um comando e selecione um alvo!")
        end
    end
})

OrionLib:Init()
