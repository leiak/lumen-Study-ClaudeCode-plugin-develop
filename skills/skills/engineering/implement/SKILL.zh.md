---
name: implement
description: "基于 spec 或一组工单实现一段工作。"
disable-model-invocation: true
---

把用户在 spec 或工单里描述的工作实现出来。

尽可能在预先约定的接缝处使用 /tdd。

定期跑 typechecking，定期单跑测试文件，最后跑一次完整测试套件。

完成后，使用 /code-review 复核本次工作。

把工作提交到当前分支。
