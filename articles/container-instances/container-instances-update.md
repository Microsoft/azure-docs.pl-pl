---
title: Aktualizuj grupę kontenerów
description: Dowiedz się, jak aktualizować uruchomione kontenery w grupach kontenerów Azure Container Instances.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: fb31eeda83532c408a303e879439006bcd7d4e45
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200652"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizowanie kontenerów w usłudze Azure Container Instances

Podczas normalnego działania wystąpień kontenera może być konieczne zaktualizowanie uruchomionych kontenerów w [grupie kontenerów](./container-instances-container-groups.md). Na przykład możesz chcieć zaktualizować właściwość, taką jak wersja obrazu, nazwa DNS lub zmienna środowiskowa, lub odświeżyć właściwość w kontenerze, którego aplikacja uległa awarii.

Zaktualizuj kontenery w uruchomionej grupie kontenerów przez ponowne wdrożenie istniejącej grupy z co najmniej jedną zmodyfikowaną właściwością. Podczas aktualizowania grupy kontenerów wszystkie uruchomione kontenery w grupie są uruchamiane ponownie w miejscu, zazwyczaj na tym samym hoście kontenera.

> [!NOTE]
> Nie można zaktualizować grup kontenerów zakończonych lub usuniętych. Po zakończeniu grupy kontenerów (w stanie powodzenie lub niepowodzenie) lub została usunięta, należy wdrożyć grupę jako nową. Zobacz inne [ograniczenia](#limitations).

## <a name="update-a-container-group"></a>Aktualizowanie grupy kontenerów

Aby zaktualizować istniejącą grupę kontenerów:

* Wydaj polecenie Create (lub użyj Azure Portal) i określ nazwę istniejącej grupy 
* Zmodyfikuj lub Dodaj co najmniej jedną właściwość grupy, która obsługuje aktualizację podczas ponownego wdrażania. Niektóre właściwości [nie obsługują aktualizacji](#properties-that-require-container-delete).
* Ustaw inne właściwości o podane wcześniej wartości. Jeśli nie ustawisz wartości dla właściwości, zostanie ona przywrócona do wartości domyślnej.

> [!TIP]
> [Plik YAML](./container-instances-container-groups.md#deployment) pomaga zachować konfigurację wdrożenia grupy kontenerów i zapewnia punkt wyjścia do wdrożenia zaktualizowanej grupy. Jeśli do utworzenia grupy użyto innej metody, można wyeksportować konfigurację do YAML przy użyciu polecenia [AZ Container Export][az-container-export]. 

### <a name="example"></a>Przykład

Poniższy przykład interfejsu wiersza polecenia platformy Azure aktualizuje grupę kontenerów za pomocą nowej etykiety nazwy DNS. Ponieważ właściwość etykieta nazwy DNS grupy jest taka, którą można zaktualizować, Grupa kontenerów jest ponownie wdrażana, a jej kontenery zostały uruchomione od początku.

Początkowe wdrożenie z etykietą nazw DNS moja *aplikacja — przemieszczanie*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Zaktualizuj grupę kontenerów za pomocą nowej etykiety nazwy DNS, *aplikacji* i ustaw pozostałe właściwości z użytymi wcześniej wartościami:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Korzyści z aktualizacji

Główną zaletą aktualizowania istniejącej grupy kontenerów jest szybsze wdrażanie. Po ponownym wdrożeniu istniejącej grupy kontenerów warstwy obrazu kontenera są ściągane z pamięci podręcznej w poprzednim wdrożeniu. Zamiast ściągania wszystkich warstw obrazu z rejestru w taki sam sposób, jak w przypadku nowych wdrożeń, pobierane są tylko zmodyfikowane warstwy (jeśli istnieją).

Aplikacje oparte na większych obrazach kontenerów, takich jak Windows Server Core, mogą mieć znaczący wpływ na szybkość wdrażania podczas aktualizacji, a nie do usuwania i wdrażania nowych.

## <a name="limitations"></a>Ograniczenia

* Nie wszystkie właściwości grupy kontenerów obsługują aktualizacje. Aby zmienić niektóre właściwości grupy kontenerów, należy najpierw usunąć, a następnie wdrożyć ponownie grupę. Zobacz [właściwości, które wymagają usunięcia kontenera](#properties-that-require-container-delete).
* Wszystkie kontenery w grupie kontenerów są uruchamiane ponownie po zaktualizowaniu grupy kontenerów. Nie można wykonać aktualizacji ani ponownego uruchomienia w miejscu dla określonego kontenera w grupie wielokontenerowej.
* Adres IP grupy kontenerów jest zwykle zachowywany między aktualizacjami, ale nie ma gwarancji, że pozostaje taki sam. Dopóki Grupa kontenerów zostanie wdrożona na tym samym hoście podstawowym, Grupa kontenerów zachowuje swój adres IP. Chociaż rzadko istnieją pewne zdarzenia wewnętrzne platformy Azure, które mogą spowodować ponowne wdrożenie na innym hoście. Aby wyeliminować ten problem, zalecamy użycie etykiety nazwy DNS dla wystąpień kontenerów.
* Nie można zaktualizować grup kontenerów zakończonych lub usuniętych. Po zatrzymaniu grupy kontenerów (w stanie *przerwania* ) lub usunięciu grupa zostanie wdrożona jako nowa.

## <a name="properties-that-require-container-delete"></a>Właściwości wymagające usunięcia kontenera

Nie można zaktualizować wszystkich właściwości grupy kontenerów. Na przykład aby zmienić zasady ponownego uruchamiania kontenera, należy najpierw usunąć grupę kontenerów, a następnie utworzyć ją ponownie.

Zmiany tych właściwości wymagają usunięcia grupy kontenerów przed ponownem wdrożeniem:

* Typ systemu operacyjnego
* Zasoby procesora CPU, pamięci lub procesora GPU
* Zasady ponownego uruchamiania
* Profil sieci

Gdy usuniesz grupę kontenerów i utworzysz ją ponownie, nie jest to "redeployed", ale utworzona nowa. Wszystkie warstwy obrazu są ściągane z rejestru, a nie z pamięci podręcznej w poprzednim wdrożeniu. Adres IP kontenera może także ulec zmianie z powodu wdrożenia na innym hoście podstawowym.

## <a name="next-steps"></a>Następne kroki

Wspomniano kilka razy w tym artykule **Grupa kontenerów**. Każdy kontener w Azure Container Instances jest wdrażany w grupie kontenerów, a grupy kontenerów mogą zawierać więcej niż jeden kontener.

[Grupy kontenerów w usłudze Azure Container Instances](./container-instances-container-groups.md)

[Wdrażanie grupy z wieloma kontenerami](container-instances-multi-container-group.md)

[Ręczne zatrzymywanie lub uruchamianie kontenerów w usłudze Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
