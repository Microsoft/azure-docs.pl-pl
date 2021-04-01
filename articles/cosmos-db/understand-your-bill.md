---
title: Informacje na temat Azure Cosmos DB rachunku
description: W tym artykule wyjaśniono, jak zrozumieć Azure Cosmos DB rachunku przy użyciu przykładów.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: b0d27cb2f941db374a144cbd5c026444730e24e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247509"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Informacje o rachunku za korzystanie z usługi Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Jako w pełni zarządzana usługa bazy danych w chmurze, Azure Cosmos DB upraszcza rozliczenia przez naliczanie opłat tylko za operacje bazy danych i Magazyn używany. Nie ma dodatkowych opłat licencyjnych, sprzętu, kosztów narzędzi ani kosztów infrastruktury w porównaniu z rozwiązaniami lokalnymi lub IaaS. Gdy rozważasz możliwości wieloregionu Azure Cosmos DB, usługa bazy danych zapewnia znaczną redukcję kosztów w porównaniu do istniejących rozwiązań lokalnych lub IaaS.

- **Operacje bazy danych**: sposób naliczania opłat za operacje bazy danych zależy od typu konta usługi Azure Cosmos, którego używasz.

  - **Zainicjowana przepływność**: opłaty są naliczane co godzinę za maksymalną zainicjowaną przepływność przez daną godzinę, w przyrostach wynoszących 100 ru/s.
  - **Bezserwerowe**: opłaty są naliczane co godzinę dla łącznej liczby jednostek żądania zużywanych przez operacje bazy danych.

- **Magazyn**: jest rozliczana stała stawka za łączną ilość miejsca w magazynie (w GB) zużywaną przez dane i indeksy w danej godzinie.

Aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) .

W tym artykule przedstawiono kilka przykładów, które ułatwiają zapoznanie się z informacjami widocznymi na rachunku miesięcznym. Liczby pokazane w przykładach mogą się różnić, jeśli dla kontenerów usługi Azure Cosmos aprowizowano inną przepływność, jeśli są one dostępne w wielu regionach lub są uruchamiane dla innych okresów w ciągu miesiąca. Wszystkie przykłady w tym artykule obliczają rachunk na podstawie informacji o cenach wyświetlanych na [stronie cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).

> [!NOTE]
> Opłaty są naliczane za każdą część godziny zegarowej, a nie czas trwania 60 minuty. Wszystkie przykłady przedstawione w tym dokumencie są oparte na cenie konta usługi Azure Cosmos, które zostało wdrożone w regionie nierządowym w Stanach Zjednoczonych. Ceny i obliczenia różnią się w zależności od regionu, z którego korzystasz, aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="billing-examples"></a>Przykłady rozliczeń

### <a name="billing-example---provisioned-throughput-on-a-container-full-month"></a>Przykład rozliczeń — zainicjowana przepływność na kontenerze (pełny miesiąc)

* Załóżmy, że skonfigurowano przepływność 1 000 RU/s w kontenerze i istnieje przez 24 godziny * 30 dni w miesiącu = 720 godz.  

* 1 000 RU/s to 10 jednostek 100 RU/s na godzinę dla każdej godziny, gdy kontenery istnieją (czyli 1000/100 = 10). 

* Mnożenie 10 jednostek na godzinę przez koszt $0,008 (za 100 RU/s na godzinę) = $0,08 na godzinę. 

* Mnożenie $0,08 na godzinę przez liczbę godzin w miesiącu równą $0,08 * 24 godziny * 30 dni = $57,60 przez miesiąc.  

* Łączny rachunek miesięczny będzie przedstawiał 7 200 jednostek (z 100 jednostek ru), które będą kosztowały $57,60.

### <a name="billing-example---provisioned-throughput-on-a-container-partial-month"></a>Przykład rozliczeń — zainicjowana przepływność na kontenerze (część miesiąca)

* Załóżmy, że utworzymy kontener z zainicjowaną przepływność wynoszącą 2 500 RU/s. Kontener jest przez 24 godziny w ciągu miesiąca (na przykład po jego utworzeniu zostanie on usunięty przez 24 godziny).  

* Następnie zobaczymy 600 jednostek na rachunku (2 500 RU/s/100 RU/sek/Unit * 24 godziny). Koszt będzie wynosił $4,80 (600 jednostek * $0.008/jednostka).

* Łączny rachunek za miesiąc będzie wynosił $4,80.

