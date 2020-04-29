---
title: Zasady ponownego uruchamiania dla zadań uruchamianych jednokrotnie
description: Dowiedz się, w jaki sposób używać Azure Container Instances do wykonywania zadań, które są wykonywane do ukończenia, na przykład w ramach zadań kompilowania, testowania lub renderowania obrazu.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 8ef4ef228038242f53abc8041470f7f596ab1157
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80131494"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Uruchamianie zadań kontenera z zasadami ponownego uruchamiania

Łatwość i szybkość wdrażania kontenerów w usłudze Azure Container Instances zapewnia atrakcyjną platformę do wykonywania jednorazowych zadań, takich jak kompilacje, testy i renderowanie obrazów w wystąpieniu kontenera.

Dzięki możliwym do skonfigurowania zasadom ponownego uruchamiania możesz określić, aby Twoje kontenery zatrzymały się po zakończeniu wykonywania procesów. Ponieważ opłaty za wystąpienia kontenerów są naliczane za sekundę, rozliczeniu podlegają tylko zasoby obliczeniowe używane, kiedy działa kontener wykonujący zadanie.

Przykłady przedstawione w tym artykule korzystają z interfejsu wiersza polecenia platformy Azure. Musisz mieć [zainstalowany][azure-cli-install]interfejs wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej lub użyć interfejsu wiersza polecenia w [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Zasady ponownego uruchamiania kontenera

Podczas tworzenia [grupy kontenerów](container-instances-container-groups.md) w Azure Container Instances można określić jedno z trzech ustawień zasad ponownego uruchomienia.

| Zasady ponownego uruchamiania   | Opis |
| ---------------- | :---------- |
| `Always` | Kontenery w grupie kontenerów są zawsze uruchamiane ponownie. Jest to **domyślne** ustawienie stosowane, gdy podczas tworzenia kontenera nie zostanie określona żadna zasada ponownego uruchamiania. |
| `Never` | Kontenery w grupie kontenerów nigdy nie są uruchamiane ponownie. Kontenery są uruchamiane co najwyżej raz. |
| `OnFailure` | Kontenery w grupie kontenerów są uruchamiane ponownie tylko wtedy, gdy proces wykonywany w kontenerze zakończy się niepowodzeniem (zostanie przerwany z kodem zakończenia różnym od zera). Kontenery są uruchamiane co najmniej raz. |

## <a name="specify-a-restart-policy"></a>Określ zasady ponownego uruchamiania

Sposób określania zasad ponownego uruchamiania zależy od sposobu tworzenia wystąpień kontenera, takich jak w przypadku interfejsu wiersza polecenia platformy Azure, poleceń cmdlet Azure PowerShell lub Azure Portal. W interfejsie wiersza polecenia platformy Azure `--restart-policy` określ parametr podczas wywoływania polecenia [AZ Container Create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Przykład przebiegu do ukończenia

Aby wyświetlić zasady ponownego uruchamiania w działaniu, Utwórz wystąpienie kontenera z obrazu Microsoft [ACI-WORDCOUNT][aci-wordcount-image] i określ zasady `OnFailure` ponownego uruchamiania. Ten przykładowy kontener uruchamia skrypt języka Python, który domyślnie analizuje tekst [Hamlet](http://shakespeare.mit.edu/hamlet/full.html)Szekspira, zapisuje 10 najpopularniejszych słów do stdout, a następnie kończy pracę.

Uruchom przykładowy kontener za pomocą następującego polecenia [AZ Container Create][az-container-create] :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Usługa Azure Container Instances uruchamia kontener, a następnie zatrzymuje go, gdy znajdująca się w nim aplikacja (lub, jak w tym przypadku, skrypt) kończy działanie. Gdy Azure Container Instances zatrzyma kontener, którego zasady ponownego `Never` uruchamiania `OnFailure`to lub, stan kontenera to **zakończony**. Stan kontenera można sprawdzić za pomocą polecenia [AZ Container show][az-container-show] :

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

Po zakończeniu wyświetlania przykładowego stanu kontenera można zobaczyć dane wyjściowe *zadania, wyświetlając*dzienniki kontenerów. Uruchom polecenie [AZ Container Logs][az-container-logs] , aby wyświetlić dane wyjściowe skryptu:

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

Ten przykład pokazuje dane wyjściowe wysyłane przez skrypt do STDOUT. Zadania kontenerów mogą jednak zapisywać dane wyjściowe do magazynu trwałego na potrzeby późniejszego pobierania. Na przykład do [udziału plików platformy Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie wsadowe dużego zestawu danych z kilkoma kontenerami, mogą korzystać z niestandardowych [zmiennych środowiskowych](container-instances-environment-variables.md) lub [wierszy poleceń](container-instances-start-command.md) w czasie wykonywania.

Aby uzyskać szczegółowe informacje na temat utrwalania danych wyjściowych kontenerów, które są uruchamiane w celu ukończenia, zobacz [Instalowanie udziału plików platformy Azure za pomocą Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
