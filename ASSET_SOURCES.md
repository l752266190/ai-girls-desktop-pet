> 本文保留素材制作与来源记录。文中素材路径指软件项目内部路径，公开仓库不包含源码及原始素材。

# AI 娘桌宠：素材与官网来源

本项目为非官方本地同人桌宠。素材使用内置 imagegen 生成，角色参考与公开同人图鉴来自“蓝色大肥鱼 AI 娘二创开放档案”：https://xn--pssy23gqgbz2d718b.com/characters.html 。不同社区作品存在不同原设，收录日期不等于创作日期；整理站不能代替原作者授权。

## 生成素材

- `astra-sprites.png`：Astra 六姿态透明精灵，提示词 `generation-prompt.txt`。
- `characters/core.png`：Sol、Claude、DeepSeek、Gemini、Grok、Qwen，提示词 `characters/prompt-1.txt`。
- `characters/china.png`：Kimi、GLM、豆包、StepFun、MiMo、混元，提示词 `characters/prompt-2.txt`。
- `characters/variants.png`：Luna、DeepSeek Flash、Seed、Llama、Muse、ClauGPT，提示词 `characters/prompt-3.txt`。
- `characters/extended.png`：ClauSeek、元宝、MiniMax、文心、星火、Mistral，提示词 `characters/prompt-4.txt`。
- `characters/global.png`：Perplexity、Copilot、Cohere、Yi、百川、SenseNova，提示词 `characters/prompt-5.txt`。

生成时间：2026-10-09。均为真实透明 alpha PNG。图鉴中明确区分社区参考改绘与本程序原创拟人版本。未声称获得原作者商业授权，未将同人版本标为品牌官方角色。

## 社区参考页

- sol: https://xn--pssy23gqgbz2d718b.com/works/gpt202609290001.html
- gpt: https://xn--pssy23gqgbz2d718b.com/works/gpt202610060003.html
- luna: https://xn--pssy23gqgbz2d718b.com/works/gpt202610070002.html
- claude: https://xn--pssy23gqgbz2d718b.com/works/claude202610030001.html
- deepseek: https://xn--pssy23gqgbz2d718b.com/works/deepseek202610080018.html
- qwen: https://xn--pssy23gqgbz2d718b.com/works/qwen202609200001.html
- kimi: https://xn--pssy23gqgbz2d718b.com/works/kimi202609270001.html
- grok: https://xn--pssy23gqgbz2d718b.com/works/grok202609270001.html
- gemini: https://xn--pssy23gqgbz2d718b.com/works/gemini202610070002.html
- glm: https://xn--pssy23gqgbz2d718b.com/works/glm202609270001.html
- doubao: https://xn--pssy23gqgbz2d718b.com/works/doubao202610060002.html
- stepfun: https://xn--pssy23gqgbz2d718b.com/works/stepfun202609210001.html
- mimo: https://xn--pssy23gqgbz2d718b.com/works/mimo202609300001.html
- claugpt: https://xn--pssy23gqgbz2d718b.com/works/other202610050003.html
- clauseek: https://xn--pssy23gqgbz2d718b.com/works/other202610050001.html
- muse: https://xn--pssy23gqgbz2d718b.com/works/other202609300001.html
- llama: https://xn--pssy23gqgbz2d718b.com/works/other202610060001.html
- hunyuan: https://xn--pssy23gqgbz2d718b.com/works/other202610060002.html
- group: https://xn--pssy23gqgbz2d718b.com/works/other202609270001.html

## 产品网站

官网 URL 与角色绑定在 `catalog.py` 中。2026-10-09 逐一检查了 27 个不同入口，其中 23 个返回 HTTP 200，ChatGPT、Claude、Meta AI、Perplexity 的自动化请求返回 403（登录/防护页），因此没有将这些状态称为登录可用性验证。浏览器打开仍由用户的网络和账户条件决定。联动角色使用双方产品入口，没有虚构独立官网。

DeepSeek 官方主页直接链接其网页助手：https://www.deepseek.com/ 。Claude 页面：https://claude.ai/ 。MiMo 官方主页：https://mimo.xiaomi.com/ 。完整本机检查记录在源码 `research/website-checks.json`。

## 运行环境组件

Windows 内嵌 CPython 3.11.9，来自 https://www.python.org/ftp/python/3.11.9/python-3.11.9-embed-amd64.zip ，保留 `runtime/LICENSE.txt`。PySide6 Essentials / shiboken6 6.8.3 来自 PyPI，相关版权、许可证和元数据保留在 `runtime/Lib/site-packages` 的包目录及 dist-info 目录。

Linux `vendor` 中 libxcb-cursor0 来自 Ubuntu Jammy 软件源，其版权文本保留在 `vendor/usr/share/doc/libxcb-cursor0/copyright`。

## 2.1.0 动作重制

使用内置 imagegen（非 CLI/API 回退）为后续 30 位角色各生成一张九宫格透明动作表，保存在 `assets/motions/<角色 id>.png`。每张的完整实际提示词保存在同目录同名 `.txt`；输入为原版 `characters/*.png` 对应角色的身份参考。运行时沿透明间隙切格并放入统一尺寸画布，不修改生成原图。

九宫格顺序：站立、闭眼、摸头、捏住脸颊、拉伸脸颊、松手揉脸、侧躺睡眠、迈步 1、迈步 2。捏脸不是站姿缩放或叠加腮红。Astra 原版素材继续保留。

## 2.1.1 Astra 动作升级

使用内置 imagegen，根据原 `astra-sprites.png` 保留 Astra 的蓝眼、白龙角、花裙、龙翼和深蓝书本，生成 `motions/astra.png` 九动作表。完整实际提示词见 `motions/astra.txt`。程序已改为加载新动作，旧六姿态图保留作为参考存档，不再作为运行时回退。全体 31 位共 279 幅独立动作图。

Astra 最终图经过内置 imagegen 排版修正，扩大透明间隙以避免邻帧边缘串入；编辑提示词见 `motions/astra-spacing.txt`。
