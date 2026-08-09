# KiraAI_Default-Chat-Z-默认消息处理插件优化版v1.5.1

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/znq19/KiraAI_Default-Chat-Z-)

修改原版开启上下文收听后默认所有语音、图片、合并转发消息都识别的逻辑，减轻小水管模型的负担。当前版本 z 1.5.1，KiraAI2.24.0+。理论上作为一种插件，KiraAI本体版本高低不影响使用。

此修改版本默认开启只有明确唤醒（如at、关键词和引用回复时的消息中带有的）的语音、图片和转发消息才会被识别。如果关闭设置里的开关，则除了唤醒消息的图片外，其他按概率和数量选取，语音、转发消息全部阅读。

## 新版特性：回复更快、更省 token

- **队列合并（积压处理）**：LLM 处理慢、消息爆发时，同一会话的积压批次自动合并为一次推送，上下文只发送一次、减少 LLM 调用次数——**更省 token**，更不刷屏。默认"不攒批"（当前批次一完成立即合并推送），软合并/超时合并阈值均可配置（`section_queue_merge`）。
- **并行媒体识别**：图片 VLM 与语音 STT 并行预处理，积压批次排队期间媒体即识别完成，推送时零等待——**回复更快**。可配最大并行数、兼容并行识图插件（`section_media_recognition`）。

安装方法：根据个人喜好可采取两种方式——

方式一：复制文件夹内容替换KiraAI-main\core\plugin\builtin_plugins\chat文件夹下内容，即直接替代原版Default Chat插件。

方式二：复制文件夹到KiraAI-main\data\plugins路径下，但必须webui里关闭原版Default Chat插件或更旧版的Message Debounce插件以免冲突。
