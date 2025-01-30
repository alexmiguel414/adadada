local players = game:GetService("Players")
local userInputService = game:GetService("UserInputService")
local runService = game:GetService("RunService")
local localPlayer = players.LocalPlayer
local camera = workspace.CurrentCamera

local aimbotEnabled = false
local keybind = Enum.KeyCode.E -- Default key to toggle aimbot

userInputService.InputBegan:Connect(function(input)
    if input.KeyCode == keybind then
        aimbotEnabled = not aimbotEnabled
    end
end)

runService.RenderStepped:Connect(function()
    if aimbotEnabled then
        local closestPlayer = nil
        local shortestDistance = math.huge

        for _, player in pairs(players:GetPlayers()) do
            if player ~= localPlayer and player.Team ~= localPlayer.Team and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local pos = camera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
                local distance = (Vector2.new(pos.X, pos.Y) - userInputService:GetMouseLocation()).magnitude
                if distance < shortestDistance then
                    closestPlayer = player
                    shortestDistance = distance
                end
            end
        end

        if closestPlayer then
            local targetPos = camera:WorldToViewportPoint(closestPlayer.Character.HumanoidRootPart.Position)
            mousemoverel(targetPos.X - userInputService:GetMouseLocation().X, targetPos.Y - userInputService:GetMouseLocation().Y)
        end
    end
end)
