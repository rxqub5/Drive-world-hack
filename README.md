local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/x3fall3nangel/mercury-lib-edit/master/src.lua"))()

local GUI = Library:Create{
    Name = "Quý hói hub",
    Size = UDim2.fromOffset(600, 400),
    Theme = Library.Themes.Serika,
    Link = "https://github.com/deeeity/mercury-lib](https://github.com/rxqub5"
}

local Main = GUI:tab{
    Name = "Main",
    Icon = "rbxassetid://8569322835" -- rbxassetid://2174510075 home icon
}

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualInputManager = game:GetService("VirtualInputManager")

local lp = Players.LocalPlayer
local Systems = ReplicatedStorage:WaitForChild("Systems")

local Race = lp.PlayerGui.Score.Frame.Race
local Timer
local Laps
local material

local Driveworld = {}

for i,v in pairs(getconnections(Players.LocalPlayer.Idled)) do
    if v["Disable"] then
        v["Disable"](v)
    elseif v["Disconnect"] then
        v["Disconnect"](v)
    end
end

local function getchar()
    return lp.Character or lp.CharacterAdded:Wait()
end

local function isvehicle()
    for i,v in next, workspace.Cars:GetChildren() do
        if (v:IsA("Model") and v:FindFirstChild("Owner") and v:FindFirstChild("Owner").Value == lp) then
            if v:FindFirstChild("CurrentDriver") and v:FindFirstChild("CurrentDriver").Value == lp then
                return true
            end
        end
    end
    return false
end

local function getvehicle()
    for i,v in next, workspace.Cars:GetChildren() do
        if v:IsA("Model") and v:FindFirstChild("Owner") and v:FindFirstChild("Owner").Value == lp then
            return v
        end
    end
    return
end

local function spawnvehicle()
    local Cars = ReplicatedStorage:WaitForChild("PlayerData"):WaitForChild(lp.Name):WaitForChild("Inventory"):WaitForChild("Cars")
    local Truck = Cars:FindFirstChild("FullE") or Cars:FindFirstChild("Casper")
    local normalcar = Cars:FindFirstChildWhichIsA("Folder")
    if Truck then
        Systems:WaitForChild("CarInteraction"):WaitForChild("SpawnPlayerCar"):InvokeServer(Truck)
    else
        Systems:WaitForChild("CarInteraction"):WaitForChild("SpawnPlayerCar"):InvokeServer(normalcar)
    end
end


Main:Toggle({
    Name = "tự động giao hàng",
	StartingState = false,
    Description = "Dùng Full-E để đc nhiều tiền nhất",
	Callback = function(state)
        Driveworld["autodelivery"] = state
    end
})

Main:Dropdown{
    Name = "Select Material",
    StartingText = "Select...",
    Description = nil,
    Items = {"Wood", "Steel"},
    Callback = function(item)
        material = item
    end
}

Main:Toggle({
    Name = "Auto Delivery Material",
	StartingState = false,
    Description = "wait 25 sec",
	Callback = function(state)
        Driveworld["autodeliverymaterial"] = state
        if state == false then
            ReplicatedStorage:WaitForChild("Systems"):WaitForChild("Contracts"):WaitForChild("EndContract"):InvokeServer()
            ReplicatedStorage:WaitForChild("Systems"):WaitForChild("Contracts"):WaitForChild("EndContract"):InvokeServer()
        end
    end
})

-- Main:Toggle({
--     Name = "Auto Delivery Food",
-- 	StartingState = false,
--     Description = "easier for quest",
-- 	Callback = function(state)
--         Driveworld["autodeliveryfood"] = state
--     end
-- })

-- Main:Button({
--     Name = "Find Barn Part",
--     Description = "Tp ur car when u near the barn part",
-- 	Callback = function()
--         for i,v in next, workspace.Objects.Destructible:GetChildren() do
--             if v.Name == "BarnFindItem" and v:FindFirstChildWhichIsA("MeshPart") then
--                 Systems:WaitForChild("Navigate"):WaitForChild("Teleport"):InvokeServer(v:FindFirstChildWhichIsA("MeshPart").CFrame)
--                 task.wait(.5)
--             end
--         end
--     end
-- })

