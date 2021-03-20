---
title: Obsługiwane funkcje usługi Azure SQL Edge
description: Więcej informacji o funkcjach obsługiwanych przez usługę Azure SQL Edge.
keywords: Wprowadzenie do usługi SQL Edge, co to jest SQL Edge, Omówienie programu SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 19dcbbf102a1d8d21f1b14780ea33816a1677c55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392031"
---
# <a name="supported-features-of-azure-sql-edge"></a>Obsługiwane funkcje usługi Azure SQL Edge 

Usługa Azure SQL Edge jest oparta na najnowszej wersji aparatu SQL Database. Obsługuje ona podzestaw funkcji obsługiwanych w SQL Server 2019 w systemie Linux, a także niektóre funkcje, które nie są obecnie obsługiwane lub dostępne w SQL Server 2019 w systemie Linux (lub w SQL Server z systemem Windows).

Aby zapoznać się z pełną listą funkcji obsługiwanych w SQL Server on Linux, zobacz [wersje i obsługiwane funkcje SQL Server 2019 w systemie Linux](/sql/linux/sql-server-linux-editions-and-components-2019). Wersje i obsługiwane funkcje SQL Server w systemie Windows znajdują się w temacie [wersje i obsługiwane funkcje SQL Server 2019 (15. x)](/sql/sql-server/editions-and-components-of-sql-server-version-15).

## <a name="azure-sql-edge-editions"></a>Wersje usługi Azure SQL Edge

Usługa Azure SQL Edge jest dostępna z dwiema różnymi wersjami lub planami oprogramowania. Te wersje mają identyczne zestawy funkcji i różnią się w zależności od ich praw użytkowania oraz ilości pamięci i rdzeni, które mogą uzyskać do nich dostęp w systemie hosta.

   |**Planowanie**  |**Opis**  |
   |---------|---------|
   |Deweloper usługi Azure SQL Edge  |  Tylko do celów programistycznych. Każdy kontener dewelopera usługi Azure SQL Edge jest ograniczony do maksymalnie 4 rdzeni i 32 GB pamięci.  |
   |Usługa Azure SQL Edge    |  Dla środowiska produkcyjnego. Każdy kontener usługi Azure SQL Edge jest ograniczony do maksymalnie 8 rdzeni i 64 GB pamięci.  |

## <a name="operating-system"></a>System operacyjny

Kontenery usługi Azure SQL Edge są oparte na Ubuntu 18,04 i są obsługiwane tylko na hostach Docker z systemem Ubuntu 18,04 LTS (zalecane) lub Ubuntu 20,04 LTS. Można uruchamiać kontenery usługi Azure SQL Edge na innych hostach z systemem operacyjnym, na przykład w przypadku innych dystrybucji systemu Linux lub Windows (przy użyciu platformy Docker CE lub platformy Docker EE), jednak firma Microsoft nie zaleca wykonywania tych czynności, ponieważ taka konfiguracja może nie zostać przetestowana.

Zalecaną konfiguracją uruchamiania usługi Azure SQL Edge w systemie Windows jest skonfigurowanie maszyny wirtualnej Ubuntu na hoście z systemem Windows, a następnie uruchomienie usługi Azure SQL Edge wewnątrz maszyny wirtualnej z systemem Linux.

Zalecany i obsługiwany system plików dla usługi Azure SQL Edge to EXT4 i XFS. Jeśli woluminy trwałe są używane do tworzenia kopii zapasowych magazynu bazy danych usługi Azure SQL Edge, podstawowy system plików hosta musi być EXT4 i XFS.

## <a name="hardware-support"></a>Obsługa sprzętowa

