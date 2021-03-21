---
title: Ręcznie Zatrzymaj lub Uruchom grupę kontenerów
description: Dowiedz się, jak ręcznie zatrzymać lub uruchomić grupę kontenerów w Azure Container Instances.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 1801dad463d478c754e621dad0ae9406899ae7e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198119"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Ręczne zatrzymywanie lub uruchamianie kontenerów w usłudze Azure Container Instances

Ustawienie [zasad ponownego uruchamiania](container-instances-restart-policy.md) grupy kontenerów określa sposób uruchamiania lub zatrzymywania wystąpień kontenerów domyślnie. Ustawienie domyślne można zastąpić ręcznie zatrzymywaniem lub uruchamianiem grupy kontenerów.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Stop

Ręcznie Zatrzymaj uruchomioną grupę kontenerów — na przykład przy użyciu polecenia [AZ Container Stop][az-container-stop] lub Azure Portal. W przypadku niektórych obciążeń kontenera można zatrzymać długotrwałą grupę kontenerów po upływie zdefiniowanego okresu, aby zaoszczędzić na kosztach. 

*Gdy grupa kontenerów przechodzi do stanu zatrzymanego, kończy i odtwarza wszystkie kontenery w grupie. Nie zachowuje stanu kontenera.*

Po ponownym przetworzeniu kontenerów [zasoby](container-instances-container-groups.md#resource-allocation) są cofane i rozliczane dla grupy kontenerów.

Akcja zatrzymania nie ma żadnego wpływu, jeśli grupa kontenerów została już zakończona (jest w stanie sukces lub niepowodzenie). Na przykład grupa kontenerów z zadaniami kontenera uruchamiania, które zostały pomyślnie zakończone w stanie sukces. Próby zatrzymania grupy w tym stanie nie zmieniają stanu. 

## <a name="start"></a>Rozpocznij

Po zatrzymaniu grupy kontenerów — ponieważ kontenery zostały zakończone lub ręcznie zatrzymana przez grupę — można uruchomić kontenery. Na przykład użyj polecenia [AZ Container Start][az-container-start] lub Azure Portal, aby ręcznie uruchomić kontenery w grupie. Jeśli obraz kontenera dla dowolnego kontenera zostanie zaktualizowany, zostanie pobrany nowy obraz. 

Rozpoczęcie tworzenia grupy kontenerów rozpoczyna nowe wdrożenie z tą samą konfiguracją kontenera. Ta akcja może ułatwić Szybkie ponowne użycie znanej konfiguracji grupy kontenerów, która działa zgodnie z oczekiwaniami. Nie trzeba tworzyć nowej grupy kontenerów w celu uruchomienia tego samego obciążenia.

Wszystkie kontenery w grupie kontenerów są uruchamiane przez tę akcję. Nie można uruchomić określonego kontenera w grupie.

Po ręcznym uruchomieniu lub ponownym uruchomieniu grupy kontenerów Grupa kontenerów jest uruchamiana zgodnie ze skonfigurowanymi zasadami ponownego uruchamiania.
  
## <a name="restart"></a>Uruchom ponownie

Można uruchomić ponownie grupę kontenerów, gdy jest ona uruchomiona — na przykład przy użyciu polecenia [AZ Container restart][az-container-restart] . Ta akcja spowoduje ponowne uruchomienie wszystkich kontenerów w grupie kontenerów. Jeśli obraz kontenera dla dowolnego kontenera zostanie zaktualizowany, zostanie pobrany nowy obraz. 

Ponowne uruchomienie grupy kontenerów jest przydatne, gdy chcesz rozwiązać problem z wdrożeniem. Na przykład jeśli tymczasowe ograniczenie zasobów uniemożliwia pomyślne uruchomienie kontenerów, ponowne uruchomienie grupy może rozwiązać problem.

Wszystkie kontenery w grupie kontenerów są ponownie uruchamiane przez tę akcję. Nie można ponownie uruchomić określonego kontenera w grupie.

Po ręcznym ponownym uruchomieniu grupy kontenerów Grupa kontenerów jest uruchamiana zgodnie ze skonfigurowanymi zasadami ponownego uruchamiania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [ustawieniach zasad ponownego uruchamiania](container-instances-restart-policy.md) w Azure Container Instances.

Oprócz ręcznego zatrzymywania i uruchamiania grupy kontenerów z istniejącą konfiguracją można [zaktualizować ustawienia](container-instances-update.md) uruchomionej grupy kontenerów.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az-container-restart
[az-container-start]: /cli/azure/container#az-container-start
[az-container-stop]: /cli/azure/container#az-container-stop
