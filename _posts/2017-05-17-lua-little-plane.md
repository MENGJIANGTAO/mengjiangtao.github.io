---
layout: post
title: Lua implement little plane game.
---

{{ page.title }}
================

<p class="meta">17 May 2017 - Lua little plane.</p>

这是我一天的战果，几乎每一个函数都会碰到问题，这里撸撸那里撸撸，结果，一天的时候只撸了190行。不过还好，功能都实现了。。。

```
local MainScene = class("MainScene", cc.load("mvc").ViewBase)

local bg
local bg1
local bgWidth = 0
local bgHeight = 0
local bgMoveSpeed = 2.5

local sPlayerBody
local iPlayerWidth
local iPlayerHeight
local iPlayerBlood

local canMove

local PlayerBullets = {}
local BulletNumber = 1
local BulletTimeSince = 0.5
local EnemyTimeSince = 1

-- local Enemy = {}
local Enemys = {}

local RandomPoints = {}

function MainScene:onCreate()
    -- add background image
    -- display.newSprite("HelloWorld.png")
    --     :move(display.center)
    --     :addTo(self)
    -- -- add HelloWorld label
    -- cc.Label:createWithSystemFont("Hello World", "Arial", 40)
    --     :move(display.cx, display.cy + 200)
    --     :addTo(self)
    local function initBg()
        bg = cc.Sprite:create("img_bg_1.jpg")
        bgWidth = bg:getTexture():getPixelsWide()
        bgHeight = bg:getTexture():getPixelsHigh()
        bg:setAnchorPoint(0,0)
        bg:setPosition(0,0)
        self:addChild(bg)
        bg1 = cc.Sprite:create("img_bg_2.jpg")
        self:addChild(bg1)
        bg1:setAnchorPoint(0,0)
        bg1:setPosition(0,bgHeight)
    end
    local function UpdateBackGround()
        if bg:getPositionY() < -bgHeight then
            bg:setPositionY(bgHeight)
        end
        if bg1:getPositionY() < -bgHeight then
            bg1:setPositionY(bgHeight)
        end

        bg:setPositionY(bg:getPositionY()-bgMoveSpeed)
        bg1:setPositionY(bg1:getPositionY()-bgMoveSpeed)
    end

    local function CreatePlayer()
        sPlayerBody = cc.Sprite:create("n1.png")
        sPlayerBody:setAnchorPoint(0.5,0.5)
        iPlayerWidth = sPlayerBody:getTexture():getPixelsWide()
        iPlayerHeight = sPlayerBody:getTexture():getPixelsHigh()
        sPlayerBody:setPosition((display.width - iPlayerWidth)/2,iPlayerHeight)
        self:addChild(sPlayerBody)
        iPlayerBlood = 100
    end

    local function CreateTouchListener()
        local  listenner = cc.EventListenerTouchOneByOne:create()
        listenner:setSwallowTouches(true)
        listenner:registerScriptHandler(function(touch, event)
                -- print(string.format("Paddle::onTouchBegan id = %d, x = %f, y = %f", touch:getId(), touch:getLocation().x, touch:getLocation().y))
                if cc.rectContainsPoint(sPlayerBody:getBoundingBox(),touch:getLocation()) then
                    canMove = true
                    -- print("Click player.")
                end
                return true
            end,cc.Handler.EVENT_TOUCH_BEGAN )
        listenner:registerScriptHandler(function(touch, event)
                -- print(string.format("Paddle::onTouchMoved id = %d, x = %f, y = %f", touch:getId(), touch:getLocation().x, touch:getLocation().y))
                if canMove then
                    sPlayerBody:setAnchorPoint(0.5,0.5)
                    sPlayerBody:setPosition(touch:getLocation())
                end
            end,cc.Handler.EVENT_TOUCH_MOVED )
        listenner:registerScriptHandler(function(touch, event)
                -- print(string.format("Paddle::onTouchEnded id = %d, x = %f, y = %f", touch:getId(), touch:getLocation().x, touch:getLocation().y))
                canMove = false
            end,cc.Handler.EVENT_TOUCH_ENDED )

        local eventDispatcher = self:getEventDispatcher()
        eventDispatcher:addEventListenerWithSceneGraphPriority(listenner, self)
    end

    local function CreatePlayerBullet()
        local cache = cc.SpriteFrameCache:getInstance()
        cache:addSpriteFrames("bullet.plist")
        local sprite = cc.Sprite:createWithSpriteFrameName("bullet_1.png")
        sprite:setPosition(cc.p(sPlayerBody:getPositionX(), sPlayerBody:getPositionY() + iPlayerHeight / 2))
        self:addChild(sprite)
        table.insert(PlayerBullets,1,sprite)
    end


    local function CreateEnemy()
        local sprite = cc.Sprite:create("n2.png")
        local randNum = math.random( table.maxn(RandomPoints)) 
        sprite:setPosition(RandomPoints[randNum])
        self:addChild(sprite)
        local Enemy= {}
        Enemy.Sp = sprite
        Enemy.Blood = 100
        Enemy.Speed = math.random(5)

        -- sprite:runAction(cc.RepeatForever:create( cc.Animate:create(animation) ) )            
        -- sprite:runAction(cc.RepeatForever:create(cc.RotateBy:create(10, 360) ) )
        table.insert(Enemys,1,Enemy)
    end

    local function DestoryEnemy(Enemy)

        local function Delete(sender)
            for k,v in pairs(Enemys) do
                if v==Enemy then
                    table.remove(Enemys,k)
                    self:removeChild(v.Sp,true)
                end 
            end
        end

        local animFrames = {}
        local cache = cc.SpriteFrameCache:getInstance()
        cache:addSpriteFrames("wsparticle_p01.plist")
        for i = 1, 8 do 
            local frame = cache:getSpriteFrame(string.format("a_00%d.png",i))
            -- print(frame)
            animFrames[i] = frame
        end

        local animation = cc.Animation:createWithSpriteFrames(animFrames, 0.3)
        -- Enemy.Sp:runAction(cc.Animate:create(animation))
        local animationOver = cc.CallFunc:create(Delete)
        Enemy.Sp:runAction(cc.Sequence:create(cc.Animate:create(animation),animationOver))
    end

    local function UpdatePlayerBullet()
        for k,v in pairs(PlayerBullets) do
            for k1,v1 in pairs(Enemys) do
               if cc.rectContainsPoint(Enemys[k1].Sp:getBoundingBox(),cc.p(PlayerBullets[k]:getPositionX(),PlayerBullets[k]:getPositionY())) then
                    DestoryEnemy(Enemys[k1])
               end
            end
        end
        for k,v in pairs(PlayerBullets) do
            if v:getPositionY() > display.height then
                table.remove(PlayerBullets,k)
                self:removeChild(v,true)
            end
        end
        for k,v in pairs(PlayerBullets) do
            v:setPositionY(v:getPositionY() + bgMoveSpeed*2)
        end
    end

    local function UpdateEnemy()
        for k,v in pairs(Enemys) do
            v.Sp:setPositionY(v.Sp:getPositionY() - v.Speed)
        end
    end

    local function GenRandomPoint()
        for i = 0,3 do
            table.insert(RandomPoints,1,cc.p(i*179,display.height))
        end
    end

    initBg()
    cc.Director:getInstance():getScheduler():scheduleScriptFunc(UpdateBackGround, 0, false)
    CreatePlayer()
    CreateTouchListener()
    CreatePlayerBullet()
    cc.Director:getInstance():getScheduler():scheduleScriptFunc(CreatePlayerBullet, BulletTimeSince, false)
    cc.Director:getInstance():getScheduler():scheduleScriptFunc(UpdatePlayerBullet, 0, false)
    GenRandomPoint()
    cc.Director:getInstance():getScheduler():scheduleScriptFunc(CreateEnemy, EnemyTimeSince, false)
    cc.Director:getInstance():getScheduler():scheduleScriptFunc(UpdateEnemy, 0, false)
end

return MainScene
```