-- task.spawn(function()
--     while task.wait() do
--         if Driveworld["autodeliveryfood"] then
--             pcall(function()
--                 if isvehicle() == false then
--                     if not getvehicle() then
--                         spawnvehicle()
--                     end
--                     getchar().HumanoidRootPart.CFrame = getvehicle().PrimaryPart.CFrame
--                     task.wait(1)
--                     VirtualInputManager:SendKeyEvent(true, "E", false, game)
--                     VirtualInputManager:SendKeyEvent(false, "E", false, game)
--                 end
--                 local completepos
--                 local CompletionRegion
--                 local job = lp.PlayerGui.Score.Frame.Jobs
--                 repeat task.wait()
--                     if job.Visible == false and Driveworld["autodeliveryfood"] then
--                         Systems:WaitForChild("Jobs"):WaitForChild("StartJob"):InvokeServer("FoodDelivery","Tavern")
--                     end
--                 until job.Visible == true or Driveworld["autodeliveryfood"] == false
--                 CompletionRegion = workspace:FindFirstChild("CompletionRegion")
--                 for i = 1, 10 do
--                     if not Driveworld["autodelivery"] or not getvehicle() or not getchar() or isvehicle() == false or job.Visible == false then
--                         break
--                     end
--                     task.wait(1)
--                 end
--                 if CompletionRegion and CompletionRegion:FindFirstChild("Primary").CFrame then
--                     completepos = CompletionRegion:FindFirstChild("Primary").CFrame * CFrame.new(0,3,0) 
--                 end
--                 getvehicle():SetPrimaryPartCFrame(completepos)
--                 task.wait(.5)
--                 Systems:WaitForChild("Jobs"):WaitForChild("CompleteJob"):InvokeServer()
--                 task.wait(.5)
--                 if lp.PlayerGui.JobComplete.Enabled == true then
--                     Systems:WaitForChild("Jobs"):WaitForChild("CashBankedEarnings"):FireServer()
--                     firesignal(lp.PlayerGui.JobComplete.Window.Content.Buttons.RetryButton.MouseButton1Click)
--                 end
--             end)
--         end
--     end
-- end)

task.spawn(function()
    while task.wait(.1) do
        if Driveworld["autodelivery"] then
            local job = lp.PlayerGui.Score.Frame.Jobs
            local jobDistance
            local function getjobdistance(Completedistance)
                local jobDist
                local yeas = string.split(Completedistance, " ")
                for i,v in next, yeas do
                    if tonumber(v) then
                        jobDist = v
                        print("Truck Job Distance : " .. jobDist)
                    end
                end
                return jobDist
            end
            if isvehicle() == false then
                if not getvehicle() then
                    spawnvehicle()
                end
                getchar().HumanoidRootPart.CFrame = getvehicle().PrimaryPart.CFrame
                task.wait(1)
                VirtualInputManager:SendKeyEvent(true, "E", false, game)
                VirtualInputManager:SendKeyEvent(false, "E", false, game)
            end
            repeat task.wait(.1)
                if job.Visible == false then
                    ReplicatedStorage:WaitForChild("Systems"):WaitForChild("Jobs"):WaitForChild("StartJob"):InvokeServer(workspace:WaitForChild("Jobs"):WaitForChild("Trucking"),workspace:WaitForChild("Jobs"):WaitForChild("Trucking"):WaitForChild("StartPoints"):WaitForChild("Logs"))
                end
            until job.Visible == true or Driveworld["autodelivery"] == false
            print("Start Job")
            repeat task.wait(.1)
                if workspace:FindFirstChild("CompletionRegion") then
                    jobDistance = getjobdistance(workspace:FindFirstChild("CompletionRegion"):FindFirstChild("Primary"):FindFirstChild("DestinationIndicator"):FindFirstChild("Distance").Text)
                end
                if jobDistance and tonumber(jobDistance) < 2.1 then
                    ReplicatedStorage:WaitForChild("Systems"):WaitForChild("Jobs"):WaitForChild("StartJob"):InvokeServer(workspace:WaitForChild("Jobs"):WaitForChild("Trucking"),workspace:WaitForChild("Jobs"):WaitForChild("Trucking"):WaitForChild("StartPoints"):WaitForChild("Logs"))
                end
            until jobDistance and tonumber(jobDistance) >= 2.1 or Driveworld["autodelivery"] == false
            for i = 1, 10 do
                if not Driveworld["autodelivery"] or not getvehicle() or not getchar() or isvehicle() == false or job.Visible == false then
                    break
                end
                task.wait(1)
            end
            if workspace:FindFirstChild("CompletionRegion") and workspace:FindFirstChild("CompletionRegion"):FindFirstChild("Primary") then
                getvehicle():SetPrimaryPartCFrame(workspace:FindFirstChild("CompletionRegion"):FindFirstChild("Primary").CFrame * CFrame.new(0,3,0))
            end
            task.wait(1)
            Systems:WaitForChild("Jobs"):WaitForChild("CompleteJob"):InvokeServer()
            task.wait(.5)
            if lp.PlayerGui.JobComplete.Enabled == true then
                Systems:WaitForChild("Jobs"):WaitForChild("CashBankedEarnings"):FireServer()
                for i,v in next, getconnections(lp.PlayerGui.JobComplete.Window.Content.Buttons.CloseButton.MouseButton1Click) do
                    v:Fire()
                end
            end
            print("Completed Job")
        end
    end
end)

