--[[ 
    ULTIMATE LOADER v6.1 - RIO RP EDITION (AUTO-ROUTE UPDATE)
    Proteções: Anti-Hook, Anti-Debug, Local Constant Cache
    Dono: qsw7 | Key: qsw7
]]

local _loadstring = loadstring
local _kick = game:GetService("Players").LocalPlayer.Kick

local SETTINGS = {
    CORRECT_KEY = "qsw7",
    DISCORD_LINK = "https://discord.gg/WJEGGeXF"
}

-- // FUNÇÃO DO PAINEL PRINCIPAL (SÓ ABRE APÓS A KEY)
local function AbrirPainelPrincipal()
    local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
    local Window = Rayfield:CreateWindow({
       Name = "Rio RP Premium | QSW7",
       LoadingTitle = "Carregando Rotas e Scripts...",
       LoadingSubtitle = "Bem-vindo, qsw7",
       ConfigurationSaving = {Enabled = true, FolderName = "RioRP_QSW7"},
       KeySystem = false
    })

    -- [ ABA COMBATE ]
    local CombatTab = Window:CreateTab("Combate", 4483345998)
    CombatTab:CreateToggle({
       Name = "Aimbot (Mobile/Head)",
       CurrentValue = false,
       Callback = function(Value)
          _G.Aimbot = Value
          spawn(function()
             while _G.Aimbot do
                local target = nil
                local dist = 2000
                for _, v in pairs(game.Players:GetPlayers()) do
                   if v ~= game.Players.LocalPlayer and v.Character and v.Character:FindFirstChild("Head") then
                      if v.Team ~= game.Players.LocalPlayer.Team and v.Character.Humanoid.Health > 0 then
                         local pos, vis = workspace.CurrentCamera:WorldToViewportPoint(v.Character.Head.Position)
                         if vis then
                            local m = (Vector2.new(pos.X, pos.Y) - Vector2.new(game.Players.LocalPlayer:GetMouse().X, game.Players.LocalPlayer:GetMouse().Y)).Magnitude
                            if m < dist then target = v; dist = m end
                         end
                      end
                   end
                end
                if target then
                   workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, target.Character.Head.Position)
                end
                task.wait()
             end
          end)
       end,
    })

    -- [ ABA AUTO FARM PRO - ROTAS ]
    local FarmTab = Window:CreateTab("Auto Farm Pro", 4483345998)
    
    _G.AutoRoute = false
    FarmTab:CreateToggle({
       Name = "Rota Automática (Caminhão/Entregas)",
       CurrentValue = false,
       Callback = function(Value)
          _G.AutoRoute = Value
          task.spawn(function()
             while _G.AutoRoute do
                -- Procura pelo Checkpoint da Rota (Geralmente CylinderMesh ou Beam no Rio RP)
                for _, v in pairs(workspace:GetDescendants()) do
                    if v.Name == "Checkpoint" or v.Name == "Entrega" or v.Name == "Objective" then
                        if v:IsA("BasePart") or v:IsA("MeshPart") then
                            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = v.CFrame
                            task.wait(0.5)
                            -- Se houver ProximityPrompt na entrega, ele ativa sozinho
                            local prompt = v:FindFirstChildOfClass("ProximityPrompt") or v.Parent:FindFirstChildOfClass("ProximityPrompt")
                            if prompt then fireproximityprompt(prompt) end
                        end
                    end
                end
                task.wait(1)
             end
          end)
       end,
    })

    FarmTab:CreateToggle({
        Name = "Auto Colheita (Fazenda)",
        CurrentValue = false,
        Callback = function(Value)
            _G.Colheita = Value
            task.spawn(function()
                while _G.Colheita do
                    for _, v in pairs(workspace:GetDescendants()) do
                        if v.Name == "Planta" or v.Name == "Colher" then
                            game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = v.CFrame
                            task.wait(0.3)
                            fireproximityprompt(v:FindFirstChildOfClass("ProximityPrompt"))
                        end
                    end
                    task.wait(1)
                end
            end)
        end
    })

    -- [ ABA MOVIMENTAÇÃO ]
    local MoveTab = Window:CreateTab("Movimentação", 4483345998)
    MoveTab:CreateSlider({
       Name = "Velocidade (Speed)",
       Range = {16, 250},
       Increment = 1,
       CurrentValue = 16,
       Callback = function(v) game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = v end,
    })

    MoveTab:CreateToggle({
       Name = "Noclip",
       CurrentValue = false,
       Callback = function(v)
          _G.Noclip = v
          game:GetService("RunService").Stepped:Connect(function()
             if _G.Noclip and game.Players.LocalPlayer.Character then
                for _, part in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
                   if part:IsA("BasePart") then part.CanCollide = false end
                end
             end
          end)
       end,
    })

    -- [ ABA UTILIDADES ]
    local UtilsTab = Window:CreateTab("Utilidades", 4483345998)
    
    UtilsTab:CreateToggle({
        Name = "Instant Prompt (Trabalho)",
        CurrentValue = true,
        Callback = function(v)
            _G.Instant = v
            while _G.Instant do
                for _, p in pairs(workspace:GetDescendants()) do
                    if p:IsA("ProximityPrompt") then p.HoldDuration = 0 end
                end
                task.wait(5)
            end
        end
    })

    UtilsTab:CreateToggle({
        Name = "Auto CL (Vida Baixa)",
        CurrentValue = false,
        Callback = function(v)
            game.Players.LocalPlayer.Character.Humanoid.HealthChanged:Connect(function(hp)
                if v and hp < 20 then game.Players.LocalPlayer:Kick("Segurança: Auto CL Ativado") end
            end)
        end
    })

    UtilsTab:CreateButton({
        Name = "Anti-AFK",
        Callback = function()
            local vu = game:GetService("VirtualUser")
            game.Players.LocalPlayer.Idled:Connect(function()
                vu:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
                task.wait(1)
                vu:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
            end)
            Rayfield:Notify({Title = "Anti-AFK", Content = "Ativado com sucesso!"})
        end
    })
