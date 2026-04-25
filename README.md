# remote — 家中 Mac 远程访问固定入口

GitHub Pages 静态门户：`https://wkw1542673895.github.io/remote/`

## 作用

家里 Mac 通过 SakuraFrp 暴露到公网，但隧道 URL 会变（隧道重连/换节点），公司浏览器没法每次都来拿最新 URL。这个仓库就是个**永远稳定的入口**：

```
公司浏览器
    │
    ▼
https://wkw1542673895.github.io/remote/      ← 固定，永不变
    │  (index.html 跑 JS 读 url.json)
    ▼
https://frp-way.com:55918/...                 ← SakuraFrp 隧道当前 URL
    │
    ▼
家里 Mac 上的 gateway.py(:9000)               ← 看板/终端/桌面
```

## 文件

| 文件 | 说明 |
|---|---|
| `index.html` | 门户页，展示 Web（看板/终端/桌面）+ Mobile（看板/终端）两组链接 |
| `url.json` | 隧道 URL 数据；家里 Mac 上的 watchdog.py 检测到隧道地址变化时自动 commit + push 到本仓 |

## 自动更新机制

家里 Mac 项目仓 [`wkw1542673895/mac-remote-station`](https://github.com/wkw1542673895/mac-remote-station) 内的 `watchdog.py` 每 30 秒巡检 frpc，发现 SakuraFrp 隧道 URL 变化时调用 `update_pages.py` 直接更新本仓库的 `url.json`。所以：

- 公司浏览器只需要**永远**记 `https://wkw1542673895.github.io/remote/`
- Mac 端隧道地址怎么变都不影响访问
- index.html 每次加载时 `fetch('url.json?' + timestamp)` 强制绕过浏览器缓存
