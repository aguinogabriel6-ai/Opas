local Players=game:GetService("Players")
local UIS=game:GetService("UserInputService")
local Tween=game:GetService("TweenService")
local SG=game:GetService("StarterGui")
local LP=Players.LocalPlayer
while not LP do task.wait(0.1) LP=Players.LocalPlayer end
local PG=LP:WaitForChild("PlayerGui")
pcall(function() local o=PG:FindFirstChild("NeonHub") if o then o:Destroy() end end)

local C={
    bg=Color3.fromRGB(18,18,20),
    sidebar=Color3.fromRGB(15,15,17),
    panel=Color3.fromRGB(24,24,27),
    card=Color3.fromRGB(30,30,34),
    accent=Color3.fromRGB(0,200,255),
    accent2=Color3.fromRGB(120,80,255),
    text=Color3.fromRGB(230,230,235),
    textDim=Color3.fromRGB(140,140,150),
    textDark=Color3.fromRGB(90,90,100),
    stroke=Color3.fromRGB(45,45,52),
    toggleOff=Color3.fromRGB(50,50,58),
    sliderBg=Color3.fromRGB(40,40,46),
    verde=Color3.fromRGB(80,220,120),
    vermelho=Color3.fromRGB(255,100,100),
    amarelo=Color3.fromRGB(255,200,80),
    roxo=Color3.fromRGB(180,100,255),
}

local CONFIG={
    Pet="Serval",
    AutoServal=false,
    ReBid=true,
    COOLDOWN=0.05,
    PrecoMax=0,
    TotalServal=0,
}

local function canto(o,r) local c=Instance.new("UICorner") c.CornerRadius=UDim.new(0,r) c.Parent=o return c end
local function stroke(o,cor,esp,tr)
    local s=Instance.new("UIStroke")
    s.Thickness=esp or 1
    s.Color=cor or C.stroke
    s.Transparency=tr or 0
    s.ApplyStrokeMode=Enum.ApplyStrokeMode.Border
    s.Parent=o
    return s
end
local function txt(parent,t,size,cor,font,alin)
    local l=Instance.new("TextLabel")
    l.BackgroundTransparency=1
    l.Text=t or ""
    l.TextSize=size or 12
    l.TextColor3=cor or C.text
    l.Font=font or Enum.Font.Gotham
    l.TextXAlignment=alin or Enum.TextXAlignment.Left
    l.Parent=parent
    return l
end
local function notificar(t,tx,d)
    pcall(function() SG:SetCore("SendNotification",{Title=t,Text=tx,Duration=d or 3}) end)
end

local function m1(p)
    if not fireproximityprompt then return false end
    pcall(function() p.HoldDuration=0 p.MaxActivationDistance=100 end)
    return pcall(fireproximityprompt,p)
end
local function m2(p)
    if not firesignal then return false end
    local ok=false
    pcall(function()
        if p.PromptButtonHoldBegan then firesignal(p.PromptButtonHoldBegan) ok=true end
        if p.Triggered then firesignal(p.Triggered) ok=true end
        if p.PromptButtonHoldEnded then firesignal(p.PromptButtonHoldEnded) end
    end)
    return ok
end
local function m3(p)
    if not getconnections then return false end
    local ok=false
    pcall(function()
        if p.Triggered then
            for _,c in ipairs(getconnections(p.Triggered)) do pcall(function() c:Fire() end) ok=true end
        end
    end)
    return ok
end
local function comprar(p)
    if not p or not p.Parent or not p.Enabled then return false end
    pcall(function()
        if m1(p) then return end
        if m2(p) then return end
        m3(p)
    end)
    return true
end

local function pegarNome(m)
    local ui=m:FindFirstChild("UI")
    local ui2=ui and ui:FindFirstChild("UI")
    local frame=ui2 and ui2:FindFirstChild("Frame")
    local title=frame and frame:FindFirstChild("Title")
    local t=title and title:FindFirstChild("Title")
    if t and t:IsA("TextLabel") then return t.Text end
    return nil
