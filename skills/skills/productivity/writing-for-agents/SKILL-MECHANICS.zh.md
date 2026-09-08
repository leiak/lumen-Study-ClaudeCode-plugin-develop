# Skill mechanics

[`writing-for-agents`](SKILL.md) 中专门讨论 skill 的那一支:当文档变成 skill 时,会发生哪些变化(frontmatter、调用方式的选择、以及 router skills)。关于写作的其他一切,都在 `SKILL.md` 这一通用参考中。

## 调用(Invocation)

两种选择,在两种 load 之间做权衡:

- **Model-invoked(模型可调用)** skill 保留 `description`,这样 agent 可以自主触发它,其他 skill 也能调用它。你仍然可以直接敲它的名字:模型调用永远**包含**用户触达;description 只会增加 agent 的可发现性,从不剥夺人类的手动触达。description 是 skill 的顶层 context pointer,被强制始终加载:用永久的 context load 换取可发现性。一个内容全是参考的 model-invoked skill,也是共享参考的一个归宿:其他 skill 可以调用它,因此多份 skill 共用的参考可以集中放在一处。机制:省略 `disable-model-invocation`,并写一段面向模型的 description,承载各个触发分支(`SKILL.md` 中关于 pointer 撰写的规则完全适用)。
- **User-invoked(用户调用)** skill 把 description 从 agent 的触达范围里剥掉:只有人类敲它的名字才能触发,其他 skill 也无法调用它。零 context load,但代价是 cognitive load:你就是这个索引,必须记得它存在。机制:设置 `disable-model-invocation: true`;`description` 变为面向用户:一句话摘要,去掉触发词列表。

只有当 agent 必须自己触达该 skill、或其他 skill 必须触达它时,才选择 model-invocation。如果它只会由人手触发,就做成 user-invoked,不付 context load。

两份 user-invoked skill 都需要的共享参考,放在哪个里面都不合适:既然没有 description,谁也调不动谁。把它推到 skill 系统之外的一个普通文件:任何 skill 都能指向的外部参考。

## 按调用方式拆分

按调用方式拆分(按顺序拆分见 `SKILL.md`):当你有一段独立的、应当独立触发的首词(你在 prompts 里确实会用的触发词),或其他 skill 必须触达它时,就把一个 model-invoked skill 拆出来。你要为新增的常驻 description 付出 context load,所以这种独立的触达必须值得。

## Router skills

当 user-invoked skill 增长到你记不住的程度,可以用 **router skill** 来治这种累积的 cognitive load:一个 user-invoked skill,它列出其他 skill,以及在何时该用哪一份,让人类只需记一个 skill。它只能暗示,不能触发:user-invoked skill 没有 description,因此除人类外没有任何东西能触达它们。
