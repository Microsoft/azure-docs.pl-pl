---
title: Tagi usługi sieciowej grupy zabezpieczeń (NSG) dla usługi Azure HDInsight
description: Użyj tagów usługi HDInsight, aby zezwolić na ruch przychodzący do klastra z węzłów usług kondycji i zarządzania bez dodawania adresów IP do sieciowej grupy zabezpieczeń.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 5608d0cd83e506bc6b30337db5148f344f59f80e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410854"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Tagi usługi NSG dla usługi Azure HDInsight

Tagi usługi Azure HDInsight dla sieciowych grup zabezpieczeń (NSG) to grupy adresów IP dla usług kondycji i zarządzania. Te grupy pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń. [Tagi usługi](../virtual-network/security-overview.md#service-tags) umożliwiają ruch przychodzący z określonych adresów IP bez wprowadzania każdego z [zarządzania adresami IP](hdinsight-management-ip-addresses.md) w sieciowej grupach zabezpieczeń.

Usługa HDInsight zarządza tymi tagami usług. Nie można utworzyć własnego tagu usługi ani zmodyfikować istniejącego tagu. Firma Microsoft zarządza prefiksami adresów zgodnymi ze znacznikiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów.

## <a name="get-started-with-service-tags"></a>Wprowadzenie do tagów usług

W sieciowych grupach zabezpieczeń dostępne są dwie opcje używania tagów usług:

- **Użyj jednego globalnego tagu usługi HDInsight:** Ta opcja otwiera sieć wirtualną na wszystkie adresy IP używane przez usługę HDInsight do monitorowania klastrów we wszystkich regionach. Ta opcja jest najprostszą metodą, ale może nie być odpowiednia, jeśli masz restrykcyjne wymagania dotyczące zabezpieczeń.

- **Użyj wielu regionalnych tagów usług:** Ta opcja otwiera sieć wirtualną tylko na adresy IP używane przez program HDInsight w tym określonym regionie. Jeśli jednak używasz wielu regionów, musisz dodać wiele tagów usługi do sieci wirtualnej.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Używanie jednego globalnego tagu usługi HDInsight

Najprostszym sposobem rozpoczęcia korzystania z tagów usługi w klastrze HDInsight jest dodanie tagu `HDInsight` globalnego do reguły sieciowej grupy sieciowej.

1. Z [witryny Azure portal](https://portal.azure.com/)wybierz grupę zabezpieczeń sieci.

1. W obszarze **Ustawienia**wybierz pozycję **Reguły zabezpieczeń ruchu przychodzącego**, a następnie wybierz pozycję **+ Dodaj**.

1. Z listy rozwijanej **Źródło** wybierz pozycję **Service Tag**.

1. Z listy rozwijanej **Tag usługi źródłowej** wybierz pozycję **HDInsight**.

    ![Dodawanie tagu usługi z witryny Azure portal](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Ten tag zawiera adresy IP usług kondycji i zarządzania dla wszystkich regionów, w których hdinsight jest dostępny. Tag zapewni, że klaster może komunikować się z niezbędnych usług zdrowotnych i zarządzania bez względu na to, gdzie jest tworzony.

## <a name="use-regional-hdinsight-service-tags"></a>Używanie regionalnych tagów usługi HDInsight

Jeśli opcja tagu globalnego nie będzie działać, ponieważ potrzebujesz bardziej restrykcyjnych uprawnień, możesz zezwolić tylko na tagi usługi mające zastosowanie do twojego regionu. Może istnieć wiele tagów usługi, w zależności od regionu, w którym jest tworzony klaster.

Aby dowiedzieć się, które tagi usługi należy dodać dla swojego regionu, przeczytaj poniższe sekcje artykułu.

### <a name="use-a-single-regional-service-tag"></a>Używanie jednego regionalnego tagu usługi

Jeśli klaster znajduje się w regionie wymienionym w tej tabeli, wystarczy dodać tylko jeden regionalny tag usługi do sieciowej grupy sieciowej.

| Kraj | Region | Tag usługi |
| ---- | ---- | ---- |
| Australia | Australia Wschodnia | HDInsight.AustraliaNajest |
| &nbsp; | Australia Południowo-Wschodnia | HDInsight.AustraliaSoutheast |
| &nbsp; | Australia Środkowa | HDInsight.AustraliaCentral |
| Chiny | Chiny Wschodnie 2 | HDInsight.ChinaEast2 |
| &nbsp; | Chiny Północne 2 | HDInsight.ChinaNorth2 |
| Stany Zjednoczone | Północno-środkowe stany USA | HDInsight.NorthCentralUS |
| &nbsp; | Zachodnie stany USA 2 | HDInsight.WestUS2 |
| &nbsp; | Zachodnio-środkowe stany USA | HDInsight.WestCentralUS |
| Kanada | Kanada Wschodnia | HDInsight.CanadaNajstrzej |
| Brazylia | Brazylia Południowa | HDInsight.BrazilSouth |
| Korea | Korea Środkowa | HDInsight.KoreaCentral |
| &nbsp; | Korea Południowa | HDInsight.KoreaSouth |
| Indie | Indie Środkowe | HDInsight.CentralIndia |
| &nbsp; | Indie Południowe | HDInsight.SouthIndia |
| Japonia | Japonia Zachodnia | HDInsight.JapanWest |
| Francja | Francja Środkowa| HDInsight.FranceCentral (Centrum) |
| Zjednoczone Królestwo | Południowe Zjednoczone Królestwo | HDInsight.UKSouth |
| Azure Government | UsDod Centralny | Centrum HDInsight.USDoD |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | UsDoD Wschód | HDInsight.USDoDEast |
| &nbsp; | USGov Arizona | HdInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Używanie wielu regionalnych tagów usług

Jeśli region, w którym został utworzony klaster, nie znajduje się na liście w poprzedniej tabeli, należy zezwolić na wiele regionalnych tagów usługi. Potrzeba użycia więcej niż jednego wynika z różnic w układzie dostawców zasobów dla różnych regionów.

Pozostałe regiony są podzielone na grupy, na podstawie których regionalne tagi usługi używają.

#### <a name="group-1"></a>Grupa 1

Jeśli klaster jest tworzony w jednym z regionów w poniższej `HDInsight.WestUS` `HDInsight.EastUS`tabeli, zezwalaj na tagi usług i . Ponadto na liście znajduje się regionalny tag usługi. Regiony w tej sekcji wymagają trzech tagów usługi.

Jeśli na przykład klaster jest `East US 2` tworzony w regionie, należy dodać następujące tagi usługi do sieciowej grupy zabezpieczeń:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Kraj | Region | Tag usługi |
| ---- | ---- | ---- |
| Stany Zjednoczone | Wschodnie stany USA 2 | HDInsight.EastUS2 |
| &nbsp; | Środkowe stany USA | HDInsight.CentralUS |
| &nbsp; | NorthCentral Stany Zjednoczone | Usługi hdinsight. Okręg wyborczy NorthCentralUS |
| &nbsp; | Południowo-środkowe stany USA | HDInsight.SouthCentralUS |
| &nbsp; | Wschodnie stany USA | HDInsight.EastUS |
| &nbsp; | Zachodnie stany USA | HDInsight.WestUS |
| Japonia | Japonia Wschodnia | HDInsight.JapanNajwalszy |
| Europa | Europa Północna | HDInsight.NorthEurope |
| &nbsp; | Europa Zachodnia| HDInsight.WestEurope |
| Azja | Azja Wschodnia | HDInsight.EastAsia |
| &nbsp; | Azja Południowo-Wschodnia | HDInsight.SoutheastA |
| Australia | Australia Wschodnia | HDInsight.AustraliaNajest |

#### <a name="group-2"></a>Grupa 2

Klastry w regionach *Chin Północnych* i *Chin Wschodnich* `HDInsight.ChinaNorth` muszą `HDInsight.ChinaEast`zezwolić na dwa tagi usług: i .

#### <a name="group-3"></a>Grupa 3

Klastry w regionach *US Gov Iowa* i US Gov *Virginia* `HDInsight.USGovIowa` muszą `HDInsight.USGovVirginia`zezwolić na dwa tagi usług: i .

#### <a name="group-4"></a>Grupa 4

Klastry w regionach Niemiec *Środkowo-Wschodnich* i Niemieckich `HDInsight.GermanyCentral` `HDInsight.GermanyNortheast`muszą zezwolić na dwa tagi usług: i . *Germany Central*

## <a name="next-steps"></a>Następne kroki

- [Sieciowe grupy zabezpieczeń: tagi usług](../virtual-network/security-overview.md#security-rules)
- [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md)
