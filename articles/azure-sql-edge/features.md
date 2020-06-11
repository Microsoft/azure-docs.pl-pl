---
title: Obsługiwane funkcje usługi Azure SQL Edge (wersja zapoznawcza)
description: Więcej informacji o funkcjach obsługiwanych przez usługę Azure SQL Edge (wersja zapoznawcza).
keywords: Wprowadzenie do usługi SQL Edge, co to jest SQL Edge, Omówienie programu SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 34d2ba05b00ab92066bc7fa3ccd0b7b2aa59e15c
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669641"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Obsługiwane funkcje usługi Azure SQL Edge (wersja zapoznawcza) 

Usługa Azure SQL Edge jest oparta na najnowszej wersji aparatu bazy danych Microsoft SQL Server w systemie Linux. Obsługuje ona podzestaw funkcji obsługiwanych w SQL Server 2019 w systemie Linux, a także niektóre funkcje, które nie są obecnie obsługiwane lub dostępne w SQL Server 2019 w systemie Linux (lub w SQL Server z systemem Windows).

Aby zapoznać się z pełną listą funkcji obsługiwanych w SQL Server on Linux, zobacz [wersje i obsługiwane funkcje SQL Server 2019 w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019). Wersje i obsługiwane funkcje SQL Server w systemie Windows znajdują się w temacie [wersje i obsługiwane funkcje SQL Server 2019 (15. x)](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15).

> [!NOTE]
> Usługa Azure SQL Edge jest obecnie w wersji zapoznawczej i nie powinna być używana w środowiskach produkcyjnych. Firma Microsoft może zalecić uruchamianie usługi Azure SQL Edge w środowiskach produkcyjnych, w zależności od weryfikacji wdrożenia i scenariuszy przypadków użycia.

## <a name="azure-sql-edge-editions"></a>Wersje usługi Azure SQL Edge

Usługa Azure SQL Edge jest dostępna z dwiema różnymi wersjami lub planami oprogramowania. Te wersje mają identyczne zestawy funkcji i różnią się w zależności od ich praw użytkowania oraz ilości pamięci i rdzeni, które mogą uzyskać do nich dostęp w systemie hosta.

   |**Planowanie**  |**Opis**  |
   |---------|---------|
   |Deweloper usługi Azure SQL Edge  |  Tylko do celów programistycznych. Każdy kontener dewelopera usługi Azure SQL Edge jest ograniczony do maksymalnie 4 rdzeni i 32 GB pamięci.  |
   |Usługa Azure SQL Edge    |  Dla środowiska produkcyjnego. Każdy kontener usługi Azure SQL Edge jest ograniczony do maksymalnie 8 rdzeni i 64 GB pamięci.  |

## <a name="operating-system"></a>System operacyjny

Kontenery usługi Azure SQL Edge są obecnie oparte na Ubuntu 16,04 i są obsługiwane tylko na hostach platformy Docker z systemem Ubuntu 16,04 (zalecane) lub Ubuntu 18,04. Usługa Azure SQL Edge może być również uruchamiana na innych hostach systemu operacyjnego. Na przykład może działać na innych dystrybucjach systemu Linux lub Windows (przy użyciu platformy Docker CE lub platformy Docker EE). Należy jednak pamiętać, że firma Microsoft nie przetestowała na te konfiguracje.

