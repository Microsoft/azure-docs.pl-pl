---
title: Ręczne zatrzymywanie lub uruchamianie grupy kontenerów
description: Dowiedz się, jak ręcznie zatrzymać lub uruchomić grupę kontenerów w Azure Container Instances.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 43498a7d31f0cb78751d2f318aede8523b7b9345
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787003"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Ręczne zatrzymywanie lub uruchamianie kontenerów w usłudze Azure Container Instances

Ustawienie [zasad ponownego](container-instances-restart-policy.md) uruchamiania grupy kontenerów określa domyślne uruchamianie lub zatrzymywanie wystąpień kontenerów. Ustawienie domyślne można przesłonić, ręcznie zatrzymując lub uruchamiając grupę kontenerów.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Stop

Ręcznie zatrzymaj uruchamianą grupę kontenerów — na przykład za pomocą polecenia [az container stop][az-container-stop] lub polecenia Azure Portal. W przypadku niektórych obciążeń kontenerów warto zatrzymać długotrwałą grupę kontenerów po upływie zdefiniowanego czasu, aby zaoszczędzić na kosztach. 

*Gdy grupa kontenerów przechodzi w stan Zatrzymany, kończy działanie i odtwarza wszystkie kontenery w grupie. Stan kontenera nie jest zachowywany.*

Gdy kontenery zostaną odzyskane, alokacja [zasobów zostanie](container-instances-container-groups.md#resource-allocation) cofzona, a opłaty za grupę kontenerów zostaną zatrzymane.

Akcja zatrzymania nie niosła żadnego efektu, jeśli grupa kontenerów została już zakończona (jest w stanie Powodzenie lub Niepowodzenie). Na przykład grupa kontenerów z uruchomionymi pomyślnie zadaniami kontenera uruchamianymi raz kończy się w stanie Powodzenie. Próby zatrzymania grupy w tym stanie nie zmieniają stanu. 

## <a name="start"></a>Rozpocznij

Po zatrzymaniu grupy kontenerów — ponieważ kontenery zostały przerwane samodzielnie lub ręcznie zatrzymano grupę — można uruchomić kontenery. Na przykład użyj polecenia [az container start][az-container-start] lub Azure Portal, aby ręcznie uruchomić kontenery w grupie. Jeśli obraz kontenera dla dowolnego kontenera zostanie zaktualizowany, zostanie ściągnięty nowy obraz. 

Uruchomienie grupy kontenerów rozpoczyna nowe wdrożenie z taką samą konfiguracją kontenera. Ta akcja może ułatwić szybkie ponowne użycie znanej konfiguracji grupy kontenerów, która działa zgodnie z oczekiwaniami. Nie musisz tworzyć nowej grupy kontenerów, aby uruchomić to samo obciążenie.

Wszystkie kontenery w grupie kontenerów są rozpoczynane przez tę akcję. Nie można uruchomić określonego kontenera w grupie.

Po ręcznym uruchomieniu lub ponownym uruchomieniu grupy kontenerów grupa kontenerów jest uruchamiana zgodnie ze skonfigurowanymi zasadami ponownego uruchamiania.
  
## <a name="restart"></a>Uruchom ponownie

Grupę kontenerów można uruchomić ponownie, gdy jest uruchomiona — na przykład za pomocą [polecenia az container restart.][az-container-restart] Ta akcja powoduje ponowne uruchomienie wszystkich kontenerów w grupie kontenerów. Jeśli obraz kontenera dla dowolnego kontenera zostanie zaktualizowany, zostanie ściągnięty nowy obraz. 

Ponowne uruchomienie grupy kontenerów jest przydatne, gdy chcesz rozwiązać problem z wdrożeniem. Jeśli na przykład tymczasowe ograniczenie zasobów uniemożliwia pomyślne uruchomienie kontenerów, ponowne uruchomienie grupy może rozwiązać problem.

Ta akcja powoduje ponowne uruchomienie wszystkich kontenerów w grupie kontenerów. Nie można ponownie uruchomić określonego kontenera w grupie.

Po ręcznym ponownym uruchomieniu grupy kontenerów zostanie ona uruchomiona zgodnie ze skonfigurowanymi zasadami ponownego uruchamiania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [ustawieniach zasad ponownego uruchamiania](container-instances-restart-policy.md) w Azure Container Instances.

Oprócz ręcznego zatrzymywania i uruchamiania grupy kontenerów z [](container-instances-update.md) istniejącą konfiguracją można zaktualizować ustawienia uruchomionej grupy kontenerów.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az_container_restart
[az-container-start]: /cli/azure/container#az_container_start
[az-container-stop]: /cli/azure/container#az_container_stop
