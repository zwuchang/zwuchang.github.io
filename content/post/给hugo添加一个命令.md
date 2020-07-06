---
title: "给hugo添加一个命令"
tags: ["go","hugo"]
categories: ["go","hugo"]
date: 2020-07-06T13:52:32+08:00
url: "/addHugoCommand.html"
---

给hugo加一个命令，并发布到github上
<!--more-->
## 添加命令
* 在commands下新建一个文件pull.go内容如下
```
package commands

import (
	"bytes"
	"fmt"
	"github.com/spf13/cobra"
	"os/exec"
	"strings"
)

var _ cmder = (*deployCmd)(nil)

type pullCmd struct {
	*baseBuilderCmd
}

func (b *commandsBuilder) newPullCmd() *pullCmd {
	cc := &pullCmd{}

	cmd := &cobra.Command{
		Use:   "pull",
		Short: "须先配置好github，调用此命令将内容推送到github",
		Long:  `须先配置好github，调用此命令将内容推送到github.`,

		RunE: func(cmd *cobra.Command, args []string) error {
			osCmdData := []string{
				"hugo",
				"git -C public add .",
				`git -C public commit -m "rebuild"`,
				"git -C public push origin master",
			}

			for _, cmd := range osCmdData {
				arr := strings.Split(cmd, " ")
				_ = runOsCmd(arr[0], arr[1:])
			}
			return nil
		},
	}

	cc.baseBuilderCmd = b.newBuilderBasicCmd(cmd)
	return cc
}

func runOsCmd(command string, arg []string) error {
	osCmd := exec.Command(command, arg...)
	var stdout bytes.Buffer
	var stderr bytes.Buffer
	osCmd.Stdout = &stdout
	osCmd.Stderr = &stderr
	_ = osCmd.Run()
	fmt.Printf("调用%s %s 返回:%v \n %v \n", command, arg, stderr.String(), stdout.String())
	return nil
}

```

## 注册此命令
编辑commands/commands.go，在方法addAll()调用b.addCommands中添加一行b.newPullCmd(), 
```
	b.addCommands(
		b.newServerCmd(),
		newVersionCmd(),
		newEnvCmd(),
		b.newConfigCmd(),
		newCheckCmd(),
		b.newDeployCmd(),
		b.newConvertCmd(),
		b.newNewCmd(),
		b.newListCmd(),
		b.newPullCmd(), // 新增
		newImportCmd(),
		newGenCmd(),
		createReleaser(),
		b.newModCmd(),
	)
```

