-- Criando a UI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

local ToggleButton = Instance.new("TextButton")
ToggleButton.Parent = ScreenGui
ToggleButton.Size = UDim2.new(0, 200, 0, 50)
ToggleButton.Position = UDim2.new(0.5, -100, 0.1, 0)
ToggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 20
ToggleButton.Text = "Ativar Farm"

local farming = false  -- Variável para controlar o farm

-- Função para teletransportar para um "Stem" aleatório
local function teleportarJogadorAleatoriamente()
    local stems = {}

    for _, plantaIlegal in pairs(game.Workspace:GetDescendants()) do
        if plantaIlegal.Name == "PlantaIlegal" then
            local stem = plantaIlegal:FindFirstChild("Stem")
            if stem then
                table.insert(stems, stem)
            end
        end
    end

    if #stems > 0 then
        local stemAleatorio = stems[math.random(1, #stems)]
        if stemAleatorio and game.Players.LocalPlayer.Character then
            local humanoide = game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if humanoide then
                humanoide.CFrame = stemAleatorio.CFrame
            end
        end
    else
        warn("Nenhum 'Stem' encontrado.")
    end
end

-- Simula a tecla "E" para interagir
local function segurarTeclaE()
    local virtualInputManager = game:GetService("VirtualInputManager")
    local interactionKey = "E"
    
    -- Mantém pressionado por 17 segundos (ou o tempo que preferir)
    virtualInputManager:SendKeyEvent(true, interactionKey, false, game)
    wait(17)  -- Tempo que a tecla ficará pressionada
    virtualInputManager:SendKeyEvent(false, interactionKey, false, game)
end

-- Loop do farm
local function farmLoop()
    while farming do
        teleportarJogadorAleatoriamente()
        segurarTeclaE()
        wait(1)  -- Espera 1 segundo antes de repetir
    end
end

-- Alternar o farm ao clicar no botão
ToggleButton.MouseButton1Click:Connect(function()
    farming = not farming  -- Inverte o estado do farm

    if farming then
        ToggleButton.Text = "Desativar Farm"
        -- Inicia o loop de farm em uma thread separada
        spawn(farmLoop)
    else
        ToggleButton.Text = "Ativar Farm"
    end
end)
