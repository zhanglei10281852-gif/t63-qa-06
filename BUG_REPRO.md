# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

正在执行的班次仍能被取消，随后行程和排班状态互相矛盾。请先不要修改代码，调查非法取消为何被接受，并提供能够证明状态流转问题的证据。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/t63-qa-06
- 仓库地址：https://github.com/zhanglei10281852-gif/t63-qa-06.git
- parent SHA：64c834749afc33763b3eb9045db48ab793aa883f

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/t63-qa-06.git bug-repro
cd bug-repro
git checkout --detach 64c834749afc33763b3eb9045db48ab793aa883f
go test ./internal/domain/workplan -run TestShiftCancellationRules -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/domain/workplan -run TestShiftCancellationRules -count=1
--- FAIL: TestShiftCancellationRules (0.00s)
    workplan_test.go:79: active shift cancelled
FAIL
FAIL	sanitation-operations/internal/domain/workplan	0.002s
FAIL

```

stderr：

```text
warning: internal/domain/workplan/workplan_test.go has type 100755, expected 100644
warning: internal/domain/workplan/workplan_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/domain/workplan -run TestShiftCancellationRules -count=1
--- FAIL: TestShiftCancellationRules (0.01s)
    workplan_test.go:79: active shift cancelled
FAIL
FAIL	sanitation-operations/internal/domain/workplan	0.132s
FAIL

```

stderr：

```text
warning: internal/domain/workplan/workplan_test.go has type 100755, expected 100644
warning: internal/domain/workplan/workplan_test.go has type 100755, expected 100644

```

## 通过条件

在触发条件下，定向测试 TestShiftCancellationRules 应通过，相关包、全量测试、竞态测试和构建检查均通过；回退 gold 唯一修复后定向测试重新失败。
