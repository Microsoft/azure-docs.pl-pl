---
title: Zasady ponownego uruchamiania dla zadań uruchamianych raz
description: Dowiedz się, jak używać Azure Container Instances do wykonywania zadań uruchamianych do ukończenia, takich jak zadania kompilacji, testowania lub renderowania obrazów.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 3bce208e3663ecfcebe520be92de3ac4443c0c8f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771159"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Uruchamianie zadań konteneryzowanych przy użyciu zasad ponownego uruchamiania

Łatwość i szybkość wdrażania kontenerów w usłudze Azure Container Instances zapewnia atrakcyjną platformę do wykonywania jednorazowych zadań, takich jak kompilacje, testy i renderowanie obrazów w wystąpieniu kontenera.

Dzięki możliwym do skonfigurowania zasadom ponownego uruchamiania możesz określić, aby Twoje kontenery zatrzymały się po zakończeniu wykonywania procesów. Ponieważ opłaty za wystąpienia kontenerów są naliczane za sekundę, rozliczeniu podlegają tylko zasoby obliczeniowe używane wtedy, kiedy działa kontener wykonujący zadanie.

Przykłady przedstawione w tym artykule korzystają z interfejsu wiersza polecenia platformy Azure. Musisz mieć zainstalowany lokalnie interfejs wiersza polecenia platformy Azure w wersji 2.0.21 lub większej [albo][azure-cli-install]użyć interfejsu wiersza polecenia [w Azure Cloud Shell.](../cloud-shell/overview.md)

## <a name="container-restart-policy"></a>Zasady ponownego uruchamiania kontenera

Podczas tworzenia grupy [kontenerów w](container-instances-container-groups.md) Azure Container Instances można określić jedno z trzech ustawień zasad ponownego uruchamiania.

| Zasady ponownego uruchamiania   | Opis |
| ---------------- | :---------- |
| `Always` | Kontenery w grupie kontenerów są zawsze uruchamiane ponownie. Jest to **domyślne** ustawienie stosowane, gdy podczas tworzenia kontenera nie zostanie określona żadna zasada ponownego uruchamiania. |
| `Never` | Kontenery w grupie kontenerów nie są nigdy uruchamiane ponownie. Kontenery są uruchamiane co najwyżej raz. |
| `OnFailure` | Kontenery w grupie kontenerów są uruchamiane ponownie tylko wtedy, gdy proces wykonywany w kontenerze zakończy się niepowodzeniem (zostanie przerwany z kodem zakończenia różnym od zera). Kontenery są uruchamiane co najmniej raz. |

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="specify-a-restart-policy"></a>Określanie zasad ponownego uruchamiania

Sposób określania zasad ponownego uruchamiania zależy od sposobu tworzenia wystąpień kontenera, takich jak interfejs wiersza polecenia platformy Azure, Azure PowerShell poleceń cmdlet lub Azure Portal. W interfejsie wiersza polecenia platformy Azure określ `--restart-policy` parametr podczas wywołania polecenia az container [create.][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Przykład uruchamiania do ukończenia

Aby zobaczyć działanie zasad ponownego uruchamiania, utwórz wystąpienie kontenera na pomocą obrazu [microsoft aci-wordcount][aci-wordcount-image] i określ `OnFailure` zasady ponownego uruchamiania. Ten przykładowy kontener uruchamia skrypt języka Python, który domyślnie analizuje tekst [hamleta](http://shakespeare.mit.edu/hamlet/full.html)Trzmieniaka, zapisuje 10 najczęściej występujących wyrazów w danych STDOUT, a następnie kończy działanie.

Uruchom przykładowy kontener za pomocą następującego [polecenia az container create:][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Usługa Azure Container Instances uruchamia kontener, a następnie zatrzymuje go, gdy znajdująca się w nim aplikacja (lub, jak w tym przypadku, skrypt) kończy działanie. Gdy Azure Container Instances kontener, którego zasady ponownego uruchamiania to lub , stan kontenera jest ustawiony `Never` `OnFailure` na **Zakończony.** Stan kontenera można sprawdzić za pomocą polecenia [az container show:][az-container-show]

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Przykładowe dane wyjściowe:

```bash
"Terminated"
```

Gdy stan przykładowego kontenera będzie mieć stan *Terminated (Zakończony),* możesz wyświetlić dane wyjściowe jego zadania, wyświetlając dzienniki kontenera. Uruchom polecenie [az container logs,][az-container-logs] aby wyświetlić dane wyjściowe skryptu:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Dane wyjściowe:

```bash
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

W tym przykładzie pokazano dane wyjściowe, które skrypt wysłał do stdout. Konteneryzowane zadania mogą jednak zamiast tego zapisywać dane wyjściowe w magazynie trwałym w celu późniejszego pobrania. Na przykład do udziału [plików platformy Azure.](./container-instances-volume-azure-files.md)

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie [wsadowe](container-instances-environment-variables.md) dużego zestawu danych z kilkoma kontenerami, mogą korzystać z niestandardowych zmiennych środowiskowych lub [wierszy poleceń](container-instances-start-command.md) w czasie wykonywania.

Aby uzyskać szczegółowe informacje na temat sposobu utrwalania danych wyjściowych kontenerów uruchamianych do ukończenia, zobacz [Instalowanie udziału plików platformy Azure](./container-instances-volume-azure-files.md)za pomocą Azure Container Instances .

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
