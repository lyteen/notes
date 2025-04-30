##  latex-fonts

### **关键词:** 
latex manjaro

### **摘要:** 
在 manjaro 上安装 latex 出现的问题


### **情形**

1.  **使用xelatex编译时中文字体报错** 
```
  ! Package fontspec Error:
  (fontspec)                The font "SimSun" cannot be found; this may be but
  (fontspec)                usually is not a fontspec bug. Either there is a
  (fontspec)                typo in the font name/file, the font is not
  (fontspec)                installed (correctly), or there is a bug in the
  (fontspec)                underlying font loading engine (XeTeX/luaotfload).

  For immediate help type H <return>.
```
---
- fc-list :lang=zh： 查看当前系统支持的中文的语言包

- \setCJKmainfont{lang-package}： 更换对应的语言包

```zsh
  fc-list :lang=zh # fc-list 列出系统安装的字体信息
```
```tex
  \documentclass{article}
  \usepackage{xeCJK}
  \usepackage[a4paper, margin=1in]{geometry}
  \usepackage{fontspec}
  \setCJKmainfont{Source Han Serif CN} % 跟据 fc-list 字体源名称配置
``` 
---
2. **安装字体**

- 查看pacman中包含的字体库
```bash
  sudo pacman -Ss chinese fonts

  extra/adobe-source-han-sans-cn-fonts 2.004-2
    Adobe Source Han Sans Subset OTF - Simplified Chinese OpenType/CFF fonts
extra/adobe-source-han-sans-hk-fonts 2.004-2
    Adobe Source Han Sans Subset OTF - Traditional Chinese (Hong Kong) OpenType/CFF fonts
extra/adobe-source-han-sans-tw-fonts 2.004-2
    Adobe Source Han Sans Subset OTF - Traditional Chinese (Taiwan) OpenType/CFF fonts
extra/adobe-source-han-serif-cn-fonts 2.003-1
    Adobe Source Han Serif Subset OTF - Simplified Chinese OpenType/CFF fonts
extra/adobe-source-han-serif-hk-fonts 2.003-1
    Adobe Source Han Serif Subset OTF - Traditional Chinese (Hong Kong) OpenType/CFF fonts
extra/adobe-source-han-serif-tw-fonts 2.003-1
    Adobe Source Han Serif Subset OTF - Traditional Chinese (Taiwan) OpenType/CFF fonts
extra/opendesktop-fonts 1.4.2-7
    Chinese TrueType Fonts
extra/texlive-games 2025.2-1 (texlive) [installed]
    TeX Live - Games typesetting
extra/ttf-hannom 2005-6
    Chinese and Vietnamese TrueType fonts
```
- 下载
```bash
  sudo pacman -S adobe-source-han-serif-cn-fonts
```
---