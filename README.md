# KeepGreen



自动保持 GitHub 提交状态常绿。

> a commit a day keeps your girlfriend away.

## 原理

使用 GitHub Actions 的定时任务功能，每隔一段时间自动执行 `git commit`，提交信息为 "a commit a day keeps your girlfriend away"，灵感来自知乎问题[在 GitHub 上保持 365 天全绿是怎样一种体验？](https://www.zhihu.com/question/34043434/answer/57826281)下某用户的回答：

> 曾经保持了 200 多天全绿，但是冷落了女朋友，一直绿到现在。

有关 Github Action 的原理，可查看官方文档 [Github Action 简介](https://docs.github.com/cn/actions/learn-github-actions/introduction-to-github-actions)

## 使用

- 点右上角 **Use this template** 按钮复制本 GitHub 仓库，**:warning: 千万不要 Fork，因为 fork 项目的动态并不会让你变绿 :warning:**
- 修改 [ci.yml 文件的第 19、20 行](https://github.com/justjavac/auto-green/blob/master/.github/workflows/ci.yml#L19-L20) 为自己的 GitHub 账号和昵称
- (可选) 你可以通过修改 [ci.yml 文件的第 8 行](https://github.com/justjavac/auto-green/blob/master/.github/workflows/ci.yml#L8)来调整频率

计划任务语法有 5 个字段，中间用空格分隔，每个字段代表一个时间单位。

> git remote set-url origin https://${{ github.actor }}:${{ secrets.GITHUB_TOKEN }}@github.com/${{ github.repository }}

这里就是指定远程仓库等地址，这里有几个占位符，github 的 actor 和 repository 对象，其实这些我们不用管，在运行的时候会被自动赋值为当前仓库的信息，另外还有 GITHUB_TOKEN 也是在该任务运行的时候自动添加的。

> git commit --allow-empty -m "a commit a day keeps your girlfriend away"

这里的 commit 操作加上了一个 --allow-empty 选项，意思就是允许空的提交，这也就解释了上文空提交的缘由了。

```plain
┌───────────── 分钟 (0 - 59)
│ ┌───────────── 小时 (0 - 23)
│ │ ┌───────────── 日 (1 - 31)
│ │ │ ┌───────────── 月 (1 - 12 或 JAN-DEC)
│ │ │ │ ┌───────────── 星期 (0 - 6 或 SUN-SAT)
│ │ │ │ │
│ │ │ │ │
│ │ │ │ │
* * * * *
```

每个时间字段的含义：

|符号   | 描述        | 举例                                        |
| ----- | -----------| -------------------------------------------|
| `*`   | 任意值      | `* * * * *` 每天每小时每分钟                  |
| `,`   | 值分隔符    | `1,3,4,7 * * * *` 每小时的 1 3 4 7 分钟       |
| `-`   | 范围       | `1-6 * * * *` 每小时的 1-6 分钟               |
| `/`   | 每         | `*/15 * * * *` 每隔 15 分钟                  |

**注**：由于 GitHub Actions 的限制，如果设置为 `* * * * *` 实际的执行频率为每 5 分执行一次。
