Ansible Role: OS-RHEL8/RH_cron/OS_build
=======================================================
# Description
本ロールは、RHEL8に関する定期自動ジョブ基本設定についての情報の設定を行います。

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
OS-RHEL8/RH_cron/OS_gatheringロールを利用します。

# Role Variables

本ロールで指定できる変数値について説明します。

## Mandatory Variables

ロール利用時に以下の変数値を指定する必要があります。

| Name | Description | 
| ---- | ----------- | 
| `VAR_RH_cron` | | 
| `- path` | ファイルパス /etc/anacrontab | 
| &nbsp;&nbsp;&nbsp;&nbsp;`text` | anacrontabファイルの内容 | 
| `- path` | ファイルパス /etc/crontab | 
| &nbsp;&nbsp;&nbsp;&nbsp;`text` | crontabファイルの内容 | 
| `- path` | ファイルパス /etc/cron.d/配下 | 
| &nbsp;&nbsp;&nbsp;&nbsp;`text` | /etc/cron.d/配下のファイル内容 | 

### Example
~~~
VAR_RH_cron:
- path: /etc/anacrontab
  text:
  - '# /etc/anacrontab: configuration file for anacron'
  - ''
  - '# See anacron(8) and anacrontab(5) for details.'
  - ''
  - SHELL=/bin/sh
  ・・・
- path: /etc/crontab
  text:
  - SHELL=/bin/bash
  - PATH=/sbin:/bin:/usr/sbin:/usr/bin
  - MAILTO=root
  - ''
  - '# For details see man 4 crontabs'
  ・・・
- path: /etc/cron.d/0hourly
  text:
  - '# Run the hourly jobs'
  - SHELL=/bin/bash
  - PATH=/sbin:/bin:/usr/sbin:/usr/bin
  - MAILTO=root
  - 01 * * * * root run-parts /etc/cron.hourly
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
    │         └── RH_cron/
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
    - role: OS-RHEL8/RH_cron/OS_build
      VAR_RH_cron:
      - path: /etc/anacrontab
        text:
        - '# /etc/anacrontab: configuration file for anacron'
        - ''
        - '# See anacron(8) and anacrontab(5) for details.'
        - ''
        - SHELL=/bin/sh
        ・・・
      - path: /etc/crontab
        text:
        - SHELL=/bin/bash
        - PATH=/sbin:/bin:/usr/sbin:/usr/bin
        - MAILTO=root
        - ''
        - '# For details see man 4 crontabs'
        ・・・
      - path: /etc/cron.d/0hourly
        text:
        - '# Run the hourly jobs'
        - SHELL=/bin/bash
        - PATH=/sbin:/bin:/usr/sbin:/usr/bin
        - MAILTO=root
        - 01 * * * * root run-parts /etc/cron.hourly
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
    - role: OS-RHEL8/RH_cron/OS_build
      VAR_RH_cron:
      - path: /etc/anacrontab
        text:
        - '# /etc/anacrontab: configuration file for anacron'
        - ''
        - '# See anacron(8) and anacrontab(5) for details.'
        - ''
        - SHELL=/bin/sh
        ・・・
      - path: /etc/crontab
        text:
        - SHELL=/bin/bash
        - PATH=/sbin:/bin:/usr/sbin:/usr/bin
        - MAILTO=root
        - ''
        - '# For details see man 4 crontabs'
        ・・・
      - path: /etc/cron.d/0hourly
        text:
        - '# Run the hourly jobs'
        - SHELL=/bin/bash
        - PATH=/sbin:/bin:/usr/sbin:/usr/bin
        - MAILTO=root
        - 01 * * * * root run-parts /etc/cron.hourly
  strategy: free

- hosts: all
  gather_facts: true
  roles:
    - role: OS-RHEL8/RH_cron/OS_gathering
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
    │              └── RH_cron/
    │                   │── command/
    │                   │      ・・・
    │                   └── file/
    │                          ・・・
    └── _parameters/
            └── 管理対象マシンホスト名 or IPアドレス/
                 └── OS/
                        RH_cron.yml
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
