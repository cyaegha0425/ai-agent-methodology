# force-push处理

## force push后必须提醒的操作

force push后必须提醒用户执行：
```
git fetch origin && git reset --hard origin/分支名
```

## pull步骤说明

不要给pull步骤，用户fetch+reset即可。
