对，**问题基本确定了**。

你现在是：

```text
Claude Code v2.1.72
```

而你遇到的：

```text
Unrecognized key: "displayName"
```

就是典型的 **Claude Code 版本与当前 `claude-plugins-official` Marketplace schema 不兼容**。

### 你现在不要去修改 `marketplace.json`

直接升级 Claude Code。

如果你是 npm 安装的，在 Windows PowerShell / CMD 执行：

```bash
npm install -g @anthropic-ai/claude-code@latest
```

然后关闭当前 Claude Code / VS Code 终端，重新打开，执行：

```bash
claude --version
```

确认版本已经不是：

```text
2.1.72
```

---

### 然后清理旧 Marketplace

PowerShell：

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.claude\plugins\marketplaces\anthropics-claude-plugins-official"
```

再进入 Claude Code：

```text
/plugin marketplace list
```

如果官方 Marketplace 还是异常，再重新添加：

```text
/plugin marketplace add anthropics/claude-plugins-official
```

然后：

```text
/plugin marketplace list
```

应该恢复正常。

---

### 最后重新装这三个

```text
/plugin install superpowers@claude-plugins-official
/plugin install claude-code-setup@claude-plugins-official
/plugin install 42crunch-api-security-testing@claude-plugins-official
```

---

## 但是我建议你先做一个检查

你现在 **2.1.72 已经比较老**，而且 Windows 上特别容易出现「升级了但实际上调用的还是旧 Claude」的情况。

所以你下一步执行：

```bash
where claude
```

把结果也发给我。

我可以根据：

```text
Claude Code v2.1.72
+
where claude
```

直接告诉你**应该怎么升级**，避免 Windows 上多个 Claude 安装路径导致升级不生效。

另外，你这批插件里我会优先帮你恢复：

```text
🥇 superpowers
🥈 claude-code-setup
🥉 42crunch
```

然后再帮你把 **Superpowers + agent-skills + ai-platform-devops** 组合成一套适合你 **Java/Go + Next.js + AI 知识库项目** 的 Claude Code 工作流。
