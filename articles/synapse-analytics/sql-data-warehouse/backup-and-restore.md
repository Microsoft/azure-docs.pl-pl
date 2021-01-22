---
title: Tworzenie kopii zapasowych i przywracanie migawek, geograficznie nadmiarowych
description: Dowiedz się, jak wykonywanie kopii zapasowych i przywracanie działa w dedykowanej puli SQL usługi Azure Synapse Analytics. Użyj kopii zapasowych, aby przywrócić magazyn danych do punktu przywracania w regionie podstawowym. Użyj geograficznie nadmiarowych kopii zapasowych do przywrócenia w innym regionie geograficznym.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: d8c680ec30dcecc56c064f08e4690cbbde9c2377
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679917"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Tworzenie kopii zapasowych i przywracanie danych w dedykowanej puli SQL Synapse platformy Azure

Dowiedz się, jak używać funkcji tworzenia kopii zapasowych i przywracania w dedykowanej puli SQL Synapse platformy Azure. Użyj dedykowanych punktów przywracania puli SQL, aby odzyskać lub skopiować magazyn danych do poprzedniego stanu w regionie podstawowym. Użyj geograficznie nadmiarowych kopii zapasowych magazynu danych do przywrócenia w innym regionie geograficznym.

## <a name="what-is-a-data-warehouse-snapshot"></a>Co to jest migawka magazynu danych

*Migawka magazynu danych* tworzy punkt przywracania, którego można użyć do odzyskania lub skopiowania magazynu danych do poprzedniego stanu.  Ponieważ dedykowana Pula SQL jest systemem rozproszonym, migawka magazynu danych składa się z wielu plików znajdujących się w usłudze Azure Storage. Migawki przechwytują zmiany przyrostowe z danych przechowywanych w magazynie danych.

*Przywracanie magazynu danych* jest nowym magazynem danych, który jest tworzony na podstawie punktu przywracania istniejącego lub usuniętego magazynu danych. Przywrócenie magazynu danych jest istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ powoduje ponowne utworzenie danych po przypadkowe uszkodzenia lub usunięciu. Magazyn danych jest również zaawansowanym mechanizmem tworzenia kopii magazynu danych na potrzeby testowania lub projektowania. Stawki za dedykowane pule SQL mogą się różnić w zależności od rozmiaru bazy danych i lokalizacji źródłowego i docelowego magazynu danych.

## <a name="automatic-restore-points"></a>Automatyczne punkty przywracania

Migawki to wbudowana funkcja, która tworzy punkty przywracania. Nie musisz włączać tej funkcji. Jednak dedykowana Pula SQL powinna znajdować się w stanie aktywnym do tworzenia punktów przywracania. Jeśli jest to często wstrzymane, automatyczne punkty przywracania mogą nie zostać utworzone, dlatego należy utworzyć punkt przywracania zdefiniowany przez użytkownika przed wstrzymaniem dedykowanej puli SQL. Automatyczne punkty przywracania obecnie nie mogą zostać usunięte przez użytkowników, ponieważ usługa używa tych punktów przywracania do obsługi umowy SLA na potrzeby odzyskiwania.

Migawki magazynu danych są wykonywane przez cały dzień tworzenia punktów przywracania dostępnych przez siedem dni. Nie można zmienić tego okresu przechowywania. Dedykowana Pula SQL obsługuje osiem godzin cel punktu odzyskiwania (RPO). Magazyn danych można przywrócić w regionie podstawowym z jednej z migawek wykonanych w ciągu ostatnich siedmiu dni.

Aby sprawdzić, kiedy Ostatnia migawka została rozpoczęta, Uruchom to zapytanie w dedykowanej puli SQL w trybie online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Punkty przywracania zdefiniowane przez użytkownika

