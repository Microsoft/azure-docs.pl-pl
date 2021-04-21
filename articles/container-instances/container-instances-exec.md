---
title: Wykonywanie poleceń w uruchomionym wystąpieniu kontenera
description: Dowiedz się, jak wykonać polecenie w kontenerze, który jest aktualnie uruchomiony w Azure Container Instances
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 42832910efff67f111c669793798d9ff0e413536
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790783"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Wykonywanie polecenia w uruchomionym wystąpieniu kontenera platformy Azure

Usługa Azure Container Instances obsługuje wykonywanie polecenia w uruchomionym kontenerze. Uruchomienie polecenia w kontenerze, który jest już uruchomiony, jest szczególnie przydatne podczas opracowywania aplikacji i rozwiązywania problemów. Najpowszechniejszym użyciem tej funkcji jest uruchomienie interaktywnej powłoki na potrzeby debugowania problemów w uruchomionym kontenerze.

## <a name="run-a-command-with-azure-cli"></a>Uruchamianie polecenia za pomocą interfejsu wiersza polecenia platformy Azure

Wykonaj polecenie w uruchomionym kontenerze za pomocą [polecenia az container exec][az-container-exec] w interfejsie wiersza [polecenia platformy Azure:][azure-cli]

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Aby na przykład uruchomić powłokę Bash w kontenerze Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

W przykładowych danych wyjściowych poniżej powłoka Bash jest uruchamiana w uruchomionym kontenerze systemu Linux, zapewniając terminal, w którym `ls` jest wykonywane:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

W tym przykładzie wiersz polecenia jest uruchamiany w uruchomionym kontenerze NanoServer:

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Grupy z wieloma kontenerami

Jeśli grupa [kontenerów](container-instances-container-groups.md) zawiera wiele kontenerów, takich jak kontener aplikacji i sidecar rejestrowania, określ nazwę kontenera, w którym ma być uruchamiane polecenie za pomocą polecenia `--container-name` .

Na przykład w grupie kontenerów *mynginx znajdują* się dwa kontenery: *nginx-app* *i rejestrator*. Aby uruchomić powłokę w *kontenerze nginx-app:*

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Ograniczenia

Azure Container Instances obecnie obsługuje uruchamianie pojedynczego procesu za pomocą [polecenia az container exec][az-container-exec]i nie można przekazać argumentów polecenia. Na przykład nie można łańcuchem poleceń, takich jak `sh -c "echo FOO && echo BAR"` w , lub wykonać `echo FOO` .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych narzędziach do rozwiązywania problemów i typowych problemach z wdrażaniem, zobacz Rozwiązywanie problemów z kontenerem i wdrażaniem w [Azure Container Instances.](container-instances-troubleshooting.md)

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[azure-cli]: /cli/azure