### <a name="billing-example---serverless-container"></a>Przykład rozliczeń — kontener bezserwerowy

* Załóżmy, że utworzymy kontener bezserwerowy. 

* W ciągu miesiąca firma Microsoft wystawia żądania bazy danych zużywające łącznie 500 000 jednostek żądań. Koszt będzie wynosił $0,125 (500 000 * $0,25/mln).

* Łączny rachunek za miesiąc będzie wynosił $0,125.

### <a name="billing-rate-if-storage-size-changes"></a>Stawka rozliczeń w przypadku zmiany rozmiaru magazynu

Pojemność magazynu jest rozliczana w jednostkach maksymalnej godzinowej ilości przechowywanych danych, w GB, w okresie miesięcznym. Jeśli na przykład wykorzystano 100 GB miejsca w magazynie przez połowę miesiąca i 50 GB przez drugą połowę miesiąca, opłaty są naliczane za równowartość 75 GB miejsca w magazynie w tym miesiącu.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Stawka rozliczeniowa, gdy kontener lub zestaw kontenerów są aktywne przez czas krótszy niż godzina

Jest naliczana stała stawka za każdą godzinę istnienia kontenera lub bazy danych, niezależnie od użycia lub aktywności kontenera lub bazy danych krótszej niż godzina. Jeśli na przykład utworzysz kontener lub bazę danych i usuniesz ją 5 minut później, rachunek będzie obejmował godzinę.

### <a name="billing-rate-when-provisioned-throughput-on-a-container-or-database-scales-updown"></a>Stawka rozliczenia w przypadku skalowania w górę lub w dół w celu zainicjowania obsługi przepływności dla kontenera lub bazy danych

W przypadku zwiększenia przepływności aprowizacji o godzinie 9:30 od 400 RU/s do 1 000 RU/s, a następnie obniżenia przepływności o przepustowości do 10:45 z powrotem do 400 RU/s, opłata zostanie naliczona za dwie godziny, w ciągu 1 000 RU/s. 

W przypadku zwiększenia elastycznej przepływności dla kontenera lub zestawu kontenerów o godzinie 9:30 od 100 – K RU/s do 200-K RU/s, a następnie obniżenia przepływności w systemie 10:45 z powrotem do 100-K ru/s, zostanie naliczona opłata za dwie godziny na 200 K RU/s.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Przykład rozliczeń: wiele kontenerów, z których każdy ma dedykowany Tryb przepływności

* Jeśli utworzysz konto usługi Azure Cosmos w regionie Wschodnie stany USA 2 z dwoma kontenerami z zainicjowaną przepływność wynoszącą 500 RU/s i 700 RU/s, należy dysponować łączną przepustowością dla 1 200 RU/s.  

* Opłata zostanie naliczona za 1200/100 * $0,008 = $0.096/godzinę. 

* Jeśli wymagana jest zmiana przepływności i zwiększono pojemność każdego kontenera o 500 RU/s, podczas tworzenia nowego nieograniczonego kontenera z 20 000 RU/s, ogólna pojemność będzie równa 22 200 RU/s (1 000 RU/s + 1 200 RU/s + 20, 000RU/s).  

* Następnie rachunek zostanie zmieniony na: $0,008 x 222 = $1.776/godz. 

* W miesiącu 720 godzin (24 godziny * 30 dni), jeśli w przypadku przepływności o pojemności 500 godz. została 1 200 RU/s, a w przypadku pozostałych usług 220 godz. o nieprzewidzianej przepływności to 22 200 RU/s, miesięczne rozliczenia: 500 x $0.096/godzina + 220 x $1.776/godzina = $438.72/miesiąc.

:::image type="content" source="./media/understand-your-bill/bill-example1.png" alt-text="Przykład dedykowanej opłaty za przepływność":::

### <a name="billing-example-containers-with-shared-provisioned-throughput-mode"></a>Przykład rozliczania: kontenery z udostępnionym (aprowizacji) trybem przepływności

* Jeśli utworzysz konto usługi Azure Cosmos w regionie Wschodnie stany USA 2 przy użyciu dwóch baz danych usługi Azure Cosmos (z zestawem kontenerów, które współużytkują przepływność na poziomie bazy danych) z elastyczną przepływność wynoszącą 50-K RU/s i 70-K RU/s, należy dysponować łączną przepustowością dla 120 K RU/s.  