Ta funkcja umożliwia ręczne wyzwalanie migawek do tworzenia punktów przywracania magazynu danych przed i po dużych modyfikacjach. Ta funkcja zapewnia, że punkty przywracania są logicznie spójne, co zapewnia dodatkową ochronę danych w przypadku wszelkich przerw w obciążeniu lub błędów użytkowników w celu szybkiego odzyskiwania. Punkty przywracania zdefiniowane przez użytkownika są dostępne przez siedem dni i są automatycznie usuwane w Twoim imieniu. Nie można zmienić okresu przechowywania zdefiniowanych przez użytkownika punktów przywracania. **42 punkty przywracania zdefiniowane przez użytkownika** są gwarantowane w dowolnym momencie, dlatego należy je [usunąć](/powershell/module/azurerm.sql/remove-azurermsqldatabaserestorepoint?viewFallbackFrom=azurermps-6.2.0) przed utworzeniem innego punktu przywracania. Możesz wyzwolić migawki, aby utworzyć punkty przywracania zdefiniowane przez użytkownika za pomocą [programu PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) lub Azure Portal.

> [!NOTE]
> Jeśli wymagane jest przywracanie punktów dłużej niż 7 dni, należy zagłosować na [tę funkcję.](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points) Można również utworzyć punkt przywracania zdefiniowany przez użytkownika i przywrócić go z nowo utworzonego punktu przywracania do nowego magazynu danych. Po przywróceniu można korzystać z dedykowanej puli SQL w trybie online i można ją wstrzymać w nieskończoność, aby zaoszczędzić koszty obliczeń. Wstrzymana baza danych wiąże się z opłatami za magazyn według stawki za Premium Storage platformy Azure. Jeśli potrzebujesz aktywnej kopii przywróconego magazynu danych, możesz wznowić działanie, które powinno trwać tylko kilka minut.

### <a name="restore-point-retention"></a>Przechowywanie punktów przywracania

Poniżej znajdują się szczegółowe informacje dotyczące okresów przechowywania punktów przywracania:

1. Dedykowana Pula SQL usuwa punkt przywracania, gdy trafi na 7-dniowy okres przechowywania **, a** w przypadku co najmniej 42 punktów przywracania (w tym zdefiniowanych przez użytkownika i automatycznie).
2. Migawki nie są wykonywane, gdy dedykowana Pula SQL jest wstrzymana.
3. Wiek punktu przywracania jest mierzony przez bezwzględne dni kalendarzowe od momentu, w którym jest wykonywana punkt przywracania, w tym gdy pula SQL jest wstrzymana.
4. W dowolnym momencie dedykowana Pula SQL ma możliwość przechowywania do 42 punktów przywracania zdefiniowanych przez użytkownika i 42 punktów przywracania automatycznego, o ile te punkty przywracania nie osiągnęły 7-dniowego okresu przechowywania.
5. Jeśli tworzona jest migawka, dedykowana Pula SQL jest wstrzymywana przez więcej niż 7 dni, a następnie wznowiona, punkt przywracania będzie trwały do momentu, gdy będzie dostępna następująca liczba punktów przywracania: 42 (w tym zdefiniowane przez użytkownika i automatyczne)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Przechowywanie migawek w przypadku porzucenia puli SQL

Po porzucenia dedykowanej puli SQL tworzona jest Ostatnia migawka, która jest zapisywana przez siedem dni. Można przywrócić dedykowaną pulę SQL do końcowego punktu przywracania utworzonego podczas usuwania. Jeśli dedykowana Pula SQL zostanie porzucona w stanie wstrzymania, migawka nie jest wykonywana. W tym scenariuszu należy utworzyć zdefiniowany przez użytkownika punkt przywracania przed usunięciem dedykowanej puli SQL.

> [!IMPORTANT]
> Jeśli usuniesz serwer/obszar roboczy obsługujący dedykowaną pulę SQL, wszystkie bazy danych należące do serwera/obszaru roboczego również zostaną usunięte i nie będzie można go odzyskać. Nie można przywrócić usuniętego serwera.

## <a name="geo-backups-and-disaster-recovery"></a>Tworzenie kopii zapasowych i odzyskiwanie po awarii

