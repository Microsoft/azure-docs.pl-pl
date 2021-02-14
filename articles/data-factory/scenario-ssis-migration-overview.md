---
title: Migrowanie obciążeń lokalnych SQL Server Integration Services (SSIS) do usług SSIS w Azure Data Factory (ADF)
description: Migrowanie lokalnych obciążeń usług SSIS do usług SSIS w podajniku ADF.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 78c488302a874319f79a143e4657d161fe849855
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373610"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Migrowanie lokalnych obciążeń usług SSIS do usług SSIS w podajniku APD

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Omówienie

W przypadku migrowania obciążeń bazy danych z SQL Server lokalnych do usług Azure Database Services, mianowicie Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL, można również migrować obciążenia ETL na SQL Server Integration Services (SSIS) jako jedną z głównych usług dodanych.

Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF) obsługuje uruchamianie pakietów usług SSIS. Po zainicjowaniu Azure-SSIS IR można używać znanych narzędzi, takich jak SQL Server narzędzia do obsługi danych (SSDT)/SQL Server Management Studio (SSMS) oraz narzędzi wiersza polecenia, takich jak dtinstall/dtutil/dtexec, aby wdrażać i uruchamiać pakiety na platformie Azure. Aby uzyskać więcej informacji, zobacz temat [usługa Azure SSIS Wind-and-Shift — Omówienie](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

W tym artykule przedstawiono proces migracji obciążeń ETL z lokalnych usług SSIS do usług SSIS w podajniku ADF. Proces migracji składa się z dwóch faz: **oceny** i **migracji**.

## <a name="assessment"></a>Ocena

Aby ustanowić Kompletny plan migracji, gruntowna Ocena pomoże zidentyfikować problemy ze źródłowym pakietem SSIS, które uniemożliwią pomyślne przeprowadzenie migracji.

Data Migration Assistant (DMA) to bezpłatnie dostępne narzędzie do pobrania, które można zainstalować i uruchomić lokalnie. Projekt oceny DMA typów **usług Integration Services** można utworzyć w celu oceny pakietów SSIS w partiach i zidentyfikowania problemów ze zgodnością, które są przedstawione w następujących kategoriach:

- Bloki migracji: problemy ze zgodnością, które blokują uruchamianie pakietów źródłowych migracji na Azure-SSIS IR. Dostęp DMA zawiera wskazówki ułatwiające rozwiązywanie tych problemów.

- Problemy informacyjne: częściowo obsługiwane lub przestarzałe funkcje, które są używane w pakietach źródłowych. Usługa DMA zapewnia kompleksowy zestaw zaleceń, alternatywne podejścia dostępne na platformie Azure i ograniczanie czynności do rozwiązania.

### <a name="four-storage-types-for-ssis-packages"></a>Cztery typy magazynów dla pakietów SSIS

- Katalog SSIS (SSISDB). Wprowadzono w SQL Server 2012 i zawiera zestaw procedur składowanych, widoków i funkcji zwracających tabele służących do pracy z projektami/pakietami SSIS.
- System plików.
- Baza danych systemu SQL Server (MSDB).
- Magazyn pakietów SSIS. Warstwa zarządzania pakietami na dwóch podtypów:
  - MSDB, która jest systemową bazą danych w SQL Server używany do przechowywania pakietów SSIS.
  - Zarządzany system plików, który jest określonym folderem w ścieżce instalacji SQL Server używany do przechowywania pakietów SSIS.

W usłudze DMA jest obecnie obsługiwana Ocena partii pakietów przechowywanych w **systemie plików**, **magazynie pakietów** i **katalogu usług SSIS** od czasu **DMA w wersji 2.0**.

Uzyskaj [dostęp DMA](/sql/dma/dma-overview)i [Przeprowadź ocenę pakietu](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Migracja

W zależności od [typów magazynu](#four-storage-types-for-ssis-packages) źródłowych pakietów SSIS i lokalizacji docelowej migracji obciążeń bazy danych, kroki służące do migracji  **pakietów SSIS** i **SQL Server zadania agenta** , które zaplanują wykonywanie pakietów SSIS. Istnieją dwa scenariusze:

- [**Wystąpienie zarządzane usługi Azure SQL** jako miejsce docelowe obciążenia bazy danych](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** jako miejsce docelowe obciążeń bazy danych](#azure-sql-database-as-database-workload-destination)

Jest to również praktyczny sposób używania [narzędzi SSIS DevOps](/sql/integration-services/devops/ssis-devops-overview)do ponownego wdrożenia pakietu wsadowego w miejscu docelowym migracji.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**Wystąpienie zarządzane usługi Azure SQL** jako miejsce docelowe obciążenia bazy danych

| **Typ magazynu pakietów** |Jak przeprowadzić migrację wsadową pakietów SSIS|Jak przeprowadzić migrację wsadową zadań SSIS|
|-|-|-|
|SSISDB|[Migruj **SSISDB**](scenario-ssis-migration-ssisdb-mi.md)|<li>[Migrowanie zadań SSIS do agenta wystąpienia zarządzanego usługi Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|System plików|Wdróż je ponownie w udziałach plików/Azure Files za pośrednictwem dtinstall/dtutil/Manual Copy lub aby zachować dostęp do systemów plików za pośrednictwem sieci wirtualnej/samoobsługowego środowiska IR. Aby uzyskać więcej informacji, zobacz [Narzędzie dtutil](/sql/integration-services/dtutil-utility).|<li>[Migrowanie zadań SSIS do agenta wystąpienia zarządzanego usługi Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Migrowanie za pomocą [Kreatora migracji zadań SSIS w programie SSMS](how-to-migrate-ssis-job-ssms.md) <li>Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Wyeksportuj je do systemów plików/udziałów plików/Azure Files za pośrednictwem programu SSMS/dtutil. Aby uzyskać więcej informacji, zobacz [Eksportowanie pakietów SSIS](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Magazyn pakietów|Wyeksportuj je do magazynu pakietów za pośrednictwem programu SSMS/dtutil lub wdróż je ponownie w sklepie Package Store za pomocą funkcji dtinstall/dtutil/Manual Copy. Aby uzyskać więcej informacji, zobacz [Zarządzanie pakietami za pomocą magazynu pakietów Azure-SSIS Integration Runtime](azure-ssis-integration-runtime-package-store.md).|<li>[Migrowanie zadań SSIS do agenta wystąpienia zarządzanego usługi Azure SQL](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** jako miejsce docelowe obciążeń bazy danych

| **Typ magazynu pakietów** |Jak przeprowadzić migrację wsadową pakietów SSIS|Jak przeprowadzić migrację wsadową zadań|
|-|-|-|
|SSISDB|Wdróż ponownie na platformie Azure — SSISDB za pośrednictwem SSDT/SSMS. Aby uzyskać więcej informacji, zobacz [wdrażanie pakietów usług SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|System plików|Wdróż je ponownie w udziałach plików/Azure Files za pośrednictwem dtinstall/dtutil/Manual Copy lub aby zachować dostęp do systemów plików za pośrednictwem sieci wirtualnej/samoobsługowego środowiska IR. Aby uzyskać więcej informacji, zobacz [Narzędzie dtutil](/sql/integration-services/dtutil-utility).|<li> Migrowanie za pomocą [Kreatora migracji zadań SSIS w programie SSMS](how-to-migrate-ssis-job-ssms.md) <li> Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Wyeksportuj je do systemów plików/udziałów plików/Azure Files za pośrednictwem programu SSMS/dtutil. Aby uzyskać więcej informacji, zobacz [Eksportowanie pakietów SSIS](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Magazyn pakietów|Wyeksportuj je do systemów plików/udziałów plików/Azure Files za pomocą narzędzia SSMS/dtutil lub wdróż je ponownie w udziałach plików/Azure Files za pośrednictwem dtinstall/dtutil/ręcznie Skopiuj lub Zachowaj dostęp do systemów plików za pośrednictwem sieci wirtualnej/samoobsługowego środowiska IR. Aby uzyskać więcej informacji, zobacz Narzędzie dtutil. Aby uzyskać więcej informacji, zobacz [Narzędzie dtutil](/sql/integration-services/dtutil-utility).|Przekonwertuj je na potoki ADF/działania/wyzwalacze za pośrednictwem skryptów/narzędzia SSMS/ADF Portal. Aby uzyskać więcej informacji, zobacz [Funkcja planowania programu SSMS](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Azure Data Factory](./introduction.md)
- [Narzędzie Database Migration Assistant](/sql/dma/dma-overview)
- [Podnieś i Przenieś obciążenia SSIS do chmury](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [Narzędzia DevOps usług SSIS](/sql/integration-services/devops/ssis-devops-overview)
- [Migrowanie pakietów SSIS do wystąpienia zarządzanego Azure SQL](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Wdróż ponownie pakiety do Azure SQL Database](../dms/how-to-migrate-ssis-packages.md)

- [Dostęp do danych lokalnych z Azure-SSIS Integration Runtime](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Dostosuj konfigurację dla Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Uzyskiwanie dostępu do magazynów danych i udziałów plików za pomocą uwierzytelniania systemu Windows z poziomu pakietów SSIS na platformie Azure](ssis-azure-connect-with-windows-auth.md)
- [Korzystanie z uwierzytelniania tożsamości zarządzanej](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Korzystanie z rozwiązania Azure Key Vault](store-credentials-in-key-vault.md)
- [Konfigurowanie Azure-SSIS Integration Runtime na potrzeby wysokiej wydajności](configure-azure-ssis-integration-runtime-performance.md)
- [How to start and stop Azure-SSIS Integration Runtime on a schedule (Jak uruchamiać i zatrzymywać środowisko Azure-SSIS Integration Runtime według harmonogramu)](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Następne kroki

- [Weryfikowanie pakietów usług SSIS wdrożonych na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Uruchamianie pakietów SSIS wdrożonych na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Monitoruj Azure-SSIS Integration Runtime](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Zaplanuj wykonywanie pakietów SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)