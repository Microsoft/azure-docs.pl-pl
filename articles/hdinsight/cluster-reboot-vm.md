---
title: Ponowne uruchamianie maszyn wirtualnych dla klastra usługi Azure HDInsight
description: Dowiedz się, jak ponownie uruchamiać maszyny wirtualne nieodpowiadające na klastry usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077018"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Ponowne uruchamianie maszyn wirtualnych dla klastra usługi HDInsight

Klastry usługi HDInsight zawierają grupy maszyn wirtualnych jako węzły klastra. W przypadku długotrwałych klastrów te węzły mogą przestać odpowiadać z różnych powodów. W tym artykule opisano sposób ponownego uruchamiania nieodpowiadających maszyn wirtualnych w klastrze usługi HDInsight.

## <a name="when-to-reboot"></a>Kiedy należy przeprowadzić ponowny rozruch

> [!WARNING]  
> Ponowny rozruch maszyn wirtualnych w klastrze powoduje przestoje węzła i ponowne uruchamianie usług w węźle. 

Gdy węzeł jest ponownie uruchamiany, klaster może stać się w złej kondycji, zadania mogą spowalniać działanie lub kończyć się niepowodzeniem. Jeśli próbujesz ponownie uruchomić aktywny węzeł główny, wszystkie uruchomione zadania zostaną zabite i nie będzie można przesłać zadań do klastra do momentu, aż usługi zostaną uruchomione ponownie. Należy rozważyć ponowne uruchomienie maszyn wirtualnych tylko w razie potrzeby. Poniżej przedstawiono kilka wskazówek dotyczących sytuacji, w których należy rozważyć ponowne uruchomienie maszyn wirtualnych.

- Nie można przeprowadzić połączenia SSH z węzłem, ale odpowiada on na polecenia ping.
- Węzeł procesu roboczego nie działa bez pulsu w interfejsie użytkownika Ambari.
- Dysk tymczasowy jest zapełniony w węźle.
- Tabela procesów na maszynie wirtualnej zawiera wiele wpisów, w których proces został ukończony, ale jest on wyświetlany ze stanem zakończono.

> [!WARNING]  
> Należy zachować ostrożność podczas ponownego uruchamiania maszyn wirtualnych dla **HBase** i **Kafka** clustes, ponieważ może to spowodować utratę danych.

## <a name="use-powershell-to-reboot-vms"></a>Ponowne uruchamianie maszyn wirtualnych przy użyciu programu PowerShell

Aby można było użyć operacji ponownego rozruchu węzła, wymagane są dwa kroki: węzły list i ponownie uruchamiające węzły.

1. Wyświetlanie listy węzłów. Listę węzłów klastra można uzyskać za pomocą [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost). 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. Uruchom ponownie hosty. Po podaniu nazw węzłów, które chcesz ponownie uruchomić, uruchom ponownie węzły przy użyciu polecenia [restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost).

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>Ponowne uruchamianie maszyn wirtualnych przy użyciu interfejsu API REST

Aby wysyłać żądania do usługi HDInsight, można użyć funkcji **Wypróbuj ją** w dokumentacji interfejsu API. Aby można było użyć operacji ponownego rozruchu węzła, wymagane są dwa kroki: węzły list i ponownie uruchamiające węzły.

1. Wyświetlanie listy węzłów. Listę węzłów klastra można pobrać z interfejsu API REST lub w Ambari. Więcej szczegółów można znaleźć na liście usługi HDInsight, która [zawiera operacje interfejsu API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Uruchom ponownie hosty. Po otrzymaniu nazw węzłów, które mają zostać ponownie uruchomione, użyj interfejsu API REST węzłów restart, aby ponownie uruchomić węzły. Nazwa węzła jest zgodna ze wzorcem **"NodeType (WN/HN/ZK/GW)" + "x" + "pierwsze 6 znaków nazwy klastra"**. Więcej szczegółowych informacji można znaleźć w usłudze [HDInsight restart hostuje operację interfejsu API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Rzeczywiste nazwy węzłów, które mają zostać ponownie rozruchowe, są określone w tablicy JSON w treści żądania.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Następne kroki

* [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [Interfejs API REST maszyn wirtualnych usługi HDInsight](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [Interfejs API REST usługi HDInsight](https://docs.microsoft.com/rest/api/hdinsight/)