* Opłata zostanie naliczona 1200 x $0,008 = $9.60/godz. 

* Jeśli wymagania dotyczące przepływności uległy zmianie, a przepustowość każdej bazy danych została zwiększona o 10 000 jednostek RU na sekundę dla każdej bazy danych, po dodaniu nowego kontenera do pierwszej bazy danych z trybem dedykowanej przepływności o wartości 15 – K/s do udostępnionej bazy danych przepływności ogólna pojemność będzie równa 155-K RU/s (60 K RU/s + 80 K RU/s + 15 K RU/s).  

* Następnie rachunek zostanie zmieniony na: 1 550 * $0,008 = $12.40/godz.  

* W miesiącu 720 godzin, jeśli dla przepływności przewidzianej przez 300 godz. została 120-K RU/s, a dla pozostałych usług 420 godz. zainicjowana przepływność wynosi 155 – K RU/s, zostanie wyświetlone miesięczne rozliczenie: 300 x $9.60/godzina + 420 x $12.40/Hour = $2 880 + $5 208 = $8088/miesiąc. 

:::image type="content" source="./media/understand-your-bill/bill-example2.png" alt-text="Przykład wspólnej przepływności":::

## <a name="billing-examples-with-geo-replication"></a>Przykłady rozliczeń z replikacją geograficzną  

W dowolnym momencie możesz dodawać i usuwać regiony platformy Azure w dowolnym miejscu na świecie do konta usługi Azure Cosmos Database. Przepływność skonfigurowana dla różnych baz danych i kontenerów usługi Azure Cosmos zostanie zarezerwowana w każdym z regionów świadczenia usługi Azure skojarzonych z kontem bazy danych usługi Azure Cosmos. Jeśli łączna liczba zainicjowanych przepływności (RU/s) skonfigurowanych dla wszystkich baz danych i kontenerów w ramach konta usługi Azure Cosmos Database (przyznanych na godzinę) to T, a liczba regionów platformy Azure skojarzonych z kontem bazy danych to N, całkowita zainicjowana przepływność dla danej godziny dla konta usługi Azure Cosmos Database jest równa T x N RU/s. Elastyczna przepływność (pojedynczy region zapisu) koszty $0.008/godzinę na 100 RU/s i elastyczna przepływność z wieloma regionami do zapisu (Konfiguracja w wielu regionach) koszty $0,016;/za godzinę na 100 RU/s (zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)). Niezależnie od tego, czy jego pojedynczy region zapisu lub wiele regionów zapisu Azure Cosmos DB umożliwia odczytywanie danych z dowolnego regionu.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Przykład rozliczeń: wieloregionowe konto usługi Azure Cosmos, zapisy jednego regionu

Załóżmy, że masz kontener platformy Azure Cosmos w regionie zachodnie stany USA. Kontener jest tworzony z szybkością przepływności na 10 000 jednostek RU/s i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzy regiony (Wschodnie stany USA, Europa Północna i Azja Wschodnia) do konta usługi Azure Cosmos, z których każdy ma ten sam magazyn i przepływność. Łączny rachunek miesięczny będzie wynosić (przy założeniu 30 dni w miesiącu). Rachunek będzie następujący: 

|**Element** |**Użycie (miesiąc)** |**Częstotliwość** |**Koszt miesięczny** |
|---------|---------|---------|-------|
|Rachunek przepływności dla kontenera w regionie zachodnie stany USA      | 10 000 RU/s * 24 * 30    |$0,008 za 100 RU/s na godzinę   |$576|
|Rachunek przepływności dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia       | 3 * 10 tys./s * 24 * 30    |$0,008 za 100 RU/s na godzinę  |$1 728|
|Magazyn dla kontenera w regionie zachodnie stany USA      | 250 GB    |$0,25/GB  |$62,50|
|Rachunek za magazyn dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Łącznie**     |     |  |**$2 554**|

*Załóżmy również, że wychodzące 100 GB danych co miesiąc z kontenera w regionie zachodnie stany USA w celu replikowania danych do regionu Wschodnie stany USA, Europa Północna i Azja Wschodnia. Opłaty są naliczane za ruch wychodzący zgodnie z stawkami za transfer danych.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Przykład rozliczeń: wieloregionowe konto platformy Azure Cosmos, zapisy z wieloregionem

