# CircleCI
“从功能开发完成直到成功部署”这一阶段被称为软件开发“最后一公里”，很多开发团队也越来越认识到，持续集成和持续部署可帮助开发团队提高迭代效率和质量。

持续集成和持续部署工具层出不穷，[CircleCI](https://circleci.com/) 就是这类工具中比较优秀的一个。

![CircleCI](../assets/circleci.webp "CircleCI")

## 什么是持续集成
持续集成（Continuous Integration）通常缩写为 CI，
持续集成指的是，当代码有变更时，立即进行构建和测试，反馈运行结果，我们可以根据测试结果，确定新代码是否可以和原有代码正确的集成在一起。
让你能够在开发中随时发现问题，在快速的产品迭代中还可以保持很高的质量。因为修复问题的成本随着时间的推移而增长，越早发现，修复成本越低。

## 什么是持续部署
持续部署（Continuous Deployment）通常缩写为 CD，
持续部署指的是，当代码有变更时，自动进行测试和构建，如果一切顺利则自动部署到服务器上。

## CircleCI 是什么
CircleCI 是一个持续集成/持续部署的服务，开源项目可以免费使用，他的价格取决于你需要并发构建实例的数量，单个实例是免费的。

## CircleCI 能做什么
他可以绑定 GitHub/Bitbucket，只要你的代码有变更，就会自动抓取，根据你的配置，提供运行环境，执行测试、构建和部署。

## CircleCI 的工作流程
![CircleCI](../assets/circleci workflow.webp "CircleCI")

## CircleCI 配置

### 添加配置文件
要使用 CircleCI，首先在你项目的根目录创建一个名为 `.circleci` 的文件夹，并新建 `config.yml` 文件。

### 在 CircleCI 设置你的构建环境
打开 [CircleCI 控制台](https://circleci.com/dashboard) 选择左侧的 Add Project , CircleCI 会列出你的 GitHub/Bitbucket 的所有项目，选择项目并单击 Set Up Project 按钮

选择合适的操作系统(Operating System)、合适的编程语言(Language) (本文使用的是 Linux/Node)

复制下面的示例配置文件到你自己的配置文件中，推送到 git 仓库，然后就可以点击 Start building 进行第一次构建了。

## CircleCI 配置文件
CircleCI 配置文件一般由三部分组成

- 版本 (version)

 - 你要使用的 CircleCI 版本

- 工作 (jobs)

 - 你要执行的 job 清单，集合中的键为 job 的名称，值是具体执行 job 的内容，如果你使用工作流(workflows)，则 job 的名称在配置文件中必须唯一，如果你不使用 工作流(workflows)，则必须包含名称为build的 job 来作为用户提交代码时的默认 job。
- 工作流(workflows)

## 工作 (jobs)
首先我们来先写一个基础的配置文件
```yml
# PHP CircleCI 2.0 configuration file
#
# Check https://circleci.com/docs/2.0/language-php/ for more details
#
version: 2
jobs:
  build:
    docker:
    - image: circleci/php:7.1-browsers
    # 必要时可以在这里指定依赖镜像
    # - image: circleci/mysql:9.4

    working_directory: ~/repo

    steps:
    - checkout
    - run: composer install -n --prefer-dist

    # 运行单元测试
    - run: phpunit
  deploy:
    docker:
    - image: circleci/python:3.6.1

    working_directory: ~/repo

    steps:
```
在上面的列子中，有两个 job 叫 build/deploy，现在讲解一下 job 里面的属性

- docker 键是用来指定 CircleCI 当前 job 使用 docker, 其值image是指 docker 所使用的镜像，必要时你可以同时指定多个镜像，比如你的项目需要依赖 mysql 或者 redis。 第一个列出的容器为主容器，steps 都会在主容器中进行。
 - CircleCI 预先定义了很多镜像，您可以在 [此处](https://circleci.com/docs/2.0/circleci-images/) 找到它们
 - 如果不想使用 docker 也可以使用 machine / macos 具体使用方法参考 [这里](https://circleci.com/docs/2.0/configuration-reference/#machine)
 - 关于 docker 键的其他属性与用法 你可以在 [此处](https://circleci.com/docs/2.0/configuration-reference/#docker) 找到

- working_directory 属性是用来定义steps 在哪个目录运行

- steps 当前 job 要运行的 命令 (command) 列表

## 步骤 (steps)
steps 将负责对环境的初始化，与项目的构建、部署和测试:

### 一. 构建、测试

1.检出代码
```yml
# 将分支中的代码检出到 working_directory
- checkout
# 让步骤中可以调用其他 docker
- setup_remote_docker
```

2.从缓存中恢复 composer 依赖目录
```yml
- restore_cache:
    keys:
    - v1-dependencies-{{ checksum "composer.json" }}
    # 如果没有匹配的缓存则使用最新的缓存
    - v1-dependencies-
```
我们可以使用缓存功能来避免每次都重新 composer install，可以节约大量时间
```yml
{{ checksum "filename" }}
```
这部分指的是，给 filename 这个文件的文件内容 Base64 后取 SHA256 hash
其他模版语法请查看 [官方文档](https://circleci.com/docs/2.0/configuration-reference/#save_cache)
- 我们使用了两个缓存的 key 第一个是精确匹配设置的缓存 key，第二个是当用户修改 composer.json 文件时，我们不能精确匹配缓存，这时候恢复最近的一次缓存

3.安装依赖
```yml
- run:
    name: Install local dependencies
    command: composer install -n --prefer-dist
```
如果上一步恢复缓存的时候已经恢复了这些依赖项，则这步将非常快。

4.缓存依赖
```yml
- save_cache:
    paths:
    - ./vendor
    key: v1-dependencies-{{ checksum "composer.json" }}
```
经历了上一步,我们就会有一份当前版本完整的依赖目录，路径为vendor,这时我们把它缓存起来方便下次使用

5.测试
```yml
- run:
    name: Testing
    command: phpunit
```
我们运行测试的命令，如果测试有任何不通过则本次构建将失败。

6.打包 docker 镜像
```yml
- run:
    name: Build image
    command: |
        docker build -t $FULL_IMAGE_NAME .
        mkdir docker-image
        docker save -o docker-image/image.tar $FULL_IMAGE_NAME
```
打包 docker 镜像并命名为 `$FULL_IMAGE_NAME`，并将镜像 保存成 tar 归档文件

7.运行并简单测试镜像
```yml
- run:
    name: Test image
    command: |
      docker run -d -p 8080:80 --name built-image $FULL_IMAGE_NAME
      sleep 10
      docker run --network container:built-image byrnedo/alpine-curl -I --retry 10 --retry-connrefused http://localhost
```
运行刚才打包好的镜像，然后使用 curl 对打包好的镜像进行简单测试


### 二. 部署

1. 检出代码

2. 加载构建好的 docker 镜像
```yml
- attach_workspace:
    at: workspace
- run:
    name: Load image
    command: |
      docker load --input workspace/docker-image/image.tar
```

挂载 `workspace` 到当前 job, 挂载后 当前 job 的 `workspace/docker-image/image.tar` 为上一步打包出的 docker 镜像
使用 `docker load` 导入镜像


3.安装 aws cli
```yml
- restore_cache:
    key: v1-{{ checksum "requirements.txt" }}
- run:
    name: Get Aws-cli
    command: |
    python3 -m venv venv
    . venv/bin/activate
    pip install -r requirements.txt
- save_cache:
    key: v1-{{ checksum "requirements.txt" }}
    paths:
    - "venv"    # Download and cache dependencies
```

安装 aws cli 到 python venv 环境


4.推送镜像到 aws ecr
```yml
- run:
    name: Push Docker Image
    command: |
      . venv/bin/activate
      $(aws ecr get-login --no-include-email)
      docker tag mobingi-api-cn $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com.cn/mobingi-api-dev:$CIRCLE_SHA1
      docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com.cn/mobingi-api-dev:$CIRCLE_SHA1
```
推送 docker 镜像到 aws ecr
为了使 aws 能正常登陆，docker 镜像能正确 push 到 ecr，你需要在 CircleCI 当前项目中设置环境变量。

更多 steps 信息请查看 [官方文档](https://circleci.com/docs/2.0/configuration-reference/#steps)

## 工作流 (workflows)
用于编排所有 job。假设您需要在特定分支或特定时间运行job，或者您希望某些 job 并行运行，而某些 job 依次运行。

在工作流配置中，使用工作流名称作为配置的键。工作流名称在当前配置文件中必须全局唯一。

下面是工作流使用的例子
```yml
workflows:
  version: 2
  nightly:
    triggers:
    - schedule:
        cron: "0 0 * * *"
        filters:
          branches:
            only:
            - master
            - beta
    jobs:
    - test
```
`nightly` 为工作流名称
`schedule` 可以指定工作流在指定时间工作
`cron`  使用POSIX定义crontab语法
`filters->branches` 过滤的分支

- 任何符合 `only` 条件的分支都会运行该工作流
- 任何符合 `ignore` 条件的分支都不会运行该工作流
- 如果未定义 `only` 和 `ignore` 则所有分支豆浆运行该工作流
- 如果同时符合 `only` 和 `ignore` 优先考虑 `ignore`


根据上面的介绍我们可以将我们前面的两个 job build 和 deploy 编排成一个工作流 (workflows) 并命名为：build-deploy
```yml
workflows:
  version: 2
  build-deploy:
    jobs:
    - build
    - deploy:
        requires:
        - build
```
更多信息请查看 [官方文档](https://circleci.com/docs/2.0/workflows/#)

## 设置项目环境变量
因为有很多值不方便出现在 CircleCI 的配置文件中，例如一些密匙、服务 IP 等等。这时候我们就可以在 CircleCI 的管理面板中设置环境变量，然后在 job 获取这些变量。

1. 在项目的的左上角，选择这个 ⚙️ 按钮

2. 选择 Environment Variables -> Add Variable

在弹出的框中输入环境变量的 name 和 value 即可

## 本地测试 CircleCI 配置文件

1. [创建个人API令牌](https://circleci.com/docs/2.0/managing-api-tokens/#creating-a-personal-api-token)  (dashboard->User Settings->Personal API Tokens->Create New Token)

2. 设置环境变量 export CIRCLE_TOKEN=<你刚刚创建的 token>
3. 收集以下信息：

 - 提交构建的哈希值
 - 用户名
 - 项目来源
 - 项目名
 - 从哪个分支建立

4. 在 `.circleci` 目录,创建 shell 脚本 `run-build-locally.sh` 文件，文件内容为

```yml
#!/usr/bin/env bash
curl --user ${CIRCLE_TOKEN}: \
    --request POST \
    --form revision=<commit hash>\
    --form config=@config.yml \
    --form notify=false \
        https://circleci.com/api/v1.1/project/<source, eg. github>/<user name>/<project name>/tree/<branch name>
```
更多信息请查看 [官方文档](https://circleci.com/docs/2.0/examples/)
