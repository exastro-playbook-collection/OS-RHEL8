Ansible Role: OS-RHEL8/RH_user/OS_build
=======================================================
# Description
本ロールは、RHEL8に関するユーザー設定についての情報の設定を行います。

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
OS-RHEL8/RH_user/OS_gatheringロールを利用します。

# Role Variables

本ロールで指定できる変数値について説明します。

## Mandatory Variables

ロール利用時に以下の変数値を指定する必要があります。

| Name | Description | 
| ---- | ----------- | 
| `VAR_RH_user` | | 
| `- user_name` | ユーザー名 | 
| &nbsp;&nbsp;&nbsp;&nbsp;`user_id` | ユーザーID | 
| &nbsp;&nbsp;&nbsp;&nbsp;`group_id` | グループID | 
| &nbsp;&nbsp;&nbsp;&nbsp;`comment` | コメント | 
| &nbsp;&nbsp;&nbsp;&nbsp;`home_dir` | ホームディレクトリ | 
| &nbsp;&nbsp;&nbsp;&nbsp;`login_shell` | ログインシェル名 | 
| &nbsp;&nbsp;&nbsp;&nbsp;`password` | パスワード | 
| &nbsp;&nbsp;&nbsp;&nbsp;`action` | 構築時の設定<br>present: 作成/更新<br>absent: 削除 | 
| &nbsp;&nbsp;&nbsp;&nbsp;`password_apply` | 構築時のパスワード設定の有無<br>true: パスワードの設定を行う<br>false: パスワードの設定を行わない | 

### Example
~~~
VAR_RH_user:
- action: present
  comment: create testuser
  group_id: '1100'
  home_dir: /home/testuser
  login_shell: /bin/bash
  password: p@ssw0rd123
  password_apply: true
  user_id: '1100'
  user_name: testuser
- action: present
  comment: update testuser2
  group_id: '1101'
  home_dir: /home/testuser2
  login_shell: /bin/bash
  password_apply: false
  user_id: '1102'
  user_name: testuser2
- action: absent
  user_name: testuser99
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
    │         └── RH_user/
    │              └── OS_build/
    │                   │── tasks/
    │                   │      check_parameter.yml
    │                   │      check.yml
    │                   │      main.yml
    │                   │      modify_user.yml
    │                   │      modify.yml
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
    - role: OS-RHEL8/RH_user/OS_build
      VAR_RH_user:
      - action: present
        comment: create testuser
        group_id: '1100'
        home_dir: /home/testuser
        login_shell: /bin/bash
        password: p@ssw0rd123
        password_apply: true
        user_id: '1100'
        user_name: testuser
      - action: present
        comment: update testuser2
        group_id: '1102'
        home_dir: /home/testuser2
        login_shell: /bin/bash
        password_apply: false
        user_id: '1102'
        user_name: testuser2
      - action: absent
        user_name: testuser99
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
    - role: OS-RHEL8/RH_user/OS_build
      VAR_RH_user:
      - action: present
        comment: create testuser
        group_id: '1100'
        home_dir: /home/testuser
        login_shell: /bin/bash
        password: p@ssw0rd123
        password_apply: true
        user_id: '1100'
        user_name: testuser
      - action: present
        comment: update testuser2
        group_id: '1102'
        home_dir: /home/testuser2
        login_shell: /bin/bash
        password_apply: false
        user_id: '1102'
        user_name: testuser2
      - action: absent
        user_name: testuser99
      ・・・

  strategy: free

- hosts: all
  gather_facts: true
  roles:
    - role: OS-RHEL8/RH_user/OS_gathering
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
    │              └── RH_user/
    │                   │── command/
    │                   │      ・・・
    │                   └── file/
    │                          ・・・
    └── _parameters/
            └── 管理対象マシンホスト名 or IPアドレス/
                 └── OS/
                        RH_user.yml
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
