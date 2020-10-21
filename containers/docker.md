# Docker

## Installation

{% hint style="info" %}
Detta kräver att du har WSL2 installerat. Kontrollera med att i Powershell köra `wsl -l -v`
{% endhint %}

* Börja med att installera Windows Subsystem Linux 2, en guide finns [här](https://docs.microsoft.com/en-us/windows/wsl/install-win10).
  * Om du installerar en ny distribution:
    * `sudo apt update`
    * `sudo apt upgrade -y`
* När det är klart, installera [Docker desktop](https://www.docker.com/) för Windows.
* Se till att du har Remote extension för Visual Studio Code, den [här](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl).

Nästa steg är att konfigurera Docker så att det använder WSL2, se fig 1 och att det har tillgång till WSL, fig 2.

![Fig 1, General settings f&#xF6;r Docker](../.gitbook/assets/docker_use1.png)

![Fig 2, Resource settings.](../.gitbook/assets/docker_use.png)

Du kan eventuellt behöva starta om datorn, WSL och eller Docker efter detta. Men du ska kunna starta WSL och köra kommandot docker. Docker körs då från Windows, men finns tillgängligt från WSL.

{% tabs %}
{% tab title="Bash" %}
```bash
docker
```
{% endtab %}
{% endtabs %}

 [https://github.com/jensnti/dockerstuff](https://github.com/jensnti/dockerstuff)

