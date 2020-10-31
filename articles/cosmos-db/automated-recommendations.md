---
title: Automatyczna wydajność, koszt, zalecenia dotyczące zabezpieczeń Azure Cosmos DB
description: Dowiedz się, jak wyświetlać dostosowaną wydajność, koszt, zabezpieczenia i inne zalecenia dotyczące Azure Cosmos DB na podstawie wzorców obciążenia.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: d9eb55030c7ec52f9b2ac79fbab19944f0a3e190
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087829"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Zautomatyzowane zalecenia dotyczące Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Wszystkie usługi w chmurze, w tym Azure Cosmos DB pobierać częste aktualizacje o nowe funkcje, możliwości i ulepszenia. Ważne jest, aby aplikacja mogła zachować najnowsze aktualizacje dotyczące wydajności i zabezpieczeń. Azure Portal oferuje dostosowane zalecenia, które umożliwiają zmaksymalizowanie wydajności aplikacji. Aparat Doradczy Azure Cosmos DB stale analizuje historię użycia zasobów Azure Cosmos DB i zapewnia zalecenia na podstawie wzorców obciążenia. Zalecenia te odnoszą się do obszarów, takich jak partycjonowanie, indeksowanie, Sieć, zabezpieczenia itp. Te niestandardowe zalecenia ułatwiają zwiększenie wydajności aplikacji.

## <a name="view-recommendations"></a>Wyświetlanie zaleceń

Zalecenia dotyczące Azure Cosmos DB można wyświetlić w następujący sposób:

- Jednym ze sposobów wyświetlania zaleceń znajduje się na karcie powiadomienia. Jeśli istnieją nowe rekomendacje, zobaczysz pasek komunikatów. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego konta usługi Azure Cosmos. W ramach konta usługi Azure Cosmos Otwórz okienko **powiadomienia** , a następnie wybierz kartę **zalecenia** . Możesz wybrać komunikat i wyświetlić zalecenia.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Wyświetl zalecenia z okienka Azure Cosmos DB":::

- Zalecenia można także znaleźć w [Azure Advisor](../advisor/advisor-overview.md) pogrupowane według różnych zasobników, takich jak koszt, zabezpieczenia, niezawodność, wydajność i doskonałości operacyjną. Można wybrać określone subskrypcje i filtrować według typu zasobu, który jest **Azure Cosmos DB kontami** .  Po wybraniu określonego zalecenia zostaną wyświetlone akcje, które można wykonać w celu skorzystania z obciążeń.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Wyświetl zalecenia z okienka Azure Cosmos DB":::

Nie wszystkie zalecenia pokazane w okienku Azure Cosmos DB są dostępne na Azure Advisor i na odwrót. Dzieje się tak, ponieważ w zależności od typu zalecenia, które mieści się w okienku Azure Advisor, Azure Cosmos DB okienku lub obu tych elementów.

Obecnie Azure Cosmos DB obsługuje zalecenia dotyczące następujących obszarów. Każdy z tych zaleceń zawiera link do odpowiedniej sekcji dokumentacji, dzięki czemu można łatwo wykonać kolejne kroki.

## <a name="sdk-usage-recommendations"></a>Zalecenia dotyczące użycia zestawu SDK

W tej kategorii Doradca wykrywa użycie starej wersji zestawów SDK i zaleca uaktualnienie do nowszej wersji, aby użyć najnowszych poprawek usterek i usprawnień wydajności. Obecnie dostępne są następujące zalecenia dotyczące zestawu SDK:

|Nazwa  |Opis  |
|---------|---------|
| Stary łącznik platformy Spark | Wykrywa użycie starych wersji łącznika Spark i zaleca uaktualnienie. |
| Stary zestaw SDK .NET | Wykrywa użycie starych wersji zestawu .NET SDK i zaleca uaktualnienie. |
| Stary zestaw SDK języka Java | Wykrywa użycie starych wersji łącznika języka Java i zaleca uaktualnienie. |

## <a name="indexing-recommendations"></a>Zalecenia dotyczące indeksowania

W tej kategorii Doradca wykrywa tryb indeksowania, zasady indeksowania, indeksowane ścieżki i zaleca zmianę, jeśli Bieżąca konfiguracja ma wpływ na wydajność zapytań. Obecnie dostępne są następujące zalecenia dotyczące indeksowania:

|Nazwa  |Opis  |
|---------|---------|
| Indeksowanie z opóźnieniem | Wykrywa Użycie trybu indeksowania z opóźnieniem i zaleca użycie trybu indeksowania spójnego. Tryb indeksowania z opóźnieniem Azure Cosmos DB jest ograniczony i może mieć wpływ na świeżość wyników zapytania w niektórych sytuacjach, dlatego zaleca się zastosowanie trybu indeksowania spójnego. |
| Indeksowanie złożone| Wykrywa konta, w których zapytania mogą korzystać z indeksów złożonych i zalecamy ich użycie. Indeksy złożone mogą znacząco poprawić wydajność i użycie przepływności niektórych zapytań.|
| Domyślne zasady indeksowania z wieloma indeksowanymi ścieżkami | Wykrywa kontenery uruchomione przy użyciu domyślnego indeksowania z wieloma ścieżkami indeksowanymi i zaleca dostosowanie zasad indeksowania.|
| Zamów zapytania z wysoką opłatą RU/s| Wykrywa kontenery wystawiające kolejność przez zapytania z wysoką opłatą RU/s i zaleca Eksplorowanie indeksów złożonych.|
| Konta MongoDB 3,6 bez indeksu i wysokiego zużycia RU/s| Wykrywa interfejs API Azure Cosmos DB dla MongoDB z 3,6 wersjami kontenerów, które wydają zapytania z wysoką opłatą za RU/s i zalecają Dodawanie indeksów.|

## <a name="cost-optimization-recommendations"></a>Zalecenia dotyczące optymalizacji kosztów

W tej kategorii klasyfikator wykrywa użycie RU/s i określa, że można zoptymalizować cenę, wprowadzając pewne zmiany do zasobów lub wykorzystując inny model cen. Obecnie dostępne są następujące zalecenia dotyczące optymalizacji kosztów:

|Nazwa  |Opis  |
|---------|---------|
| Pojemność zarezerwowana | Wykrywa użycie RU/s i zaleca wystąpienia zarezerwowane użytkownikom, którzy mogą z nich korzystać. |
| Nieaktywne kontenery | Wykrywa kontenery, które nie były używane przez ponad 30 dni i zaleca zmniejszenie przepływności dla takich kontenerów lub ich usunięcie.|
| Nowe subskrypcje o wysokiej przepływności | Wykrywaj nowe subskrypcje z kontami, które nie zwykle mają wysokie wartości RU/s dziennie i udostępniają im powiadomienia. To powiadomienie jest przeznaczone głównie do zapewnienia świadomości nowym klientom, którzy Azure Cosmos DB operować na modelu opartym na przepływności, a nie na modelu opartym na użyciu. |

## <a name="migration-recommendations"></a>Zalecenia dotyczące migracji

W tej kategorii klasyfikator wykryje, że używasz starszych funkcji zaleca się migrację, aby można było wykorzystać ogromną skalowalność Azure Cosmos DB i inne korzyści. Obecnie dostępne są następujące zalecenia dotyczące migracji:

|Nazwa  |Opis  |
|---------|---------|
| Kontenery bez partycjonowania | Wykrywa kontenery o stałym rozmiarze, które zbliżają się do maksymalnego limitu magazynu i zalecają migrowanie ich do kontenerów z podziałem na partycje.|

## <a name="query-usage-recommendations"></a>Zalecenia dotyczące użycia zapytań

W tej kategorii Doradca wykrywa wykonywanie zapytań i określa, że wydajność zapytań można dostrajać przy użyciu niektórych zmian. Obecnie dostępne są następujące zalecenia dotyczące użycia zapytań:

|Nazwa  |Opis  |
|---------|---------|
| Zapytania o stałym rozmiarze strony | Wykrywa zapytania wystawione ze stałym rozmiarem strony i zaleca użycie-1 (brak limitu rozmiaru strony) zamiast definiować konkretną wartość. Ta opcja zmniejsza liczbę podróży rundy sieci wymaganych do pobrania wszystkich wyników. |

## <a name="next-steps"></a>Następne kroki

* [Dostrajanie wydajności zapytań w Azure Cosmos DB](sql-api-query-metrics.md)
* [Rozwiązywanie problemów z kwerendą](troubleshoot-query-performance.md) podczas korzystania z Azure Cosmos DB