end
local function pegarPreco(m)
    local ui=m:FindFirstChild("UI")
    local ui2=ui and ui:FindFirstChild("UI")
    local frame=ui2 and ui2:FindFirstChild("Frame")
    local price=frame and frame:FindFirstChild("Price")
    if not price or not price:IsA("TextLabel") then return 0 end
    local t=tostring(price.Text)
    if t=="" then return 0 end
    local num,sufixo=t:match("(%d+[%.,]?%d*)%s*([KkMmBb]?)")
    if not num then return 0 end
    if num:find(",") and not num:find("%.") then num=num:gsub(",",".") else num=num:gsub(",","") end
    local n=tonumber(num) or 0
    if sufixo=="K" or sufixo=="k" then n=n*1000 end
    if sufixo=="M" or sufixo=="m" then n=n*1000000 end
    if sufixo=="B" or sufixo=="b" then n=n*1000000000 end
    return math.floor(n)
end

local gui=Instance.new("ScreenGui")
gui.Name="NeonHub"
gui.ResetOnSpawn=false
gui.IgnoreGuiInset=true
gui.DisplayOrder=999
gui.Parent=PG

local fab=Instance.new("TextButton")
fab.Size=UDim2.new(0,50,0,50)
fab.Position=UDim2.new(0,20,0.5,-25)
fab.BackgroundColor3=C.accent
fab.Text="N"
fab.Font=Enum.Font.GothamBlack
fab.TextSize=22
fab.TextColor3=Color3.fromRGB(255,255,255)
fab.BorderSizePixel=0
fab.AutoButtonColor=false
fab.Active=true
fab.Parent=gui
canto(fab,12)
stroke(fab,C.accent,2,0.3)

local janela=Instance.new("Frame")
janela.Size=UDim2.new(0,720,0,440)
janela.Position=UDim2.new(0.5,-360,0.5,-220)
janela.BackgroundColor3=C.bg
janela.BorderSizePixel=0
janela.Active=true
janela.ClipsDescendants=true
janela.Visible=false
janela.Parent=gui
canto(janela,10)
stroke(janela,C.stroke,1,0.3)

local sidebar=Instance.new("Frame")
sidebar.Size=UDim2.new(0,180,1,0)
sidebar.BackgroundColor3=C.sidebar
sidebar.BorderSizePixel=0
sidebar.Parent=janela
canto(sidebar,10)

local logoFrame=Instance.new("Frame")
logoFrame.Size=UDim2.new(1,0,0,70)
logoFrame.BackgroundTransparency=1
logoFrame.Parent=sidebar

local logoTexto=txt(logoFrame,"NEON",24,C.accent,Enum.Font.GothamBlack,Enum.TextXAlignment.Center)
logoTexto.Size=UDim2.new(1,0,0,32)
logoTexto.Position=UDim2.new(0,0,0,16)
local logoGrad=Instance.new("UIGradient")
logoGrad.Color=ColorSequence.new(C.accent,C.accent2)
logoGrad.Parent=logoTexto

local logoSub=txt(logoFrame,"H U B",9,C.textDim,Enum.Font.GothamBold,Enum.TextXAlignment.Center)
logoSub.Size=UDim2.new(1,0,0,14)
logoSub.Position=UDim2.new(0,0,0,46)

local itensSidebar={}
local funcoesAba={}
local tituloAba,subAba

