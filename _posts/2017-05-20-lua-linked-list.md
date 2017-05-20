---
layout: post
title: Lua implement simple linked list.
---

{{ page.title }}
================

<p class="meta">20 May 2017 - Lua implement simple linked list.</p>


使用lua实现基本的增加删除功能。

```
local list = {}

--local function initList()
--  list.data = 0
--  list.next = nil
--end

local function addToTrail(d)
  local ll = {}
  ll.data = d
  ll.next = nil
  local l = {}
  l = list
  if l.next == nil then
      l.next = ll
  else
      while l.next do
        l = l.next
      end
      l.next = ll
  end
end

local function addToHead(d)
  local ll = {}
  ll.data = d
  ll.next = list.next
  list.next = ll
end

local function getData(i)
  if not list then
--    io.write("list is null","\n")
    return
  end
  
  if i<1 then
--    io.write("Wrong index.","\n")
    return
  end
  
  local l = list
  local k = 0
  while l do
    l = l.next
    k = k + 1
    if k == i  then
      return l
    end
  end
end

local function getLen()
  if not list then
--    io.write("list is null","\n")
    return
  end
  local l = list.next
  local k = 0
  while l do
    l = l.next
    k = k + 1
  end
  return k
end

local function insert(i,d)
  if i < 1 then
--    print("Wrong index.")
    return
  end
  
  local lnew = {}
  lnew.data = d
  lnew.next = nil
  
  local lcurrent = getData(i)
  local lnext = getData(i + 1)
  
  if lcurrent == nil then
--    print("Wrong index.")
    return
  end
  
  if lnext == nil then
    lcurrent.next = lnew
  else
    lcurrent.next = lnew
    lnew.next = lnext
  end

end

local function Delete(i)
  if i < 1 then
--    print("Wrong index.")
    return
  end
    
  local lcurrent = getData(i)
  local lprev = getData(i - 1)
  local lnext = getData(i + 1)
  
  if lprev == nil then
    --Head
    local ll = lnext
    lcurrent = nil
    list.next = ll  
  elseif lnext == nil then
    --Trail
    lcurrent = nil
    lprev.next = nil
  else
    lprev.next = lnext
    lcurrent = nil
  end
end

local function Clear()
  if not list then
--    io.write("list is null","\n")
    return
  end
  
  while true do
    firstNode = list.next
    if not firstNode then
      break
    end
    t = firstNode.next
    list.next = nil
    list.data = 0
    list.next = t
  end
end

local function Destory()
  Clear()
  list = nil
end

local function Display()
  local l = list.next
  while l do
    io.write(l.data,"\n")
    l = l.next
  end
end
```