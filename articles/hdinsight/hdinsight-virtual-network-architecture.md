---
title: Architektura sieci wirtualnej usługi Azure HDInsight
description: Informacje o zasobach dostępnych podczas tworzenia klastra usługi HDInsight w usłudze Azure Virtual Network.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d5b8bdf5577642290cee7250e0f21f8a69dfd248
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931149"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architektura sieci wirtualnej usługi Azure HDInsight

W tym artykule opisano zasoby, które są obecne podczas wdrażania klastra usługi HDInsight, do niestandardowego Virtual Network platformy Azure. Te informacje ułatwią połączenie zasobów lokalnych z klastrem usługi HDInsight na platformie Azure. Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [co to jest usługa azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Typy zasobów w klastrach usługi Azure HDInsight

Klastry usługi Azure HDInsight mają różne typy maszyn wirtualnych lub węzłów. Każdy typ węzła odgrywa rolę w działaniu systemu. Poniższa tabela zawiera podsumowanie typów węzłów i ich ról w klastrze.

| Typ | Opis |
| --- | --- |
| Węzeł główny |  W przypadku wszystkich typów klastrów, z wyjątkiem Apache Storm, węzły główne obsługują procesy zarządzające wykonywaniem aplikacji rozproszonej. Węzeł główny jest również węzłem, w którym można używać protokołu SSH, i wykonywać aplikacje, które następnie są skoordynowane do uruchamiania przez zasoby klastra. Liczba węzłów głównych jest ustalona w dwóch dla wszystkich typów klastrów. |
| Węzeł dozorcy | Dozorcy koordynuje zadania między węzłami, które przetwarzają przetwarzanie danych. Powoduje również wybór lidera węzła głównego i śledzi, w którym węźle głównym działa określona usługa główna. Liczba węzłów dozorcy jest ustalona na trzy. |
| Węzeł procesu roboczego | Reprezentuje węzły obsługujące funkcje przetwarzania danych. Węzły procesu roboczego mogą być dodawane lub usuwane z klastra w celu skalowania możliwości obliczeniowych i zarządzania kosztami. |
| R Server węzeł krawędzi | Węzeł brzegowy R Server reprezentuje węzeł, do którego można przeprowadzić protokół SSH, i wykonywać aplikacje, które następnie są skoordynowane do uruchamiania przez zasoby klastra. Węzeł brzegowy nie uczestniczy w analizie danych w klastrze. Ten węzeł hostuje również program R Studio Server, umożliwiając uruchamianie aplikacji języka R przy użyciu przeglądarki. |
| Węzeł regionu | W przypadku typu klastra HBase węzeł regionu (nazywany także węzłem danych) uruchamia serwer regionu. Serwery regionów oferują i zarządzają częścią danych zarządzanych przez HBase. Węzły regionów można dodawać i usuwać z klastra w celu skalowania możliwości obliczeniowych i zarządzania kosztami.|
| Węzeł Nimbus | W przypadku typu klastra burzy węzeł Nimbus zapewnia funkcjonalność podobną do węzła głównego. Węzeł Nimbus przypisuje zadania do innych węzłów w klastrze za pomocą dozorcy, który koordynuje uruchamianie topologii burzy. |
| Węzeł nadzoru | W przypadku typu klastra burzy węzeł nadzoru wykonuje instrukcje dostarczone przez węzeł Nimbus, aby wykonać przetwarzanie. |

## <a name="resource-naming-conventions"></a>Konwencje nazewnictwa zasobów

Użyj w pełni kwalifikowanych nazw domen (FQDN) podczas adresowania węzłów w klastrze. Możesz uzyskać nazwy FQDN dla różnych typów węzłów w klastrze przy użyciu [interfejsu API Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Te nazwy FQDN będą mieć postać `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net` .

Wartość `<node-type-prefix>` będzie *HN* dla węzłów głównych, *WN* dla węzłów procesu roboczego i *Zn* dla węzłów dozorcy.

Jeśli potrzebujesz tylko nazwy hosta, użyj tylko pierwszej części nazwy FQDN: `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Podstawowe zasoby sieci wirtualnej

Na poniższym diagramie przedstawiono rozmieszczenie węzłów usługi HDInsight i zasobów sieciowych na platformie Azure.

![Diagram obiektów usługi HDInsight utworzonych w niestandardowej sieci wirtualnej platformy Azure](./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png)

Domyślne zasoby na platformie Azure Virtual Network obejmują typy węzłów klastra wymienione w poprzedniej tabeli. I urządzeń sieciowych, które obsługują komunikację między siecią wirtualną i sieciami zewnętrznymi.

Poniższa tabela zawiera podsumowanie dziewięciu węzłów klastra utworzonych podczas wdrażania usługi HDInsight w niestandardowym Virtual Network platformy Azure.

| Typ zasobu | Liczba obecna | Szczegóły |
| --- | --- | --- |
|Węzeł główny | dwa |    |
|Węzeł usługi ZooKeeper | trzy | |
|Węzeł procesu roboczego | dwa | Ta liczba może się różnić w zależności od konfiguracji i skalowania klastra. Do Apache Kafka jest wymagany co najmniej trzy węzły procesu roboczego.  |
|Węzeł bramy | dwa | Węzły bramy są maszynami wirtualnymi platformy Azure, które są tworzone na platformie Azure, ale nie są widoczne w Twojej subskrypcji. Skontaktuj się z pomocą techniczną, jeśli chcesz ponownie uruchomić te węzły. |

Następujące zasoby sieciowe są tworzone automatycznie w ramach sieci wirtualnej używanej z usługą HDInsight:

| Zasób sieciowy | Liczba obecna | Szczegóły |
| --- | --- | --- |
|Moduł równoważenia obciążenia | trzy | |
|Interfejsy sieciowe | dziewięć | Ta wartość jest oparta na normalnym klastrze, gdzie każdy węzeł ma własny interfejs sieciowy. Dziewięć interfejsów: dwa węzły główne, trzy węzły dozorcy, dwa węzły procesu roboczego i dwa węzły bramy wymienione w poprzedniej tabeli. |
|Publiczne adresy IP | dwa |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Punkty końcowe do łączenia się z usługą HDInsight

Dostęp do klastra usługi HDInsight można uzyskać na trzy sposoby:

- Punkt końcowy HTTPS poza siecią wirtualną w `CLUSTERNAME.azurehdinsight.net` .
- Punkt końcowy SSH służący do bezpośredniego łączenia się z usługą węzła głównego pod adresem `CLUSTERNAME-ssh.azurehdinsight.net` .
- Punkt końcowy HTTPS w sieci wirtualnej `CLUSTERNAME-int.azurehdinsight.net` . Zwróć uwagę na " `-int` " w tym adresie URL. Ten punkt końcowy zostanie rozpoznany jako prywatny adres IP w tej sieci wirtualnej i nie będzie dostępny z publicznego Internetu.

Te trzy punkty końcowe są przypisane do modułu równoważenia obciążenia.

Publiczne adresy IP są również dostarczane do dwóch punktów końcowych, które zezwalają na połączenie spoza sieci wirtualnej.

1. Jeden publiczny adres IP jest przypisywany do modułu równoważenia obciążenia dla w pełni kwalifikowanej nazwy domeny (FQDN) do użycia podczas nawiązywania połączenia z klastrem z Internetu `CLUSTERNAME.azurehdinsight.net` .
1. Drugi publiczny adres IP jest używany jako nazwa domeny tylko SSH `CLUSTERNAME-ssh.azurehdinsight.net` .

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczanie ruchu przychodzącego do klastrów usługi HDInsight w sieci wirtualnej za pomocą prywatnego punktu końcowego](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