Załóżmy, że utworzysz kontener usługi Azure Cosmos w regionie zachodnie stany USA. Kontener jest tworzony z szybkością przepływności na 10 000 jednostek RU/s i przechowujesz 1 TB danych w tym miesiącu. Załóżmy, że dodasz trzy regiony (Wschodnie stany USA, Europa Północna i Azja Wschodnia), z których każdy ma ten sam magazyn i przepływność, i chcesz mieć możliwość zapisu w kontenerach we wszystkich regionach skojarzonych z kontem usługi Azure Cosmos. Łączny rachunek miesięczny będzie wynosić (przy założeniu 30 dni w miesiącu) w następujący sposób:

|**Element** |**Użycie (miesiąc)**|**Częstotliwość** |**Koszt miesięczny** |
|---------|---------|---------|-------|
|Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)       | 10 000 RU/s * 24 * 30    |$0,016 za 100 RU/s na godzinę    |$1 152 |
|Rachunek przepływności dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia (wszystkie regiony są zapisywalne)        | 3 * 10 tys./s * 24 * 30    |$0,016 za 100 RU/s na godzinę   |$3 456 |
|Magazyn dla kontenera w regionie zachodnie stany USA      | 250 GB    |$0,25/GB  |$62,50|
|Rachunek za magazyn dla 3 dodatkowych regionów — Wschodnie stany USA, Europa Północna i Azja Wschodnia      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Łącznie**     |     |  |**$6 010**|

*Załóżmy również, że wychodzące 100 GB danych co miesiąc z kontenera w regionie zachodnie stany USA w celu replikowania danych do regionu Wschodnie stany USA, Europa Północna i Azja Wschodnia. Opłaty są naliczane za ruch wychodzący zgodnie z stawkami za transfer danych.*

### <a name="billing-example-azure-cosmos-account-with-multi-region-writes-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Przykład rozliczeń: konto usługi Azure Cosmos z wieloregionowymi zapisami, przepływności na poziomie bazy danych, w tym dedykowany Tryb przepływności dla niektórych kontenerów

Rozważmy poniższy przykład, w którym mamy wieloregionowe konto platformy Azure Cosmos, w którym wszystkie regiony są zapisywalne (konfiguracja wielu regionów zapisu). Dla uproszczenia przyjęto, że rozmiar magazynu pozostaje stały i nie zostanie zmieniony i pominięty w tym miejscu, aby uprościć przykład. Zainicjowana przepływność w miesiącu, w którym nadano następujące czynności (przy założeniu 30 dni lub 720 godzin): 

[0-100 godzin]:  

* Utworzyliśmy trzy regiony konto platformy Azure Cosmos (Zachodnie stany USA, Wschodnie stany USA, Europa Północna), gdzie wszystkie regiony są zapisywalne 

* Utworzyliśmy bazę danych (D1) z udostępnioną przepływności 10 000 jednostek/s 

* Utworzyliśmy bazę danych (D2) z udostępnioną przepływności 30-K RU/s i  

* Utworzyliśmy kontener (C1) z dedykowaną przepustowością 20 KB/s 

[101-200 godzin]:  

* Skalowanie bazy danych (D1) do 50 K RU/s 

* Skalowanie bazy danych (D2) do 70 K RU/s  

* Usunięto kontener (C1)  

[201-300 godzin]:  

* Utworzono kontener (C1) z dedykowaną przepływności 20 KB/s 

[301-400 godzin]:  

* Usunięto jeden z regionów z konta usługi Azure Cosmos (liczba regionów z możliwością zapisu to teraz 2) 

* Skalowanie bazy danych (D1) do 10 000 jednostek RU na sekundę 

* Skalowanie bazy danych (D2) do 80 K RU/s  

* Usunięto kontener (C1) 

[401-500 godzin]:  

* Skalowanie bazy danych (D2) do 10 000 jednostek RU na sekundę  

* Utworzono kontener (C1) z dedykowaną przepływności 20 KB/s 

[501-700 godzin]:  

* Skalowanie bazy danych (D1) do 20 KB/s  

* Skalowanie bazy danych (D2) do 100 K RU/s  

* Usunięto kontener (C1)  

[701-720 godzin]:  

* Skalowanie bazy danych (D2) do 50 K RU/s  

Na poniższej ilustracji przedstawiono wizualnie zmiany całkowitej alokowanej przepływności w czasie 720 godzin dla miesiąca. 

