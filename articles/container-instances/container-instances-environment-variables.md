---
title: Ustawianie zmiennych środowiskowych w wystąpieniu kontenera
description: Dowiedz się, jak ustawić zmienne środowiskowe w kontenerach uruchamianych w programie Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 9d95ee3d64460aa5e11f450c9e582cdc0de4f0ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790801"
---
# <a name="set-environment-variables-in-container-instances"></a>Ustawianie zmiennych środowiskowych w wystąpieniach kontenerów

Ustawienie zmiennych środowiskowych w wystąpieniach kontenerów umożliwia udostępnienie dynamicznej konfiguracji aplikacji lub skryptu uruchamianego przez kontener. Jest to podobne do `--env` argumentu wiersza polecenia do `docker run` . 

Aby ustawić zmienne środowiskowe w kontenerze, określ je podczas tworzenia wystąpienia kontenera. W tym artykule przedstawiono przykłady ustawiania zmiennych środowiskowych podczas uruchamiania kontenera przy użyciu interfejsu wiersza polecenia platformy [Azure,](#azure-cli-example)interfejsu [Azure PowerShell](#azure-powershell-example)i [Azure Portal](#azure-portal-example). 

Jeśli na przykład uruchamiasz obraz kontenera [aci-wordcount][aci-wordcount] firmy Microsoft, możesz zmodyfikować jego zachowanie, określając następujące zmienne środowiskowe:

*NumWords:* liczba wyrazów wysłanych do stdout.

*MinLength:* minimalna liczba znaków w wyrazie do zliczenia. Większa liczba ignoruje typowe słowa, takie jak "of" i "the".

Jeśli musisz przekazać wpisy tajne jako zmienne środowiskowe, Azure Container Instances bezpieczne [wartości](#secure-values) dla kontenerów systemów Windows i Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Aby wyświetlić domyślne dane wyjściowe kontenera [aci-wordcount,][aci-wordcount] uruchom je najpierw za pomocą tego [polecenia az container create][az-container-create] (nie określono zmiennych środowiskowych):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Aby zmodyfikować dane wyjściowe, uruchom drugi kontener z dodanym argumentem, określając wartości zmiennych `--environment-variables` *NumWords* i *MinLength.* (W tym przykładzie przyjęto założenie, że interfejs wiersza polecenia jest uruchomiony w powłoce Bash lub Azure Cloud Shell. Jeśli używasz wiersza polecenia systemu Windows, określ zmienne z cudzysłowami, takimi jak `--environment-variables "NumWords"="5" "MinLength"="8"` ).)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Gdy stan obu kontenerów będzie wyświetlany jako *Terminated* (Zakończ) (użyj az [container show,][az-container-show] aby sprawdzić stan), wyświetl swoje dzienniki za pomocą az [container logs,][az-container-logs] aby wyświetlić dane wyjściowe.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe kontenerów pokazują, jak zmodyfikowaliśmy zachowanie skryptu drugiego kontenera przez ustawienie zmiennych środowiskowych.

**mycontainer1**
```output
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell przykład

Ustawianie zmiennych środowiskowych w programie PowerShell jest podobne do interfejsu wiersza polecenia, ale używa `-EnvironmentVariable` argumentu wiersza polecenia.

Najpierw uruchom kontener [aci-wordcount][aci-wordcount] w konfiguracji domyślnej za pomocą tego polecenia [New-AzContainerGroup:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Teraz uruchom następujące polecenie [New-AzContainerGroup.][new-Azcontainergroup] Ten element określa zmienne *środowiskowe NumWords* i *MinLength* po wypełnieniu zmiennej tablicowej `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Gdy stan obu kontenerów zostanie zatrzymany *(sprawdź* stan za pomocą polecenia [Get-AzContainerInstanceLog),][azure-instance-log] ściągnij dzienniki za pomocą polecenia [Get-AzContainerInstanceLog.][azure-instance-log]

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Dane wyjściowe dla każdego kontenera pokazują, jak zmodyfikowaliśmy skrypt uruchamiany przez kontener, ustawiając zmienne środowiskowe.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure Portal przykład

Aby ustawić zmienne środowiskowe podczas uruchamiania kontenera w Azure Portal, określ  je na stronie Zaawansowane podczas tworzenia kontenera.

1. Na stronie **Zaawansowane** ustaw zasady ponownego **uruchamiania na** Wartość *Wł.*
2. W **obszarze Zmienne środowiskowe** wprowadź wartość z wartością dla pierwszej zmiennej, a wprowadź wartość z wartością dla `NumWords` `5` `MinLength` `8` drugiej zmiennej. 
1. Wybierz **pozycję Przejrzyj i utwórz,** aby zweryfikować, a następnie wdrożyć kontener.

![Strona portalu przedstawiająca zmienną środowiskową Przycisk Włącz i pola tekstowe][portal-env-vars-01]

Aby wyświetlić dzienniki kontenera, w obszarze Ustawienia **wybierz pozycję** **Kontenery**, a następnie pozycję **Dzienniki.** Podobnie jak w przypadku danych wyjściowych poprzednich sekcji interfejsu wiersza polecenia i programu PowerShell, możesz zobaczyć, jak zachowanie skryptu zostało zmodyfikowane przez zmienne środowiskowe. Wyświetlanych jest tylko pięć słów, z których każdy ma minimalną długość ośmiu znaków.

![Portal przedstawiający dane wyjściowe dziennika kontenera][portal-env-vars-02]

## <a name="secure-values"></a>Bezpieczne wartości

Obiekty z bezpiecznymi wartościami są przeznaczone do przechowywania poufnych informacji, takich jak hasła lub klucze dla aplikacji. Korzystanie z bezpiecznych wartości dla zmiennych środowiskowych jest bezpieczniejsze i bardziej elastyczne niż ich stosowanie w obrazie kontenera. Inną opcją jest użycie woluminów tajnych opisanych w tece [Mount a secret volume in Azure Container Instances](container-instances-volume-secret.md).

Zmienne środowiskowe z bezpiecznymi wartościami nie są widoczne we właściwościach kontenera — do ich wartości można uzyskać dostęp tylko z poziomu kontenera. Na przykład właściwości kontenera wyświetlane w interfejsie Azure Portal lub interfejsie wiersza polecenia platformy Azure wyświetlają tylko nazwę bezpiecznej zmiennej, a nie jej wartość.

Ustaw bezpieczną zmienną środowiskową, określając właściwość zamiast zwykłej dla `secureValue` `value` typu zmiennej. Dwie zmienne zdefiniowane w poniższym pliku YAML pokazują dwa typy zmiennych.

### <a name="yaml-deployment"></a>Wdrażanie YAML

Utwórz plik `secure-env.yaml` z następującym fragmentem kodu.

```yaml
apiVersion: 2019-12-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Uruchom następujące polecenie, aby wdrożyć grupę kontenerów przy użyciu pliku YAML (w razie potrzeby dostosuj nazwę grupy zasobów):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Weryfikowanie zmiennych środowiskowych

Uruchom polecenie [az container show,][az-container-show] aby odpytywać zmienne środowiskowe kontenera:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

Odpowiedź JSON zawiera zarówno klucz, jak i wartość niezabezpieczonych zmiennych środowiskowych, ale tylko nazwę bezpiecznej zmiennej środowiskowej:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Za pomocą [polecenia az container exec,][az-container-exec] które umożliwia wykonywanie polecenia w uruchomionym kontenerze, możesz sprawdzić, czy ustawiono bezpieczną zmienną środowiskową. Uruchom następujące polecenie, aby uruchomić interaktywną sesję powłoki bash w kontenerze:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Po otwarciu interaktywnej powłoki w kontenerze możesz uzyskać dostęp do `SECRET` wartości zmiennej:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie wsadowe dużego zestawu danych z kilkoma kontenerami, mogą korzystać z niestandardowych zmiennych środowiskowych w czasie wykonywania. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie konteneryzowanych zadań przy użyciu zasad ponownego uruchamiania.](container-instances-restart-policy.md)

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
