---
layout: post
title: Lua implement simple linked list.
---

{{ page.title }}
================

<p class="meta">20 May 2017 - Lua implement simple linked list.</p>


使用lua实现基本的增加删除功能。

```
list = {}

local mt = {}

function list.new()
  local set = {}
  mt.__add = list.PPlus
  setmetatable(set,mt)
  return set
end

function list.PPlus(lp,ln)
  list.GetData(lp,list.GetLen(lp)).next = list.GetData(ln,1)
  return lp
end

function list.Add(self,d)
  local ll = {}
  ll.data = d
  ll.next = nil
  local l = {}
  l = self
  if l.next == nil then
      l.next = ll
  else
      while l.next do
        l = l.next
      end
      l.next = ll
  end
end

function list.AddToHead(self,d)
  local ll = {}
  ll.data = d
  ll.next = self.next
  self.next = ll
end

function list.GetData(self,i)
  if not self then
--    io.write("list is null","\n")
    return
  end
  
  if i<1 then
--    io.write("Wrong index.","\n")
    return
  end
  
  local l = self
  local k = 0
  while l do
    l = l.next
    k = k + 1
    if k == i  then
      return l
    end
  end
end

function list.GetLen(self)
  if not self then
--    io.write("list is null","\n")
    return
  end
  local l = self.next
  local k = 0
  while l do
    l = l.next
    k = k + 1
  end
  return k
end

function list.Insert(self,i,d)
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

function list.Delete(self,i)
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
    self.next = ll  
  elseif lnext == nil then
    --Trail
    lcurrent = nil
    lprev.next = nil
  else
    lprev.next = lnext
    lcurrent = nil
  end
end

function list.Clear(self)
  if not self then
--    io.write("list is null","\n")
    return
  end
  
  while true do
    firstNode = self.next
    if not firstNode then
      break
    end
    t = firstNode.next
    self.next = nil
    self.data = 0
    self.next = t
  end
end

function list.Destory(self)
  Clear()
  self = nil
end

function list.Display(s)
  local l = s.next
  while l do
    io.write(l.data,"\n")
    l = l.next
  end
end



local l1 =  list.new()
list.Add(l1,1)
list.Display(l1)

local l2 = list.new()
list.Add(l2,2)
list.Add(l2,"123")
list.Add(l2,"456")
list.Display(l2)

local l3 = l1 + l2
list.Display(l3)
```
