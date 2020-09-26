---
title: Warstwy usług dla celów ogólnych i krytycznych
titleSuffix: Azure SQL Database & SQL Managed Instance
description: W tym artykule omówiono warstwy usług ogólnych i krytycznych dla firmy w modelu zakupu opartym na rdzeń wirtualny, używanym przez Azure SQL Database i wystąpienie zarządzane usługi Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/30/2020
ms.openlocfilehash: c1f60888a3d1fda50e63c7d11ea5d871f7c1e9fc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321343"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Azure SQL Database i warstwy usługi wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database i wystąpienie zarządzane SQL platformy Azure są oparte na architekturze SQL Server Database Engine, która jest dostosowywana do środowiska chmury w celu zapewnienia dostępności 99,99% nawet w przypadku awarii infrastruktury. Dwie warstwy usług są używane przez Azure SQL Database i wystąpienie zarządzane usługi Azure SQL, z których każdy ma inny model architektury. Następujące warstwy usług:

- [Ogólnego przeznaczenia](service-tier-general-purpose.md), który jest przeznaczony do obsługi obciążeń zorientowanych na budżet.
- [Krytyczne](service-tier-business-critical.md)dla działania firmy, które jest przeznaczone do obciążeń o małym opóźnieniu i wysokiej odporności na awarie i szybkie przełączanie do trybu failover.

Azure SQL Database ma dodatkową warstwę usługi: 

- [Skalowanie](service-tier-hyperscale.md), które jest przeznaczone dla większości obciążeń firmowych, zapewniając wysoce skalowalny magazyn, możliwość odczytywania skalowalnego w poziomie i szybkie przywracanie bazy danych.

W tym artykule omówiono różnice między warstwami usług, zagadnieniami dotyczącymi magazynu i kopii zapasowych dla warstw usług ogólnych i krytycznych dla działalności biznesowej w modelu zakupu opartego na rdzeń wirtualny.

## <a name="service-tier-comparison"></a>Porównanie warstwy usług

W poniższej tabeli opisano kluczowe różnice między warstwami usług dla najnowszej generacji (5 rdzeń). Należy pamiętać, że cechy warstwy usług mogą się różnić w SQL Database i wystąpieniu zarządzanym SQL.

