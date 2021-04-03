---
title: Warstwy usług — model zakupu oparty na jednostkach DTU
description: Dowiedz się więcej o warstwach usług w modelu zakupu opartym na jednostkach DTU dla Azure SQL Database, aby zapewnić rozmiary zasobów obliczeniowych i magazynowych.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 10/15/2020
ms.reviewer: ''
ms.openlocfilehash: 19178359d1eeb935499a01828f7c53b123e17571
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793181"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Warstwy usług w modelu zakupów opartym na jednostkach DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Warstwy usług w modelu zakupu opartego na jednostkach DTU są zróżnicowane przez wiele rozmiarów obliczeń ze stałą ilością dołączonego magazynu, stałym okresem przechowywania kopii zapasowych i ustaloną ceną. Wszystkie warstwy usług w modelu zakupu opartego na jednostkach DTU zapewniają elastyczność zmiany rozmiarów obliczeń przy minimalnym [przestoju](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/). Jednak istnieje przedział czasu, w którym połączenie jest tracone do bazy danych przez krótki czas, co można wyeliminować przy użyciu logiki ponawiania. Pojedyncze bazy danych i elastyczne pule są rozliczane godzinowo na podstawie warstwy usług i rozmiaru obliczeniowego.

> [!IMPORTANT]
> [Wystąpienie zarządzane Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) nie obsługuje modelu zakupu opartego na jednostkach DTU. 