local function criarItem(icone,nome,y)
    local item=Instance.new("TextButton")
    item.Size=UDim2.new(1,-16,0,34)
    item.Position=UDim2.new(0,8,0,y)
    item.BackgroundColor3=Color3.fromRGB(0,0,0)
    item.BackgroundTransparency=1
    item.Text=""
    item.AutoButtonColor=false
    item.BorderSizePixel=0
    item.Parent=sidebar
    canto(item,6)
    local ic=txt(item,icone,14,C.textDim,Enum.Font.Gotham,Enum.TextXAlignment.Center)
    ic.Size=UDim2.new(0,28,1,0)
    ic.Position=UDim2.new(0,6,0,0)
    local t=txt(item,nome,12,C.textDim,Enum.Font.GothamMedium)
    t.Size=UDim2.new(1,-42,1,0)
    t.Position=UDim2.new(0,38,0,0)
    local barra=Instance.new("Frame")
    barra.Size=UDim2.new(0,3,0,18)
    barra.Position=UDim2.new(0,0,0.5,-9)
    barra.BackgroundColor3=C.accent
    barra.BorderSizePixel=0
    barra.Visible=false
    barra.Parent=item
    canto(barra,2)
    itensSidebar[nome]={item=item,icone=ic,texto=t,barra=barra}
end

local function trocarAba(nome)
    for n,dados in pairs(itensSidebar) do
        local ativo=(n==nome)
        dados.item.BackgroundTransparency=ativo and 0 or 1
        dados.item.BackgroundColor3=ativo and C.card or Color3.fromRGB(0,0,0)
        dados.icone.TextColor3=ativo and C.accent or C.textDim
        dados.texto.TextColor3=ativo and C.text or C.textDim
        dados.barra.Visible=ativo
    end
    for n,f in pairs(funcoesAba) do
        f.Visible=(n==nome)
        if f.Visible and f:IsA("ScrollingFrame") then f.CanvasPosition=Vector2.new(0,0) end
    end
    local icones={["AUTO-BUY"]="⚡",LEILÃO="📋"}
    local subs={
        ["AUTO-BUY"]="Serval (Secret)",
        LEILÃO="Pets disponiveis agora",
    }
    if tituloAba then tituloAba.Text=(icones[nome] or "•").." "..nome end
    if subAba then subAba.Text=subs[nome] or "" end
end

criarItem("⚡","AUTO-BUY",90)
criarItem("📋","LEILÃO",130)

local userFrame=Instance.new("Frame")
userFrame.Size=UDim2.new(1,-16,0,56)
userFrame.Position=UDim2.new(0,8,1,-64)
userFrame.BackgroundColor3=C.card
userFrame.BackgroundTransparency=0.4
userFrame.BorderSizePixel=0
userFrame.Parent=sidebar
canto(userFrame,8)
stroke(userFrame,C.stroke,1,0.5)

local avatarImg=Instance.new("ImageLabel")
avatarImg.Size=UDim2.new(0,36,0,36)
avatarImg.Position=UDim2.new(0,10,0.5,-18)
avatarImg.BackgroundColor3=C.accent
avatarImg.BorderSizePixel=0
avatarImg.Image=""
avatarImg.Parent=userFrame
canto(avatarImg,18)

local avatarTxt=txt(avatarImg,string.sub(LP.Name,1,1):upper(),16,Color3.fromRGB(255,255,255),Enum.Font.GothamBlack,Enum.TextXAlignment.Center)
avatarTxt.Size=UDim2.new(1,0,1,0)

task.spawn(function()
    local ok,url=pcall(function()
        return Players:GetUserThumbnailAsync(LP.UserId,Enum.ThumbnailType.HeadShot,Enum.ThumbnailSize.Size100x100)
    end)
    if ok and url and avatarImg then
        avatarImg.Image=url
        avatarTxt.Visible=false
    end
end)

local userName=txt(userFrame,LP.Name,11,C.text,Enum.Font.GothamBold)
userName.Size=UDim2.new(1,-56,0,16)
userName.Position=UDim2.new(0,54,0,12)

local userPlan=txt(userFrame,"Ativo",9,C.verde,Enum.Font.Gotham)
userPlan.Size=UDim2.new(1,-56,0,14)
userPlan.Position=UDim2.new(0,54,0,30)

