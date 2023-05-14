- what is github actions?
```
コードを保存するのと同じ場所でソフトウェア開発のワークフローを自動化し、プルリクエストやIssueで協力することを支援する
Githubの機能拡張である。
GithubのPluginと理解して良い。

workflow->job->step(action)
```

- concepts
```
artifact (the files created when you build and test your code.)
event (A specific activity that triggers a workflow run)
job (A set of steps that execute on the same runner.)
runner (Any machine with the GitHub Actions runner application installed)


``` 

- how to use
```
GitHub ActionsではGitHubが提供するハードウェア上でプログラムを実行する、いわゆるクラウド型のサービス

自動化したいことの流れはWorkFlowと呼び、yamlファイルで定義し、
リポジトリ内の.github/workflowsディレクトリ内に保存することで実行できるようになる
```