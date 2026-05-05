-- Script de Movimento com Noclip e Flutuação
-- Coordenadas: (313,71,22) -> (396,-120,31) -> após 4s -> (421,-124,31)

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")

-- Configurações
local velocidade = 60
local coordenadas = {
    {X = 313, Y = 71, Z = 22},
    {X = 396, Y = -120, Z = 31},
    {X = 421, Y = -124, Z = 31}
}

-- Ativar Noclip (atravessa blocos)
local noclipConnection
local function ativarNoclip()
    noclipConnection = game:GetService("RunService").Stepped:Connect(function()
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") and part.CanCollide == true then
                part.CanCollide = false
            end
        end
    end)
end

-- Desativar Noclip
local function desativarNoclip()
    if noclipConnection then
        noclipConnection:Disconnect()
        noclipConnection = nil
    end
    for _, part in pairs(character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = true
        end
    end
end

-- Flutuação no ar (anti-gravidade)
local bodyVelocity
local function ativarFlutuacao()
    bodyVelocity = Instance.new("BodyVelocity")
    bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
    bodyVelocity.Velocity = Vector3.new(0, 0, 0)
    bodyVelocity.Parent = rootPart
end

local function desativarFlutuacao()
    if bodyVelocity then
        bodyVelocity:Destroy()
        bodyVelocity = nil
    end
end

-- Função de movimento suave até coordenada
local function moverAteCoordenada(destino)
    local alvo = Vector3.new(destino.X, destino.Y, destino.Z)
    
    while true do
        local posicaoAtual = rootPart.Position
        local direcao = (alvo - posicaoAtual).Unit
        local distancia = (alvo - posicaoAtual).Magnitude
        
        if distancia < 2 then
            rootPart.CFrame = CFrame.new(alvo)
            break
        end
        
        local deslocamento = direcao * velocidade * 0.03
        rootPart.CFrame = CFrame.new(posicaoAtual + deslocamento)
        rootPart.Velocity = Vector3.new(0, 0, 0)
        
        task.wait(0.03)
    end
end

-- Script Principal
local function iniciarMovimento()
    -- Ativar noclip e flutuação
    ativarNoclip()
    ativarFlutuacao()
    
    -- Mover para primeira coordenada (313, 71, 22)
    print("Indo para: 313, 71, 22")
    moverAteCoordenada(coordenadas[1])
    task.wait(0.5)
    
    -- Mover para segunda coordenada (396, -120, 31)
    print("Indo para: 396, -120, 31")
    moverAteCoordenada(coordenadas[2])
    
    -- Esperar 4 segundos
    print("Esperando 4 segundos...")
    task.wait(4)
    
    -- Mover para terceira coordenada (421, -124, 31)
    print("Indo para: 421, -124, 31")
    moverAteCoordenada(coordenadas[3])
    
    -- Finalizar
    print("Movimento completo!")
    desativarFlutuacao()
    -- Noclip permanece ativo (se quiser desativar, use: desativarNoclip())
end

-- Iniciar automaticamente
iniciarMovimento()

