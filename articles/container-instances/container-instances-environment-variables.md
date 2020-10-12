---
title: Ustawianie zmiennych środowiskowych w wystąpieniu kontenera
description: Dowiedz się, jak ustawiać zmienne środowiskowe w kontenerach uruchamianych w Azure Container Instances
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 92ae59f69b7cb43fee1d3ce8190a85fc20a11f60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169769"
---
# <a name="set-environment-variables-in-container-instances"></a>Ustawianie zmiennych środowiskowych w wystąpieniach kontenerów

Ustawienie zmiennych środowiskowych w wystąpieniach kontenerów umożliwia udostępnienie dynamicznej konfiguracji aplikacji lub skryptu uruchamianego przez kontener. Jest to podobne do `--env` argumentu wiersza polecenia do `docker run` . 

Aby ustawić zmienne środowiskowe w kontenerze, należy je określić podczas tworzenia wystąpienia kontenera. W tym artykule przedstawiono przykłady ustawiania zmiennych środowiskowych podczas uruchamiania kontenera za pomocą [interfejsu wiersza polecenia platformy Azure](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)i [Azure Portal](#azure-portal-example). 

Na przykład jeśli zostanie uruchomiony obraz kontenera Microsoft [ACI-WORDCOUNT][aci-wordcount] , można zmodyfikować jego zachowanie, określając następujące zmienne środowiskowe:

*NumWords*: liczba wyrazów wysyłanych do stdout.

*MinLength*: minimalna liczba znaków w wyrazie do zliczenia. Wyższa liczba ignoruje typowe słowa, takie jak "of" i ".".

Jeśli musisz przekazać klucze tajne jako zmienne środowiskowe, Azure Container Instances obsługuje [bezpieczne wartości](#secure-values) dla kontenerów systemów Windows i Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Aby wyświetlić domyślne dane wyjściowe kontenera [ACI-WORDCOUNT][aci-wordcount] , uruchom je najpierw za pomocą polecenia [AZ Container Create][az-container-create] (nie określono zmiennych środowiskowych):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Aby zmodyfikować dane wyjściowe, należy uruchomić drugi kontener z `--environment-variables` dodanym argumentem, określając wartości zmiennych *NumWords* i *minLength* . (W tym przykładzie przyjęto założenie, że interfejs wiersza polecenia jest uruchamiany w bash Shell lub Azure Cloud Shell. W przypadku korzystania z wiersza polecenia systemu Windows należy określić zmienne z podwójnymi cudzysłowami, takimi jak `--environment-variables "NumWords"="5" "MinLength"="8"` .).

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Gdy stan obu kontenerów jest wyświetlany jako *zakończony* (Użyj polecenia [AZ Container show][az-container-show] by Check State), Wyświetl jego dzienniki za pomocą polecenia [AZ Container Logs][az-container-logs] , aby wyświetlić dane wyjściowe.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe kontenerów pokazują, jak zmodyfikowano zachowanie skryptu drugiego kontenera przez ustawienie zmiennych środowiskowych.

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

## <a name="azure-powershell-example"></a>Przykład Azure PowerShell

Ustawianie zmiennych środowiskowych w programie PowerShell jest podobne do interfejsu wiersza polecenia, ale przy użyciu `-EnvironmentVariable` argumentu wiersz poleceń.

Najpierw uruchom kontener [ACI-WORDCOUNT][aci-wordcount] w konfiguracji domyślnej przy użyciu tego nowego polecenia [-AzContainerGroup][new-Azcontainergroup] :

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Teraz uruchom następujące polecenie [New-AzContainerGroup][new-Azcontainergroup] . Ten element określa zmienne środowiskowe *NumWords* i *minLength* po wypełnieniu zmiennej tablicowej `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Gdy stan obu kontenerów jest *zakończony* (Użyj polecenia [Get-AzContainerInstanceLog][azure-instance-log] , aby sprawdzić stan), ściągnij swoje dzienniki za pomocą poleceń [Get-AzContainerInstanceLog][azure-instance-log] .

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Dane wyjściowe dla każdego kontenera przedstawiają sposób modyfikacji skryptu uruchamianego przez kontener przez ustawienie zmiennych środowiskowych.

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

## <a name="azure-portal-example"></a>Przykład Azure Portal

Aby ustawić zmienne środowiskowe podczas uruchamiania kontenera w Azure Portal, należy je określić na stronie **Zaawansowane** podczas tworzenia kontenera.

1. Na stronie **Zaawansowane** Ustaw **zasady ponownego uruchamiania** na *Niepowodzenie*
2. W obszarze **zmienne środowiskowe**wprowadź `NumWords` wartość `5` dla pierwszej zmiennej, a `MinLength` następnie wprowadź wartość `8` dla drugiej zmiennej. 
1. Wybierz pozycję **Przegląd + Utwórz** , aby sprawdzić, a następnie wdrożyć kontener.

![Strona portalu przedstawiająca przycisk włączania zmiennych środowiskowych i pól tekstowych][portal-env-vars-01]

Aby wyświetlić dzienniki kontenera, w obszarze **Ustawienia** wybierz **kontenery**, a następnie pozycję **dzienniki**. Podobnie jak w przypadku danych wyjściowych przedstawionych w poprzednich sekcjach interfejsu wiersza polecenia i programu PowerShell, można zobaczyć, jak zachowanie skryptu zostało zmodyfikowane przez zmienne środowiskowe. Wyświetlane są tylko pięć słów, z których każdy ma minimalną długość osiem znaków.

![Portal przedstawiający dane wyjściowe dziennika kontenera][portal-env-vars-02]

## <a name="secure-values"></a>Bezpieczne wartości

Obiekty z bezpiecznymi wartościami są przeznaczone do przechowywania informacji poufnych, takich jak hasła lub klucze aplikacji. Używanie bezpiecznych wartości zmiennych środowiskowych jest bezpieczniejsze i bardziej elastyczne niż dołączanie go do obrazu kontenera. Innym rozwiązaniem jest użycie woluminów tajnych, opisanych w artykule [Instalowanie woluminu tajnego w Azure Container Instances](container-instances-volume-secret.md).

Zmienne środowiskowe z bezpiecznymi wartościami nie są widoczne we właściwościach kontenera — ich wartości są dostępne tylko z poziomu kontenera. Na przykład właściwości kontenera wyświetlane w Azure Portal lub interfejs wiersza polecenia platformy Azure wyświetlają tylko nazwę bezpiecznej zmiennej, a nie jej wartość.

Ustaw bezpieczną zmienną środowiskową, określając `secureValue` właściwość zamiast zwykłych `value` dla typu zmiennej. Dwie zmienne zdefiniowane w następujących YAML przedstawiają dwa typy zmiennych.

### <a name="yaml-deployment"></a>Wdrożenie YAML

Utwórz `secure-env.yaml` plik z następującym fragmentem kodu.

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

Uruchom następujące polecenie, aby wdrożyć grupę kontenerów z YAML (w razie potrzeby dostosować nazwę grupy zasobów):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Weryfikuj zmienne środowiskowe

Uruchom polecenie [AZ Container show][az-container-show] , aby zbadać zmienne środowiskowe kontenera:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

Odpowiedź JSON pokazuje zarówno klucz, jak i wartość niezabezpieczonej zmiennej środowiskowej, ale tylko nazwę zmiennej bezpiecznego środowiska:

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

Za pomocą polecenia [AZ Container exec][az-container-exec] , które umożliwia wykonywanie polecenia w działającym kontenerze, można sprawdzić, czy została ustawiona zmienna środowiskowa Secure. Uruchom następujące polecenie, aby uruchomić interaktywną sesję bash w kontenerze:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Po otwarciu interaktywnej powłoki w kontenerze możesz uzyskać dostęp do `SECRET` wartości zmiennej:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie wsadowe dużego zestawu danych z kilkoma kontenerami, mogą korzystać ze niestandardowych zmiennych środowiskowych w czasie wykonywania. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie zadań kontenera z zasadami ponownego uruchamiania](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
