# Snort-With-ELK

Snort 網路入侵預防軟體與網路入侵檢測軟體。透過網路攔截下來的 Packets 加以分析，並將過濾的事件送入 ELK 。

參考 [ELK on Docker Compsoe](https://github.com/FaelDi/docker-elk) 架設 ELK ，並將 Snort 整合進去。

## Snort3

- `oliwave/snort3` image 已經有安裝 `OpenAppID` 的相關套件，如有其他套件可以到 `snort3/Dockerfile` 自行添加

- 為了讓 snort3 container 可以監聽到 host 的 network traffics ， snort3 container 以 `network_mode: host` 方式運行



