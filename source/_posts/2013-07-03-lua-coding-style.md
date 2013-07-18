---
title: Lua 编程规范
published: true
layout: post

---

## 概述

如果你正在编辑代码，花几分钟看一下周边代码，然后决定风格。如果它们在所有的算术操作符两边都使用空格，那么你也应该这样做。如果它们的注释都用标记包围起来，那么你的注释也要这样.
制定风格指南的目的在于让代码有规可循，这样人们就可以专注于你在说什么，而不是你在怎么说。我们在这里给出的是全局的规范，但是局部的规范同样重要。如果你加到一个文件里的代码和原有代码大相径庭，它会让读者不知所措，避免这种情况。

## 命名

* 模块名/文件名/变量名/函数名等标识符采用同一命名规则。借鉴 Lua 内置库函数命名规则，使用小写标识，可适当使用缩写单词，但不应影响辨识。对于明显违反此约定的匈牙利命名法、驼峰命名法等明确禁止，传统 C 语言命名方式也不提倡使用。

* 所有标识均不应以 `_` 及 `__` 打头。

## 行的长度

* 所有语言都应当遵循行的最大长度 80 字符的限定。

## 缩进

**Tip** 用4个空格来缩进代码。

* 必须使用空格缩进，禁止使用 tab，也不要 tab 和空格混用。
* tab 长度为 4。
* vim 的设定为：

{% codeblock lang:bash %}
set sw=4
set st=4
set tabstop=4
{% endcodeblock %}

## 空格

**Tip** 按照标准的排版规范来使用标点两边的空格。

* 括号内侧不能加空格：

    {% codeblock lang:lua %}
    spam(ham[1], {egges = 2}, {})  -- Yes
    spam( ham[1], {egges = 2}, {} ) -- No
    {% endcodeblock %}

* 不能在逗号、分号，冒号等前面加空格，但应该在逗号后面加空格（行尾除外）：

    {% codeblock lang:lua %}
    local x, y = self:get_position() -- Yes
    local x , y = self : get_position()-- No
    {% endcodeblock %}

* 函数、索引的左括号外侧不能加空格：

    {% codeblock lang:lua %}
    handle_event(ev) -- Yes
    handle_event (ev) -- No
    local skillpro = skill_setting[11001] -- Yes
    local skillpro = skill_setting [11001] -- No
    {% endcodeblock %}

* 二元操作符（赋值、比较、算术、布尔）两侧都添加空格：

    {% codeblock lang:lua %}
    x == 1 -- Yes
    x<1 -- No
    {% endcodeblock %}

## 空行

**Tip** 顶级定义之间空两行，方法定义之间空一行。

* 不同函数间定义空一行。
* 不同语义的代码间空一行。

## 括号

**Tip** 左大括号不能出现在行首：

    {% codeblock lang:lua %}
    S = {
        ability = {},
        buff = {},
        effect = {},
        item = {},
        missile = {},
        summon = {},
    }
    {% endcodeblock %}

## 注释

**Tip** 确保对模块，函数和行内注释使用正确的风格。

* 单行注释使用 `--` 开头，例如：

    {% codeblock lang:lua %}
    -- register a timer
    scheduler()

    get_position() -- get the position of object
    {% endcodeblock %}

* 为了能够方便地基于注释生成文档，多行注释使用如下格式：

    {% codeblock lang:lua %}
    --[[
    --! @function aoe_attack
    --! @brief do a AOE attack
    --! @param x coordinate
    --! @param y coordinate
    --! @param shap range of AOE attack
    --! @param attacker_id id of attacker
    --! @param skill_id id of current skill
    --! @return true if success, else return false
    --]]
    function aoe_attack(x, y, shape, attacker_id, skill_id)
    end
    {% endcodeblock %}

## 示例

    {% codeblock lang:lua %}
    --[[
    --! @file init.lua
    --! @brief 初始化脚本模块
    --]]

    S = {
        ability = {},
        buff = {},
        effect = {},
        item = {},
        missile = {},
        summon = {},
    }

    S.ENV = {
        PATH = "scripts", -- 脚本根目录
    }

    --[[
    --! @function copyfile
    --! @brief 复制 `file` 表，以插入到文件列表中
    --! @param file 待复制的 `file` 表
    --! @return 复制的 `file` 表
    --]]
    local function copyfile(file)
        return {
            dir = file.dir,
            path = file.path,
            name = file.name,
            ext = file.ext,
            scope = file.scope,
            mod = file.mod
        }
    end

    --[[
    --! @function listfiles
    --! @brief 获取指定目录下的脚本文件列表（含子目录）
    --! @return 脚本文件列表
    --]]
    local function listfiles()
        -- 将指定目录下的文件列表输出到 `tmp` 文件以备处理
        local path = S.ENV.PATH
        local file = {}
        local files = {}

        os.execute('dir "' .. path .. '" /s > tmp')
        io.input("tmp")

        -- 过滤所有文件
        for line in io.lines() do
            -- 匹配目录
            local dir = string.match(line, "^%s*.+(" .. path .. ".*)%s+")

            if dir then
                file.dir = dir
                file.scope = string.gsub(dir, "\\", ".")
            end

            -- 匹配文件
            file.name, file.ext = string.match(line,
            "^%d%d%d%d.%d%d.%d%d%s-%d%d:%d%d%s-[%d%,]+%s+(.+)%.(.+)%s-$")
            if file.name and file.ext == 'lua' then
                file.path = string.gsub("$dir\\$name.$ext", "%$(%w+)", file)
                file.mod = string.gsub("$scope.$name", "%$(%w+)", file)
                table.insert(files, copyfile(file))
            end
        end
        table.remove(files, 1)
        return files
    end

    --[[
    --! @function listfunctions
    --! @brief 显示指定表中的函数列表
    --]]
    local listfunctions
    function listfunctions(name, tab, lev)
        if name == "_M" then
            return
        end

        local ind = ""
        for i = 1, lev do
            ind = ind .. "\t"
        end
        print(ind .. name)

        -- 显示函数列表
        local i = 0
        for n, t in pairs(tab) do
            if type(t) == "function" then
                i = i + 1
                print(ind .. i, n)
            end
        end

        -- 递归处理子表
        for n, t in pairs(tab) do
            if type(t) == "table" then
                listfunctions(n, t, lev + 1)
            end
        end
    end

    --[[
    --! @function stat
    --! @brief 输出统计信息
    --]]
    local function stat()
        for n, t in pairs(S) do
            if type(t) == "table" then
                listfunctions(n, t, 0)
            end
        end
    end

    --[[
    --! @function init
    --! @brief 初始化入口函数
    --]]
    local function init()
        local files = listfiles()
        for _, file in pairs(files) do
            require(file.mod)
        end
    end

    init()
    stat()

    S.ability.cast(10000, 100)
    os.execute("pause")
    {% endcodeblock %}