Usługa Azure SQL Edge wymaga procesora 64-bitowego (x64 lub ARM64), co najmniej jednego procesora i 1 GB pamięci RAM na hoście. Podczas gdy rozmiar pamięci początkowej usługi Azure SQL Edge jest bliski 450MB, dodatkowa pamięć jest wymagana dla innych modułów IoT Edge lub procesów uruchomionych na urządzeniu brzegowym. Rzeczywiste wymagania dotyczące pamięci i procesora dla programu Azure SQL Edge różnią się w zależności od złożoności obciążenia i ilości przetwarzanych danych. W przypadku wybrania sprzętu dla rozwiązania firma Microsoft zaleca uruchomienie obszernych testów wydajnościowych, aby upewnić się, że są spełnione wymagane charakterystyki wydajności dla danego rozwiązania.  

## <a name="azure-sql-edge-components"></a>Składniki usługi Azure SQL Edge

Usługa Azure SQL Edge obsługuje tylko aparat bazy danych. Nie obejmuje to obsługi innych składników dostępnych w SQL Server 2019 w systemie Windows lub SQL Server 2019 w systemie Linux. W przeciwnym razie usługa Azure SQL Edge nie obsługuje składników SQL Server, takich jak Analysis Services, usługi Reporting Services, usługi integracji, Master Data Services, Machine Learning Services (w bazie danych) i Machine Learning Server (autonomiczne).

## <a name="supported-features"></a>Obsługiwane funkcje

Oprócz obsługi podzestawu funkcji programu SQL Server on Linux usługa Azure SQL Edge obejmuje obsługę następujących nowych funkcji: 

- Funkcja przesyłania strumieniowego SQL, która jest oparta na tym samym aparacie, który ma Azure Stream Analytics, zapewnia możliwości przesyłania strumieniowego danych w czasie rzeczywistym w usłudze Azure SQL Edge. 
- Wywołanie funkcji T-SQL `Date_Bucket` dla Time-Series analizy danych.
- Możliwości uczenia maszynowego za pomocą środowiska uruchomieniowego ONNX, dołączonego do aparatu SQL.

## <a name="unsupported-features"></a>Nieobsługiwane funkcje

Poniższa lista zawiera SQL Server 2019 funkcji systemu Linux, które nie są obecnie obsługiwane w usłudze Azure SQL Edge.

| Warstwowy | Nieobsługiwana funkcja lub usługa |
|-----|-----|
| **Projekt bazy danych** | OLTP w pamięci i powiązane polecenia języka DDL oraz funkcje Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania. |
| &nbsp; | `HierarchyID` Typ danych i powiązane polecenia języka DDL oraz funkcje Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania. |
| &nbsp; | `Spatial` Typ danych i powiązane polecenia języka DDL oraz funkcje Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania. |
| &nbsp; | Rozciągaj bazę danych oraz powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania. |
| &nbsp; | Indeksy pełnotekstowe i wyszukiwanie oraz powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania.|
| &nbsp; | `FileTable`, `FILESTREAM` , i powiązane polecenia języka DDL oraz funkcje języka Transact-SQL, widoki wykazu i dynamiczne widoki zarządzania.|
| **Aparat bazy danych** | Replikacji. Należy pamiętać, że usługi Azure SQL Edge można skonfigurować jako subskrybenta wypychania topologii replikacji. |
| &nbsp; | PolyBase. Należy pamiętać, że usługa Azure SQL Edge można skonfigurować jako element docelowy dla tabel zewnętrznych w bazie kodu. |
| &nbsp; | Rozszerzalność języka za poorednictwem języka Java i platformy Spark. |
| &nbsp; | Active Directory integrację. |
| &nbsp; | Funkcja autozmniejszania bazy danych. Właściwość Autozmniejszanie dla bazy danych można ustawić przy użyciu `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` polecenia, ale zmiana nie ma żadnego wpływu. Zadanie automatycznego zmniejszania nie zostanie uruchomione względem bazy danych. Użytkownicy mogą nadal zmniejszać pliki bazy danych przy użyciu poleceń DBCC. |
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
| &nbsp; | Service Broker |
| &nbsp; | Zarządzanie oparte na zasadach |
| &nbsp; | Magazyn danych zarządzania |
| &nbsp; | Zawarte bazy danych |
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