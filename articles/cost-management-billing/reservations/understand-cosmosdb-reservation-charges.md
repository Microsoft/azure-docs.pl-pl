---
title: Informacje o rabacie na rezerwacje w usłudze Azure Cosmos DB
description: Dowiedz się, w jaki sposób rabat za rezerwację jest stosowany do aprowizowanej przepływności (RU/s) w usłudze Azure Cosmos DB.
author: bandersmsft
ms.author: banders
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: ca4c1810912771f56661ca5b682b6991735f526e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150823"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Omówienie stosowania rabatu za rezerwację dla usługi Azure Cosmos DB

Po zakupie zarezerwowanej pojemności usługi Azure Cosmos DB rabat za rezerwację jest automatycznie stosowany do zasobów usługi Azure Cosmos DB pasujących do atrybutów i ilości rezerwacji. Rezerwacja obejmuje przepływność aprowizowaną dla zasobów usługi Azure Cosmos DB. Nie obejmuje ona opłat za oprogramowanie, sieć, magazyn ani wstępnie zdefiniowanych opłat za kontenery.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Rabat za rezerwację zastosowany dla kont usługi Azure Cosmos DB

Rabat za rezerwację ma zastosowanie do [aprowizowanej przepływności](../../cosmos-db/request-units.md) w zakresie jednostek żądania na sekundę (RU/s) w cyklach cogodzinnych. W przypadku zasobów usługi Azure Cosmos DB niepracujących przez całą godzinę rabat za rezerwację jest automatycznie stosowany dla innych zasobów usługi Azure Cosmos DB zgodnych z atrybutami rezerwacji. Rabat może dotyczyć zasobów usługi Azure Cosmos DB, które są uruchamiane równolegle. Jeśli nie masz zasobów usługi Cosmos DB, działających przez całą godzinę oraz pasujących do atrybutów rezerwacji, nie wykorzystasz w pełni korzyści z rabatu za rezerwację w tej godzinie.

Dla rabatów są stosowane warstwy. Rezerwacje z wyższą liczbą jednostek żądania zapewniają wyższe rabaty.