task.spawn(function()
    while task.wait(.1) do
        if Driveworld["autodeliverymaterial"] and material then
            local cargo
            local completepos
            local CompletionRegion
            local Contracts = lp.PlayerGui.Score.Frame.Contracts
            if isvehicle() == false then
                if not getvehicle() then
                    spawnvehicle()
                end
                getchar().HumanoidRootPart.CFrame = getvehicle().PrimaryPart.CFrame
                task.wait(1)
                VirtualInputManager:SendKeyEvent(true, "E", false, game)
                VirtualInputManager:SendKeyEvent(false, "E", false, game)
            end
            repeat task.wait(.1)
                if Contracts.Visible == false then
                    ReplicatedStorage:WaitForChild("Systems"):WaitForChild("Contracts"):WaitForChild("PrecalculateRoutes"):InvokeServer()
                    task.wait(.5)        
                    ReplicatedStorage:WaitForChild("Systems"):WaitForChild("Contracts"):WaitForChild("StartContract"):InvokeServer(material)
                end
            until Contracts.Visible == true or Driveworld["autodeliverymaterial"] == false
            task.wait(1)
            repeat task.wait(.5)
                for i,v in next, workspace:GetChildren() do
                    if v.Name == "Model" and (v:FindFirstChild("SteelPalettes") or v:FindFirstChild("WoodCrates") or v:FindFirstChild("ShippingCargo")) and v:FindFirstChildWhichIsA("Model").PrimaryPart then
                        cargo = v:FindFirstChildWhichIsA("Model").PrimaryPart
                    end
                end
            until cargo or Driveworld["autodeliverymaterial"] == false
            if lp.PlayerGui.GarageContracts.Frame.ContractFinished.Visible == true then
                for i,v in next, getconnections(lp.PlayerGui.GarageContracts.Frame.Header.CloseButton.MouseButton1Click) do
                    v:Fire()
                end
            end
            if lp.PlayerGui.CancelActivityConfirmation.Enabled == true then
                for i,v in next, getconnections(lp.PlayerGui.CancelActivityConfirmation.Window.Content.Buttons.Cancel.MouseButton1Click) do
                    v:Fire()
                end
            end
            if cargo and getvehicle() and getvehicle().PrimaryPart then
                getvehicle():SetPrimaryPartCFrame(cargo.CFrame)
                task.wait(1)
                if (cargo.Position - getvehicle().PrimaryPart.Position).magnitude <= 30 then
                    VirtualInputManager:SendKeyEvent(true, "E", false, game)
                end
            end
            task.wait(1)
            if lp.PlayerGui.CancelActivityConfirmation.Enabled == true then
                for i,v in next, getconnections(lp.PlayerGui.CancelActivityConfirmation.Window.Content.Buttons.Cancel.MouseButton1Click) do
                    v:Fire()
                end
            end
            local count = 0
            repeat task.wait(.1)
                if workspace:FindFirstChild("CompletionRegion") then
                    CompletionRegion = workspace.CompletionRegion
                end
                count = count + 1
            until CompletionRegion or Driveworld["autodeliverymaterial"] == false or count >= 50
            if CompletionRegion and CompletionRegion:FindFirstChild("Primary").CFrame then
                completepos = CompletionRegion:FindFirstChild("Primary").CFrame * CFrame.new(0,3,0)
            end
            for i = 1, 27 do
                if not Driveworld["autodeliverymaterial"] or not getvehicle() or not getchar() or isvehicle() == false or Contracts.Visible == false then
                    break
                end
                task.wait(1)
            end
            if completepos then
                getvehicle():SetPrimaryPartCFrame(completepos)
                task.wait(1)
                ReplicatedStorage:WaitForChild("Systems"):WaitForChild("Contracts"):WaitForChild("DropoffCargo"):InvokeServer()
            end
        end
    end
end)

GUI:Credit{
    Name = "x3Fall3nAngel",
    Description = "Made the script",
    V3rm = "https://v3rmillion.net/member.php?action=profile&uid=2270329",
    Discord = "https://discord.gg/b9QX5rnkT5"
}

GUI:set_status("Status | Active")