Usługa Azure SQL Edge jest obecnie obsługiwana tylko w przypadku wdrażania za pomocą Azure IoT Edge. Aby uzyskać więcej informacji, zobacz [Azure IoT Edge obsługiwane systemy](https://docs.microsoft.com/azure/iot-edge/support).

Zalecaną konfiguracją uruchamiania usługi Azure SQL Edge w systemie Windows jest skonfigurowanie maszyny wirtualnej Ubuntu na hoście z systemem Windows, a następnie uruchomienie usługi Azure SQL Edge wewnątrz maszyny wirtualnej z systemem Linux.

## <a name="hardware-support"></a>Obsługa sprzętu

Usługa Azure SQL Edge wymaga procesora 64-bitowego, który może być z procesorów Intel, AMD lub ARM, z co najmniej jednym procesorem i jedną GB pamięci RAM na hoście. Podczas gdy rozmiar pamięci początkowej programu Azure SQL Edge jest bliski 500 MB, dodatkowa pamięć jest wymagana dla innych modułów IoT Edge uruchomionych na urządzeniu brzegowym.

## <a name="azure-sql-edge-components"></a>Składniki usługi Azure SQL Edge

Usługa Azure SQL Edge obsługuje tylko aparat bazy danych. Nie obejmuje to obsługi innych składników dostępnych w SQL Server 2019 w systemie Windows lub SQL Server 2019 w systemie Linux. W przeciwnym razie usługa Azure SQL Edge nie obsługuje składników SQL Server, takich jak Analysis Services, usługi Reporting Services, usługi integracji, Master Data Services, Machine Learning Services (w bazie danych) i Machine Learning Server (autonomiczne).

## <a name="supported-features"></a>Obsługiwane funkcje

Oprócz obsługi podzestawu funkcji programu SQL Server on Linux usługa Azure SQL Edge obejmuje obsługę następujących nowych funkcji: 

- Funkcja przesyłania strumieniowego SQL, która jest oparta na tym samym aparacie, który ma Azure Stream Analytics, zapewnia możliwości przesyłania strumieniowego danych w czasie rzeczywistym w usłudze Azure SQL Edge. 
- Wywołanie funkcji T-SQL `Date_Bucket` do analizy danych szeregów czasowych.
- Możliwości uczenia maszynowego za pomocą środowiska uruchomieniowego ONNX, dołączonego do aparatu SQL.

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

Poniższa lista zawiera SQL Server 2019 funkcji systemu Linux, które nie są obecnie obsługiwane w usłudze Azure SQL Edge.

| Obszar | Nieobsługiwana funkcja lub usługa |
|-----|-----|
| **Projekt bazy danych** | OLTP w pamięci i powiązane polecenia języka DDL oraz funkcje Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania. |
| &nbsp; | `HierarchyID`Typ danych i powiązane polecenia języka DDL oraz funkcje Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania. |
| &nbsp; | `Spatial`Typ danych i powiązane polecenia języka DDL oraz funkcje Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania. |
| &nbsp; | Rozciągaj bazę danych oraz powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania. |
| &nbsp; | Indeksy pełnotekstowe i wyszukiwanie oraz powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania.|
| &nbsp; | `FileTable`, `FILESTREAM` , i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania.|
| **Aparat bazy danych** | Replikacji. Należy pamiętać, że usługi Azure SQL Edge można skonfigurować jako subskrybenta wypychania topologii replikacji. |
| &nbsp; | PolyBase. Należy pamiętać, że usługa Azure SQL Edge można skonfigurować jako element docelowy dla tabel zewnętrznych w bazie kodu. |
| &nbsp; | Rozszerzalność języka za poorednictwem języka Java i platformy Spark. |
| &nbsp; | Active Directory integrację. |
| &nbsp; | Migawki baz danych. |
| &nbsp; | Obsługa pamięci trwałej. |
| &nbsp; | Microsoft Distributed Transaction Coordinator. |
| &nbsp; | Zarządca zasobów i zarządzanie zasobami we/wy. |
| &nbsp; | Rozszerzenie puli buforów. |
| &nbsp; | Zapytanie rozproszone z połączeniami innych firm. |
| &nbsp; | Połączone serwery. |
| &nbsp; | Rozszerzone procedury składowane systemu (takie jak `XP_CMDSHELL` ). |
| &nbsp; | Zestawy CLR i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania. |
| &nbsp; | Zależne od CLR funkcje T-SQL, takie jak `ASSEMBLYPROPERTY` ,, `FORMAT` `PARSE` , i `TRY_PARSE` . |
| &nbsp; | Widok wykazu dat i godzin zależnych od CLR, funkcje i klauzule zapytania. |
| &nbsp; | Rozszerzenie puli buforów. |
| &nbsp; | Poczta bazy danych. |
| **Program SQL Server Agent** |  Podsystemy: CmdExec, PowerShell, czytniki kolejek, SSIS, SSAS i SSRS. |
| &nbsp; | Alerty. |
| &nbsp; | Zarządzana kopia zapasowa. |
| **Wysoka dostępność** | Zawsze włączone grupy dostępności.  |
| &nbsp; | Podstawowe grupy dostępności. |
| &nbsp; | Zawsze włączone wystąpienie klastra trybu failover. |
| &nbsp; | Dublowanie bazy danych. |
| &nbsp; | Gorące Dodawanie pamięci i procesora CPU. |
| **Bezpieczeństwo** | Rozszerzalne zarządzanie kluczami. |
| &nbsp; | Active Directory integrację.|
| &nbsp; | Obsługa bezpiecznego enclaves.|
| **Usługi** | SQL Server Browser. |
| &nbsp; | Machine Learning za poorednictwem języków R i Python. |
| &nbsp; | StreamInsight. |
| &nbsp; | Analysis Services. |
| &nbsp; | Usługi Reporting Services. |
| &nbsp; | Usługi Data Quality Services. |
| &nbsp; | Master Data Services. |
| &nbsp; | Distributed Replay. |
| **Możliwości zarządzania** | SQL Server Utility punkt kontroli. |

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie usługi Azure SQL Edge](deploy-portal.md)
- [Konfigurowanie usługi Azure SQL Edge](configure.md)
- [Nawiązywanie połączenia z wystąpieniem usługi Azure SQL Edge przy użyciu narzędzi SQL Server Client Tools](connect.md)
- [Tworzenie kopii zapasowych i przywracanie baz danych w usłudze Azure SQL Edge](backup-restore.md)
