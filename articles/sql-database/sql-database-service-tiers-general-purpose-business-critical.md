---
title: Najważniejsze znaczenie dla działania firmy
description: W tym artykule omówiono warstwy usług ogólnych i krytycznych dla firmy w modelu zakupu opartego na rdzeń wirtualny.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76937839"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database warstw usług

Azure SQL Database jest oparta na architekturze SQL Server Database Engine, która jest dostosowywana do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. Trzy warstwy usług są używane w Azure SQL Database, z których każdy ma inny model architektury. Następujące warstwy usług:

- [Ogólnego przeznaczenia](sql-database-service-tier-general-purpose.md), który jest przeznaczony do obsługi obciążeń zorientowanych na budżet.
- [Skalowanie](sql-database-service-tier-hyperscale.md), które jest przeznaczone dla większości obciążeń firmowych, zapewniając wysoce skalowalny magazyn, możliwość odczytywania skalowalnego w poziomie i szybkie przywracanie bazy danych.
- [Krytyczne](sql-database-service-tier-business-critical.md)dla działania firmy, które jest przeznaczone do obciążeń o małym opóźnieniu i wysokiej odporności na awarie i szybkie przełączanie do trybu failover.

W tym artykule omówiono różnice między warstwami usług, zagadnieniami dotyczącymi magazynu i kopii zapasowych w przypadku warstw usług dla celów ogólnych i krytycznych w modelu zakupu opartego na rdzeń wirtualny.

## <a name="service-tier-comparison"></a>Porównanie warstwy usług

W poniższej tabeli opisano kluczowe różnice między warstwami usług dla najnowszej generacji (5 rdzeń). Należy zauważyć, że cechy warstwy usług mogą się różnić w pojedyncza baza danych i wystąpienia zarządzanego.

