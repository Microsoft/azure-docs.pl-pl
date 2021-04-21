---
title: Aktualizowanie grupy kontenerów
description: Dowiedz się, jak zaktualizować uruchomione kontenery w Azure Container Instances grup kontenerów.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cbb2e830490d2591645b8156ee830856da0f9049
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786967"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizowanie kontenerów w usłudze Azure Container Instances

Podczas normalnego działania wystąpień kontenerów może być konieczne zaktualizowanie uruchomionych kontenerów w grupie [kontenerów](./container-instances-container-groups.md). Na przykład możesz zaktualizować właściwość, taką jak wersja obrazu, nazwa DNS lub zmienna środowiskowa, albo odświeżyć właściwość w kontenerze, którego aplikacja ulega awarii.

Zaktualizuj kontenery w uruchomionej grupie kontenerów, ponownie wdając istniejącą grupę z co najmniej jedną zmodyfikowaną właściwością. Po zaktualizowaniu grupy kontenerów wszystkie uruchomione kontenery w grupie są ponownie uruchamiane w miejscu, zazwyczaj na tym samym hoście kontenera źródłowego.

> [!NOTE]
> Nie można zaktualizować zakończonych lub usuniętych grup kontenerów. Po zakończeniu pracy grupy kontenerów (jest ona w stanie Powodzenie lub Niepowodzenie) lub została usunięta, należy wdrożyć grupę jako nową. Zobacz inne [ograniczenia.](#limitations)

## <a name="update-a-container-group"></a>Aktualizowanie grupy kontenerów

Aby zaktualizować istniejącą grupę kontenerów:

* Wydaj polecenie create (lub użyj Azure Portal) i określ nazwę istniejącej grupy 
* Zmodyfikuj lub dodaj co najmniej jedną właściwość grupy, która obsługuje aktualizację podczas ponownego lodowania. Niektóre właściwości [nie obsługują aktualizacji](#properties-that-require-container-delete).
* Ustaw inne właściwości przy użyciu podanych wcześniej wartości. Jeśli nie ustawisz wartości właściwości, zostanie przywrócona jej wartość domyślna.

> [!TIP]
> Plik [YAML](./container-instances-container-groups.md#deployment) pomaga zachować konfigurację wdrożenia grupy kontenerów i stanowi punkt wyjścia do wdrożenia zaktualizowanej grupy. Jeśli do utworzenia grupy została użyta inna metoda, możesz wyeksportować konfigurację do pliku YAML przy użyciu [narzędzia az container export][az-container-export], 

### <a name="example"></a>Przykład

Poniższy przykład interfejsu wiersza polecenia platformy Azure aktualizuje grupę kontenerów przy użyciu nowej etykiety nazwy DNS. Ponieważ właściwość etykiety nazwy DNS grupy jest taka, która może zostać zaktualizowana, grupa kontenerów jest ponownie wdowy i jej kontenery są ponownie uruchomione.

Początkowe wdrożenie z etykietą nazwy DNS *myapplication-staging:*

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Zaktualizuj grupę kontenerów przy użyciu nowej etykiety nazwy DNS *myapplication* i ustaw pozostałe właściwości przy użyciu wartości użytych wcześniej:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Korzyści związane z aktualizacją

Główną zaletą aktualizowania istniejącej grupy kontenerów jest szybsze wdrażanie. Podczas ponownego wdrażania istniejącej grupy kontenerów jej warstwy obrazu kontenera są ściągane z warstw buforowanych przez poprzednie wdrożenie. Zamiast ściągać wszystkie warstwy obrazu od nowa z rejestru, tak jak w przypadku nowych wdrożeń, ściągane są tylko zmodyfikowane warstwy (jeśli istnieją).

Aplikacje oparte na większych obrazach kontenerów, takie jak Windows Server Core, mogą zobaczyć znaczną poprawę szybkości wdrażania podczas aktualizacji zamiast usuwania i wdrażania nowych.

## <a name="limitations"></a>Ograniczenia

* Nie wszystkie właściwości grupy kontenerów obsługują aktualizacje. Aby zmienić niektóre właściwości grupy kontenerów, należy najpierw usunąć, a następnie ponownie wdokreślić grupę. Zobacz [Właściwości, które wymagają usunięcia kontenera.](#properties-that-require-container-delete)
* Wszystkie kontenery w grupie kontenerów są ponownie uruchomione po zaktualizowaniu grupy kontenerów. Nie można przeprowadzić aktualizacji ani ponownego uruchomienia w miejscu określonego kontenera w grupie z wieloma kontenerami.
* Adres IP grupy kontenerów jest zwykle zachowywany między aktualizacjami, ale nie ma gwarancji, że pozostanie taki sam. Tak długo, jak grupa kontenerów jest wdrażana na tym samym hoście podstawowym, grupa kontenerów zachowuje swój adres IP. Chociaż zdarza się to rzadko, istnieją pewne zdarzenia wewnętrzne platformy Azure, które mogą spowodować ponowne ich ponowne hostowanie. Aby rozwiązać ten problem, zalecamy użycie etykiety nazwy DNS dla wystąpień kontenera.
* Nie można zaktualizować zakończonych lub usuniętych grup kontenerów. Po zatrzymaniu lub usunięciu grupy  kontenerów (jest ona w stanie Zakończono) grupa jest wdrażana jako nowa.

## <a name="properties-that-require-container-delete"></a>Właściwości, które wymagają usunięcia kontenera

Nie wszystkie właściwości grupy kontenerów można zaktualizować. Aby na przykład zmienić zasady ponownego uruchamiania kontenera, należy najpierw usunąć grupę kontenerów, a następnie utworzyć ją ponownie.

Zmiany tych właściwości wymagają usunięcia grupy kontenerów przed jej ponownegolodowania:

* Typ systemu operacyjnego
* Zasoby procesora CPU, pamięci lub procesora GPU
* Zasady ponownego uruchamiania
* Profil sieci

Usunięcie grupy kontenerów i jej ponowne utworzenie nie zostanie "ponownie wdeploowane", ale zostanie utworzona nowa. Wszystkie warstwy obrazu są ściągane od nowa z rejestru, a nie z tych buforowanych przez poprzednie wdrożenie. Adres IP kontenera może również ulec zmianie z powodu wdrożenia na innym bazowym hoście.

## <a name="next-steps"></a>Następne kroki

Wspomniano kilka razy w tym artykule, że jest to **grupa kontenerów**. Każdy kontener w Azure Container Instances jest wdrażany w grupie kontenerów, a grupy kontenerów mogą zawierać więcej niż jeden kontener.

[Grupy kontenerów w usłudze Azure Container Instances](./container-instances-container-groups.md)

[Wdrażanie grupy z wieloma kontenerami](container-instances-multi-container-group.md)

[Ręczne zatrzymywanie lub uruchamianie kontenerów w usłudze Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az_container_export
