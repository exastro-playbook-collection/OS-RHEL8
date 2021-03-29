Ansible Role: OS-RHEL8/RH_postfix/OS_build
=======================================================
# Description
本ロールは、RHEL8に関するpostfix設定についての情報の設定を行います。

# Supports
- 管理マシン(Ansibleサーバ)
  * Linux系OS（RHEL7）
  * Ansible バージョン 2.7 以上 (動作確認バージョン 2.7, 2.9)
  * Python バージョン 3.x  (動作確認バージョン 3.6)
- 管理対象マシン
  * RHEL8

# Requirements
- 管理マシン(Ansibleサーバ)
  * Ansibleサーバは管理対象マシンへssh接続できる必要があります。
- 管理対象マシン
  * RHEL8

# Dependencies

本ロールでは、他のロールは必要ありません。
ただし、本READMEに書かれている「エビデンスを取得する場合」の手順を行う場合は、
OS-RHEL8/RH_postfix/OS_gatheringロールを利用します。

# Role Variables

本ロールで指定できる変数値について説明します。

## Mandatory Variables

ロール利用時に以下の変数値を指定する必要があります。

| Name | Description | 
| ---- | ----------- | 
| `VAR_RH_postfix` | | 
| `- path` | ファイルパス /etc/postfix/main.cf | 
| &nbsp;&nbsp;&nbsp;&nbsp;`text` | main.cfファイルの内容 | 

### Example
~~~
VAR_RH_postfix:
- path: /etc/postfix/main.cf
  text:
  - '# Global Postfix configuration file. This file lists only a subset'
  - '# of all parameters. For the syntax, and for a complete parameter'
  - '# list, see the postconf(5) manual page (command: "man 5 postconf").'
  - '#'
  - '# For common configuration examples, see BASIC_CONFIGURATION_README'
  ・・・
~~~


## Optional Variables

特にありません。

# Usage

1. 本ロールを用いたPlaybookを作成します。
2. 変数を必要に応じて設定します。
3. Playbookを実行します。

# Example Playbook

## ■エビデンスを取得しない場合の呼び出す方法

本ロールを"roles"ディレクトリに配置して、以下のようなPlaybookを作成してください。

- フォルダ構成

~~~
 - playbook/
    │── roles/
    │    └── OS-RHEL8
    │         └── RH_postfix/
    │              └── OS_build/
    │                   │── tasks/
    │                   │      build_flat.yml
    │                   │      main.yml
    │                   │── templates/
    │                   │      flat_template
    │                   └─ README.md
    └─ master_playbook.yml
~~~

- マスターPlaybook サンプル[master_playbook.yml]

~~~
#master_playbook.yml
---
- hosts: all
  gather_facts: true
  roles:
    - role: OS-RHEL8/RH_postfix/OS_build
      VAR_RH_postfix:
      - path: /etc/postfix/main.cf
        text:
        - '# Global Postfix configuration file. This file lists only a subset'
        - '# of all parameters. For the syntax, and for a complete parameter'
        - '# list, see the postconf(5) manual page (command: "man 5 postconf").'
        - '#'
        - '# For common configuration examples, see BASIC_CONFIGURATION_README'
        ・・・
  strategy: free
~~~

- Running Playbook

~~~
> ansible-playbook master_playbook.yml
~~~

## ■エビデンスを取得する場合の呼び出す方法

エビデンスを収集する場合、以下のようなエビデンス収集用のPlaybookを作成してください。  

- マスターPlaybook サンプル[master_playbook.yml]

~~~
#master_playbook.yml
---
- hosts: all
  gather_facts: true
  roles:
    - role: OS-RHEL8/RH_postfix/OS_build
      VAR_RH_postfix:
      - path: /etc/postfix/main.cf
        text:
        - '# Global Postfix configuration file. This file lists only a subset'
        - '# of all parameters. For the syntax, and for a complete parameter'
        - '# list, see the postconf(5) manual page (command: "man 5 postconf").'
        - '#'
        - '# For common configuration examples, see BASIC_CONFIGURATION_README'
        ・・・
  strategy: free

- hosts: all
  gather_facts: true
  roles:
    - role: OS-RHEL8/RH_postfix/OS_gathering
  strategy: free
~~~

- エビデンス収集結果一覧

エビデンス収集結果は、以下のように格納されます。
エビデンス収集結果の詳細は、OS_gatheringロールを確認してください。

~~~
#エビデンス構成
 - playbook/
    │── _gathered_data/
    │    └── 管理対象マシンホスト名 or IPアドレス/
    │         └── OS/
    │              └── RH_postfix/
    │                   │── command/
    │                   │      ・・・
    │                   └── file/
    │                          ・・・
    └── _parameters/
            └── 管理対象マシンホスト名 or IPアドレス/
                 └── OS/
                        RH_postfix.yml
~~~

# Remarks
-------

# License
-------

# Copyright
---------
Copyright (c) 2021 NEC Corporation

# Author Information
------------------
NEC Corporation
