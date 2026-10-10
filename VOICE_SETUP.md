# 豆包语音提醒接入说明 · 2.3.0

核对日期：2026-10-10。目标是吃饭和闹钟的文字转语音（TTS），不采集麦克风，也不调用语音识别或实时对话接口。

## 用户要准备什么

| 项目 | 从哪里获取 / 怎么填 |
| --- | --- |
| 火山引擎账号及语音服务额度 | 在豆包语音控制台开通语音合成服务，确认计费和音色权限 |
| 新版语音 API Key | 控制台 → API Key 管理；填入桌宠“语音 API Key” |
| 旧版凭证（与新版二选一） | App ID 和 Access Token，分别填写；不是通用 IAM AccessKey / SecretKey |
| Resource ID | 普通官方音色选 `seed-tts-2.0`；自己已准备的复刻音色选 `seed-icl-2.0` |
| Speaker / 音色 ID | 从该账户音色库复制；须与资源版本和权限匹配。普通音色例：`zh_female_vv_uranus_bigtts` |
| 网络和输出设备 | 能访问官方语音接口；扬声器或耳机可用，系统音量未静音 |

新版只用 API Key 鉴权，不要求另外填 App ID / Access Token。旧版通过 `X-Api-App-Id` 和 `X-Api-Access-Key` 发送对应凭证。两种方式不会混用。**火山方舟聊天 API Key、豆包网页登录密码和豆包语音凭证不是同一项设置**，程序不会尝试挪用聊天密钥。

无需购买或调用聊天模型来生成普通提醒。台词由本地人设模板生成，仅把提醒台词、音色和说话语气发给豆包语音。每次点击试听、每次启用后的提醒合成都可能计费；没有自动重试或重复循环合成。

## 在桌宠里配置

1. 右键桌宠 → **语音提醒与角色人设**，或从闹钟窗口点击“设置语音播报”。
2. 在“语音服务”选择鉴权方式，填入自己的凭证、调整播放音量。语音默认关闭。
3. 在“角色人设与音色”选择角色、填写有权限的音色 ID，按需修改语气、语速和两种提醒台词。台词必须保留 `{event}`，以保证实际闹钟内容被读出来；`{name}` 是角色名。
4. 点击“保存并试听（使用语音额度）”。即使自动提醒开关关闭，也可以主动试听。先用官方标准音色确认可用，再换成其他音色。
5. 勾选“启用吃饭 / 闹钟语音播报”并保存；在闹钟窗口启用三餐或添加闹钟。提醒发生时使用**当时陪伴的角色**的人设和音色。稍后提醒再次到点会重新合成。

角色配置分别保存。所有 31 个内置桌宠都有原创语气、吃饭台词和闹钟台词，自定义角色有通用模板。初始音色使用一个可编辑的官方示例 ID，**不代表 31 套独占声线或官方角色配音**。实际声音由所选音色、语气、语速共同决定，需试听确认。

例如同一个午饭提醒：

- Astra：白龙把星光收好啦，该吃午饭啦。先好好吃饭，我在这里等你。
- Claude：给手上的事夹一枚书签吧，该吃午饭啦。我们休息一会儿。
- DeepSeek：大肥鱼浮上来报到：该吃午饭啦。人类也要按时补充能量呀。
- 豆包：豆包来喊你啦，该吃午饭啦！放下手里的事，吃点热乎的。

标准 TTS 2.0 使用 `req_params.additions` 内 JSON 字符串的 `context_texts` 表达说话语气。复刻标准版选择 `seed-tts-2.0-standard`，不发送该语气指令，台词和语速仍独立。程序只使用用户已在官方平台准备好的复刻音色，不上传录音或执行声音克隆。

## 播报行为与凭证保存

- 提醒弹窗立即出现；语音采用异步合成，接收完整 PCM 后播放，不会卡住桌宠。多个提醒依次播报，最多排队 10 条，等待超过 2 分钟则保留文字提醒。
- 点击“知道啦”、稍后提醒、关闭弹窗会取消对应的请求或播放；关闭设置窗口停止试听。已经到达服务商的请求可能仍被处理或计费。
- 每次提醒只播报一次。语音失败、未配置或禁用时，文字弹窗仍正常；提示音是否响铃由闹钟页面的“提醒时响铃”控制。语音音量有单独设置。
- 程序运行时才能提醒；不会主动唤醒休眠电脑或关机电脑。原有 15 分钟补提醒与去重逻辑保持生效。
- Windows 可选择通过当前用户 DPAPI 加密保存凭证。Linux 凭证仅留在本次运行内；重启后须重新填写。语音凭证与聊天凭证分开保存。
- 不写入聊天日志或永久音频缓存。播放时生成临时 WAV，完成/取消后删除；进程被强制终止时，系统临时目录可能残留未清理音频。

## 对接参数

固定接口：`POST https://openspeech.bytedance.com/api/v3/tts/unidirectional`。

新版请求头为 `X-Api-Key`、`X-Api-Resource-Id` 和自动生成的 `X-Api-Request-Id`。旧版替换鉴权头为 `X-Api-App-Id` + `X-Api-Access-Key`。不会跟随重定向转发凭证。

普通音色请求体示意（凭证只进请求头）：

```json
{
  "req_params": {
    "text": "午饭时间到了，休息一下吧。",
    "speaker": "zh_female_vv_uranus_bigtts",
    "audio_params": {"format": "pcm", "sample_rate": 24000, "speech_rate": -8},
    "additions": "{\"context_texts\":[\"轻柔明亮，像可靠的小伙伴\"]}"
  }
}
```

解析 HTTP 分块返回的 JSON 和 base64 音频，在内存中拼接 24 kHz 单声道 16-bit PCM 后封装 WAV。Windows 通过内置 `winsound` 播放，Linux 使用 `paplay` 或 `aplay`，无需新增 Qt Multimedia Windows 依赖。超时 45 秒、合成文本最多 600 字、PCM 最多 6 MiB；不完整响应不播放。

## 验证边界

已用本地模拟服务覆盖新旧鉴权头、UTF-8/JSON 分块边界、音频拼接、拒绝/重定向/超时/取消、角色隔离及提醒触发。Linux 实际音频后端已用音量为零的测试音频验证完成和临时文件清理；这不代表已听验角色音色。真实云端凭证、账户额度、音色表现、Windows 播放和 DPAPI 仍需实机验证。

## 官方来源

- [单向流式语音合成 HTTP](https://docs.volcengine.com/docs/DoubaoVoice/unidirectional-streaming-text-to-speech-http?lang=zh)：新版 API Key、资源、音色、PCM、语速、附加语气参数。
- [旧版控制台鉴权](https://docs.volcengine.com/docs/DoubaoVoice/old-version-console-authentication-reference-example?lang=zh)：App ID 与 Access Token。
- [双向流式接口参数说明](https://docs.volcengine.com/docs/DoubaoVoice/WebSocketBidirectionalStreaming-V3?lang=zh)：`req_params.additions.context_texts` 语气指令位置和版本限制（本程序使用 HTTP 接口，不使用 WebSocket）。
- [豆包语音控制台](https://console.volcengine.com/speech/app)：开通服务、管理凭证和音色。
