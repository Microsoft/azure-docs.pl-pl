---
title: Pobierz dzienniki wystąpienia kontenera & zdarzenia
description: Dowiedz się, jak pobrać dzienniki kontenerów i zdarzenia w Azure Container Instances, aby pomóc w rozwiązywaniu problemów z kontenerami
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 20b6e1cfe6bb8f6ac721a401c3d0831d4f447edb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746973"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Pobieranie zdarzeń i dzienników kontenerów w usłudze Azure Container Instances

Jeśli masz kontener błędna w Azure Container Instances, Zacznij od wyświetlenia jego dzienników przy użyciu [AZ Container Logs][az-container-logs]i strumieniowo jego standardowego i standardowego błędu za pomocą [AZ Container Attach][az-container-attach]. Możesz również wyświetlać dzienniki i zdarzenia dla wystąpień kontenera w Azure Portal lub wysyłać dane dziennika i zdarzeń dla grup kontenerów do [Azure monitor dzienników](container-instances-log-analytics.md).

## <a name="view-logs"></a>Wyświetlanie dzienników

Aby wyświetlić dzienniki z kodu aplikacji w kontenerze, można użyć polecenia [AZ Container Logs][az-container-logs] .

Poniżej znajdują się dane wyjściowe dziennika z przykładowego kontenera opartego na zadaniach w przypadku [ustawienia wiersza polecenia w wystąpieniu kontenera](container-instances-start-command.md#azure-cli-example), po podaniu nieprawidłowego adresu URL przy użyciu przesłonięcia wiersza polecenia:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Dołączanie strumieni wyjściowych

Polecenie [AZ Container Attach][az-container-attach] zawiera informacje diagnostyczne podczas uruchamiania kontenera. Po rozpoczęciu pracy kontenera strumienie STDOUT i STDERR są przesyłane do lokalnej konsoli.

Na przykład dane wyjściowe z kontenera opartego na zadaniach są [ustawiane w wierszu polecenia w wystąpieniu kontenera](container-instances-start-command.md#azure-cli-example), po podaniu prawidłowego adresu URL dużego pliku tekstowego do przetworzenia:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Pobieranie zdarzeń diagnostycznych

Jeśli nie można pomyślnie wdrożyć kontenera, przejrzyj informacje diagnostyczne dostarczone przez dostawcę zasobów Azure Container Instances. Aby wyświetlić zdarzenia dla swojego kontenera, uruchom polecenie [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Dane wyjściowe obejmują podstawowe właściwości kontenera oraz zdarzenia wdrażania (pokazane tutaj obcięte):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [rozwiązywać typowe problemy dotyczące kontenera i wdrażania](container-instances-troubleshooting.md) Azure Container Instances.

Dowiedz się, jak wysyłać dane dzienników i zdarzeń dla grup kontenerów do [dzienników Azure monitor](container-instances-log-analytics.md).

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