:::image type="content" source="./media/understand-your-bill/bill-example3.png" alt-text="Przykład rzeczywistej żywotności":::

Łączny rachunek miesięczny (przy założeniu, że 30 dni/720 godzin w miesiącu) będzie obliczany w następujący sposób:

|**Hours**  |**RU/s** |**Element** |**Użycie (co godzinę)** |**Koszty** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10 TYS. <br/>D2:30K <br/>C1:20 000 |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[101-200] |D1:50 000 <br/>D2:70K <br/>C1:-- |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5 760  |
|[201-300]  |D1:50 000 <br/>D2:70K <br/>C1:20 000 |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2 240  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6 720 |
|[301-400] |D1:10 TYS. <br/>D2:80K <br/>C1:-- |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1 440   |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2 880  |
|[401-500] |D1:10 TYS. <br>D2:10 TYS. <br>C1:20 000 |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1 280  |
|[501-700] |D1:20 000 <br>D2:100 000 <br>C1:-- |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3 840  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7 680  |
|[701-720] |D1:20 000 <br/>D2:50 000 <br/>C1:-- |Rachunek przepływności dla kontenera w regionie zachodnie stany USA (wszystkie regiony są zapisywalne)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Opłaty za przepływność za 2 dodatkowe regiony: Wschodnie stany USA, Europa Północna (wszystkie regiony są zapisywalne)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Łączny koszt miesięczny**  | |**$38 688**   |

## <a name="billing-examples-with-azure-cosmos-db-free-tier-accounts"></a><a id="azure-free-tier"></a>Przykłady rozliczeń z Azure Cosmos DB kontami w warstwie Bezpłatna

Dzięki Azure Cosmos DB warstwy Bezpłatna uzyskasz bezpłatnie 400 RU/s i 5 GB miejsca na koncie, które są stosowane na poziomie konta. Każdy RU/s i magazyn przekraczający 400 RU/s i 5 GB będą rozliczane według zwykłych stawek cenowych na stronie z cennikiem. Na rachunku nie będzie widoczna opłata ani linia za bezpłatny 400 jednostek ru/s i 5 GB, tylko te jednostki, które są objęte bezpłatną warstwą. 400 RU/s ma zastosowanie do dowolnego typu danych przepływności, automatycznego skalowania i zapisu w wieloregionach.  

> [!NOTE]
> Azure Cosmos DB bezpłatna jest różna od bezpłatnego konta platformy Azure. Bezpłatne konto platformy Azure oferuje środki na korzystanie z platformy Azure bezpłatnie, przez ograniczony czas. W przypadku korzystania z Azure Cosmos DB w ramach tego bezpłatnego konta uzyskasz 25 GB miejsca do magazynowania i 400 RU/s zainicjowanej przepływności przez 12 miesięcy.

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Przykład rozliczeń — kontener lub baza danych z zainicjowaną przepływność
- Załóżmy, że utworzymy bazę danych lub kontener na koncie warstwy Bezpłatna z 400 RU/s i 5 GB miejsca w magazynie.
- Na rachunku nie będą wyświetlane żadne opłaty za ten zasób. Opłata godzinowa i miesięczna będą $0.
- Teraz Załóżmy, że na tym samym koncie zostanie dodana kolejna baza danych lub kontener z 1000 RU/s i 10 GB miejsca w magazynie.
- Na rachunku będzie teraz wyświetlana opłata za 1000 RU/s i 10 GB miejsca w magazynie. 

