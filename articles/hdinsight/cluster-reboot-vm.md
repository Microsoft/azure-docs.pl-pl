---
title: Ponowne uruchamianie maszyn wirtualnych dla klastra usługi Azure HDInsight
description: Dowiedz się, jak ponownie uruchamiać maszyny wirtualne nieodpowiadające na klastry usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 46efccad60c06c73145bbf30c119f6a47cb856d5
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323460"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Ponowne uruchamianie maszyn wirtualnych dla klastra usługi HDInsight

Klastry usługi HDInsight zawierają grupy maszyn wirtualnych jako węzły klastra. W przypadku długotrwałych klastrów te węzły mogą przestać odpowiadać z różnych powodów. W tym artykule opisano sposób ponownego uruchamiania nieodpowiadających maszyn wirtualnych w klastrze usługi HDInsight.

## <a name="when-to-reboot"></a>Kiedy należy przeprowadzić ponowny rozruch

Ponowny rozruch maszyn wirtualnych w klastrze powoduje przestoje węzła i ponowne uruchamianie usług w węźle. Gdy węzeł jest ponownie uruchamiany, klaster może stać się w złej kondycji, zadania mogą spowalniać działanie lub kończyć się niepowodzeniem. Jeśli próbujesz ponownie uruchomić aktywny węzeł główny, wszystkie uruchomione zadania zostaną zabite i nie będzie można przesłać zadań do klastra do momentu, aż usługi zostaną uruchomione ponownie. Należy rozważyć ponowne uruchomienie maszyn wirtualnych tylko w razie potrzeby. Poniżej przedstawiono kilka wskazówek dotyczących sytuacji, w których należy rozważyć ponowne uruchomienie maszyn wirtualnych.

- Nie można przeprowadzić połączenia SSH z węzłem, ale odpowiada on na polecenia ping.
- Węzeł procesu roboczego nie działa bez pulsu w interfejsie użytkownika Ambari.
- Dysk tymczasowy jest zapełniony w węźle.
- Tabela procesów na maszynie wirtualnej zawiera wiele wpisów, w których proces został ukończony, ale jest on wyświetlany ze stanem zakończono.

## <a name="use-rest-api-to-reboot-vms"></a>Ponowne uruchamianie maszyn wirtualnych przy użyciu interfejsu API REST

Ponowny rozruch węzła jest obecnie obsługiwany tylko za pośrednictwem interfejsu API REST. Aby wysyłać żądania do usługi HDInsight, można użyć funkcji **Wypróbuj ją** w dokumentacji interfejsu API. Aby można było użyć operacji ponownego rozruchu węzła, wymagane są dwa kroki: węzły list i ponownie uruchamiające węzły.

1. Wyświetlanie listy węzłów. Listę węzłów klastra można pobrać z interfejsu API REST lub w Ambari. Więcej szczegółów można znaleźć na [liście usługi HDInsight — operacja interfejsu API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts) .

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Uruchom ponownie hosty. Po otrzymaniu nazw węzłów, które mają zostać ponownie uruchomione, należy ponownie uruchomić węzły przy użyciu interfejsu API REST usługi hosty. Więcej szczegółów można znaleźć w witrynie usługi [HDInsight restart hostuje operację interfejsu API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Rzeczywiste nazwy węzłów, które mają zostać ponownie rozruchowe, są określone w tablicy JSON w treści żądania.

```json
[
  "gateway1",
  "gateway3"
]
```

## <a name="next-steps"></a>Następne kroki

* [Interfejs API REST maszyn wirtualnych usługi HDInsight](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [Interfejs API REST usługi HDInsight](https://docs.microsoft.com/rest/api/hdinsight/)