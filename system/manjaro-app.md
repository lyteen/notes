##  manjaro-app

### **关键词:** 
manjaro pacman yay

### **摘要:** 
manjaro中的软件安装问题

### **情形**

1. **截屏软件** archlinux中的截屏需求

- deepin-screen-recorder： 截屏软件

```bash
  yay deepin-screem-recorder
```

<details>
    <summary>deepin</summary>
        <ul>
	      <li>Deepin Linux，又称 deepin 或前身为 Linux Deepin 和 Hiweed Linux，是由武汉深度科技有限公司在中国开发的一个免费开源的 Linux 发行版。它的初始版本于 2004 年 2 月 28 日发布，基于 Debian 的 stable 分支。</li>
        <li>deepin-screen-recorder由深度作系统团队开发： 深度录屏软件是由武汉深度科技有限公司开发的原创应用，该公司与开发深度作系统 Linux 发行版的公司相同</li>
        </ul>
</details>

- i3配置: 快捷键

(未知 PriSc 在 i3.conf 的名称)

```config
  bindsym $mod+p exec deepin-screen-recorder
```