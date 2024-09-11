-- Configurações
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local Camera = Workspace.CurrentCamera
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local espEnabled = true  -- Controle de ativação/desativação do ESP

-- Função para criar a caixa ESP
local function createESP(targetPlayer)
    local esp = Instance.new("BillboardGui")
    esp.Name = "ESP"
    esp.Adornee = targetPlayer.Character:WaitForChild("Head")
    esp.Size = UDim2.new(0, 200, 0, 200)
    esp.StudsOffset = Vector3.new(0, 5, 0)
    esp.Parent = targetPlayer.Character:WaitForChild("Head")

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.BackgroundTransparency = 0.5
    frame.BackgroundColor3 = Color3.fromRGB(255, 255, 0)  -- Cor amarela
    frame.Parent = esp

    return esp
end

-- Função para atualizar o FOV
local function updateFOV()
    if espEnabled then
        for _, player in pairs(Players:GetPlayers()) do
            if player ~= Players.LocalPlayer and player.Team ~= Players.LocalPlayer.Team and player.Character and player.Character:FindFirstChild("Head") then
                local esp = player.Character.Head:FindFirstChild("ESP")
                if not esp then
                    createESP(player)
                end
            end
        end
    else
        -- Remove todas as caixas ESP
        for _, player in pairs(Players:GetPlayers()) do
            if player.Character and player.Character.Head then
                local esp = player.Character.Head:FindFirstChild("ESP")
                if esp then
                    esp:Destroy()
                end
            end
        end
    end
end

-- Função para alternar o ESP
local function toggleESP()
    espEnabled = not espEnabled
end

-- Captura a entrada do teclado para alternar o ESP
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.F then
        toggleESP()
    end
end)

-- Conectar o evento de atualizar o FOV ao RenderStepped
RunService.RenderStepped:Connect(function()
    updateFOV()
end)