### <a name="billing-example---container-with-autoscale-throughput"></a>Przykład rozliczeń — kontener z przepływem automatycznego skalowania
- Załóżmy, że w ramach konta warstwy Bezpłatna utworzysz kontener z włączoną funkcją automatycznego skalowania z maksymalną wartością RU/s z 4000 RU/s. Ten zasób będzie automatycznie skalowany między 400 RU/s-4000 RU/s. 
- Załóżmy, że w ciągu 1 godziny do 10 godzin zasób wynosi co najmniej 400 RU/s. W ciągu godziny 11 zasób jest skalowany w górę do 1000 RU/s, a następnie z powrotem do 400 RU/s w ciągu godziny.
- W godzinach od 1 do 10 opłaty zostaną naliczone $0 za przepływność, ponieważ 400 RU/s zostały objęte bezpłatną warstwą. 
- W godzinie 11 zostanie naliczona stawka za rzeczywiste 1000 RU/s-400 RU/s = 600 RU/s, ponieważ jest to najwyższa wartość RU/s w danej godzinie. Będzie to 6 jednostek z 100 RU na godzinę, więc łączny koszt przepływności dla godziny będzie wynosić 6 jednostek * $0,012 = $0,072. 
- Każdy magazyn przekraczający 5 GB zostanie rozliczony według normalnych stawek za magazyn. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Przykład rozliczeń — wieloregionowe, jedno konto regionu zapisu
- Załóżmy, że w ramach konta warstwy Bezpłatna utworzysz bazę danych lub kontener z 1200 RU/s i 10 GB miejsca w magazynie. Replikujemy konto do 3 regionów i mamy jedno konto w regionie zapisu.
- W sumie, bez warstwy Bezpłatna, opłaty są naliczane za 3 * 1200 RU/s = 3600 RU/s i 3 * 10 GB = 30 GB miejsca w magazynie.
- W przypadku rabatu warstwy Bezpłatna po usunięciu 400 RU/s i 5 GB miejsca do magazynowania zostanie naliczona stawka za rzeczywiste 3200 RU/s (32 jednostek), która ma zainicjowaną przepływność na poziomie jednego regionu zapisu i 25 GB miejsca w magazynie.
- Miesięczny koszt dla RU/s to: 32 jednostek * $0,008 * 24 godziny * 31 dni = $190,46. Miesięczny koszt magazynu to: 25 GB * 0,25/GB = $6,25. Łączny koszt to $190,46 + $6,25 = $196,71.
- Uwaga: Jeśli cena jednostkowa obiektu RU/s lub magazynu jest różna w regionach, warstwa Bezpłatna 400 RU/s i 5 GB będzie odzwierciedlać stawki regionu, w którym zostało utworzone konto.

### <a name="billing-example---multi-region-account-with-multiple-write-regions"></a>Przykład rozliczeń — wiele regionów, konto z wieloma regionami zapisu

