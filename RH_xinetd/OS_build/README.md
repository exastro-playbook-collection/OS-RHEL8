Ansible Role: OS-RHEL8/RH_xinetd/OS_build
=======================================================
# Description
本ロールは、RHEL8に関するスーパーデーモン設定についての情報の設定を行います。

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
OS-RHEL8/RH_xinetd/OS_gatheringロールを利用します。

# Role Variables

本ロールで指定できる変数値について説明します。

## Mandatory Variables

ロール利用時に以下の変数値を指定する必要があります。

| Name | Description | 
| ---- | ----------- | 
| `VAR_RH_xinetd` | | 
| `- path` | ファイルパス /etc/xinetd.conf | 
| &nbsp;&nbsp;&nbsp;&nbsp;`text` | xinetd.confファイルの内容 | 
| `- path` | ファイルパス /etc/xinetd.d/配下 | 
| &nbsp;&nbsp;&nbsp;&nbsp;`text` | /etc/xinetd.d/配下のファイル内容 | 

### Example
~~~
VAR_RH_xinetd:
- path: /etc/xinetd.conf
  text:
  - '# RHEL8_UPDATE 20200915-1'
  - '#'
  - '# This is the master xinetd configuration file. Settings in the'
  - '# default section will be inherited by all service configurations'
  - '# unless explicitly overridden in the service configuration. See'
  ・・・
- path: /etc/xinetd.d/chargen-dgram
  text:
  - '# RHEL8_UPDATE 20200911-2'
  - '# This is the configuration for the udp/dgram chargen service.'
  - ''
  ・・・
~~~


## Optional Variables

ロール利用時に以下の変数値を指定することができます。

| Name | Default Value | Description | 
| ---- | ------------- | ----------- | 
| `VAR_xinetd_reboot` | false | スーパーデーモンの情報設定後の再起動実行有無<br>true: 再起動する<br>false: 再起動しない | 

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
    │         └── RH_xinetd/
    │              └── OS_build/
    │                   │── defaults/
    │                   │      main.yml
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
    - role: OS-RHEL8/RH_xinetd/OS_build
      VAR_RH_xinetd:
      - path: /etc/xinetd.conf
        text:
        - '# RHEL8_UPDATE 20200915-1'
        - '#'
        - '# This is the master xinetd configuration file. Settings in the'
        - '# default section will be inherited by all service configurations'
        - '# unless explicitly overridden in the service configuration. See'
        ・・・
      - path: /etc/xinetd.d/chargen-dgram
        text:
        - '# RHEL8_UPDATE 20200911-2'
        - '# This is the configuration for the udp/dgram chargen service.'
        - ''
        - service chargen
        - '{'
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
    - role: OS-RHEL8/RH_xinetd/OS_build
      VAR_RH_xinetd:
      - path: /etc/xinetd.conf
        text:
        - '# RHEL8_UPDATE 20200915-1'
        - '#'
        - '# This is the master xinetd configuration file. Settings in the'
        - '# default section will be inherited by all service configurations'
        - '# unless explicitly overridden in the service configuration. See'
        ・・・
      - path: /etc/xinetd.d/chargen-dgram
        text:
        - '# RHEL8_UPDATE 20200911-2'
        - '# This is the configuration for the udp/dgram chargen service.'
        - ''
        - service chargen
        - '{'
        ・・・
  strategy: free

- hosts: all
  gather_facts: true
  roles:
    - role: OS-RHEL8/RH_xinetd/OS_gathering
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
    │              └── RH_xinetd/
    │                   │── command/
    │                   │      ・・・
    │                   └── file/
    │                          ・・・
    └── _parameters/
            └── 管理対象マシンホスト名 or IPアドレス/
                 └── OS/
                        RH_xinetd.yml
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
