---
Create: <% tp.date.now("YYYY年 MMMM Do, dddd") %>
---


<< [[{{date-1d:YYYY-MM-DD-dddd}}|上一天的日记]] | [[{{date+1d:YYYY-MM-DD-dddd}}|后一天的日记]] >>

## 🔥🔥我的重要目标
```query
task-todo:"#task/vvip" path:DN
```

## 今天要完成工作
- [ ] xxxx #task/重要又紧急
- [ ] xxxx #task/紧急不重要
- [ ] xxxx #task/重要不紧急

## 本周未完成的任务
```query
task-todo:"#task" path:{{date:WW}}_ -file:{{date:YYYY-MM-DD}}
```

