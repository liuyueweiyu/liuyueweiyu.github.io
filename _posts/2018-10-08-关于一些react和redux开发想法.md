---
layout:     post                    # 使用的布局（不需要改）
title:      关于一些react和redux开发想法               # 标题 
# subtitle: L
date:       2018-10-08              # 时间
author:     顾小五                      # 作者
catalog: true                       # 是否归档
tags:                               #标签
    - 前端
    - react
typora-copy-images-to: ..\img
typora-root-url: ..\img
---

1. 按功能组织代码

   之前使用MVC框架开发的时候使用这样的代码组织方式（按角色组织）

   ```
   controllers/
   	todoController.js
   	fillterController.js
   models/
   	todoModels.js
   	filterModels.js
   views/
   	todo.js
   	todolist.js
   	filter.js
   ```

   redux应用适于"按功能组织"，也就是把完成同一应用功能的代码放在同一文件夹中

   ```
   todoList/
   	actions.js
   	actionType.js
   	index.js
   	reducer.js
   	views/
   		component.js
   		containter.js
   filter/
   	actions.js
   	actionType.js
   	index.js
   	reducer.js
   	views/
   		component.js
   		containter.js
   ```

   注意，index.js这个文件把**所有的角色导入，然后统一导出**

2. 模块接口

   index.js作为所有模块对外导出接口

3. 状态树的设计

   - 一个模块控制一个状态节点
   - 避免冗余数据
   - 树形结构扁平