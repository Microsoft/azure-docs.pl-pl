---
title: Skalowanie zasobów
description: W tym artykule wyjaśniono, jak skalować bazę danych w Azure SQL Database i wystąpieniu zarządzanym SQL, dodając lub usuwając przydzielone zasoby.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: ca1a2edec70b13f111ffd89278aa39d1ddea7f67
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035646"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamiczne skalowanie zasobów bazy danych przy minimalnym przestoju
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database i wystąpienie zarządzane SQL umożliwiają dynamiczne dodawanie większej liczby zasobów do bazy danych z minimalnym [czasem przestoju](https://azure.microsoft.com/support/legal/sla/sql-database); Jednak istnieje przedział czasu, w którym połączenie jest tracone do bazy danych przez krótki czas, co można wyeliminować przy użyciu logiki ponawiania.

## <a name="overview"></a>Omówienie

Gdy zapotrzebowanie na aplikację rośnie od kilku urządzeń i klientów do milionów, Azure SQL Database i skalowanie wystąpienia zarządzanego SQL na bieżąco z minimalnym przestojem. Skalowalność to jedna z najważniejszych cech platformy jako usługi (PaaS), która umożliwia dynamiczne dodawanie zasobów do usługi w razie potrzeby. Azure SQL Database umożliwia łatwe zmienianie zasobów (mocy procesora CPU, pamięci, przepływności we/wy i magazynu) przyznanych do baz danych.

Możesz ograniczyć problemy z wydajnością z powodu zwiększonego użycia aplikacji, których nie można naprawić przy użyciu metod indeksowania lub zapisywania zapytań. Dodanie większej liczby zasobów pozwala szybko reagować, gdy baza danych osiągnie bieżące limity zasobów i potrzebuje większej mocy do obsługi przychodzącego obciążenia. Azure SQL Database również umożliwia skalowanie zasobów w dół, gdy nie są one potrzebne do obniżenia kosztów.

Nie musisz martwić się o zakup sprzętu i zmienianie podstawowej infrastruktury. Skalowanie bazy danych może być łatwo wykonywane za pośrednictwem Azure Portal za pomocą suwaka.

![Skalowanie wydajności bazy danych](./media/scale-resources/scale-performance.svg)

Azure SQL Database oferuje model [zakupu oparty](service-tiers-dtu.md) na jednostkach DTU i [model zakupu oparty na rdzeń wirtualny](service-tiers-vcore.md), podczas gdy wystąpienie zarządzane usługi Azure SQL oferuje tylko [model zakupu oparty na rdzeń wirtualny](service-tiers-vcore.md). 

- [Model zakupu oparty na](service-tiers-dtu.md) jednostkach DTU oferuje mieszankę zasobów obliczeniowych, pamięci i operacji we/wy w trzech warstwach usług w celu obsługi lekkich i intensywnych obciążeń bazy danych: Basic, standard i Premium. Poziomy wydajności w każdej warstwie udostępniają różne kombinacje tych zasobów i można do nich dodawać kolejne zasoby magazynu.
- [Model zakupu oparty na rdzeń wirtualny](service-tiers-vcore.md) umożliwia wybranie liczby rdzeni wirtualnych, ilości lub pamięci oraz ilości i szybkości magazynu. Ten model zakupów oferuje trzy warstwy usług: Ogólnego przeznaczenia, Krytyczne dla działania firmy i skalowania.

Możesz utworzyć swoją pierwszą aplikację w małej, pojedynczej bazie danych przy niskich kosztach miesięcznie w warstwie usług podstawowa, standardowa lub Ogólnego przeznaczenia, a następnie ręcznie zmienić jej warstwę usług lub programowo w dowolnym momencie do warstwy usług premium lub Krytyczne dla działania firmy, aby zaspokoić potrzeby rozwiązania. Wydajność można dostosować bez przestoju aplikacji i przerwy w świadczeniu usługi klientom. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów oraz płacenie tylko za potrzebne zasoby i tylko gdy są używane.

> [!NOTE]
> Dynamiczna skalowalność różni się od skalowania automatycznego. Skalowanie automatyczne polega na tym, że usługa jest skalowana automatycznie na podstawie kryteriów, a dynamiczna skalowalność umożliwia skalowanie ręczne z minimalnym czasem przestoju.

Pojedyncze bazy danych w Azure SQL Database obsługują ręczną skalowalność, ale nie Skalowanie automatyczne. Aby zapewnić środowisko bardziej *zautomatyzowane*, rozważ użycie elastycznych pul baz danych, które umożliwiają bazom danych udostępnianie zasobów w puli w zależności od potrzeb.
Istnieją jednak skrypty, które mogą ułatwić automatyzację skalowalności pojedynczej bazy danych w Azure SQL Database. Na przykład zobacz temat [Monitorowanie i skalowanie pojedynczej bazy danych SQL za pomocą programu PowerShell](scripts/monitor-and-scale-database-powershell.md).

Możesz zmienić [warstwy usług jednostek DTU](service-tiers-dtu.md) lub [charakterystyki rdzeni wirtualnych](resource-limits-vcore-single-databases.md) w dowolnym momencie przy minimalnych przestojach w działaniu aplikacji (zwykle poniżej czterech sekund). W przypadku wielu firm i aplikacji możliwość tworzenia baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza jeśli wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. W tym scenariuszu używana jest pula elastyczna z określoną liczbą jednostek eDTU, które są współużytkowane przez wiele baz danych w puli.

![Wprowadzenie do usługi SQL Database: jednostki DTU z jedną bazą danych według warstwy i poziomu.](./media/scale-resources/single_db_dtus.png)

Azure SQL Database oferuje możliwość dynamicznego skalowania baz danych:

- Za pomocą [pojedynczej bazy danych](single-database-scale.md)można używać modeli [DTU](resource-limits-dtu-single-databases.md) lub [rdzeń wirtualny](resource-limits-vcore-single-databases.md) do definiowania maksymalnej ilości zasobów, które zostaną przypisane do poszczególnych baz danych.
- [Pule elastyczne](elastic-pool-scale.md) umożliwiają zdefiniowanie maksymalnego limitu zasobów dla każdej grupy baz danych w puli.

Wystąpienie zarządzane Azure SQL umożliwia również skalowanie: 

- [Wystąpienie zarządzane SQL](../managed-instance/sql-managed-instance-paas-overview.md) używa trybu [rdzeni wirtualnych](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) i pozwala definiować maksymalne rdzenie procesora CPU i maksymalną ilość miejsca do magazynowania przydzieloną do wystąpienia. Wszystkie bazy danych w ramach wystąpienia zarządzanego będą współużytkować zasoby przydzielono do wystąpienia.

Inicjowanie działania skalowania w górę lub w dół w dowolnym z tych typów spowoduje ponowne uruchomienie procesu aparatu bazy danych i przeniesienie go do innej maszyny wirtualnej w razie potrzeby. Proces przeniesienia aparatu bazy danych do nowej maszyny wirtualnej jest w **trybie online** , w którym można kontynuować korzystanie z istniejącej usługi Azure SQL Database, gdy proces jest w toku. Gdy docelowy aparat bazy danych zostanie w pełni zainicjowany i będzie gotowy do przetwarzania zapytań, połączenia zostaną [przełączone ze źródła do docelowego aparatu bazy danych](single-database-scale.md#impact).

> [!NOTE]
> Nie zaleca się skalowania wystąpienia zarządzanego, jeśli długotrwała transakcja, taka jak Importowanie danych, zadania przetwarzania danych, Odbudowywanie indeksu itp., jest uruchomiona, lub jeśli masz aktywne połączenie w wystąpieniu. Aby uniknąć dłuższego czasu wykonywania skalowania niż zwykle, należy skalować wystąpienie po zakończeniu wszystkich długotrwałych operacji.

> [!NOTE]
> Po zakończeniu procesu skalowania w górę/w dół można oczekiwać krótkiego przerwania połączenia. Jeśli wdrożono [logikę ponowień dla standardowych błędów przejściowych](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors), nie będzie można wyszukać trybu failover.

## <a name="alternative-scale-methods"></a>Alternatywne metody skalowania

Skalowanie zasobów jest najłatwiejszym i najbardziej efektywnym sposobem poprawy wydajności bazy danych bez zmiany kodu bazy danych lub aplikacji. W niektórych przypadkach nawet najwyższa warstwa usług, rozmiary obliczeniowe i optymalizacje wydajności mogą nie obsłużyć obciążenia w sposób skuteczny i ekonomiczny. W takim przypadku dostępne są następujące dodatkowe opcje skalowania bazy danych:

- [Skalowanie](read-scale-out.md) w poziomie to dostępna funkcja, w której jest pobierana jedna replika tylko do odczytu danych, w której można wykonywać wysokie zapytania tylko do odczytu, takie jak raporty. Replika tylko do odczytu będzie obsługiwać obciążenie tylko do odczytu bez wpływu na użycie zasobów w podstawowej bazie danych.
- [Fragmentowania Database](elastic-scale-introduction.md) to zestaw technik, które umożliwiają dzielenie danych na kilka baz danych i ich niezależne skalowanie.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o ulepszaniu wydajności bazy danych przez zmianę kodu bazy danych, zobacz [Znajdowanie i stosowanie zaleceń dotyczących wydajności](database-advisor-find-recommendations-portal.md).
- Aby uzyskać informacje na temat zapewnienia, że wbudowana Analiza bazy danych optymalizuje bazę danych, zobacz [dostrajanie automatyczne](automatic-tuning-overview.md).
- Aby uzyskać informacje o skalowaniu w poziomie Azure SQL Database, zobacz jak [używać replik tylko do odczytu do równoważenia obciążenia zapytań tylko do odczytu](read-scale-out.md).
- Aby uzyskać informacje dotyczące fragmentowania bazy danych, zobacz [skalowanie w dół przy użyciu Azure SQL Database](elastic-scale-introduction.md).