local area=Instance.new("Frame")
area.Size=UDim2.new(1,-180,1,0)
area.Position=UDim2.new(0,180,0,0)
area.BackgroundTransparency=1
area.Parent=janela

local headerArea=Instance.new("Frame")
headerArea.Size=UDim2.new(1,-60,0,50)
headerArea.Position=UDim2.new(0,10,0,10)
headerArea.BackgroundTransparency=1
headerArea.Parent=area

tituloAba=txt(headerArea,"AUTO-BUY",18,C.text,Enum.Font.GothamBlack)
tituloAba.Size=UDim2.new(1,0,0,26)
tituloAba.Position=UDim2.new(0,0,0,0)

subAba=txt(headerArea,"Serval (Secret)",10,C.textDim,Enum.Font.Gotham)
subAba.Size=UDim2.new(1,0,0,14)
subAba.Position=UDim2.new(0,0,0,26)

local btnFechar=Instance.new("TextButton")
btnFechar.Size=UDim2.new(0,30,0,30)
btnFechar.Position=UDim2.new(1,-40,0,12)
btnFechar.BackgroundColor3=Color3.fromRGB(40,40,45)
btnFechar.Text="X"
btnFechar.Font=Enum.Font.GothamBold
btnFechar.TextSize=14
btnFechar.TextColor3=C.vermelho
btnFechar.BorderSizePixel=0
btnFechar.AutoButtonColor=false
btnFechar.Parent=area
canto(btnFechar,8)
stroke(btnFechar,C.vermelho,1,0.5)

local conteudo=Instance.new("Frame")
conteudo.Size=UDim2.new(1,-20,1,-80)
conteudo.Position=UDim2.new(0,10,0,70)
conteudo.BackgroundTransparency=1
conteudo.Parent=area

local function criarSlider(parent,labelTxt,valor,minV,maxV,callback)
    local box=Instance.new("Frame")
    box.Size=UDim2.new(1,0,0,55)
    box.BackgroundColor3=C.panel
    box.BorderSizePixel=0
    box.Parent=parent
    canto(box,8)
    local label=txt(box,labelTxt,11,C.text,Enum.Font.GothamMedium)
    label.Size=UDim2.new(1,-80,0,16)
    label.Position=UDim2.new(0,14,0,8)
    local vBox=Instance.new("Frame")
    vBox.Size=UDim2.new(0,60,0,20)
    vBox.Position=UDim2.new(1,-74,0,6)
    vBox.BackgroundColor3=C.card
    vBox.BorderSizePixel=0
    vBox.Parent=box
    canto(vBox,4)
    local vTxt=txt(vBox,tostring(valor),11,C.text,Enum.Font.GothamBold,Enum.TextXAlignment.Center)
    vTxt.Size=UDim2.new(1,0,1,0)
    local track=Instance.new("Frame")
    track.Size=UDim2.new(1,-28,0,4)
    track.Position=UDim2.new(0,14,1,-14)
    track.BackgroundColor3=C.sliderBg
    track.BorderSizePixel=0
    track.Parent=box
    canto(track,2)
    local pct=(valor-minV)/(maxV-minV)
    local fill=Instance.new("Frame")
    fill.Size=UDim2.new(pct,0,1,0)
    fill.BackgroundColor3=C.accent
    fill.BorderSizePixel=0
    fill.Parent=track
    canto(fill,2)
    local knob=Instance.new("Frame")
    knob.Size=UDim2.new(0,12,0,12)
    knob.Position=UDim2.new(pct,-6,0.5,-6)
    knob.BackgroundColor3=Color3.fromRGB(255,255,255)
    knob.BorderSizePixel=0
    knob.Parent=track
    canto(knob,6)
    local arrastando=false
    local function atualizar(x)
        local rl=math.clamp((x-track.AbsolutePosition.X)/track.AbsoluteSize.X,0,1)
        local v=math.floor(minV+(maxV-minV)*rl)
        fill.Size=UDim2.new(rl,0,1,0)
        knob.Position=UDim2.new(rl,-6,0.5,-6)
        vTxt.Text=tostring(v)
        if callback then pcall(callback,v) end
    end
    track.InputBegan:Connect(function(i)
        if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then
            arrastando=true;atualizar(i.Position.X)
        end
    end)
    UIS.InputChanged:Connect(function(i)
        if arrastando and (i.UserInputType==Enum.UserInputType.MouseMovement or i.UserInputType==Enum.UserInputType.Touch) then
            atualizar(i.Position.X)
        end
    end)
    UIS.InputEnded:Connect(function(i)
        if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then
            arrastando=false
        end
    end)
    return box
