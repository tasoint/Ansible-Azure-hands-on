# Chapter 0: 事前準備

この章では、ハンズオンを実施するための環境を準備します。

この章のゴール: Ansible経由でAzureのリソースが参照できること。

## Azureアカウントの作成

Azureアカウントをまだお持ちでない場合は、[こちら](https://azure.microsoft.com/ja-jp/free/)から無料で作成できます。

## Handson環境について

Ansible

## Ansibleのインストールと設定

Ansibleをインストールします。お使いのOSに合わせて適切な方法でインストールしてください。

```bash
# 例: Linuxの場合
sudo apt update
sudo apt install ansible

# 例: macOSの場合
brew update
brew install ansible
```

## 必要なAzureモジュールのインストール

AnsibleでAzureを操作するために必要なモジュールをインストールします。

```bash
pip install azure-identity azure-mgmt-resource azure-mgmt-compute
```

## Azure CLIのインストールと認証設定

Azure CLIをインストールし、Azureアカウントにログインします。

```bash
# Azure CLIのインストール
# (OSに合わせた方法でインストール)

# Azureへのログイン
az login
```

これで事前準備は完了です。次の章からAnsibleによるAzureリソース操作を学習していきます。