> [!NOTE]
> Aby uzyskać informacje na temat warstw usług opartych na rdzeń wirtualny, zobacz [warstwy usług oparte na rdzeń wirtualny](service-tiers-vcore.md). Aby uzyskać informacje o rozróżnieniu warstw usług opartych na jednostkach DTU i warstwach usług opartych na rdzeń wirtualny, zobacz [zakupy modeli](purchasing-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>Porównanie warstw usług opartych na jednostkach DTU

Wybór warstwy usług zależy głównie od ciągłości biznesowej, magazynu i wymagań dotyczących wydajności.

||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: |--:| --:|
|**Docelowe obciążenie**|Programowanie i produkcja|Programowanie i produkcja|Programowanie i produkcja|
|**Umowa SLA dla czasu działania**|99,99%|99,99%|99,99%|
|**Maksymalne przechowywanie kopii zapasowych**|7 dni|35 dni|35 dni|
|**Procesor CPU**|Niski|Niski, średni, wysoki|Średni, wysoki|
|**Operacje we/wy (przybliżone)**\* |1-4 operacji we/wy na jednostkę DTU| 1-4 operacji we/wy na jednostkę DTU | 25 operacji we/wy na jednostkę DTU|
|**Opóźnienie we/wy (przybliżone)**|5 ms (odczyt), 10 ms (zapis)|5 ms (odczyt), 10 ms (zapis)|2 ms (odczyt i zapis)|
|**Indeksowanie magazynu kolumn** |Nie dotyczy|S3 i nowsze|Obsługiwane|
|**Przetwarzanie OLTP w pamięci**|NIE DOTYCZY|NIE DOTYCZY|Obsługiwane|

\* Wszystkie operacje we/wy odczytu i zapisu dla plików danych, w tym w przypadku tworzenia w tle (Checkpoint i składnika zapisywania z opóźnieniem)

> [!IMPORTANT]
> Cele usługi Basic, S0, S1 i S2 zapewniają mniej niż jeden rdzeń wirtualny (CPU).  W przypadku obciążeń intensywnie korzystających z procesora CPU zaleca się przeznaczenie usługi S3 lub wyższej. 
>
> W celach usługi Basic, S0 i S1 pliki bazy danych są przechowywane w usłudze Azure Standard Storage, która używa nośnika magazynu opartego na dysku twardym (dysk twardy). Te cele usługi najlepiej nadają się do programowania, testowania i innych rzadko używanych obciążeń, które są mniej wrażliwe na zmienności wydajności.
>

> [!TIP]
> Aby wyświetlić rzeczywiste limity [ładu zasobów](resource-limits-logical-server.md#resource-governance) dla bazy danych lub puli elastycznej, wykonaj zapytanie do widoku [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) .

> [!NOTE]
> Możesz uzyskać bezpłatną bazę danych w Azure SQL Database w podstawowej warstwie usług w połączeniu z bezpłatnym kontem platformy Azure, aby poznać platformę Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie zarządzanej bazy danych w chmurze przy użyciu bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Liczba jednostek DTU i magazyn pojedynczej bazy danych

Rozmiary obliczeń są wyrażone w kategoriach jednostek transakcji bazy danych (DTU) dla pojedynczych baz danych i jednostek transakcji Elastic Database (jednostek eDTU) dla pul elastycznych. Aby uzyskać więcej informacji na temat DTU i jednostek eDTU, zobacz [model zakupów oparty na](purchasing-models.md#dtu-based-purchasing-model)jednostkach DTU.

||Podstawowa|Standardowa (Standard)|Premium|
| :-- | --: | --: | --: |
| **Maksymalny rozmiar magazynu** | 2 GB | 1 TB | 4 TB  |
| **Maksymalna DTU** | 5 | 3000 | 4000 |

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Limity liczby jednostek eDTU puli elastycznej, magazynu i bazy danych w puli

|| **Podstawowa** | **Standardowa** | **Premium** |
| :-- | --: | --: | --: |
| **Maksymalny rozmiar magazynu na bazę danych**  | 2 GB | 1 TB | 1 TB |
| **Maksymalny rozmiar magazynu na pulę** | 156 GB | 4 TB | 4 TB |
| **Maksymalna liczba jednostek eDTU na bazę danych** | 5 | 3000 | 4000 |
| **Maksymalna liczba jednostek eDTU na pulę** | 1600 | 3000 | 4000 |
| **Maksymalna liczba baz danych na pulę** | 500  | 500 | 100 |

> [!IMPORTANT]
> Więcej niż 1 TB magazynu w warstwie Premium jest obecnie dostępne we wszystkich regionach z wyjątkiem: Chiny Wschodnie, Chiny Północne, Niemcy środkowe i Niemcy północno-wschodnie. W tych regionach maksymalna wielkość magazynu w warstwie Premium jest ograniczona do 1 TB.  Aby uzyskać więcej informacji, zobacz [bieżące ograniczenia poziomów P11–P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

## <a name="dtu-benchmark"></a>Wzorzec jednostek DTU

Cechy fizyczne (procesor CPU, pamięć, we/wy) skojarzone z każdą miarą jednostek DTU są skalibrowane przy użyciu testu porównawczego, który symuluje rzeczywiste obciążenie bazy danych.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korelacja wyników testów porównawczych z rzeczywistą światową bazą danych

Ważne jest, aby zrozumieć, że wszystkie wzorce są reprezentatywne i tylko indykatywne. Stawki za transakcje uzyskane przy użyciu aplikacji testowej nie będą takie same, jak te, które mogą zostać osiągnięte przez inne aplikacje. Wzorzec ten składa się z kolekcji różnych typów transakcji uruchamianych na schemacie zawierającym zakres tabel i typów danych. Chociaż wzorzec wykonuje te same operacje podstawowe, które są wspólne dla wszystkich obciążeń OLTP, nie reprezentuje żadnej określonej klasy bazy danych lub aplikacji. Celem testu porównawczego jest zapewnienie rozsądnego przewodnika względem względnej wydajności bazy danych, która może być oczekiwana podczas skalowania w górę lub w dół między wielkościami obliczeniowymi. W rzeczywistości bazy danych mają różne rozmiary i złożoność, napotykają różne kombinacje obciążeń i reagują na różne sposoby. Na przykład aplikacja intensywnie korzystające z operacji we/wy może szybko trafiać progi we/wy, a aplikacja intensywnie korzystających z procesora CPU może szybko napotkać limity procesora CPU. Nie ma gwarancji, że jakakolwiek konkretna baza danych będzie skalowana w taki sam sposób jak w przypadku testu porównawczego w przypadku wzrostu obciążenia.

Wzorzec i jego metodologia zostały opisane bardziej szczegółowo poniżej.

### <a name="benchmark-summary"></a>Podsumowanie testu porównawczego

Test porównawczy mierzy wydajność różnych podstawowych operacji baz danych, które często występują w obciążeniach przetwarzania transakcji online (OLTP). Mimo że test porównawczy został zaprojektowany z myślą o obliczeniach w chmurze, schemat bazy danych, populacja danych i transakcje zostały zaprojektowane tak, aby były szeroko reprezentatywne dla podstawowych elementów najczęściej używanych w obciążeniach OLTP.

### <a name="schema"></a>Schemat

Schemat został zaprojektowany pod kątem wystarczającej różnorodności i złożoności do obsługi szerokiego zakresu operacji. Testy porównawcze są uruchamiane względem bazy danych składającej się z sześciu tabel. Tabele dzielą się na trzy kategorie: stały rozmiar, skalowanie i zwiększanie. Istnieją dwie tabele o stałym rozmiarze; trzy tabele skalowania; i jednej rosnącej tabeli. Tabele o ustalonym rozmiarze mają stałą liczbę wierszy. Skalowanie tabel ma Kardynalność, która jest proporcjonalna do wydajności bazy danych, ale nie zmienia się podczas testu porównawczego. Rosnąca tabela jest taka, jak tabela skalowania podczas ładowania początkowego, ale Kardynalność zmienia się w trakcie uruchamiania testu porównawczego w miarę wstawiania i usuwania wierszy.

Schemat zawiera różne typy danych, w tym liczby całkowite, cyfry, znaki i daty/godziny. Schemat zawiera klucze podstawowe i pomocnicze, ale nie wszystkie klucze obce — oznacza to, że nie ma żadnych ograniczeń integralności referencyjnej między tabelami.

Program generowania danych generuje dane dla początkowej bazy danych. Dane całkowite i liczbowe są generowane z różnymi strategiami. W niektórych przypadkach wartości są rozproszone losowo względem zakresu. W innych przypadkach zestaw wartości jest losowo permuted, aby zapewnić, że jest utrzymywana określona dystrybucja. Pola tekstowe są generowane na podstawie ważonej listy wyrazów, aby generować realistyczne dane.

Rozmiar bazy danych jest określany na podstawie "współczynnika skalowania". Współczynnik skalowania (skrócony jako SF) określa Kardynalność tabel skalowania i powiększania. Zgodnie z poniższym opisem w sekcji Użytkownicy i tempem, rozmiar bazy danych, liczba użytkowników i Maksymalna wydajność wszystkie są skalowane proporcjonalnie do siebie.

### <a name="transactions"></a>Transakcje

Obciążenie obejmuje dziewięć typów transakcji, jak pokazano w poniższej tabeli. Każda transakcja została zaprojektowana w celu wyróżnienia określonego zestawu charakterystyk systemu w aparacie bazy danych i sprzęcie systemowym z dużym kontrastem od innych transakcji. Takie podejście ułatwia ocenę wpływu różnych składników do ogólnej wydajności. Na przykład transakcja "Odczyt ciężki" powoduje znaczną liczbę operacji odczytu z dysku.

| Typ transakcji | Opis |
| --- | --- |
| Przeczytaj Lite |ZAZNACZENIA w pamięci; tylko do odczytu |
| Odczytaj nośnik |ZAZNACZENIA głównie w pamięci; tylko do odczytu |
| Czytaj ciężki |ZAZNACZENIA przede wszystkim nie w pamięci; tylko do odczytu |
| Aktualizacja Lite |AKTUALIZACJI w pamięci; Odczyt i zapis |
| Aktualizuj ciężki |AKTUALIZACJI przede wszystkim nie w pamięci; Odczyt i zapis |
| Wstaw Lite |WSTAWIENIA w pamięci; Odczyt i zapis |
| Wstaw ciężki |WSTAWIENIA przede wszystkim nie w pamięci; Odczyt i zapis |
| Usuń |USUNIĘTY mieszanie w pamięci, a nie w pamięci; Odczyt i zapis |
| Duże użycie procesora |ZAZNACZENIA w pamięci; relatywnie duże obciążenie procesora CPU; tylko do odczytu |

### <a name="workload-mix"></a>Mieszanie obciążeń

Transakcje są wybierane losowo z dystrybucji ważonej przy użyciu następującej ogólnej mieszanki. Całkowity mieszany ma współczynnik odczytu/zapisu wynoszący około 2:1.

| Typ transakcji | % Mieszanki |
| --- | --- |
| Przeczytaj Lite |35 |
| Odczytaj nośnik |20 |
| Czytaj ciężki |5 |
| Aktualizacja Lite |20 |
| Aktualizuj ciężki |3 |
| Wstaw Lite |3 |
| Wstaw ciężki |2 |
| Usuń |2 |
| Duże użycie procesora |10 |

### <a name="users-and-pacing"></a>Użytkownicy i tempem

Obciążenie porównawcze jest oparte na narzędziu, które przesyła transakcje w zestawie połączeń, aby symulować zachowanie wielu równoczesnych użytkowników. Mimo że wszystkie połączenia i transakcje są generowane maszynowo, dla uproszczenia odnosimy się do tych połączeń jako "Użytkownicy". Mimo że każdy użytkownik działa niezależnie od wszystkich innych użytkowników, wszyscy użytkownicy wykonują ten sam cykl kroków przedstawionych poniżej:

1. Ustanów połączenie z bazą danych.
2. Powtarzaj do momentu opuszczenia:
   - Wybieraj losowo transakcję (z rozkładu ważonego).
   - Wykonaj wybraną transakcję i zmierz czas odpowiedzi.
   - Poczekaj na opóźnienie tempem.
3. Zamknij połączenie z bazą danych.
4. Opuść.

Opóźnienie tempem (w kroku 2C) jest wybierane losowo, ale z rozkładem, który ma średnią 1,0 sekund. Z tego względu każdy użytkownik może, średnio, generować co najwyżej jedną transakcję na sekundę.

### <a name="scaling-rules"></a>Skalowanie reguł

Liczba użytkowników jest określana na podstawie rozmiaru bazy danych (w jednostkach skalowania w poziomie). Dla każdego pięciu jednostek współczynnika skalowania jest jednego użytkownika. Ze względu na opóźnienie tempem jeden użytkownik może generować co najwyżej jedną transakcję na sekundę.

Na przykład baza danych o skali 500 (SF = 500) będzie mieć 100 użytkowników i może osiągnąć maksymalną stawkę 100 TPS. Zwiększenie szybkości TPS wymaga większej liczby użytkowników i większej bazy danych.

### <a name="measurement-duration"></a>Czas trwania pomiaru

Prawidłowy przebieg testu porównawczego wymaga czasu trwania pomiaru stanu ustalonego co najmniej jedną godzinę.

### <a name="metrics"></a>Metryki

Kluczowe metryki testu porównawczego to przepływność i czas odpowiedzi.

- Przepływność jest istotnym środkiem wydajności w teście porównawczym. Przepływność jest raportowana w transakcjach na jednostkę czasu, licząc wszystkie typy transakcji.
- Czas odpowiedzi to miara przewidywalności wydajności. Ograniczenie czasu odpowiedzi różni się w zależności od klasy usługi, a wyższe klasy usługi mają bardziej rygorystyczne wymagania dotyczące czasu odpowiedzi, jak pokazano poniżej.

| Klasa usługi | Pomiar przepływności | Wymagany czas odpowiedzi |
| --- | --- | --- |
| Premium |Liczba transakcji na sekundę |używany 95. percentyl o 0,5 sekund |
| Standardowa (Standard) |Liczba transakcji na minutę |90 percentyl o 1,0 s |
| Podstawowa |Liczba transakcji na godzinę |80th percentyl o 2,0 sekund |

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pojedynczych baz danych, zobacz [SQL Database limity zasobów opartych na jednostkach DTU dla pojedynczych baz danych](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pul elastycznych, zobacz [SQL Database limitów zasobów opartych](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)na jednostkach DTU.