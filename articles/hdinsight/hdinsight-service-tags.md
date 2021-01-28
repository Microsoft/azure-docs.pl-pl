---
title: Tagi usług sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla usługi Azure HDInsight
description: Użyj tagów usługi HDInsight, aby zezwolić na ruch przychodzący do klastra z węzłów usług kondycji i zarządzania bez dodawania adresów IP do sieciowych grup zabezpieczeń.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: d17d067b88add3006bc5c7fb10caa6b80a80a827
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931763"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>SIECIOWEJ grupy zabezpieczeń Tagi usług dla usługi Azure HDInsight

Tagi usługi Azure HDInsight dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) to grupy adresów IP dla usług kondycji i zarządzania. Te grupy pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń. [Tagi usług](../virtual-network/network-security-groups-overview.md#service-tags) zezwalają na ruch przychodzący z określonych adresów IP bez wprowadzania do nich każdego [adresu IP zarządzania](hdinsight-management-ip-addresses.md) w sieciowych grup zabezpieczeń.

Usługa HDInsight zarządza tymi tagami usług. Nie można utworzyć własnego tagu usługi ani zmodyfikować istniejącego tagu. Firma Microsoft zarządza prefiksami adresów zgodnymi z tagiem usługi i automatycznie aktualizuje tag usługi jako adresy.

Jeśli chcesz użyć określonego regionu, a tag usługi nie został jeszcze udokumentowany na tej stronie, możesz użyć [interfejsu API odnajdywania tagów usługi](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) , aby znaleźć swój tag usługi. Możesz również pobrać [plik JSON znacznika usługi](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) i wyszukać żądany region.

## <a name="get-started-with-service-tags"></a>Wprowadzenie do tagów usługi

Dostępne są dwie opcje używania tagów usługi w sieciowych grupach zabezpieczeń:

- **Użyj jednego globalnego tagu usługi HDInsight**: Ta opcja umożliwia otworzenie sieci wirtualnej ze wszystkimi adresami IP używanymi przez usługę HDInsight do monitorowania klastrów we wszystkich regionach. Ta opcja jest najprostszą metodą, ale może nie być odpowiednia, jeśli istnieją restrykcyjne wymagania dotyczące zabezpieczeń.

- **Użyj wielu tagów usługi regionalnej**: Ta opcja umożliwia otwarcie sieci wirtualnej tylko do adresów IP używanych przez usługę HDInsight w tym konkretnym regionie. Jeśli jednak używasz wielu regionów, musisz dodać wiele tagów usługi do sieci wirtualnej.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Użyj jednego globalnego tagu usługi HDInsight

Najprostszym sposobem rozpoczęcia korzystania z tagów usług w klastrze usługi HDInsight jest dodanie tagu globalnego `HDInsight` do reguły sieciowej grupy zabezpieczeń.

1. W [Azure Portal](https://portal.azure.com/)wybierz grupę zabezpieczeń sieci.

1. W obszarze **Ustawienia** wybierz pozycję **reguły zabezpieczeń ruchu przychodzącego**, a następnie wybierz pozycję **+ Dodaj**.

1. Z listy rozwijanej **Źródło** wybierz pozycję **tag usługi**.

1. Z listy rozwijanej **tag usługi źródłowej** wybierz pozycję **HDInsight**.

    ![Dodaj tag usługi z Azure Portal](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Ten tag zawiera adresy IP usług kondycji i zarządzania dla wszystkich regionów, w których Usługa HDInsight jest dostępna. Ten tag zapewni, że klaster będzie mógł komunikować się z niezbędnymi usługami kondycji i zarządzania niezależnie od miejsca, w którym zostały utworzone.

## <a name="use-regional-hdinsight-service-tags"></a>Korzystanie z regionalnych tagów usługi HDInsight

Jeśli opcja tagu globalnego nie będzie działała, ponieważ potrzebne są bardziej restrykcyjne uprawnienia, można zezwolić tylko na Tagi usługi odpowiednie dla danego regionu. W zależności od regionu, w którym jest tworzony klaster, może istnieć wiele tagów usługi.

Aby dowiedzieć się, które Tagi usług dodać do regionu, przeczytaj następujące sekcje artykułu.

### <a name="use-a-single-regional-service-tag"></a>Użyj jednego tagu usługi regionalnej

Jeśli klaster znajduje się w regionie wymienionym w tej tabeli, wystarczy dodać tylko jeden tag usługi regionalnej do sieciowej grupy zabezpieczeń.

| Country (Kraj) | Region | Tag usługi |
| ---- | ---- | ---- |
| Australia | Australia Wschodnia | HDInsight. AustraliaEast |
| &nbsp; | Australia Południowo-Wschodnia | HDInsight. AustraliaSoutheast |
| &nbsp; | Australia Środkowa | HDInsight. AustraliaCentral |
| Chiny | Chiny Wschodnie 2 | HDInsight. ChinaEast2 |
| &nbsp; | Chiny Północne 2 | HDInsight. ChinaNorth2 |
| Stany Zjednoczone | Północno-środkowe stany USA | HDInsight. NorthCentralUS |
| &nbsp; | Zachodnie stany USA 2 | HDInsight. WestUS2 |
| &nbsp; | Zachodnio-środkowe stany USA | HDInsight. WestCentralUS |
| Kanada | Kanada Wschodnia | HDInsight. CanadaEast |
| Brazylia | Brazylia Południowa | HDInsight. BrazilSouth |
| Korea | Korea Środkowa | HDInsight. KoreaCentral |
| &nbsp; | Korea Południowa | HDInsight. KoreaSouth |
| Indie | Indie Środkowe | HDInsight. CentralIndia |
| &nbsp; | Indie Południowe | HDInsight. SouthIndia |
| Japonia | Japonia Zachodnia | HDInsight. JapanWest |
| Francja | Francja Środkowa| HDInsight. FranceCentral |
| Zjednoczone Królestwo | Południowe Zjednoczone Królestwo | HDInsight. UKSouth |
| Azure Government | USDoD środkowe | HDInsight. USDoDCentral |
| &nbsp; | USGov Teksas | HDInsight. USGovTexas |
| &nbsp; | UsDoD wschód | HDInsight. USDoDEast |
| &nbsp; | USGov Arizona | HDInsight. USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Używanie wielu tagów usługi regionalnej

Jeśli region, w którym został utworzony klaster, nie znajduje się w powyższej tabeli, należy zezwolić na wiele tagów usługi regionalnej. Potrzeba użycia więcej niż jednego z nich wynika z różnic w rozmieszczeniu dostawców zasobów dla różnych regionów.

Pozostałe regiony są podzielone na grupy w oparciu o używane przez nie Tagi usług regionalnych.

#### <a name="group-1"></a>Grupa 1

Jeśli klaster jest tworzony w jednym z regionów w poniższej tabeli, Zezwól na Tagi usługi `HDInsight.WestUS` i `HDInsight.EastUS` . Ponadto znajduje się na nim tag usługi regionalnej. Regiony w tej sekcji wymagają trzech tagów usługi.

Na przykład jeśli klaster jest tworzony w `East US 2` regionie, należy dodać następujące znaczniki usługi do sieciowej grupy zabezpieczeń:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country (Kraj) | Region | Tag usługi |
| ---- | ---- | ---- |
| Stany Zjednoczone | Wschodnie stany USA 2 | HDInsight. EastUS2 |
| &nbsp; | Central US | HDInsight. środkowe |
| &nbsp; | NorthCentral nam | HDInsight. NorthCentralUS |
| &nbsp; | South Central US | HDInsight. SouthCentralUS |
| &nbsp; | East US | HDInsight. wschód |
| &nbsp; | Zachodnie stany USA | HDInsight. Zachodnie |
| Japonia | Japonia Wschodnia | HDInsight. JapanEast |
| Europa | Europa Północna | HDInsight. NorthEurope |
| &nbsp; | West Europe| HDInsight. WestEurope |
| Azja | Azja Wschodnia | HDInsight. EastAsia |
| &nbsp; | Southeast Asia | HDInsight. SoutheastAsia |
| Australia | Australia Wschodnia | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Grupa 2

Klastry w regionach *Chiny Północne* i *Chiny Wschodnie* muszą zezwalać na dwa Tagi usługi: `HDInsight.ChinaNorth` i `HDInsight.ChinaEast` .

#### <a name="group-3"></a>Grupa 3

Klastry w regionach *US gov Iowa* i *US gov Wirginia* muszą zezwalać na dwa Tagi usługi: `HDInsight.USGovIowa` i `HDInsight.USGovVirginia` .

#### <a name="group-4"></a>Grupa 4

Klastry w regionach *Niemiec środkowe* i *Niemcy północ* muszą zezwalać na dwie znaczniki usługi: `HDInsight.GermanyCentral` i `HDInsight.GermanyNortheast` .

## <a name="next-steps"></a>Następne kroki

- [Sieciowe grupy zabezpieczeń: Tagi usług](../virtual-network/network-security-groups-overview.md#security-rules)
- [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md)