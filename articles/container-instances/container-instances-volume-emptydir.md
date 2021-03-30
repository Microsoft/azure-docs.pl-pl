---
title: Zainstaluj wolumin emptyDir do grupy kontenerów
description: Dowiedz się, jak zainstalować wolumin emptyDir, aby udostępnić dane między kontenerami w grupie kontenerów w Azure Container Instances
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77117736"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Zainstaluj wolumin emptyDir w Azure Container Instances

Dowiedz się, jak zainstalować wolumin *emptyDir* , aby udostępnić dane między kontenerami w grupie kontenerów w Azure Container Instances. Używaj woluminów *emptyDir* jako tymczasowych pamięci podręcznych dla obciążeń kontenerów.

> [!NOTE]
> Instalowanie woluminu *emptyDir* jest obecnie ograniczone do kontenerów systemu Linux. Gdy pracujemy nad przełączeniem wszystkich funkcji do kontenerów systemu Windows, w [przeglądzie](container-instances-overview.md#linux-and-windows-containers)można znaleźć bieżące różnice między platformami.

## <a name="emptydir-volume"></a>wolumin emptyDir

Wolumin *emptyDir* udostępnia zapisywalny katalog dla każdego kontenera w grupie kontenerów. Kontenery w grupie mogą odczytywać i zapisywać te same pliki w woluminie i mogą być instalowane przy użyciu tych samych lub różnych ścieżek w każdym kontenerze.

Przykład użycia dla woluminu *emptyDir* :

* Miejsce na pliki tymczasowe
* Tworzenie punktów kontrolnych podczas długotrwałych zadań
* Przechowuj dane pobierane przez kontener przyczepek i obsługiwane przez kontener aplikacji

Dane w woluminie *emptyDir* są utrwalane przez awarie kontenera. Jednak kontenery, które są ponownie uruchamiane, nie są gwarantowane, aby zachować dane na woluminie *emptyDir* . Jeśli zatrzymasz grupę kontenerów, wolumin *emptyDir* nie zostanie utrwalony.

Maksymalny rozmiar woluminu *emptyDir* systemu Linux to 50 GB.

## <a name="mount-an-emptydir-volume"></a>Instalowanie woluminu emptyDir

Aby zainstalować wolumin emptyDir w wystąpieniu kontenera, można wdrożyć przy użyciu [szablonu Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), [pliku YAML](container-instances-reference-yaml.md)lub innych metod programistycznych do wdrażania grupy kontenerów.

Najpierw Wypełnij `volumes` tablicę w sekcji Grupa kontenerów `properties` pliku. Następnie dla każdego kontenera w grupie kontenerów, w której chcesz zainstalować wolumin *emptyDir* , Wypełnij `volumeMounts` tablicę w `properties` sekcji definicji kontenera.

Na przykład poniższy szablon Menedżer zasobów tworzy grupę kontenerów składającą się z dwóch kontenerów, z których każdy instaluje wolumin *emptyDir* :

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Aby zapoznać się z przykładami wdrożenia grupy kontenerów, zobacz [wdrażanie grupy wielokontenerowej przy użyciu szablonu Menedżer zasobów](container-instances-multi-container-group.md) i [wdrażanie grupy wielokontenera przy użyciu pliku YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zainstalować inne typy woluminów w Azure Container Instances:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Zainstaluj wolumin gitRepo w Azure Container Instances](container-instances-volume-gitrepo.md)
* [Zainstaluj wolumin tajny w Azure Container Instances](container-instances-volume-secret.md)