Zakup rezerwacji spowoduje zastosowanie rabatów do wszystkich regionów w proporcji takiej samej jak w przypadku regionalnych cen dla użycia na żądanie. Informacje o współczynnikach rabatu za rezerwację w każdym regionie, zobacz sekcję [Rabat za rezerwację według regionu](#reservation-discount-per-region) w tym artykule.

## <a name="reservation-discount-per-region"></a>Rabat za rezerwację według regionu
Rabat za rezerwację jest stosowany do godzinowych kosztów przepływności usługi Azure Cosmos DB. Jest on stosowany dla pojedynczej subskrypcji lub zakresu rejestracji/kont. Rabat za rezerwację jest stosowany do użycia wg miernika w różnych regionach przy pomocy następujących współczynników:

|Opis miernika  |Region |Współczynnik  |
|---------|---------|---------|
|Azure Cosmos DB — 100 RU/s/godzinę — Południowo-Wschodni region Azji i Pacyfiku  |  Południowo-Wschodni region Azji i Pacyfiku    |   1      |
|Azure Cosmos DB — 100 RU/s/godzinę — Wschodni region Azji i Pacyfiku |   Wschodni region Azji i Pacyfiku   |    1     |
|Azure Cosmos DB — 100 RU/s/godzinę — Europa Północna|  Europa Północna       |    1     |
|Azure Cosmos DB — 100 RU/s/godzinę — Korea Południowa|    Korea Południowa     |     1    |
|Azure Cosmos DB — 100 RU/s/godzinę — Europa Zachodnia|    Europa Zachodnia     |      1   |
|Azure Cosmos DB — 100 RU/s/godzinę — Korea środkowa|   Korea Środkowa    |       1  |
|Azure Cosmos DB — 100 RU/s/godzinę — Południowe Zjednoczone Królestwo|   Południowe Zjednoczone Królestwo      |     1    |
|Azure Cosmos DB — 100 RU/s/godzinę — Zachodnie Zjednoczone Królestwo|   Zachodnie Zjednoczone Królestwo      |    1     |
|Azure Cosmos DB — 100 RU/s/godzinę — Północne Zjednoczone Królestwo |   Północne Zjednoczone Królestwo    |     1    |
|Azure Cosmos DB — 100 RU/s/godzinę — Południowe Zjednoczone Królestwo 2|   Południowe Zjednoczone Królestwo 2      |     1    |
|Azure Cosmos DB — 100 RU/s/godzinę — Wschodnie stany USA 2|  Wschodnie stany USA 2     |     1    |
|Azure Cosmos DB — 100 RU/s/godzinę — Północno-środkowe stany USA|   Północno-środkowe stany USA      |     1    |
|Azure Cosmos DB — 100 RU/s/godzinę — Zachodnie stany USA|   Zachodnie stany USA      |     1    |
|Azure Cosmos DB — 100 RU/s/godzinę — Środkowe stany USA| Środkowe stany USA        |     1    |
|Azure Cosmos DB — 100 RU/s/godzinę — Zachodnie stany USA 2|   Zachodnie stany USA 2      |      1   |
|Azure Cosmos DB — 100 RU/s/godzinę — Zachodnio-środkowe stany USA|   Zachodnio-środkowe stany USA      |       1  |
|Azure Cosmos DB — 100 RU/s/godzinę — Wschodnie stany USA|   Wschodnie stany USA      |  1       |
|Azure Cosmos DB — 100 RU/s/godzinę — Północna RPA|     Północna RPA    |   1      |
|Azure Cosmos DB — 100 RU/s/godzinę — Zachodnia RPA |    Zachodnia RPA      |    1     |
|Azure Cosmos DB — 100 RU/s/godzinę — Indie Południowe|    Indie Południowe     |    1,0375    |
|Azure Cosmos DB — 100 RU/s/godzinę — Kanada Wschodnia|   Kanada Wschodnia      |    1.1      |
|Azure Cosmos DB — 100 RU/s/godzinę — Japonia Wschodnia|   Japonia Wschodnia      |    1,125     |
|Azure Cosmos DB — 100 RU/s/godzinę — Japonia Zachodnia|     Japonia Zachodnia    |   1,125       |
|Azure Cosmos DB — 100 RU/s/godzinę — Indie Zachodnie|     Indie Zachodnie    |    1,1375     |
|Azure Cosmos DB — 100 RU/s/godzinę — Indie Środkowe|    Indie Środkowe     |  1,1375       |
|Azure Cosmos DB — 100 RU/s/godzinę — Australia Wschodnia|     Australia Wschodnia    |   1,15       |
|Azure Cosmos DB — 100 RU/s/godzinę — Kanada Środkowa|  Kanada Środkowa       |   1.2       |
|Azure Cosmos DB — 100 RU/s/godzinę — Francja Środkowa|   Francja Środkowa      |    1,25      |
|Azure Cosmos DB — 100 RU/s/godzinę — Brazylia Południowa|  Brazylia Południowa       |   1.5      |
|Azure Cosmos DB — 100 RU/s/godzinę — Australia Środkowa|   Australia Środkowa      |   1.5      |
|Azure Cosmos DB — 100 RU/s/godzinę — Australia Środkowa 2| Australia Środkowa 2        |    1.5     |
|Azure Cosmos DB — 100 RU/s/godzinę — Francja Południowa|    Francja Południowa     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Scenariusze pokazujące sposób stosowania rabatu za rezerwację

Weź pod uwagę następujące wymagania dotyczące rezerwacji:

* Wymagana przepływność: 50 000 RU/s  
* Używane regiony: 2

W takim przypadku łączne opłaty na żądanie dotyczą ilości równej 500 dla miernika 100 RU/s w tych dwóch regionach. Całkowite zużycie jednostek RU/s wynosi 100 000 co godzinę.

**Scenariusz 1**

Załóżmy na przykład, że potrzebujesz wdrożeń usługi Azure Cosmos DB w regionach Północno-środkowe stany USA i Zachodnie stany USA. Każdy region zużywa 50 000 RU/s przepływności. Zakup rezerwacji w wysokości 100 000 RU/s może całkowicie zrównoważyć opłaty za użycie na żądanie.

Rabat, który obejmuje rezerwacja, jest obliczany następująco: zużycie przepływności * współczynnik rabatu za rezerwację dla danego regionu. W przypadku regionów Północno-środkowe stany USA i Zachodnie stany USA wskaźnik rabatu za rezerwację wynosi 1. W związku z tym łączna liczba jednostek RU/s objęta rabatem to 100 000. Ta wartość jest obliczana następująco: 50 000 * 1 + 50 000 * 1 = 100 000 RU/s. Nie musisz uiszczać żadnych dodatkowych opłat według zwykłych stawek płatności zgodnie z rzeczywistym użyciem.

|Opis miernika | Region |Zużycie przepływności (RU/s) |Rabat za rezerwację dotyczący jednostek RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB — 100 RU/s/godzinę — Północno-środkowe stany USA  |   Północno-środkowe stany USA  | 50 000  | 50 000  |
|Azure Cosmos DB — 100 RU/s/godzinę — Zachodnie stany USA  |  Zachodnie stany USA   |  50 000  |  50 000 |

**Scenariusz 2**

Załóżmy na przykład, że potrzebujesz wdrożeń usługi Azure Cosmos DB w regionach Australia Środkowa 2 i Francja Południowa. Każdy region zużywa 50 000 RU/s przepływności. Zakup rezerwacji 100 000 RU/s będzie miał zastosowanie w następujący sposób (przy założeniu, że rabat zastosowano najpierw dla użycia regionu Australia Środkowa 2):

|Opis miernika | Region |Zużycie przepływności (RU/s) |Rabat za rezerwację dotyczący jednostek RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB — 100 RU/s/godzinę — Australia Środkowa 2  |  Australia Środkowa 2   |  50 000  |  50 000   |
|Azure Cosmos DB — 100 RU/s/godzinę — Francja Południowa  |  Francja Południowa   |  50 000 |  15 384  |

Użycie 50 000 jednostek w regionie Australia Środkowa 2 odpowiada 75 000 RU/s rozliczanego (czyli znormalizowanego) użycia. Ta wartość jest obliczana następująco: zużycie przepływności * współczynnik rabatu za rezerwację dla danego regionu. Wynik to 75 000 RU/s rozliczanego (znormalizowanego) zużycia. Ta wartość jest obliczana następująco: 50 000 * 1,5 = 75 000 RU/s.

Zakup rezerwacji 100 000 RU/s obejmie 75 000 RU/s w regionie Australia Środkowa 2. Pozostanie 25 000 RU/s dla regionu Francja Południowa. Z pozostałych 25 000 RU/s rabat za rezerwację zostanie zastosowany dla 15 384 RU/s w regionie Francja Południowa. Wartość rabatu jest obliczana następująco: 25 000 / 1,625 = 15 384 RU/s. Pozostałe 34 616 RU/s w regionie Francja Południowa zostanie rozliczonych według standardowych stawek płatności zgodnie z rzeczywistym użyciem.

System rozliczeń platformy Azure przypisze korzyść rozliczenia za rezerwację do pierwszego przetwarzanego wystąpienia, które pasuje do konfiguracji rezerwacji. W tym przykładzie jest to region Australia Środkowa 2.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji platformy Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

* [Co to są rezerwacje platformy Azure](save-compute-costs-reservations.md)  
* [Opłata z góry za zasoby usługi Azure Cosmos DB za pomocą zarezerwowanej pojemności usługi Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Prepay for SQL Database compute resources with Azure SQL Database reserved capacity (Opłacanie zasobów obliczeniowych usługi SQL Database z góry przy użyciu zarezerwowanej pojemności usługi Azure SQL Database)](../../azure-sql/database/reserved-capacity-overview.md)  
* [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md)  
* [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)  
* [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
* [Understand reservation usage for CSP subscriptions (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji CSP)](/partner-center/azure-reservations)