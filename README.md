# Clocal player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hrp = char:WaitForChild("HumanoidRootPart")

local enemiesFolder = workspace:WaitForChild("MugenCityEnemies")
local chestsFolder = workspace:WaitForChild("Chests")

local button = script.Parent
local autoFarm = false

button.Text = "Auto Farm [OFF]"

function startAutoFarm()
    spawn(function()
        while autoFarm do
            -- FARMEIA NPCs
            for _, enemy in pairs(enemiesFolder:GetChildren()) do
                if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                    -- Teleporta até o inimigo
                    hrp.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0, 0, -4)

                    -- Ataca até morrer
                    while enemy.Humanoid.Health > 0 and autoFarm do
                        -- 🔥 Troque essa linha pelo seu RemoteEvent de ataque, se tiver
                        enemy.Humanoid.Health -= 10 -- Dano manual
                        wait(0.15) -- Delay entre ataques
                    end

                    wait(0.5)
                end
            end

            -- FARMEIA BAÚS
            for _, chest in pairs(chestsFolder:GetChildren()) do
                if chest:IsA("Model") or chest:IsA("Part") then
                    local prompt = chest:FindFirstChildWhichIsA("ProximityPrompt")
                    if prompt then
                        -- Teleporta até o baú
                        if chest:IsA("Model") and chest:FindFirstChild("PrimaryPart") then
                            hrp.CFrame = chest.PrimaryPart.CFrame * CFrame.new(0, 0, -3)
                        elseif chest:IsA("Part") then
                            hrp.CFrame = chest.CFrame * CFrame.new(0, 0, -3)
                        end

                        wait(0.5)

                        -- Ativa o prompt
                        fireproximityprompt(prompt)

                        wait(1) -- Espera o baú abrir
                    end
                end
            end

            wait(1) -- Pequeno delay e repete
        end
    end)
end

-- Botão liga/desliga
button.MouseButton1Click:Connect(function()
    autoFarm = not autoFarm
    button.Text = autoFarm and "Auto Farm [ON]" or "Auto Farm [OFF]"

    if autoFarm then
        startAutoFarm()
    end
end)
