# 如何贡献

## Pull Requests

1. [Fork our repository](https://docs.github.com/en/get-started/quickstart/fork-a-repo) 到你自己的账户。

2. 为你的工作创建一个新分支。请尽量使用明确的分支名。如果你的工作与某个现有 issue 相关，请使用 `DOCS-<ISSUE>` 作为分支名格式。

3. 完成工作后，将分支推送到你的 fork，并创建一个新的 Pull Request。

4. 在持续评审过程中，请按常规方式提交 commit，commit 之间*不要*执行 rebase。这样可以保持 commit 树清晰。

5. 如果你的 Pull Request 被批准，我们会要求你将工作 squash 为一个 commit（`git rebase -i HEAD~n`，其中 `n` 是该分支上的 commit 数量）。

6. 当你已将获批的 Pull Request 压缩为一个 squash commit 后，执行 `git pull --rebase` 并解决所有剩余的合并冲突。

7. 将更改再次推送到你的分支。完成 squash + rebase 后，你可能需要执行 `git push -f`

8. 我们会在条件允许时进行合并。

感谢你的贡献，也请你在评审期间保持耐心。

## Issues

创建新 issue 时，请考虑以下事项：

- 先搜索确认你的 issue 是否已存在。对于重复或高度相关的 issue，我们会按重复 issue 处理并关闭。

- 提供相关页面链接。如果你在教程过程中遇到问题，请补充你的部署环境以及你看到的具体错误信息。

- 保持简洁。请求越大或越复杂，短期内被处理的可能性越低。如果我们无法在合理时间内处理某个功能请求，我们保留关闭该请求的权利。

## 不是支持渠道

如果你需要 MinIO 部署支持，请查看我们的
[community Slack Channel](https://slack.min.io/)。

我们会尽力协助处理与文档相关的问题。对于有严格时效要求的用户，可以加入 [MinIO Subscription Network (SUBNET)](https://min.io/pricing)。


# Contributors License Agreement

本作品采用 [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/legalcode) 许可。对本作品的所有贡献都必须基于同一许可。

# Contributor Covenant Code of Conduct

## Our Pledge

为了营造开放且友好的环境，我们作为贡献者和维护者承诺：无论年龄、体型、残障、族裔、性别特征、性别认同与表达、经验水平、教育程度、社会经济地位、国籍、个人外貌、种族、宗教或性取向，每个人都能在我们的项目和社区中获得免受骚扰的参与体验。

## Our Standards

有助于营造积极环境的行为示例包括：

* 使用欢迎且包容的语言
* 尊重不同观点和经验
* 以恰当方式接受建设性批评
* 关注对社区最有利的事情
* 对其他社区成员保持同理心

参与者不可接受的行为示例包括：

* 使用性暗示语言或图像，以及不受欢迎的性关注或性挑逗
* 挑衅行为、侮辱/贬损性评论，以及人身或政治攻击
* 公开或私下骚扰
* 未经明确许可发布他人的隐私信息，例如实体地址或电子地址
* 在职业场景中可被合理视为不当的其他行为

## Our Responsibilities

项目维护者有责任明确可接受行为的标准，并应当针对任何不可接受行为的实例采取适当且公平的纠正措施。

项目维护者有权并有责任移除、编辑或拒绝与本行为准则不一致的评论、commit、代码、wiki 编辑、issue 及其他贡献；对于其认定为不当、威胁性、冒犯性或有害的其他行为，项目维护者也可以临时或永久禁止相关贡献者。

## Scope

本行为准则适用于所有项目空间，也适用于个人在公共空间中代表项目或其社区的场景。
代表项目或社区的行为示例包括：使用官方项目电子邮件地址、通过官方社交媒体账户发帖，或作为指定代表参加线上或线下活动。项目维护者可进一步定义并澄清“代表项目”的具体范围。

## Enforcement

如遇辱骂、骚扰或其他不可接受行为，可联系项目团队 `docs@min.io` 举报。所有投诉都会被审查和调查，并根据具体情况给予必要且适当的响应。项目团队有义务对事件举报人的身份信息保密。
更多具体的执行政策细则可能会另行发布。

未能本着诚信遵守或执行本行为准则的项目维护者，可能会面临由项目其他领导成员决定的临时或永久后果。

## Attribution

本行为准则改编自 [Contributor Covenant][homepage] 1.4 版，
原文地址：https://www.contributor-covenant.org/version/1/4/code-of-conduct.html

[homepage]: https://www.contributor-covenant.org

关于本行为准则常见问题的解答，请参见
https://www.contributor-covenant.org/faq