W tym przykładzie uwzględniono [Cennik zapisu wieloregionowego](https://azure.microsoft.com/pricing/details/cosmos-db/) dla kont utworzonych po 1 grudnia 2019. 
- Załóżmy, że w ramach konta warstwy Bezpłatna utworzysz bazę danych lub kontener z 1200 RU/s i 10 GB miejsca w magazynie. Replikujemy konto do 3 regionów i mamy konto z wieloma regionami zapisu. 
- W sumie, bez warstwy Bezpłatna, opłaty są naliczane za 3 * 1200 RU/s = 3600 RU/s i 3 * 10 GB = 30 GB miejsca w magazynie.
- W przypadku rabatu warstwy Bezpłatna po usunięciu 400 RU/s i 5 GB miejsca do magazynowania zostanie naliczona stawka za rzeczywiste 3200 RU/s (32 jednostek), która zapewnia przepływność na poziomie wielu regionów zapisu oraz 25 GB miejsca w magazynie.
- Miesięczny koszt dla RU/s to: 32 jednostek * $0,016 * 24 godziny * 31 dni = $380,93. Miesięczny koszt magazynu to: 25 GB * 0,25/GB = $6,25. Łączny koszt to $380,93 + $6,25 = $387,18.

## <a name="proactively-estimating-your-monthly-bill"></a>Proaktywne szacowanie rachunku miesięcznego  

Rozważmy inny przykład, w którym chcesz proaktywnie oszacować swój rachunek przed końcem miesiąca. Możesz oszacować swój rachunek w następujący sposób:

**Koszty magazynu**

* Średni rozmiar rekordu (KB) = 1 
* Liczba rekordów = 100 000 000 
* Łączny magazyn (GB) = 100 
* Koszt miesięczny za GB = $0,25 
* Oczekiwany miesięczny koszt magazynu = $25,00 

**Koszt przepływności**

|Typ operacji| Liczba żądań na sekundę| Średni/RU/żądanie| Jednostek ru|
|----|----|----|----|
|Zapisywanie| 100 | 5 | 500|
|Odczyt| 400| 1| 400|

Łączny koszt RU/s: 500 + 400 = 900 godzinowo: 900/100 * $0,008 = $0,072 oczekiwany miesięczny koszt dla przepływności (przy założeniu 31 dni): $0,072 * 24 * 31 = $53,57

**Łączny koszt miesięczny**

Łączny koszt miesięczny = miesięczny koszt magazynu i miesięczny koszt dla przepływności łączny koszt miesięczny = $25,00 + $53,57 = $78,57

*Ceny mogą się różnić w zależności od regionu. Aby uzyskać aktualną cenę, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Rozliczanie za pomocą zarezerwowanej pojemności Azure Cosmos DB

Azure Cosmos DB zarezerwowana pojemność umożliwia zakupienie alokowanej przepływności (zarezerwowanej pojemności lub rezerwacji), która może zostać zastosowana do wszystkich baz danych i kontenerów platformy Azure Cosmos (dla dowolnego interfejsu API lub modelu danych) we wszystkich regionach świadczenia usługi Azure. Ze względu na to, że zainicjowana cena przepływności różni się w zależności od regionu, można traktować zastrzeżoną pojemność jako środki pieniężne, które zostały nabyte w ramach rabatu, które mogą być naliczane z tytułu przepustowości w poszczególnych regionach. Załóżmy na przykład, że masz konto usługi Azure Cosmos z jednym kontenerem z obsługą jednostki 50 – K RU/s i globalnie zreplikowane dwa regiony — Wschodnie stany USA i Japonia Wschodnia. Jeśli wybierzesz opcję płatność zgodnie z rzeczywistym użyciem, płacisz:  

* we wschodnich stanach USA: dla 50 – K RU/s według stawki $0,008 za 100 RU/s w tym regionie 

* w regionie Japonia Wschodnia: dla 50 – K RU/s o stawce $0,009 za 100 RU/s

Łączny rachunek (bez zarezerwowanej pojemności) (przy założeniu 30 dni lub 720 godzin): 

|**Region**| **Cena godzinowa za 100 RU/s**|**Jednostki (RU/s)**|**Kwota rozliczana (co godzinę)**| **Kwota rozliczana (miesięcznie)**|
|----|----|----|----|----|
|East US|$0,008 |50 K|$4|$2 880 |
|Japonia Wschodnia|$0,009 |50 K| $4,50 |$3 240 |
|Łącznie|||$8,50|$6 120 |

Rozważmy, że zamiast tego zakupiono zastrzeżoną pojemność. Możesz kupić zarezerwowaną pojemność dla 100 – K RU/s w cenie $56 064 przez jeden rok (za 20% rabatu) lub $6,40 na godzinę. Zobacz cennik pojemności zarezerwowanych na [stronie cennika](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Koszt przepływności (płatność zgodnie z rzeczywistym użyciem): 100 000 RU/s/100 * $0.008/godz. * 8760 godz. w roku = $70 080 

* Koszt przepływności (z zarezerwowaną pojemnością) $70 080 z rabatem w wysokości 20% = $56 064 

Rzeczywiste zakupione dane to kredyt z $8 USD za godzinę dla 100 K/s, przy użyciu cennika na wschód, przy cenie $6,40 za godzinę. Następnie można wystawić na podstawie tej wstępnie płatnej rezerwacji dotyczącej przepływności co godzinę dla alokowanej przepływności w dowolnym globalnym regionie platformy Azure, zgodnie z odpowiednimi cenami list regionalnych ustawionymi dla Twojej subskrypcji. W tym przykładzie, gdzie zainicjujesz 50 K RU/s każdego w regionach Wschodnie stany USA i Japonia Wschodnia, będziesz mieć możliwość naliczania na $8,00. zainicjowanej przepływności na godzinę i zostanie naliczona nadwyżka za $0,50 za godzinę (lub USD za miesiąc). 

|**Region**| **Cena godzinowa za 100 RU/s**|**Jednostki (RU/s)**| **Kwota rozliczana (co godzinę)**| **Kwota rozliczana (miesięcznie)**|
|----|----|----|----|----|
|East US|$0,008 |50 K|$4|$2 880 |
|Japonia Wschodnia|$0,009 |50 K| $4,50 |$3 240 |
|||Płatność zgodnie z rzeczywistym użyciem|$8,50|$6120|
|Zarezerwowana pojemność zakupiona|$0,0064 (20% rabatu) |100 jednostki RU/s lub $8 pojemności wstępnie kupione |-$8|-$5 760 |
|Rachunek netto|||(0,50 USD) |$360 |

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej o tym, [jak Cosmos DB model cenowy jest ekonomiczny dla klientów](total-cost-ownership.md)
* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów operacji odczytu i zapisu](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](./optimize-cost-reads-writes.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)