end

local function criarToggle(parent,labelTxt,ativo,corAtiva,callback)
    local box=Instance.new("Frame")
    box.Size=UDim2.new(1,0,0,42)
    box.BackgroundColor3=C.panel
    box.BorderSizePixel=0
    box.Parent=parent
    canto(box,8)
    local label=txt(box,labelTxt,12,C.text,Enum.Font.GothamMedium)
    label.Size=UDim2.new(1,-80,1,0)
    label.Position=UDim2.new(0,14,0,0)
    local btn=Instance.new("TextButton")
    btn.Size=UDim2.new(0,44,0,22)
    btn.Position=UDim2.new(1,-58,0.5,-11)
    btn.BackgroundColor3=ativo and (corAtiva or C.accent) or C.toggleOff
    btn.Text=""
    btn.AutoButtonColor=false
    btn.BorderSizePixel=0
    btn.Parent=box
    canto(btn,11)
    local knob=Instance.new("Frame")
    knob.Size=UDim2.new(0,16,0,16)
    knob.Position=ativo and UDim2.new(1,-20,0.5,-8) or UDim2.new(0,4,0.5,-8)
    knob.BackgroundColor3=Color3.fromRGB(255,255,255)
    knob.BorderSizePixel=0
    knob.Parent=btn
    canto(knob,8)
    local estado=ativo
    btn.MouseButton1Click:Connect(function()
        estado=not estado
        Tween:Create(btn,TweenInfo.new(0.2),{
            BackgroundColor3=estado and (corAtiva or C.accent) or C.toggleOff
        }):Play()
        Tween:Create(knob,TweenInfo.new(0.2),{
            Position=estado and UDim2.new(1,-20,0.5,-8) or UDim2.new(0,4,0.5,-8)
        }):Play()
        if callback then pcall(callback,estado) end
    end)
    return box,btn
end

local function criarLabel(parent,t,size,cor)
    local box=Instance.new("Frame")
    box.Size=UDim2.new(1,0,0,24)
    box.BackgroundTransparency=1
    box.Parent=parent
    local l=txt(box,t,size or 11,cor or C.textDim,Enum.Font.GothamBold)
    l.Size=UDim2.new(1,0,1,0)
    return box,l
end

local function novaAba(nome)
    local scroll=Instance.new("ScrollingFrame")
    scroll.Size=UDim2.new(1,0,1,0)
    scroll.BackgroundTransparency=1
    scroll.BorderSizePixel=0
    scroll.ScrollBarThickness=3
    scroll.ScrollBarImageColor3=C.accent
    scroll.CanvasSize=UDim2.new(0,0,0,0)
    scroll.AutomaticCanvasSize=Enum.AutomaticSize.Y
    scroll.Visible=false
    scroll.Parent=conteudo
    local lay=Instance.new("UIListLayout")
    lay.Padding=UDim.new(0,8)
    lay.Parent=scroll
    local p=Instance.new("UIPadding")
    p.PaddingTop=UDim.new(0,4)
    p.PaddingBottom=UDim.new(0,4)
    p.Parent=scroll
    funcoesAba[nome]=scroll
    return scroll
end