| | Typ zasobu | Ogólnego przeznaczenia |  Hiperskala | Krytyczne dla działania firmy |
|:---:|:---:|:---:|:---:|:---:|
| **Najlepsze dla** | |  Oferuje zorientowane na budżety Opcje obliczeniowe i magazynowe. | Większość obciążeń firmowych. Skalowanie automatyczne rozmiaru magazynu o rozmiarze do 100 TB, płynne skalowanie w pionie i w poziomie, szybkie przywracanie bazy danych. | Aplikacje OLTP o wysokim współczynniku transakcji i niskim opóźnieniu we/wy. Oferuje największą odporność na błędy i szybkie przełączanie w tryb failover przy użyciu wielu replik synchronicznie zaktualizowanych.|
|  **Dostępne w typie zasobu:** ||Pojedyncza baza danych/Pula elastyczna/wystąpienie zarządzane | Pojedyncza baza danych | Pojedyncza baza danych/Pula elastyczna/wystąpienie zarządzane |
| **Rozmiar obliczeń**|Pojedyncza baza danych/Pula elastyczna | od 1 do 80 rdzeni wirtualnych | od 1 do 80 rdzeni wirtualnych | od 1 do 80 rdzeni wirtualnych |
| | Wystąpienie zarządzane | 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych | Nie dotyczy | 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych |
| | Zarządzane pule wystąpień | 2, 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych | Nie dotyczy | Nie dotyczy |
| **Typ magazynu** | Wszystkie | Magazyn zdalny w warstwie Premium (na wystąpienie) | Niepołączony magazyn z lokalną pamięcią podręczną dysków SSD (na wystąpienie) | Lokalny magazyn SSD o wysokiej szybkości (na wystąpienie) |
| **Rozmiar bazy danych** | Pojedyncza baza danych/Pula elastyczna | 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane  | 32 GB – 8 TB | Nie dotyczy | 32 GB – 4 TB |
| **Rozmiar magazynu** | Pojedyncza baza danych/Pula elastyczna | 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane  | 32 GB – 8 TB | Nie dotyczy | 32 GB – 4 TB |
| **Rozmiar bazy danych TempDB** | Pojedyncza baza danych/Pula elastyczna | [32 GB na rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB na rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB na rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Wystąpienie zarządzane  | [24 GB na rdzeń wirtualny](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Nie dotyczy | Do 4 TB — [ograniczone przez rozmiar magazynu](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Przepływność zapisu dziennika** | Pojedyncza baza danych | [1,875 MB/s na rdzeń wirtualny (maksymalnie 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s na rdzeń wirtualny (maksymalnie 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Wystąpienie zarządzane | [3 MB/s na rdzeń wirtualny (maksymalnie 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Nie dotyczy | [4 MB/s na rdzeń wirtualny (maksymalnie 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Dostępność**|Wszystkie| 99,99% |  [99,95% z jedną repliką pomocniczą, 99,99% z więcej replik](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% ze strefą nadmiarową pojedynczą bazą danych](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Tworzenie kopii zapasowych**|Wszystkie|RA-GRS, 7-35 dni (domyślnie 7 dni)| RA-GRS, 7 dni, stałe odzyskiwanie do czasu w czasie (kopie) | RA-GRS, 7-35 dni (domyślnie 7 dni) |
|**Przetwarzanie OLTP w pamięci** | | Nie dotyczy | Nie dotyczy | Dostępne |
|**Repliki tylko do odczytu**| | 0 wbudowane <br> 0-4 przy użyciu [replikacji geograficznej](sql-database-active-geo-replication.md) | 0-4 wbudowane | 1 wbudowana, uwzględniona w cenie <br> 0-4 przy użyciu [replikacji geograficznej](sql-database-active-geo-replication.md) |
|**Cennik/rozliczenia** | Pojedyncza baza danych | [rdzeń wirtualny, zarezerwowany magazyn i magazyn kopii zapasowych](https://azure.microsoft.com/pricing/details/sql-database/single/) są rozliczone. <br/>Liczba operacji we/wy nie jest naliczana. | [rdzeń wirtualny dla każdej repliki i używanej pamięci masowej](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>Liczba operacji we/wy nie została jeszcze obciążona. | [rdzeń wirtualny, zarezerwowany magazyn i magazyn kopii zapasowych](https://azure.microsoft.com/pricing/details/sql-database/single/) są rozliczone. <br/>Liczba operacji we/wy nie jest naliczana. |
|| Wystąpienie zarządzane | [rdzeń wirtualny, zarezerwowany magazyn i magazyn kopii zapasowych](https://azure.microsoft.com/pricing/details/sql-database/managed/) są naliczone. <br/>Liczba operacji we/wy nie jest naliczana| Nie dotyczy | [rdzeń wirtualny, zarezerwowany magazyn i magazyn kopii zapasowych](https://azure.microsoft.com/pricing/details/sql-database/managed/) są naliczone. <br/>Liczba operacji we/wy nie jest naliczana.| 
|**Modele rabatów**| | [Wystąpienia zarezerwowane](sql-database-reserved-capacity.md)<br/>[Korzyść użycia hybrydowego platformy Azure](sql-database-azure-hybrid-benefit.md) (niedostępne w subskrypcjach tworzenia i testowania)<br/>Subskrypcje dla [przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) i [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0023p/) użyciem — tworzenie i testowanie| [Korzyść użycia hybrydowego platformy Azure](sql-database-azure-hybrid-benefit.md) (niedostępne w subskrypcjach tworzenia i testowania)<br/>Subskrypcje dla [przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) i [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0023p/) użyciem — tworzenie i testowanie| [Wystąpienia zarezerwowane](sql-database-reserved-capacity.md)<br/>[Korzyść użycia hybrydowego platformy Azure](sql-database-azure-hybrid-benefit.md) (niedostępne w subskrypcjach tworzenia i testowania)<br/>Subskrypcje dla [przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) i [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0023p/) użyciem — tworzenie i testowanie|

Aby uzyskać więcej informacji, Zobacz szczegółowe różnice między warstwami usług w [jednej bazie danych (rdzeń wirtualny)](sql-database-vcore-resource-limits-single-databases.md), [pojedynczej puli baz danych (rdzeń wirtualny)](sql-database-dtu-resource-limits-single-databases.md), [pojedynczej bazie danych (DTU)](sql-database-dtu-resource-limits-single-databases.md), [pulach pojedynczej bazy danych (DTU)](sql-database-dtu-resource-limits-single-databases.md)i stronach [wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md) .

> [!NOTE]
> Aby uzyskać informacje na temat warstwy usługi na potrzeby skalowania w modelu zakupu opartego na rdzeń wirtualny, zobacz [podskalowanie warstwy usług](sql-database-service-tier-hyperscale.md). Aby zapoznać się z porównaniem modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU, zobacz [Azure SQL Database kupowanie modeli i zasobów](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Magazyn danych i dzienników

Poniższe czynniki wpływają na ilość miejsca do magazynowania używanego dla plików danych i dziennika, a Ogólnego przeznaczenia i Krytyczne dla działania firmy. Aby uzyskać szczegółowe informacje dotyczące magazynu danych i dzienników w ramach skalowania, zobacz stronę [skalowania usługi](sql-database-service-tier-hyperscale.md).

- Przydzielone magazyny są używane przez pliki danych (MDF) i pliki dziennika (LDF).
- Każdy rozmiar obliczeń pojedynczej bazy danych obsługuje maksymalny rozmiar bazy danych, a domyślny maksymalny rozmiar 32 GB.
- W przypadku skonfigurowania wymaganego pojedynczego rozmiaru bazy danych (rozmiaru pliku MDF) do obsługi plików LDF zostanie automatycznie dodany dodatkowy magazyn o rozmiarze 30%.
- Rozmiar magazynu dla SQL Database wystąpienia zarządzanego musi być określony w wielokrotnościach 32 GB.
- Można wybrać dowolny rozmiar bazy danych z zakresu od 10 GB do obsługiwanej wartości maksymalnej.
  - W przypadku magazynu w warstwach usług standardowych lub ogólnych należy zwiększyć lub zmniejszyć rozmiar w przyrostach 10 GB.
  - W przypadku magazynu w warstwach usługi krytycznej w wersji Premium lub biznesowej Zwiększ lub Zmniejsz rozmiar w 250 GB.
- W warstwie usług ogólnego przeznaczenia program `tempdb` używa dołączonego dysku SSD, a koszt magazynu jest uwzględniany w cenie rdzeń wirtualny.
- W warstwie usługi krytycznej dla firm `tempdb` udostępnia dołączony dysk SSD z plikami MDF i ldf, a koszt `tempdb` magazynu jest uwzględniany w cenie rdzeń wirtualny.

> [!IMPORTANT]
> Opłaty są naliczane za łączny magazyn przeznaczony dla plików MDF i LDF.

Aby monitorować bieżący łączny rozmiar plików MDF i LDF, użyj [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Aby monitorować bieżący rozmiar poszczególnych plików MDF i LDF, użyj [widoku sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Kopie zapasowe i magazyn

Magazyn dla kopii zapasowych bazy danych jest przypisywany do obsługi funkcji przywracania do punktu w czasie (kopie) oraz [długoterminowego przechowywania (LTR)](sql-database-long-term-retention.md) SQL Database. Ten magazyn jest przypisywany osobno dla każdej bazy danych i rozliczany jako dwa oddzielne opłaty za bazę danych.

- **Kopie**: kopie zapasowe pojedynczych baz danych są kopiowane do [magazynu geograficznie nadmiarowego (RA-GRS) z dostępem do odczytu](../storage/common/storage-designing-ha-apps-with-ragrs.md) . Rozmiar magazynu jest zwiększany dynamicznie w miarę tworzenia nowych kopii zapasowych. Magazyn jest używany przez cotygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji, które są kopiowane co 5 minut. Użycie magazynu zależy od szybkości zmiany bazy danych i okresu przechowywania kopii zapasowych. Dla każdej bazy danych można skonfigurować oddzielny okres przechowywania od 7 do 35 dni. Minimalna wielkość magazynu równa 100% (1x) rozmiaru bazy danych jest zapewniana bez dodatkowych opłat. W przypadku większości baz danych ta kwota jest wystarczająca do przechowywania 7 dni wykonywania kopii zapasowych.
- **Ltr**: SQL Database oferuje możliwość skonfigurowania długoterminowego przechowywania pełnych kopii zapasowych przez maksymalnie 10 lat. W przypadku skonfigurowania zasad LTR te kopie zapasowe są przechowywane w magazynie RA-GRS automatycznie, ale można kontrolować, jak często są kopiowane kopie zapasowe. Aby spełnić inne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, comiesięcznych i/lub corocznych kopii zapasowych. Wybrana konfiguracja określa, ile miejsca do magazynowania będzie używany do tworzenia kopii zapasowych. Aby oszacować koszt magazynu LTR, możesz użyć kalkulatora cen. Aby uzyskać więcej informacji, zobacz [SQL Database okres przechowywania długoterminowego](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje o określonych rozmiarach obliczeniowych i rozmiarach magazynu dostępnych dla pojedynczej bazy danych w warstwach ogólnych i krytycznych dla działania firmy, zobacz [SQL Database limitów zasobów opartych na rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- Aby uzyskać szczegółowe informacje o określonych rozmiarach obliczeniowych i rozmiarach magazynów dostępnych dla pul elastycznych w warstwach usług ogólnych i krytycznych dla firmy, zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).
