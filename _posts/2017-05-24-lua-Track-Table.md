---
layout: post
title: Lua Track table value.
---

{{ page.title }}
================

<p class="meta">24 May 2017 - Lua Track table value.</p>

```
local index = {}

local mt =
{
  __index = 
  function(t,k)
    print("*Access to element " ..tostring(k))
    return t[index][k]
  end,
  
  __newindex = 
  function(t,k,v)
    print("*update of element " ..tostring(k) .. " to " ..tostring(v))
    t[index][k] = v
  end
}

function track(t)
  local proxy = {}
  proxy[index] = t
  setmetatable(proxy,mt)
  return proxy
end


t = {}

t = track(t)

t[1] = 123
print(t[1])

-- make a table read-only.
local function readOnly(t)
  local proxy = {}
  local mt = {
    __index = t,
    __newindex = 
    function(t,k,v)
      error("attempt to update a read-only table.",2)
    end
  }
  setmetatable(proxy,mt)
  return proxy
end
```