local abaAuto=novaAba("AUTO-BUY")
criarLabel(abaAuto,"PETS ALVO",10,C.textDark)
criarToggle(abaAuto,"Auto-Buy Serval (Secret)",false,C.roxo,function(v) CONFIG.AutoServal=v end)
criarLabel(abaAuto,"AJUSTES",10,C.textDark)
criarToggle(abaAuto,"RE-BID",true,C.verde,function(v) CONFIG.ReBid=v end)
criarSlider(abaAuto,"Cooldown",50,10,500,function(v) CONFIG.COOLDOWN=v/1000 end)
criarSlider(abaAuto,"Preco Maximo",0,0,500000,function(v) CONFIG.PrecoMax=v end)

local abaLeilao=novaAba("LEILÃO")
local cardsAtuais={}

local function criarCardPet(nome,preco,raridade,corRar)
    local card=Instance.new("Frame")
    card.Size=UDim2.new(1,0,0,70)
    card.BackgroundColor3=C.panel
    card.BorderSizePixel=0
    card.Parent=abaLeilao
    canto(card,8)
    stroke(card,corRar,1,0.5)
    local ic=txt(card,"●",24,corRar,Enum.Font.Gotham,Enum.TextXAlignment.Center)
    ic.Size=UDim2.new(0,50,1,0)
    local nm=txt(card,nome,13,corRar,Enum.Font.GothamBold)
    nm.Size=UDim2.new(1,-70,0,18)
    nm.Position=UDim2.new(0,55,0,8)
    local rar=txt(card,string.upper(raridade),9,C.textDim,Enum.Font.GothamBold)
    rar.Size=UDim2.new(1,-70,0,14)
    rar.Position=UDim2.new(0,55,0,26)
    local prc=txt(card,tostring(preco),11,C.amarelo,Enum.Font.GothamBold)
    prc.Name="PrecoTxt"
    prc.Size=UDim2.new(1,-70,0,16)
    prc.Position=UDim2.new(0,55,0,42)
    return card
end

local function atualizarLeilao()
    local cli=workspace:FindFirstChild("Client")
    local sp=cli and cli:FindFirstChild("Spawn")
    local pf=sp and sp:FindFirstChild("Pets")
    if not pf then return end
    local vistos={}
    for _,m in ipairs(pf:GetChildren()) do
        if m:IsA("Model") then
            local nome=pegarNome(m)
            if nome==CONFIG.Pet then
                vistos[m]=true
                if not cardsAtuais[m] or not cardsAtuais[m].Parent then
                    cardsAtuais[m]=criarCardPet(nome,pegarPreco(m),"Secret",C.roxo)
                else
                    local prc=cardsAtuais[m]:FindFirstChild("PrecoTxt")
                    if prc then prc.Text=tostring(pegarPreco(m)) end
                end
            end
        end
    end
    for m,c in pairs(cardsAtuais) do
        if not vistos[m] then
            pcall(function() c:Destroy() end)
            cardsAtuais[m]=nil
        end
    end
end

task.spawn(function()
    while gui and gui.Parent do
        pcall(atualizarLeilao)
        task.wait(2)
    end
end)

local ultimaTentativa={}
local tentando={}
local ultimoEstado={}

