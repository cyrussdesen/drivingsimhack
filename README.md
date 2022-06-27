--Vars
LocalPlayer = game:GetService("Players").LocalPlayer
Camera = workspace.CurrentCamera
VirtualUser = game:GetService("VirtualUser")
MarketplaceService = game:GetService("MarketplaceService")

--Güncel Aracı Alın
function GetCurrentVehicle()
    return LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") and LocalPlayer.Character.Humanoid.SeatPart and LocalPlayer.Character.Humanoid.SeatPart.Parent
end

--Bildirim İşleyici
function SendNotification(Title, Message, Duration)
    game.StarterGui:SetCore("SendNotification", {
        Title = Title;
        Text = Message;
        Duration = Duration;
    })
end

--Normal TP
function TP(cframe)
    GetCurrentVehicle():SetPrimaryPartCFrame(cframe)
end

--Hız TP
function VelocityTP(cframe)
    TeleportSpeed = math.random(200, 600)
    Car = GetCurrentVehicle()
    local BodyGyro = Instance.new("BodyGyro", Car.PrimaryPart)
    BodyGyro.P = 5000
    BodyGyro.maxTorque = Vector3.new(9e9, 9e9, 9e9)
    BodyGyro.CFrame = Car.PrimaryPart.CFrame
    local BodyVelocity = Instance.new("BodyVelocity", Car.PrimaryPart)
    BodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
    BodyVelocity.Velocity = CFrame.new(Car.PrimaryPart.Position, cframe.p).LookVector * TeleportSpeed
    wait((Car.PrimaryPart.Position - cframe.p).Magnitude / TeleportSpeed)
    BodyVelocity.Velocity = Vector3.new()
    wait(0.1)
    BodyVelocity:Destroy()
    BodyGyro:Destroy()
end

--Auto Farm
StartPosition = CFrame.new(Vector3.new(811.013184, 27.3421249, 2203.55542), Vector3.new(-187, 25.7, 1982))
EndPosition = CFrame.new(Vector3.new(-76.4760208, 27.7194824, 1993.84229), Vector3.new(-187, 25.7, 1982))
AutoFarmFunc = coroutine.create(function()
    while wait() do
        if not AutoFarm then
            AutoFarmRunning = false
            coroutine.yield()
        end
        AutoFarmRunning = true
        pcall(function()
            if not GetCurrentVehicle() and tick() - (LastNotif or 0) > 5 then
                LastNotif = tick()
                SendNotification("sunnystar", "Lütfen Bir Araç Giriniz!")
            else
                TP(StartPosition + (TouchTheRoad and Vector3.new() or Vector3.new(0, 1, 0)))
                VelocityTP(EndPosition + (TouchTheRoad and Vector3.new() or Vector3.new(0, 1, 0)))
                TP(EndPosition + (TouchTheRoad and Vector3.new() or Vector3.new(0, 1, 0)))
                VelocityTP(StartPosition + (TouchTheRoad and Vector3.new() or Vector3.new(0, 1, 0)))
            end
        end)
    end
end)
--Sunny_ST4R abone olun
--Anti AFK
AntiAFK = true
LocalPlayer.Idled:Connect(function()
    VirtualUser:CaptureController()
    VirtualUser:ClickButton2(Vector2.new(), Camera.CFrame)
end)
--Sunny_ST4R abone olun
--UI
local library = loadstring(game:HttpGet("https://raw.githubusercontent.com/GreenDeno/Venyx-UI-Library/main/source.lua"))()
local venyx = library.new(MarketplaceService:GetProductInfo(game.PlaceId).Name)
--Sunny_ST4R abone olun
--Temalar
local themes = {
    Background = Color3.fromRGB(24, 24, 24),
    Glow = Color3.fromRGB(0, 0, 0),
    Accent = Color3.fromRGB(10, 10, 10),
    LightContrast = Color3.fromRGB(20, 20, 20),
    DarkContrast = Color3.fromRGB(14, 14, 14),
    TextColor = Color3.fromRGB(255, 255, 255)
}

--Sayfa
local page1 = venyx:addPage("Ana")
local page2 = venyx:addPage("Diğer")
--Sunny_ST4R abone olun
--Sayfa 1
local FirstSection1 = page1:addSection("Auto Farm")
local FirstSection2 = page1:addSection("Seçenekler")

FirstSection1:addToggle(
    "Etkinleştirilmiş",
    nil,
    function(value)
        AutoFarm = value
        if value and not AutoFarmRunning then
            coroutine.resume(AutoFarmFunc)
        end
    end
)
FirstSection2:addToggle(
    "Yere dokun",
    nil,
    function(value)
        TouchTheRoad = value
    end
)
--Sunny_ST4R abone olun
--Sayfa 2
local SecondSection1 = page2:addSection("İnfo")
local SecondSection2 = page2:addSection("Ayarlar")

SecondSection1:addButton("Partnerler:")
SecondSection1:addButton("Sunny_ST4R - sahip", function() setclipboard("Sunny_ST4R") jailbreakNotify("Copied to clipboard.") end)
SecondSection1:addButton("Swordking12341 - kodlayıcı", function() setclipboard(""Swordking12341") jailbreakNotify("Copied to clipboard.") end)
SecondSection1:addButton("Discord:")
SecondSection1:addButton("discordumuz yok", function() setclipboard("discordumuz yok") jailbreakNotify("Copied to clipboard.") end)
SecondSection2:addToggle(
    "Anti AFK",
    true,
    function(value)
        AntiAFK = value
    end
)
SecondSection2:addKeybind(
    "Tuş Bağlantısını",
    Enum.KeyCode.RightShift,
    function()
        venyx:toggle()
    end,
    function(key)
        Keybind = key.KeyCode.Name
    end
)
for theme, color in pairs(themes) do
    SecondSection2:addColorPicker(
        theme,
        color,
        function(color3)
            venyx:setTheme(theme, color3)
        end
    )
end

--Sunny_ST4R abone olun
--load
venyx:SelectPage(venyx.pages[1], true)
