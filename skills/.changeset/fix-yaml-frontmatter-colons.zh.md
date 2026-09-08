---
"mattpocock-skills": patch
---

给 `to-spec`、`code-review`、`setup-matt-pocock-skills`、`writing-fragments`、`writing-shape` 与 `wait-what` 的 `description` frontmatter 加上引号。一处未被引号包裹的"冒号+空格"是 #905 中替换破折号时遗留的，会让每段都成为非法 YAML，于是 `skills.sh` 在发现阶段跳过这六份，它们也无法通过 `npx skills` 被列出或安装。
