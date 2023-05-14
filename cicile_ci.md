- Terraformとの連携
```
CIがTerraform用のImageを作って、そのImageからTerraformを実行ののこと　（hashicorp/terraform:light）	
Kubernetesも同じイメージかな、kubectlから各Cloudのサービスをアクセス	
```
    
- orbs	
```
可以理解成TOOL的一个集合，减少自己的设定	
packages of parameterizable reusable configuration elements	
The Orb Registry is an open repository of all published orbs	

orb-name: <namespace>/<orb-name>@1.2.3	

example:	
    version: 2.1
    orbs:
        hello: circleci/hello-build@0.0.5
    workflows:
        "Hello Workflow":
            jobs:
              - hello/hello-build
```    
		
- commands	
```
A command definition defines a sequence of steps as a map to be executed in a job, 
enabling you to reuse a single command definition across multiple jobs.	
example:

	install_something:	
	  description: "Install something"	
	  steps:	
	    - run:	
	        name: Install sth	
	        command: |	
	          curl -sfL xxxx
```

- references 
```
参照用かな
Yaml にはアンカー(&)とエイリアス(*)の機能がある。
&で定義、*で参照　これを使用し処理を一箇所

example:

    references:
        cache_key: &cache_key v1-dependencies-{{ checksum "yarn.lock" }}
        save_node_modules: &save_node_modules
            save_cache:
               paths:
                 - node_modules
               key: *cache_key
    ...
    
        steps:
               - checkout
              - *restore_node_modules

```

- executors
```
実行環境を定義
job で Executor を使用（実行環境を選ぶ）

example:

    executors:
      frontend-executor:
        docker:
          - image: circleci/node:12.13-stretch-browsers
    
    jobs:
       package-install-and-lint:
         executor: frontend-executor
         working_directory: ~/repo  #In which directory to run the steps,DefaultはPorject
         steps:
           - checkout

```

- jobs
```
ワークフローを使用している場合は、.circleci/config.yml ファイル内でジョブの名前が一意である必要
ワークフローを使用しない場合は、jobs マップに build という名前のジョブを含める必要
working_directory: $CIRCLE_WORKING_DIRECTORY 環境変数を介してこのディレクトリ
environment: CircleCI アプリケーションで設定した環境変数をオーバーライドする
persist_to_workspace: Circle CI 2.0ではjobごとに異なるDockerコンテナが起動するので、別のjobにファイルを受け渡すにはpersist_to_workspaceを使う

```

- workflows 
```
planName->jobs->job->
context: ジョブは、組織に設定されたグローバル環境変数を使用するように構成

各 job をどのような順番で実行するかを決める
a job requires b job
example:
    workflows:
      version: 2
      all-check:
        jobs:
          - lint
          - build:
              requires:
                - lint
```

- pipline
```
Workflow以上はpipelineである。
Yamlファイルのこと
```

- parameter
```
pipline(yamlファイル),job,workflow,command can have parameters

example
     commands:
       sayhello:
         description: "A very simple command for demonstration purposes"
         parameters:
           to:
             type: string
             default: "Hello World"
         steps:
           - run: echo << parameters.to >>

環境変数は、以下に示す優先順位に従う
FOO=bar make install など、run ステップのシェル コマンド内で宣言された環境変数
run ステップで environment キーを使用して宣言された環境変数
ジョブで environment キーを使用して設定された環境変数
コンテナで environment キーを使用して設定された環境変数
コンテキスト環境変数 (ユーザーがコンテキストへのアクセス権を持つ場合)
[Project Settings (プロジェクト設定)] ページで設定されたプロジェクトレベルの環境変数
CircleCI 環境変数に関するドキュメントで説明されている定義済みの特別な Cir



別の単位で定義されたParameterを参照できる
ex:
    version: 2.1
    #pipeline's parameter
    parameters:
      workingdir:
        type: string
        default: '~/someplace'

    executors:
      default-executor:
        docker:
          - image: hashicorp/someimage
        working_directory: << pipeline.parameters.workingdir >> #pipelineのParameterを使う
    
    jobs:
      joba:
        executor: default-executor
        parameters:
          env:
            type: string
            default: dev
        environment:
          ENV: << parameters.env >>　#自分定義したParameterで環境変数を上書きする
        working_directory: << pipeline.parameters.workingdir >>/<< parameters.env >>　#use pipeline paramter ,use job parameter

```

- tips
```
#自分のUserのhome directory
echo $HOME 

# @-はcurlの送信Dataとして、なんかの処理をCurlする
なんかの処理 | curl http://hoge.com -X POST -d @-


#make file
@でcmdを実行
${ENV}を環境変数を使う

ex:
 plan:
 	@terraform plan --out plan ${ENV}
```