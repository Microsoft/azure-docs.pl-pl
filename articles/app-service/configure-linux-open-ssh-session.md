---
title: Dostęp SSH dla kontenerów systemu Linux
description: Możesz otworzyć sesję SSH z kontenerem systemu Linux w Azure App Service. Niestandardowe kontenery systemu Linux są obsługiwane z pewnymi modyfikacjami obrazu niestandardowego.
keywords: azure app service, web app, linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/23/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 5a4572c1292f691f1883a720d07c3f0130f1c8f3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480294"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Otwórz sesję SSH z kontenerem systemu Linux w Azure App Service

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) jest często używany do zdalnego wykonywania poleceń administracyjnych z terminalu wiersza polecenia. App Service dla systemu Linux zapewnia obsługę SSH w kontenerze aplikacji. 

![Linux App Service SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

Możesz również połączyć się z kontenerem bezpośrednio z lokalnej maszyny dewelopera przy użyciu protokołu SSH i SFTP.

## <a name="open-ssh-session-in-browser"></a>Otwieranie sesji SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Używanie obsługi SSH z niestandardowymi obrazami platformy Docker

Zobacz [Konfigurowanie SSH w kontenerze niestandardowym.](configure-custom-container.md#enable-ssh)

## <a name="open-ssh-session-from-remote-shell"></a>Otwieranie sesji SSH z powłoki zdalnej

> [!NOTE]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej.
>

Za pomocą tunelowania TCP można utworzyć połączenie sieciowe między maszyną dewelopera i Web App for Containers za pośrednictwem uwierzytelnionego połączenia protokołu WebSocket. Umożliwia on otwarcie sesji SSH z kontenerem uruchomionym w App Service z wybranego klienta.

Aby rozpocząć pracę, musisz zainstalować interfejs wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli) Aby zobaczyć, jak to działa bez instalowania interfejsu wiersza polecenia platformy Azure, [otwórz Azure Cloud Shell](../cloud-shell/overview.md). 

Otwórz połączenie zdalne z aplikacją za pomocą [polecenia az webapp remote-connection create.](/cli/azure/ext/webapp/webapp/remote-connection#ext-webapp-az-webapp-remote-connection-create) Określ _\<subscription-id>_ wartości , i _ dla _\<group-name>_ \_ \<app-name> aplikacji.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` Na końcu polecenia jest tylko dla wygody, jeśli używasz Cloud Shell. Uruchamia proces w tle, aby można było uruchomić następne polecenie w tej samej powłoki.

> [!NOTE]
> Jeśli to polecenie nie powiedzie się, upewnij się, [że debugowanie](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) *zdalne jest wyłączone,* za pomocą następującego polecenia:
>
> ```azurecli-interactive
> az webapp config set --resource-group <resource-group-name> -n <app-name> --remote-debugging-enabled=false
> ```

Dane wyjściowe polecenia zawierają informacje potrzebne do otwarcia sesji SSH.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Otwórz sesję SSH z kontenerem przy użyciu wybranego klienta przy użyciu portu lokalnego. W poniższym przykładzie użyto domyślnego [polecenia SSH:](https://ss64.com/bash/ssh.html)

```bash
ssh root@127.0.0.1 -p <port>
```

Po wyświetleniu monitu wpisz , `yes` aby kontynuować nawiązywanie połączenia. Następnie zostanie wyświetlony monit o hasło. Użyj `Docker!` pokazanego wcześniej przykładu .

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

Po uwierzytelnieniu powinien zostać wyświetlony ekran powitalny sesji.

<pre>
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  &gt;
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
</pre>

Masz teraz połączenie z łącznikiem.  

Spróbuj uruchomić [najważniejsze](https://ss64.com/bash/top.html) polecenie. Proces aplikacji powinien być wyświetlony na liście procesów. W przykładowych danych wyjściowych poniżej jest to ten, który zawiera `PID 263` .

<pre>
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
</pre>

## <a name="next-steps"></a>Następne kroki

Pytania i wątpliwości można publikować na [forum platformy Azure.](/answers/topics/azure-webapps.html)

Aby uzyskać więcej informacji na Web App for Containers, zobacz:

* [Wprowadzenie do zdalnego debugowania aplikacji Node.js na Azure App Service z VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Szybki start: uruchamianie niestandardowego kontenera na platformie App Service](quickstart-custom-container.md?pivots=container-linux)
* [Using Ruby in Azure App Service on Linux](quickstart-ruby.md) (Używanie języka Ruby w usłudze Azure App Service w systemie Linux)
* [Azure App Service Web App for Containers FAQ](faq-app-service-linux.md) (Usługa Web App for Containers w usłudze Azure App Service — często zadawane pytania)
