# KiraAI_Default-Chat-Z-默认消息处理插件优化版v1.5.9

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/znq19/KiraAI_Default-Chat-Z-)

修改原版开启上下文收听后默认所有语音、图片、合并转发消息都识别的逻辑，减轻小水管模型的负担。当前版本 z 1.5.9，KiraAI2.29.6+ 可用（原生多模态兼容需 2.31.0+）。

此修改版本默认开启只有明确唤醒（如at、关键词和引用回复时的消息中带有的）的语音、图片和转发消息才会被识别。如果关闭设置里的开关，则除了唤醒消息的图片外，其他按概率和数量选取，语音、转发消息全部阅读。

## 新版特性：回复更快、更省 token

- **队列合并（积压处理）**：LLM 处理慢、消息爆发时，同一会话的积压批次自动合并为一次推送，上下文只发送一次、减少 LLM 调用次数——**更省 token**，更不刷屏。默认"不攒批"（当前批次一完成立即合并推送），软合并/超时合并阈值均可配置（`section_queue_merge`）。内置「批次卡死超时」兜底：当前批次超时无响应（LLM 挂起/异常崩溃）时强制推送积压批次，避免会话队列死锁（默认自动跟随 LLM 模型超时）。
- **并行媒体识别**：图片 VLM 与语音 STT 并行预处理，积压批次排队期间媒体即识别完成，推送时零等待——**回复更快**。并发限制分三级（批次级/会话级/全局级）可配，兼容并行识图插件（`section_media_recognition`）；同一消息内的每个媒体最多识别一次，模型限流（429）时不会反复重试。
- **媒体识别填充 file_path**（v1.5.9）：识别后的图片标识符带本地文件路径（`[Image #id: 描述, file_path: data/temp/xxx.jpg]`），对齐原版 `message_format_to_text` 行为，LLM 可直接用路径做图生图/上传等操作。
- **热重载不丢消息**：插件终止/重载时积压批次会以全新批次安全重发，不再出现消息积压后永久无法处理的问题。
- **原生多模态兼容**（v1.5.6）：KiraAI v2.31.0 新增 native 图片模式（`bot_config.capabilities.image_recognition.mode = "native"`）时，图片保留在消息链中由框架直传模型（官方压缩 + 持久化引用），本插件只做音频 STT、不再走 VLM 描述；默认 `vlm_description` 模式行为完全不变。
- **最后一步带工具也即时收尾**（v1.5.5）：agent 在最大步数仍返回工具调用时（最后一步带工具，工具执行完即结束、无最终文本收尾），队列合并不再等「批次卡死超时」（默认 180s）才推送积压批次，而是立刻释放——消除工具循环收尾时最长 3 分钟的“哑巴”窗口。
- **媒体识别填充崩溃修复**（v1.5.8）：`_fill_text` / `_fill_chain` 的 `re.sub` 改为 `str.replace`，修复 VLM/STT 描述含反斜杠序列（如 Windows 路径 `\U`）时抛 `re.PatternError: bad escape` 导致 stage2 整批媒体识别崩溃的问题。

安装方法：根据个人喜好可采取两种方式——

方式一：复制文件夹内容替换KiraAI-main\core\plugin\builtin_plugins\chat文件夹下内容，即直接替代原版Default Chat插件。

方式二：复制文件夹到KiraAI-main\data\plugins路径下，但必须webui里关闭原版Default Chat插件或更旧版的Message Debounce插件以免冲突。
