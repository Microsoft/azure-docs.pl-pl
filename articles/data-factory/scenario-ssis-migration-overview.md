---
title: Migrowanie lokalnych obciążeń SSIS do usługi SSIS w usłudze Azure Data Factory
description: Migrowanie lokalnych obciążeń SSIS do SSIS w ujrzeniu usługi ADF.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 2b23ffec76de3fa644abe3b65876a60c65c05eb8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686002"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrowanie lokalnych obciążeń SSIS do SSIS w ujmniku usługi ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Omówienie

Podczas migracji obciążeń bazy danych z programu SQL Server lokalnie do usług bazy danych platformy Azure, a mianowicie usługi Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL Database, obciążenia ETL w usługach SQL Server Integration Services (SSIS) jako jedna z podstawowych usług o wartości dodanej będą również musiały zostać zmigrowane.

Środowisko uruchomieniowe integracji platformy Azure-SSIS (IR) w usłudze Azure Data Factory (ADF) obsługuje uruchamianie pakietów SSIS. Po zainicjowaniu obsługi administracyjnej platformy Azure-SSIS można użyć znanych narzędzi, takich jak NARZĘDZIA SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) i narzędzia wiersza polecenia, takie jak dtinstall/dtutil/dtexec, do wdrażania i uruchamiania pakietów na platformie Azure. Aby uzyskać więcej informacji, zobacz [omówienie usługi Azure SSIS w zakresie lift-and-shift](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

W tym artykule przedstawiono proces migracji obciążeń ETL z lokalnego SSIS do SSIS w UOI. Proces migracji składa się z dwóch etapów: **Ocena** i **Migracja**.

## <a name="assessment"></a>Ocena

Aby ustanowić kompletny plan migracji, dokładna ocena pomoże zidentyfikować problemy ze źródłowymi pakietami SSIS, które uniemożliwiłyby pomyślną migrację.

Asystent migracji danych (DMA) to bezpłatne narzędzie do pobrania w tym celu, które można zainstalować i wykonać lokalnie. Projekt oceny DMA typu **Integration Services** można utworzyć w celu oceny pakietów SSIS w partiach i identyfikacji problemów ze zgodnością, które są prezentowane w następujących kategoriach:

- Blokery migracji: są to problemy ze zgodnością, które blokują pakiety źródeł migracji do uruchomienia w usłudze Azure-SSIS IR. DMA zawiera wskazówki, które pomogą Ci rozwiązać te problemy.

- Problemy informacyjne: Są to częściowo obsługiwane lub przestarzałe funkcje, które są używane w pakietach źródłowych. DMA zawiera kompleksowy zestaw zaleceń, alternatywne podejścia dostępne na platformie Azure i kroki ograniczające zagrożenie, aby rozwiązać.

### <a name="four-storage-types-for-ssis-packages"></a>Cztery typy pamięci masowej dla pakietów SSIS

- Katalog SSIS (SSISDB). Zostało to wprowadzone w programie SQL Server 2012 i zawiera zestaw procedur składowanych, widoków i funkcji o wartości tabeli używanych do pracy z projektami/pakietami SSIS.
- System plików.
- Baza danych systemu PROGRAMU SQL Server (MSDB).
- sklep z paczkami SSIS. Jest to warstwa zarządzania pakietami na dwóch podtypach:
  - MSDB, która jest systemową bazą danych w programie SQL Server używanym do przechowywania pakietów SSIS.
  - Zarządzany system plików, który jest określonym folderem w ścieżce instalacji programu SQL Server używanej do przechowywania pakietów SSIS.

DMA obsługuje obecnie wsadową ocenę pakietów przechowywanych w **systemie plików,** **magazynie pakietów**i **katalogu SSIS** od **wersji DMA v5.0**.

Pobierz [DMA](https://docs.microsoft.com/sql/dma/dma-overview)i [wykonaj swoją ocenę pakietu z nim.](https://docs.microsoft.com/sql/dma/dma-assess-ssis)

## <a name="migration"></a>Migracja

W zależności od [typów magazynu](#four-storage-types-for-ssis-packages) źródłowych pakietów SSIS i miejsca docelowego migracji obciążeń bazy danych kroki migracji **pakietów SSIS** i **zadań programu SQL Server Agent,** które planują wykonanie pakietów SSIS, mogą się różnić. Istnieją dwa scenariusze:

- [**Wystąpienie zarządzanego usługi Azure SQL Database** jako miejsce docelowe obciążenia bazy danych](#azure-sql-database-managed-instance-as-database-workload-destination)
- [**Usługa Azure SQL Database** jako miejsce docelowe obciążenia bazy danych](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>**Wystąpienie zarządzanego usługi Azure SQL Database** jako miejsce docelowe obciążenia bazy danych

| **Typ magazynu pakietów** |Jak wsadować pakiety SSIS|Jak wsadowa migracja zadań SSIS|
|-|-|-|
|SSISDB (WYW.|[Migrowanie **bazy SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|[Migrowanie zadań SSIS do agenta wystąpienia zarządzanego usługi Azure SQL Database](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|System plików|Ponownie rozmieszczaj je do udziału plików/usługi Azure Files za pośrednictwem dtinstall/dtutil/ręcznej kopii lub do przechowywania w systemach plików, aby uzyskać dostęp za pośrednictwem sieci wirtualnej/self-hosted IR. Aby uzyskać więcej informacji, zobacz [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Migrowanie za pomocą [Kreatora migracji zadań SSIS w ssms](how-to-migrate-ssis-job-ssms.md) <li>Konwertuj je na potoki/działania/wyzwalacze usługi ADF za pośrednictwem portala skryptów/SSMS/ADF. Aby uzyskać więcej informacji, zobacz [Funkcja planowania SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Program SQL Server (MSDB)|Eksportuj je do systemów plików/udziałów plików/usługi Azure Files za pośrednictwem usługi SSMS/dtutil. Aby uzyskać więcej informacji, zobacz [Eksportowanie pakietów SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Konwertuj je na potoki/działania/wyzwalacze usługi ADF za pośrednictwem portala skryptów/SSMS/ADF. Aby uzyskać więcej informacji, zobacz [Funkcja planowania SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Magazyn pakietów|Wyeksportuj je do systemów plików/udziałów plików/usługi Azure Files za pośrednictwem SSMS/dtutil lub ponownie wdrożyć je do udziałów plików/usługi Azure Files za pomocą dtinstall/dtutil/ręcznej kopii lub zachować je w systemach plików, aby uzyskać dostęp za pośrednictwem sieci wirtualnej / self-hosted IR. Aby uzyskać więcej informacji, zobacz narzędzie dtutil. Aby uzyskać więcej informacji, zobacz [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konwertuj je na potoki/działania/wyzwalacze usługi ADF za pośrednictwem portala skryptów/SSMS/ADF. Aby uzyskać więcej informacji, zobacz [Funkcja planowania SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Usługa Azure SQL Database** jako miejsce docelowe obciążenia bazy danych

| **Typ magazynu pakietów** |Jak wsadować pakiety SSIS|Jak wsadowe zadania migracji|
|-|-|-|
|SSISDB (WYW.|Ponowne wdrożenie do usługi Azure-SSISDB za pośrednictwem protokołu SSDT/SSMS. Aby uzyskać więcej informacji, zobacz [Wdrażanie pakietów SSIS na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Konwertuj je na potoki/działania/wyzwalacze usługi ADF za pośrednictwem portala skryptów/SSMS/ADF. Aby uzyskać więcej informacji, zobacz [Funkcja planowania SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|System plików|Ponownie rozmieszczaj je do udziału plików/usługi Azure Files za pośrednictwem dtinstall/dtutil/ręcznej kopii lub do przechowywania w systemach plików, aby uzyskać dostęp za pośrednictwem sieci wirtualnej/self-hosted IR. Aby uzyskać więcej informacji, zobacz [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|<li> Migrowanie za pomocą [Kreatora migracji zadań SSIS w ssms](how-to-migrate-ssis-job-ssms.md) <li> Konwertuj je na potoki/działania/wyzwalacze usługi ADF za pośrednictwem portala skryptów/SSMS/ADF. Aby uzyskać więcej informacji, zobacz [Funkcja planowania SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Program SQL Server (MSDB)|Eksportuj je do systemów plików/udziałów plików/usługi Azure Files za pośrednictwem usługi SSMS/dtutil. Aby uzyskać więcej informacji, zobacz [Eksportowanie pakietów SSIS](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service).|Konwertuj je na potoki/działania/wyzwalacze usługi ADF za pośrednictwem portala skryptów/SSMS/ADF. Aby uzyskać więcej informacji, zobacz [Funkcja planowania SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Magazyn pakietów|Wyeksportuj je do systemów plików/udziałów plików/usługi Azure Files za pośrednictwem SSMS/dtutil lub ponownie wdrożyć je do udziałów plików/usługi Azure Files za pomocą dtinstall/dtutil/ręcznej kopii lub zachować je w systemach plików, aby uzyskać dostęp za pośrednictwem sieci wirtualnej / self-hosted IR. Aby uzyskać więcej informacji, zobacz narzędzie dtutil. Aby uzyskać więcej informacji, zobacz [dtutil utility](https://docs.microsoft.com/sql/integration-services/dtutil-utility).|Konwertuj je na potoki/działania/wyzwalacze usługi ADF za pośrednictwem portala skryptów/SSMS/ADF. Aby uzyskać więcej informacji, zobacz [Funkcja planowania SSMS](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Asystent migracji bazy danych](https://docs.microsoft.com/sql/dma/dma-overview)
- [Przenoszenie i przenoszenie obciążeń SSIS do chmury](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [Migrowanie pakietów SSIS do wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [Ponowne rozmieszczenie pakietów do bazy danych SQL platformy Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>Następne kroki

- [Sprawdzanie poprawności pakietów SSIS wdrożonych na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Uruchamianie pakietów SSIS wdrożonych na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitorowanie środowiska wykonawczego integracji platformy Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Planowanie wykonywania pakietów SSIS na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
