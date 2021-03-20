---
title: Ponowne uruchamianie maszyn wirtualnych dla klastrów usługi Azure HDInsight
description: Dowiedz się, jak ponownie uruchamiać maszyny wirtualne niereagujące na potrzeby klastrów usługi Azure HDInsight.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: a61735dba60860459d007eb54d4655f41d5ae51a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946871"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>Ponowne uruchamianie maszyn wirtualnych dla klastrów usługi HDInsight

Klastry usługi Azure HDInsight zawierają grupy maszyn wirtualnych jako węzły klastra. W przypadku długotrwałych klastrów te węzły mogą przestać odpowiadać z różnych powodów. W tym artykule opisano sposób ponownego uruchamiania nieodpowiadających maszyn wirtualnych w klastrze usługi HDInsight.

## <a name="when-to-reboot"></a>Kiedy należy przeprowadzić ponowny rozruch

> [!WARNING]
> Po ponownym uruchomieniu maszyn wirtualnych w klastrze węzeł jest niedostępny do użytku i usługi w węźle muszą zostać uruchomione ponownie.

Gdy węzeł jest ponownie uruchamiany, klaster może stać się w złej kondycji, a zadania mogą spowalniać pracę lub kończyć się niepowodzeniem. Jeśli próbujesz ponownie uruchomić aktywny węzeł główny, wszystkie uruchomione zadania zostaną zatrzymane. Nie będzie można przesłać zadań do klastra, dopóki usługi nie zostaną uruchomione ponownie. Z tego względu należy ponownie uruchomić maszyny wirtualne tylko wtedy, gdy jest to konieczne. Rozważ ponowne uruchomienie maszyn wirtualnych w przypadku:

- Nie można użyć protokołu SSH, aby przejść do węzła, ale reaguje na polecenia ping.
- Węzeł procesu roboczego nie działa bez pulsu w interfejsie użytkownika Ambari.
- Dysk tymczasowy jest zapełniony w węźle.
- Tabela procesów na maszynie wirtualnej zawiera wiele wpisów, w których proces został ukończony, ale jest on wyświetlany ze stanem zakończono.

> [!NOTE]
> Ponowne uruchamianie maszyn wirtualnych nie jest obsługiwane w przypadku klastrów **HBase** i **Kafka** , ponieważ ponowne uruchomienie może spowodować utratę danych.

## <a name="use-powershell-to-reboot-vms"></a>Ponowne uruchamianie maszyn wirtualnych przy użyciu programu PowerShell

Aby można było użyć operacji ponownego rozruchu węzła, wymagane są dwa kroki: węzły list i ponownie uruchomione węzły.

1. Wyświetlanie listy węzłów. Listę węzłów klastra można pobrać pod adresem [Get-AzHDInsightHost](/powershell/module/az.hdinsight/get-azhdinsighthost).

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. Uruchom ponownie hosty. Po otrzymaniu nazw węzłów, które mają zostać ponownie uruchomione, uruchom ponownie węzły za pomocą polecenia [restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost).

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>Ponowne uruchamianie maszyn wirtualnych przy użyciu interfejsu API REST

Aby wysyłać żądania do usługi HDInsight, można użyć funkcji **Wypróbuj ją** w dokumentacji interfejsu API. Aby można było użyć operacji ponownego rozruchu węzła, wymagane są dwa kroki: węzły list i ponownie uruchomione węzły.

1. Wyświetlanie listy węzłów. Listę węzłów klastra można pobrać z interfejsu API REST lub w Ambari. Aby uzyskać więcej informacji, zobacz [Obsługa interfejsu API REST na liście usługi HDInsight](/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. Uruchom ponownie hosty. Po otrzymaniu nazw węzłów, które mają zostać ponownie uruchomione, uruchom ponownie węzły przy użyciu interfejsu API REST, aby ponownie uruchomić węzły. Nazwa węzła jest zgodna ze wzorcem *NodeType (WN/HN/ZK/GW)*  +  *x*  +  *pierwszych sześciu znaków nazwy klastra*. Aby uzyskać więcej informacji, zobacz [operacja interfejsu API REST hosta usługi HDInsight](/rest/api/hdinsight/virtualmachines/restarthosts).

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

* [Restart-AzHDInsightHost](/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [Interfejs API REST maszyn wirtualnych usługi HDInsight](/rest/api/hdinsight/virtualmachines)
* [Interfejs API REST usługi HDInsight](/rest/api/hdinsight/)