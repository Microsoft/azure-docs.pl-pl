---
title: Obsługiwane funkcje usługi Azure SQL Edge (wersja zapoznawcza)
description: Więcej informacji o funkcjach obsługiwanych przez usługę Azure SQL Edge (wersja zapoznawcza)
keywords: Wprowadzenie do usługi SQL Edge, co to jest SQL Edge, Omówienie programu SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e3bac1cbb0e57f9be4a489207f2fe8b30b5d457e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233209"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Obsługiwane funkcje usługi Azure SQL Edge (wersja zapoznawcza) 

Ten artykuł zawiera szczegółowe informacje o funkcjach obsługiwanych przez usługę Azure SQL Edge. Usługa Azure SQL Edge została utworzona w oparciu o najnowszą wersję aparatu bazy danych Microsoft SQL Server w systemie Linux i obsługuje podzestaw funkcji obsługiwanych przez SQL Server 2019 dla systemu Linux, a także niektóre funkcje, które nie są obecnie obsługiwane lub dostępne w SQL Server 2019 w systemie Linux lub Windows. Aby zapoznać się z pełną listą funkcji obsługiwanych w SQL Server on Linux, zobacz [wersje i obsługiwane funkcje SQL Server 2019 w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Wersje i obsługiwane funkcje SQL Server w systemie Windows można znaleźć w artykułach [wersje i obsługiwane funkcje SQL Server 2019 (15. x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> Usługa Azure SQL Edge jest obecnie w wersji zapoznawczej i nie powinna być używana w środowiskach produkcyjnych. Firma Microsoft może zalecić uruchomienie usługi Azure SQL Edge w środowiskach produkcyjnych po sprawdzeniu poprawności wdrożenia i scenariuszy przypadków użycia.

## <a name="azure-sql-edge-editions"></a>Wersje usługi Azure SQL Edge

Usługa Azure SQL Edge jest dostępna z dwiema różnymi wersjami lub planami oprogramowania. Wersje te mają identyczne zestawy funkcji i różnią się w zależności od ich praw użytkowania i ilości procesora CPU/pamięci, do których mogą uzyskać dostęp w systemie hosta.

   |**Planowanie**  |**Opis**  |
   |---------|---------|
   |Deweloper usługi Azure SQL Edge  |  Tylko programowanie SKU, każdy kontener dewelopera usługi Azure SQL Edge jest ograniczony do maksymalnie 4 rdzeni i 32 GB pamięci  |
   |Usługa Azure SQL Edge    |  Jednostka SKU produkcji, każdy kontener usługi Azure SQL Edge jest ograniczony do maksymalnie 8 rdzeni i 64 GB pamięci  |

## <a name="operating-system"></a>System operacyjny

Kontenery usługi Azure SQL Edge są obecnie oparte na Ubuntu 16,04 i są obsługiwane tylko na hostach platformy Docker z systemem Ubuntu 16,04 (zalecane) lub Ubuntu 18,04. Usługa Azure SQL Edge może być również uruchamiana na innych hostach z systemem operacyjnym, na przykład w innych dystrybucjach systemu Linux lub Windows (przy użyciu platformy Docker CE lub platformy Docker EE), ale te konfiguracje nie są szeroko testowane przez firmę Microsoft.

Usługa Azure SQL Edge jest obecnie obsługiwana tylko w przypadku wdrażania za pomocą Azure IoT Edge. Aby uzyskać więcej informacji na temat obsługiwanych systemów Azure IoT Edge, zapoznaj się z [Azure IoT Edge obsługiwanymi systemami](https://docs.microsoft.com/azure/iot-edge/support).

Zalecana konfiguracja uruchamiania usługi Azure SQL Edge w systemie Windows polega na skonfigurowaniu maszyny wirtualnej Ubuntu na hoście z systemem Windows, a następnie uruchomieniu programu SQL Edge wewnątrz maszyny wirtualnej z systemem Linux.

## <a name="hardware-support"></a>Obsługa sprzętu

Usługa Azure SQL Edge wymaga procesora 64-bitowego, który może być z procesorów Intel, AMD lub ARM, z co najmniej jednym procesorem i jedną GB pamięci RAM na hoście. Podczas gdy rozmiar pamięci początkowej programu Azure SQL Edge jest bliski 500 MB, dodatkowa pamięć jest wymagana dla innych modułów IoT Edge uruchomionych na urządzeniu brzegowym.

## <a name="azure-sql-edge-components"></a>Składniki usługi Azure SQL Edge

Usługa Azure SQL Edge obsługuje tylko aparat bazy danych i nie obejmuje obsługi innych składników dostępnych w SQL Server 2019 w systemie Windows lub SQL Server 2019 w systemie Linux. W przeciwnym razie usługa Azure SQL Edge nie obsługuje składników SQL Server, takich jak Analysis Services, usług Reporting Services, usług Integration Services, Master Data Services, Machine Learning Services (w bazie danych) i Machine Learning Server (autonomiczne).

## <a name="supported-features"></a>Obsługiwane funkcje

Oprócz obsługi podzbioru funkcji SQL Server on Linux usługa Azure SQL Edge oferuje obsługę następujących nowych funkcji. 

- Funkcja przesyłania strumieniowego SQL, która jest oparta na tym samym aparacie, który ma Azure Stream Analytics, zapewnia możliwości przesyłania strumieniowego danych w czasie rzeczywistym w usłudze Azure SQL Edge. 
- Nowe wywołanie funkcji T-SQL Date_Bucket do analizy danych szeregów czasowych.
- Machine Learning możliwości za pomocą środowiska uruchomieniowego ONNX, dołączonego do aparatu SQL.

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Funkcje SQL Server on Linux nie są obsługiwane w usłudze Azure SQL Edge

Poniższa lista zawiera SQL Server 2019 funkcji systemu Linux, które nie są obecnie obsługiwane w usłudze Azure SQL Edge.

| Warstwowy | Nieobsługiwana funkcja lub usługa |
|-----|-----|
| **Projekt bazy danych** | OLTP w pamięci i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania |
| &nbsp; | HierarchyID typ danych i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania |
| &nbsp; | Typ danych przestrzennych i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania |
| &nbsp; | Rozciąganie DB i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania |
| &nbsp; | Pełnotekstowe indeksy i wyszukiwanie i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania|
| &nbsp; | FileTable, FILESTREAM i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania|
| **Aparat bazy danych** | Replikacji. Jednak usługę Azure SQL Edge można skonfigurować jako subskrybenta wypychania topologii replikacji. |
| &nbsp; | PolyBase. Można jednak skonfigurować usługę Azure SQL Edge jako element docelowy dla tabel zewnętrznych w bazie kodu. |
| &nbsp; | Rozszerzalność języka za poorednictwem języka Java i platformy Spark |
| &nbsp; | Integracja z usługą Active Directory |
| &nbsp; | Migawki baz danych |
| &nbsp; | Obsługa pamięci trwałej |
| &nbsp; | Distributed Transaction Coordinator firmy Microsoft |
| &nbsp; | Zarządca zasobów i zarządzanie zasobami we/wy |
| &nbsp; | Rozszerzenie puli buforów |
| &nbsp; | Zapytanie rozproszone z połączeniami innych firm |
| &nbsp; | Serwery połączone |
| &nbsp; | Rozszerzone procedury składowane systemu (XP_CMDSHELL itd.) |
| &nbsp; | Zestawy CLR i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania |
| &nbsp; | Zależne od CLR funkcje T-SQL, takie jak ASSEMBLYPROPERTY, FORMAT, PARSE, TRY_PARSE |
| &nbsp; | Widok wykazu daty i godziny zależnych od CLR, funkcje i klauzule zapytania |
| &nbsp; | Rozszerzenie puli buforów |
| &nbsp; | Poczta bazy danych |
| **Program SQL Server Agent** |  Podsystemy: CmdExec, PowerShell, Reader do kolejki, SSIS, SSAS, SSRS |
| &nbsp; | Alerty |
| &nbsp; | Tworzenie zarządzanej kopii zapasowej |
| **Wysoka dostępność** | Zawsze włączone grupy dostępności  |
| &nbsp; | Podstawowe grupy dostępności |
| &nbsp; | Zawsze włączone wystąpienie klastra trybu failover |
| &nbsp; | Dublowanie bazy danych |
| &nbsp; | Gorące Dodawanie pamięci i procesora CPU |
| **Bezpieczeństwo** | Rozszerzalne zarządzanie kluczami |
| &nbsp; | Integracja z usługą Active Directory|
| &nbsp; | Obsługa bezpiecznych Enclaves|
| **Usługi** | SQL Server Browser |
| &nbsp; | Machine Learning za poorednictwem języków R i Python |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Usługi Data Quality Services |
| &nbsp; | Master Data Services |
| &nbsp; | Distributed Replay |
| **Możliwości zarządzania** | SQL Server Utility punkt kontroli |

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie usługi Azure SQL Edge](deploy-portal.md)
- [Konfigurowanie usługi Azure SQL Edge](configure.md)
- [Nawiązywanie połączenia z wystąpieniem usługi Azure SQL Edge przy użyciu narzędzi SQL Server Client Tools](connect.md)
- [Tworzenie kopii zapasowych i przywracanie baz danych w usłudze Azure SQL Edge](backup-restore.md)