Geograficzna kopia zapasowa jest tworzona raz dziennie dla [sparowanego centrum danych](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Cel punktu odzyskiwania w przypadku przywracania geograficznego wynosi 24 godziny. Można przywrócić geograficzną kopię zapasową do serwera w dowolnym innym regionie, w którym obsługiwana jest dedykowana Pula SQL. Geograficzna kopia zapasowa zapewnia możliwość przywrócenia magazynu danych na wypadek, gdyby nie można było uzyskać dostępu do punktów przywracania w regionie podstawowym.

> [!NOTE]
> Jeśli potrzebujesz krótszego celu punktu odzyskiwania dla geograficznie kopii zapasowych, zagłosuj na tę funkcję [tutaj](https://feedback.azure.com/forums/307516-sql-data-warehouse). Można również utworzyć zdefiniowany przez użytkownika punkt przywracania i przywrócić go z nowo utworzonego punktu przywracania do nowego magazynu danych w innym regionie. Po przywróceniu będziesz mieć magazyn danych w trybie online i można go wstrzymać w nieskończoność, aby zaoszczędzić koszty obliczeniowe. Wstrzymana baza danych wiąże się z opłatami za magazyn według stawki za Premium Storage platformy Azure. Jeśli potrzebujesz aktywnej kopii hurtowni danych, możesz wznowić działanie, które powinno trwać tylko kilka minut.

## <a name="backup-and-restore-costs"></a>Koszty tworzenia kopii zapasowej i przywracania

Na rachunku za platformę Azure zostanie wystawiony element line for Storage i element line dla magazynu odzyskiwania po awarii. Opłata za magazyn to łączny koszt przechowywania danych w regionie podstawowym wraz ze zmianami przyrostowymi przechwytywanymi przez migawki. Aby uzyskać bardziej szczegółowy opis sposobu, w jaki są rozliczane migawki, zapoznaj się z tematem  [jak naliczanie opłat za migawki](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Opłata za nadmiarowy geograficznie obejmuje koszt przechowywania kopii zapasowych.  

Łączny koszt podstawowego magazynu danych i siedem dni zmian migawek jest zaokrąglany do najbliższej TB. Na przykład jeśli magazyn danych to 1,5 TB, a migawki przechwytuje 100 GB, opłaty są naliczane za 2 TB danych w ramach stawek za usługę Azure Premium Storage.

W przypadku korzystania z magazynu geograficznie nadmiarowego otrzymujesz oddzielną opłatą za magazyn. Magazyn Geograficznie nadmiarowy jest rozliczany zgodnie ze Read-Access standardową stawką za magazyn GRS geograficzną nadmiarowy.

Aby uzyskać więcej informacji o cenach usługi Azure Synapse, zobacz [Cennik usługi Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Nie jest naliczana opłata za dane wychodzące podczas przywracania między regionami.

## <a name="restoring-from-restore-points"></a>Przywracanie z punktów przywracania

Każda migawka tworzy punkt przywracania, który reprezentuje czas rozpoczęcia migawki. Aby przywrócić magazyn danych, należy wybrać punkt przywracania i wydać polecenie przywracania.  

Możesz zachować przywrócony magazyn danych i bieżący lub usunąć jeden z nich. Jeśli chcesz zamienić bieżący magazyn danych na przywrócony magazyn danych, możesz zmienić jego nazwę za pomocą [polecenia ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) z opcją Modify Name.

Aby przywrócić magazyn danych, zobacz [przywracanie dedykowanej puli SQL](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Aby przywrócić usunięty lub wstrzymany magazyn danych, możesz [utworzyć bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Przywracanie między subskrypcjami

Jeśli musisz bezpośrednio przywrócić subskrypcję, zagłosuj na tę funkcję w [tym miejscu](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Przywróć na inny serwer i ["Przenieś"](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) serwer między subskrypcjami, aby przeprowadzić przywracanie między subskrypcjami.

## <a name="geo-redundant-restore"></a>Przywracanie nadmiarowe geograficznie

Możesz [przywrócić dedykowaną pulę SQL](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) do dowolnego regionu obsługującego dedykowaną pulę SQL na wybranym poziomie wydajności.

> [!NOTE]
> Aby wykonać przywracanie Geograficznie nadmiarowy, nie trzeba wymusić tej funkcji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o punktach przywracania, zobacz [punkty przywracania zdefiniowane przez użytkownika](sql-data-warehouse-restore-points.md)