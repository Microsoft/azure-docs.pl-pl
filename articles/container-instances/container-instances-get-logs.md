---
title: Uzyskiwanie dzienników wystąpień kontenera & zdarzeń
description: Dowiedz się, jak pobierać dzienniki kontenerów i zdarzenia w Azure Container Instances, aby ułatwić rozwiązywanie problemów z kontenerem
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f5eb8c878164846ed2f1daf1cb7e5014e0c62c55
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764049"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Pobieranie zdarzeń i dzienników kontenerów w usłudze Azure Container Instances

W przypadku błędnie zachowującego się kontenera w programie Azure Container Instances rozpocznij od wyświetlenia jego dzienników za pomocą az [container logs][az-container-logs]i przesyłaj strumieniowo jego standardowy błąd out i błąd standardowy za pomocą az [container attach][az-container-attach]. Można również wyświetlać dzienniki i zdarzenia dla wystąpień kontenerów w Azure Portal lub wysyłać dane dzienników i zdarzeń dla grup kontenerów do Azure Monitor [dzienników.](container-instances-log-analytics.md)

## <a name="view-logs"></a>Wyświetlanie dzienników

Aby wyświetlić dzienniki z kodu aplikacji w kontenerze, możesz użyć [polecenia az container logs.][az-container-logs]

Poniżej przedstawiono dane wyjściowe dziennika z przykładowego kontenera opartego na zadaniach z tematu Ustawianie wiersza polecenia w wystąpieniu kontenera [po](container-instances-start-command.md#azure-cli-example)podaniu nieprawidłowego adresu URL przy użyciu przesłonięcia wiersza polecenia:

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

Polecenie [az container attach][az-container-attach] dostarcza informacje diagnostyczne podczas uruchamiania kontenera. Po zakończeniu pracy kontener przesyła strumieniowo strumienie STDOUT i STDERR do konsoli lokalnej.

Na przykład poniżej podano prawidłowy adres [](container-instances-start-command.md#azure-cli-example)URL dużego pliku tekstowego do przetwarzania z kontenera opartego na zadaniach w części Ustawianie wiersza polecenia w wystąpieniu kontenera:

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

## <a name="get-diagnostic-events"></a>Uzyskiwanie zdarzeń diagnostycznych

Jeśli wdrożenie kontenera zakończy się niepowodzeniem, przejrzyj informacje diagnostyczne dostarczone przez dostawcę Azure Container Instances zasobów. Aby wyświetlić zdarzenia dla kontenera, uruchom [polecenie az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Dane wyjściowe zawierają podstawowe właściwości kontenera oraz zdarzenia wdrażania (pokazane tutaj obcięte):

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
Dowiedz się, jak [rozwiązywać typowe problemy z kontenerem i wdrażaniem](container-instances-troubleshooting.md) Azure Container Instances.

Dowiedz się, jak wysyłać dane dzienników i zdarzeń dla grup kontenerów [do Azure Monitor dzienników.](container-instances-log-analytics.md)

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