|-| Typ zasobu | Ogólnego przeznaczenia |  Hiperskala | Krytyczne dla działania firmy |
|:---:|:---:|:---:|:---:|:---:|
| **Optymalne zastosowanie** | |  Oferuje zorientowane na budżety Opcje obliczeniowe i magazynowe. | Większość obciążeń firmowych. Skalowanie automatyczne rozmiaru magazynu o rozmiarze do 100 TB, płynne skalowanie w pionie i w poziomie, szybkie przywracanie bazy danych. | Aplikacje OLTP o wysokim współczynniku transakcji i niskim opóźnieniu we/wy. Oferuje największą odporność na błędy i szybkie przełączanie w tryb failover przy użyciu wielu replik synchronicznie zaktualizowanych.|
|  **Dostępne w typie zasobu:** ||Wystąpienie zarządzane SQL Database/SQL | Pojedyncze Azure SQL Database | Wystąpienie zarządzane SQL Database/SQL |
| **Rozmiar obliczeń**| SQL Database | od 1 do 80 rdzeni wirtualnych | od 1 do 80 rdzeni wirtualnych | od 1 do 80 rdzeni wirtualnych |
| | Wystąpienie zarządzane SQL | 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych | Nie dotyczy | 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych |
| | Pule wystąpień zarządzanych przez program SQL | 2, 4, 8, 16, 24, 32, 40, 64, 80 rdzeni wirtualnych | NIE DOTYCZY | NIE DOTYCZY |
| **Typ magazynu** | Wszystko | Magazyn zdalny w warstwie Premium (na wystąpienie) | Niepołączony magazyn z lokalną pamięcią podręczną dysków SSD (na wystąpienie) | Lokalny magazyn SSD o wysokiej szybkości (na wystąpienie) |
| **Rozmiar bazy danych** | SQL Database | 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane SQL  | 32 GB – 8 TB | Nie dotyczy | 32 GB – 4 TB |
| **Rozmiar magazynu** | SQL Database | 5 GB – 4 TB | Do 100 TB | 5 GB – 4 TB |
| | Wystąpienie zarządzane SQL  | 32 GB – 8 TB | Nie dotyczy | 32 GB – 4 TB |
| **Rozmiar bazy danych TempDB** | SQL Database | [32 GB na rdzeń wirtualny](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB na rdzeń wirtualny](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB na rdzeń wirtualny](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Wystąpienie zarządzane SQL  | [24 GB na rdzeń wirtualny](../managed-instance/resource-limits.md#service-tier-characteristics) | Nie dotyczy | Do 4 TB — [ograniczone przez rozmiar magazynu](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Przepływność zapisu dziennika** | SQL Database | [1,875 MB/s na rdzeń wirtualny (maksymalnie 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s na rdzeń wirtualny (maksymalnie 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Wystąpienie zarządzane SQL | [3 MB/s na rdzeń wirtualny (maksymalnie 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | Nie dotyczy | [4 MB/s na rdzeń wirtualny (maksymalnie 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Dostępność**|Wszystko| 99,99% |  [99,95% z jedną repliką pomocniczą, 99,99% z więcej replik](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% ze strefą nadmiarową pojedynczą bazą danych](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Tworzenie kopii zapasowych**|Wszystko|RA-GRS, 7-35 dni (domyślnie 7 dni)| RA-GRS, 7 dni, stałe odzyskiwanie do czasu w czasie (kopie) | RA-GRS, 7-35 dni (domyślnie 7 dni) |
|**Przetwarzanie OLTP w pamięci** | | NIE DOTYCZY | NIE DOTYCZY | Dostępne |
|**Repliki tylko do odczytu**| | 0 wbudowane <br> 0-4 przy użyciu [replikacji geograficznej](active-geo-replication-overview.md) | 0-4 wbudowane | 1 wbudowana, uwzględniona w cenie <br> 0-4 przy użyciu [replikacji geograficznej](active-geo-replication-overview.md) |
|**Cennik/rozliczenia** | SQL Database | [rdzeń wirtualny, zarezerwowany magazyn i magazyn kopii zapasowych](https://azure.microsoft.com/pricing/details/sql-database/single/) są rozliczone. <br/>Liczba operacji we/wy nie jest naliczana. | [rdzeń wirtualny dla każdej repliki i używanej pamięci masowej](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>Liczba operacji we/wy nie została jeszcze obciążona. | [rdzeń wirtualny, zarezerwowany magazyn i magazyn kopii zapasowych](https://azure.microsoft.com/pricing/details/sql-database/single/) są rozliczone. <br/>Liczba operacji we/wy nie jest naliczana. |
|| Wystąpienie zarządzane SQL | [rdzeń wirtualny, zarezerwowany magazyn i magazyn kopii zapasowych](https://azure.microsoft.com/pricing/details/sql-database/managed/) są naliczone. <br/>Liczba operacji we/wy nie jest naliczana| Nie dotyczy | [rdzeń wirtualny, zarezerwowany magazyn i magazyn kopii zapasowych](https://azure.microsoft.com/pricing/details/sql-database/managed/) są naliczone. <br/>Liczba operacji we/wy nie jest naliczana.| 
|**Modele rabatów**| | [Wystąpienia zarezerwowane](reserved-capacity-overview.md)<br/>[Korzyść użycia hybrydowego platformy Azure](../azure-hybrid-benefit.md) (niedostępne w subskrypcjach tworzenia i testowania)<br/>Subskrypcje dla [przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) i [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0023p/) użyciem — tworzenie i testowanie| [Korzyść użycia hybrydowego platformy Azure](../azure-hybrid-benefit.md) (niedostępne w subskrypcjach tworzenia i testowania)<br/>Subskrypcje dla [przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) i [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0023p/) użyciem — tworzenie i testowanie| [Wystąpienia zarezerwowane](reserved-capacity-overview.md)<br/>[Korzyść użycia hybrydowego platformy Azure](../azure-hybrid-benefit.md) (niedostępne w subskrypcjach tworzenia i testowania)<br/>Subskrypcje dla [przedsiębiorstw](https://azure.microsoft.com/offers/ms-azr-0148p/) i [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0023p/) użyciem — tworzenie i testowanie|

Aby uzyskać więcej informacji, Zobacz szczegółowe różnice między warstwami usług w [Azure SQL Database (rdzeń wirtualny)](resource-limits-vcore-single-databases.md), [Single Azure SQL Database (dtu)](resource-limits-dtu-single-databases.md), [Azure SQL Database w puli (DTU)](resource-limits-dtu-single-databases.md)i stron [wystąpienia zarządzanego Azure SQL](../managed-instance/resource-limits.md) .

> [!NOTE]
> Aby uzyskać informacje na temat warstwy usługi na potrzeby skalowania w modelu zakupu opartego na rdzeń wirtualny, zobacz [podskalowanie warstwy usług](service-tier-hyperscale.md). Porównanie modelu zakupu opartego na rdzeń wirtualny z modelem zakupów opartym na jednostkach DTU można znaleźć w temacie [kupowanie modeli i zasobów](purchasing-models.md).

## <a name="data-and-log-storage"></a>Magazyn danych i dzienników

Poniższe czynniki wpływają na ilość miejsca do magazynowania używanego dla plików danych i dziennika, a następnie mają zastosowanie do Ogólnego przeznaczenia i Krytyczne dla działania firmy. Aby uzyskać szczegółowe informacje dotyczące magazynu danych i dzienników w ramach skalowania, zobacz stronę [skalowania usługi](service-tier-hyperscale.md).

- Przydzielone magazyny są używane przez pliki danych (MDF) i pliki dziennika (LDF).
- Każdy rozmiar obliczeń pojedynczej bazy danych obsługuje maksymalny rozmiar bazy danych, a domyślny maksymalny rozmiar 32 GB.
- W przypadku skonfigurowania wymaganego pojedynczego rozmiaru bazy danych (rozmiaru pliku MDF) do obsługi plików LDF zostanie automatycznie dodany dodatkowy magazyn o rozmiarze 30%.
- Można wybrać dowolny rozmiar bazy danych z zakresu od 10 GB do obsługiwanej wartości maksymalnej.
  - W przypadku magazynu w warstwach usług standardowych lub ogólnych należy zwiększyć lub zmniejszyć rozmiar w przyrostach 10 GB.
  - W przypadku magazynu w warstwach usługi krytycznej w wersji Premium lub biznesowej Zwiększ lub Zmniejsz rozmiar w 250 GB.
- W warstwie usług ogólnego przeznaczenia program `tempdb` używa dołączonego dysku SSD, a koszt magazynu jest uwzględniany w cenie rdzeń wirtualny.
- W warstwie usługi krytycznej dla firm `tempdb` udostępnia dołączony dysk SSD z plikami MDF i ldf, a `tempdb` koszt magazynu jest uwzględniany w cenie rdzeń wirtualny.
- Rozmiar magazynu dla wystąpienia zarządzanego SQL musi być określony w wielokrotnościach 32 GB.


> [!IMPORTANT]
> Opłaty są naliczane za łączny magazyn przeznaczony dla plików MDF i LDF.

Aby monitorować bieżący łączny rozmiar plików MDF i LDF, użyj [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Aby monitorować bieżący rozmiar poszczególnych plików MDF i LDF, użyj [widoku sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> W pewnych okolicznościach może być konieczne zmniejszenie bazy danych w celu Odbierz nieużywanej przestrzeni. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](file-space-manage.md).

## <a name="backups-and-storage"></a>Kopie zapasowe i magazyn

Magazyn dla kopii zapasowych bazy danych jest przypisywany do obsługi funkcji przywracania do punktu w czasie (kopie) oraz [długoterminowego przechowywania (LTR)](long-term-retention-overview.md) SQL Database i wystąpienia zarządzanego SQL. Ten magazyn jest przypisywany osobno dla każdej bazy danych i rozliczany jako dwa oddzielne opłaty za bazę danych.

- **Kopie**: kopie zapasowe pojedynczych baz danych są kopiowane do [magazynu geograficznie nadmiarowego (RA-GRS) z dostępem do odczytu](../../storage/common/geo-redundant-design.md) . Rozmiar magazynu jest zwiększany dynamicznie w miarę tworzenia nowych kopii zapasowych. Magazyn jest używany przez cotygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji, które są kopiowane co 5 minut. Użycie magazynu zależy od szybkości zmiany bazy danych i okresu przechowywania kopii zapasowych. Dla każdej bazy danych można skonfigurować oddzielny okres przechowywania od 7 do 35 dni. Minimalna wielkość magazynu równa 100% (1x) rozmiaru bazy danych jest zapewniana bez dodatkowych opłat. W przypadku większości baz danych ta kwota jest wystarczająca do przechowywania 7 dni wykonywania kopii zapasowych.
- **Ltr**: masz również możliwość skonfigurowania długoterminowego przechowywania pełnych kopii zapasowych przez maksymalnie 10 lat (Ta funkcja jest w [ograniczonej publicznej wersji zapoznawczej dla wystąpienia zarządzanego SQL](long-term-retention-overview.md#sql-managed-instance-support). W przypadku skonfigurowania zasad LTR te kopie zapasowe są przechowywane w magazynie RA-GRS automatycznie, ale można kontrolować, jak często są kopiowane kopie zapasowe. Aby spełnić inne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, comiesięcznych i/lub corocznych kopii zapasowych. Wybrana konfiguracja określa, ile miejsca do magazynowania będzie używany do tworzenia kopii zapasowych. Aby oszacować koszt magazynu LTR, możesz użyć kalkulatora cen. Aby uzyskać więcej informacji, zobacz [SQL Database okres przechowywania długoterminowego](long-term-retention-overview.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje o określonych rozmiarach obliczeniowych i magazynowych dostępnych w warstwach usług ogólnych i krytycznych dla działania firmy, zobacz: 

- [limity zasobów opartych na rdzeń wirtualny Azure SQL Database](resource-limits-vcore-single-databases.md).
- [limity zasobów opartych na rdzeń wirtualny dla baz danych w puli w Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [limity zasobów opartych na rdzeń wirtualny dla wystąpienia zarządzanego Azure SQL](../managed-instance/resource-limits.md). 