task.spawn(function()
    while gui and gui.Parent do
        if CONFIG.AutoServal then
            pcall(function()
                local cli=workspace:FindFirstChild("Client")
                local sp=cli and cli:FindFirstChild("Spawn")
                local pf=sp and sp:FindFirstChild("Pets")
                if pf then
                    for _,m in ipairs(pf:GetChildren()) do
                        if m:IsA("Model") then
                            local nome=pegarNome(m)
                            if nome==CONFIG.Pet then
                                local p=m:FindFirstChild("ProximityPrompt")
                                if p and p:IsA("ProximityPrompt") then
                                    local est=p.Enabled
                                    local ant=ultimoEstado[m]
                                    if CONFIG.ReBid and est==true and ant==false then
                                        ultimaTentativa[m]=0
                                        tentando[m]=false
                                    end
                                    ultimoEstado[m]=est
                                    if est then
                                        local preco=pegarPreco(m)
                                        local podeComprar=(CONFIG.PrecoMax==0) or (preco<=CONFIG.PrecoMax)
                                        if podeComprar then
                                            local agora=tick()
                                            if (agora-(ultimaTentativa[m] or 0))>=CONFIG.COOLDOWN and not tentando[m] then
                                                tentando[m]=true
                                                ultimaTentativa[m]=agora
                                                comprar(p)
                                                if not p.Enabled then
                                                    CONFIG.TotalServal+=1
                                                    notificar("NEON","Comprou Serval",3)
                                                end
                                                tentando[m]=false
                                            end
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end)
        end
        task.wait(CONFIG.COOLDOWN)
    end
end)

local aberto=false

local function abrir()
    aberto=true
    janela.Visible=true
    janela.Size=UDim2.new(0,720,0,0)
    Tween:Create(janela,TweenInfo.new(0.3,Enum.EasingStyle.Back,Enum.EasingDirection.Out),{
        Size=UDim2.new(0,720,0,440)
    }):Play()
end

local function fechar()
    aberto=false
    Tween:Create(janela,TweenInfo.new(0.2,Enum.EasingStyle.Quart,Enum.EasingDirection.In),{
        Size=UDim2.new(0,720,0,0)
    }):Play()
    task.delay(0.2,function()
        if not aberto then janela.Visible=false end
    end)
end

fab.MouseButton1Click:Connect(function()
    if aberto then fechar() else abrir() end
end)
btnFechar.MouseButton1Click:Connect(fechar)

for nome,dados in pairs(itensSidebar) do
    dados.item.MouseButton1Click:Connect(function()
        trocarAba(nome)
    end)
end

trocarAba("AUTO-BUY")

local drag=false
local startPos,startInput
local dragArea=Instance.new("Frame")
dragArea.Size=UDim2.new(1,-220,0,50)
dragArea.Position=UDim2.new(0,180,0,0)
dragArea.BackgroundTransparency=1
dragArea.Parent=janela

dragArea.InputBegan:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then
        drag=true
        startInput=i.Position
        startPos=janela.Position
    end
end)
UIS.InputChanged:Connect(function(i)
    if drag and (i.UserInputType==Enum.UserInputType.MouseMovement or i.UserInputType==Enum.UserInputType.Touch) then
        local d=i.Position-startInput
        janela.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+d.X,startPos.Y.Scale,startPos.Y.Offset+d.Y)
    end
end)
UIS.InputEnded:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then
        drag=false
    end
end)

local fabDrag,fabMoved,fabStartPos,fabStartInput=false,false,nil,nil

fab.InputBegan:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then
        fabDrag=true
        fabMoved=false
        fabStartPos=fab.Position
        fabStartInput=i.Position
    end
end)

UIS.InputChanged:Connect(function(i)
    if fabDrag and (i.UserInputType==Enum.UserInputType.MouseMovement or i.UserInputType==Enum.UserInputType.Touch) then
        local delta=i.Position-fabStartInput
        if math.abs(delta.X)>4 or math.abs(delta.Y)>4 then
            fabMoved=true
        end
        local nx=fabStartPos.X.Offset+delta.X
        local ny=fabStartPos.Y.Offset+delta.Y
        local vs=workspace.CurrentCamera.ViewportSize
        nx=math.clamp(nx,0,vs.X-50)
        ny=math.clamp(ny,0,vs.Y-50)
        fab.Position=UDim2.new(0,nx,0,ny)
    end
end)

UIS.InputEnded:Connect(function(i)
    if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then
        if fabDrag and not fabMoved then
            if aberto then fechar() else abrir() end
        end
        fabDrag=false
    end
end)

notificar("NEON HUB","Serval (Secret)",3)
print("[NEON HUB] Carregado")
