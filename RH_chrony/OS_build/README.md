Ansible Role: OS-RHEL8/RH_chrony/OS_build
=======================================================
# Description
本ロールは、RHEL8に関する時刻同期設定についての情報の設定を行います。

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
OS-RHEL8/RH_chrony/OS_gatheringロールを利用します。

# Role Variables

本ロールで指定できる変数値について説明します。

## Mandatory Variables

ロール利用時に以下の変数値を指定する必要があります。

| Name | Description | 
| ---- | ----------- | 
| `VAR_RH_chrony` | | 
| `- path` | ファイルパス /etc/chrony.conf | 
| &nbsp;&nbsp;&nbsp;&nbsp;`text` | chrony.confファイルの内容 | 
| `- path` | ファイルパス /etc/chrony.keys | 
| &nbsp;&nbsp;&nbsp;&nbsp;`text` | chrony.keysファイルの内容 |  

### Example
~~~
VAR_RH_chrony:
- path: /etc/chrony.conf
  text:
  - '# Use public servers from the pool.ntp.org project.'
  - '# Please consider joining the pool (http://www.pool.ntp.org/join.html).'
  - server 0.rhel.pool.ntp.org iburst
  - server 1.rhel.pool.ntp.org iburst
  - server 2.rhel.pool.ntp.org iburst
  ・・・
- path: /etc/chrony.keys
  text:
  - '# This is an example chrony keys file.  It is used for NTP authentication with'
  - '# symmetric keys.  It should be readable only by root or the user to which'
  - '# chronyd is configured to switch to after start.'
  - '#'
  - '# Don''t use the example keys!  It''s recommended to generate random keys using'
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
    │         └── RH_chrony/
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
    - role: OS-RHEL8/RH_chrony/OS_build
      VAR_RH_chrony:
      - path: /etc/chrony.conf
        text:
        - '# Use public servers from the pool.ntp.org project.'
        - '# Please consider joining the pool (http://www.pool.ntp.org/join.html).'
        - server 0.rhel.pool.ntp.org iburst
        - server 1.rhel.pool.ntp.org iburst
        - server 2.rhel.pool.ntp.org iburst
        ・・・
      - path: /etc/chrony.keys
        text:
        - '# This is an example chrony keys file.  It is used for NTP authentication with'
        - '# symmetric keys.  It should be readable only by root or the user to which'
        - '# chronyd is configured to switch to after start.'
        - '#'
        - '# Don''t use the example keys!  It''s recommended to generate random keys using'
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
    - role: OS-RHEL8/RH_chrony/OS_build
      VAR_RH_chrony:
      - path: /etc/chrony.conf
        text:
        - '# Use public servers from the pool.ntp.org project.'
        - '# Please consider joining the pool (http://www.pool.ntp.org/join.html).'
        - server 0.rhel.pool.ntp.org iburst
        - server 1.rhel.pool.ntp.org iburst
        - server 2.rhel.pool.ntp.org iburst
        ・・・
      - path: /etc/chrony.keys
        text:
        - '# This is an example chrony keys file.  It is used for NTP authentication with'
        - '# symmetric keys.  It should be readable only by root or the user to which'
        - '# chronyd is configured to switch to after start.'
        - '#'
        - '# Don''t use the example keys!  It''s recommended to generate random keys using'
        ・・・
  strategy: free

- hosts: all
  gather_facts: true
  roles:
    - role: OS-RHEL8/RH_chrony/OS_gathering
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
    │              └── RH_chrony/
    │                   │── command/
    │                   │      ・・・
    │                   └── file/
    │                          ・・・
    └── _parameters/
            └── 管理対象マシンホスト名 or IPアドレス/
                 └── OS/
                        RH_chrony.yml
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