end

-- // SISTEMA DE KEY INTEGRADO (UI)
local function IniciarInterfaceKey()
    local LP = game:GetService("Players").LocalPlayer
    if LP.PlayerGui:FindFirstChild("KeySystemQSW7") then return end
    
    local SG = Instance.new("ScreenGui", LP.PlayerGui)
    SG.Name = "KeySystemQSW7"
    
    local Frame = Instance.new("Frame", SG)
    Frame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
    Frame.Size = UDim2.new(0, 350, 0, 220)
    Frame.Position = UDim2.new(0.5, -175, 0.5, -110)
    Instance.new("UICorner", Frame)
    local stroke = Instance.new("UIStroke", Frame)
    stroke.Thickness, stroke.Color = 2, Color3.fromRGB(0, 180, 255)

    local Titulo = Instance.new("TextLabel", Frame)
    Titulo.Text = "RIO RP - PRIVATE HUB"
    Titulo.Size = UDim2.new(1, 0, 0.3, 0)
    Titulo.TextColor3 = Color3.new(1,1,1)
    Titulo.BackgroundTransparency = 1
    Titulo.TextScaled = true

    local Box = Instance.new("TextBox", Frame)
    Box.Size = UDim2.new(0.8, 0, 0.2, 0)
    Box.Position = UDim2.new(0.1, 0, 0.4, 0)
    Box.PlaceholderText = "Key: qsw7"
    Box.BackgroundColor3 = Color3.fromRGB(25,25,25)
    Box.TextColor3 = Color3.new(1,1,1)
    Instance.new("UICorner", Box)

    local BtnGo = Instance.new("TextButton", Frame)
    BtnGo.Size = UDim2.new(0.8, 0, 0.2, 0)
    BtnGo.Position = UDim2.new(0.1, 0, 0.7, 0)
    BtnGo.Text = "VERIFICAR E ENTRAR"
    BtnGo.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
    BtnGo.TextColor3 = Color3.new(1,1,1)
    Instance.new("UICorner", BtnGo)

    BtnGo.MouseButton1Click:Connect(function()
        if Box.Text == SETTINGS.CORRECT_KEY then
            SG:Destroy()
            AbrirPainelPrincipal()
        else
            Box.Text = ""
            Box.PlaceholderText = "ERRO: KEY INVÁLIDA"
            task.wait(1)
            Box.PlaceholderText = "Key: qsw7"
        end
    end)
end

-- // INICIALIZAÇÃO
if tostring(loadstring) ~= "function: builtin#52" and not tostring(loadstring):find("native") then
    _kick(game.Players.LocalPlayer, "Segurança: Hook Detectado.")
else
    IniciarInterfaceKey()
end
