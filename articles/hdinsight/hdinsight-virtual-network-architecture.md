---
title: Architektura sieci wirtualnej usługi Azure HDInsight
description: Poznaj zasoby dostępne podczas tworzenia klastra USŁUGI HDInsight w sieci wirtualnej platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ad0e0250b32f2bdef4944e6e148be3215f3822f7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390221"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architektura sieci wirtualnej usługi Azure HDInsight

W tym artykule opisano zasoby, które są obecne podczas wdrażania klastra HDInsight w niestandardowej sieci wirtualnej platformy Azure. Te informacje ułatwią łączenie zasobów lokalnych z klastrem usługi HDInsight na platformie Azure. Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Co to jest usługa Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Typy zasobów w klastrach usługi Azure HDInsight

Klastry usługi Azure HDInsight mają różne typy maszyn wirtualnych lub węzłów. Każdy typ węzła odgrywa rolę w działaniu systemu. W poniższej tabeli podsumowano te typy węzłów i ich role w klastrze.

| Typ | Opis |
| --- | --- |
| Węzeł główny |  Dla wszystkich typów klastrów z wyjątkiem Apache Storm węzły głównego hosta procesów, które zarządzają wykonywaniem aplikacji rozproszonej. Węzeł główny jest również węzłem, który można SSH do i wykonywania aplikacji, które są następnie koordynowane do uruchamiania zasobów klastra. Liczba węzłów głównego jest ustalona na dwa dla wszystkich typów klastra. |
| Węzeł ZooKeeper | Zookeeper koordynuje zadania między węzłami, które wykonują przetwarzanie danych. Wykonuje również wybór lidera węzła głównego i śledzi, który węzeł główny jest uruchomiony określoną usługę główną. Liczba węzłów ZooKeeper jest ustalona na trzy. |
| Węzeł pracownika | Reprezentuje węzły, które obsługują funkcje przetwarzania danych. Węzły procesu roboczego można dodawać lub usuwać z klastra, aby skalować możliwości obliczeniowe i zarządzać kosztami. |
| Węzeł krawędzi serwera R | Węzeł brzegowy serwera R reprezentuje węzeł, który można ssh do i wykonywania aplikacji, które są następnie koordynowane do uruchamiania zasobów klastra. Węzeł brzegowy nie uczestniczy w analizie danych w klastrze. Ten węzeł obsługuje również serwer R Studio, umożliwiając uruchamianie aplikacji Języka R za pomocą przeglądarki. |
| Węzeł regionu | Dla typu klastra HBase węzeł regionu (nazywany również węzłem danych) uruchamia serwer regionu. Serwery regionu obsługują część danych zarządzanych przez firmę HBase i zarządzają nimi. Węzły regionu można dodawać lub usuwać z klastra, aby skalować możliwości obliczeniowe i zarządzać kosztami.|
| Węzeł Nimbus | Dla typu klastra Burza węzeł Nimbus zapewnia funkcje podobne do węzła Głównego. Węzeł Nimbus przypisuje zadania do innych węzłów w klastrze za pośrednictwem zookeeper, który koordynuje uruchamianie topologii storm. |
| Węzeł przełożonego | Dla typu klastra Storm węzeł nadzorcy wykonuje instrukcje dostarczone przez węzeł Nimbus do przetwarzania. |

## <a name="resource-naming-conventions"></a>Konwencje nazewnictwa zasobów

Podczas adresowania węzłów w klastrze należy używać w pełni kwalifikowanych nazw domen (FQDN). Nazwy FQDN dla różnych typów węzłów w klastrze można uzyskać za pomocą [interfejsu API Ambari.](hdinsight-hadoop-manage-ambari-rest-api.md)

Te FQDN będą w `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`formie .

Będzie `<node-type-prefix>` *hn* dla headnodes, *wn* dla węzłów robotniczych i *zn* dla węzłów zookeeper.

Jeśli potrzebujesz tylko nazwy hosta, użyj tylko pierwszej części nazwy FQDN:`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Podstawowe zasoby sieci wirtualnej

Na poniższym diagramie przedstawiono rozmieszczenie węzłów usługi HDInsight i zasobów sieciowych na platformie Azure.

![Diagram jednostek usługi HDInsight utworzonych w niestandardowej sieci wirtualnej platformy Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Domyślne zasoby w sieci wirtualnej platformy Azure obejmują typy węzłów klastra wymienione w poprzedniej tabeli. I urządzeń sieciowych, które obsługują komunikację między siecią wirtualną i sieciami zewnętrznymi.

W poniższej tabeli podsumowano dziewięć węzłów klastra utworzonych podczas wdrażania usługi HDInsight w niestandardowej sieci wirtualnej platformy Azure.

| Typ zasobu | Liczba obecnych | Szczegóły |
| --- | --- | --- |
|Węzeł główny | dwa |    |
|Węzeł usługi ZooKeeper | trzy | |
|Węzeł pracownika | dwa | Ta liczba może się różnić w zależności od konfiguracji klastra i skalowania. Co najmniej trzy węzły procesu roboczego jest potrzebne dla apache kafka.  |
|Węzeł bramy | dwa | Węzły bramy to maszyny wirtualne platformy Azure, które są tworzone na platformie Azure, ale nie są widoczne w subskrypcji. Jeśli chcesz ponownie uruchomić te węzły, skontaktuj się z pomocą techniczną. |

Następujące obecne zasoby sieciowe są tworzone automatycznie wewnątrz sieci wirtualnej używanej z programem HDInsight:

| Zasób sieciowy | Liczba obecnych | Szczegóły |
| --- | --- | --- |
|Moduł równoważenia obciążenia | trzy | |
|Interfejsy sieciowe | Dziewięć | Ta wartość jest oparta na normalnym klastrze, w którym każdy węzeł ma swój własny interfejs sieciowy. Dziewięć interfejsów jest przeznaczonych dla: dwóch węzłów głównego, trzech węzłów zookeeper, dwóch węzłów procesu roboczego i dwóch węzłów bramy wymienionych w poprzedniej tabeli. |
|Publiczne adresy IP | dwa |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Punkty końcowe do łączenia się z hdinsight

Dostęp do klastra HDInsight można uzyskać na trzy sposoby:

- Punkt końcowy HTTPS poza siecią `CLUSTERNAME.azurehdinsight.net`wirtualną pod adresem .
- Punkt końcowy SSH do bezpośredniego podłączenia do `CLUSTERNAME-ssh.azurehdinsight.net`headnode w .
- Punkt końcowy HTTPS w `CLUSTERNAME-int.azurehdinsight.net`sieci wirtualnej . Zwróć uwagę`-int`na " " w tym adresie URL. Ten punkt końcowy rozwiąże problem prywatnego adresu IP w tej sieci wirtualnej i nie jest dostępny z publicznego Internetu.

Te trzy punkty końcowe są przypisane moduł równoważenia obciążenia.

Publiczne adresy IP są również dostarczane do dwóch punktów końcowych, które umożliwiają połączenie spoza sieci wirtualnej.

1. Jeden publiczny adres IP jest przypisany do modułu równoważenia obciążenia dla w pełni kwalifikowanej `CLUSTERNAME.azurehdinsight.net`nazwy domeny (FQDN) do użycia podczas łączenia się z klastrem z Internetu .
1. Drugi publiczny adres IP jest używany dla `CLUSTERNAME-ssh.azurehdinsight.net`nazwy domeny tylko SSH .

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczanie przychodzącego ruchu do klastrów USŁUGI HDInsight w sieci wirtualnej z prywatnym punktem końcowym](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
