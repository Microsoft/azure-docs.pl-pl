---
title: Wykonaj polecenia w uruchomionym wystąpieniu kontenera
description: Dowiedz się, jak wykonać polecenie w kontenerze, który jest obecnie uruchomiony w Azure Container Instances
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: de48e6ac246e2b0751561b4c60bb63d88b599bdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "79247204"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Wykonywanie polecenia w uruchomionym wystąpieniu kontenera platformy Azure

Usługa Azure Container Instances obsługuje wykonywanie polecenia w uruchomionym kontenerze. Uruchomienie polecenia w kontenerze, który jest już uruchomiony, jest szczególnie przydatne podczas opracowywania aplikacji i rozwiązywania problemów. Najpowszechniejszym użyciem tej funkcji jest uruchomienie interaktywnej powłoki na potrzeby debugowania problemów w uruchomionym kontenerze.

## <a name="run-a-command-with-azure-cli"></a>Uruchom polecenie za pomocą interfejsu wiersza polecenia platformy Azure

Wykonaj polecenie w działającym kontenerze za pomocą polecenia [AZ Container exec][az-container-exec] w [interfejsie wiersza polecenia platformy Azure][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Na przykład, aby uruchomić powłokę bash w kontenerze Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

W poniższym przykładzie danych wyjściowych powłoka bash jest uruchamiana w działającym kontenerze systemu Linux, co zapewnia Terminal, który `ls` jest wykonywany:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

W tym przykładzie wiersz polecenia jest uruchamiany w działającym kontenerze Nanoserver:

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

Jeśli [Grupa kontenerów](container-instances-container-groups.md) ma wiele kontenerów, takich jak kontener aplikacji i Przyczepka zarejestrowana, należy określić nazwę kontenera, w którym ma zostać uruchomione polecenie `--container-name` .

Na przykład w grupie kontenerów *mynginx* są dwa kontenery, *Nginx-App* i *Rejestrator*. Aby uruchomić powłokę w kontenerze *Nginx-App* :

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Ograniczenia

Azure Container Instances obecnie obsługuje uruchamianie pojedynczego procesu za pomocą polecenia [AZ Container exec][az-container-exec]i nie można przekazać argumentów poleceń. Na przykład nie można łańcucha poleceń, takich jak w `sh -c "echo FOO && echo BAR"` , lub Execute `echo FOO` .

## <a name="next-steps"></a>Następne kroki

Poznaj inne narzędzia do rozwiązywania problemów i typowe problemy z wdrażaniem w temacie [Rozwiązywanie problemów z kontenerami i wdrażaniem w programie Azure Container Instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure