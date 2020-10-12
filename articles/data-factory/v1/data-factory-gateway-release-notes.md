---
title: Informacje o wersji bramy zarządzania danymi
description: Informacje o wersji tory bramy Zarządzanie danymi
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0538777cadf7935a6684932a17fadc10849518d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89490092"
---
# <a name="release-notes-for-data-management-gateway"></a>Informacje o wersji bramy zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [własne środowisko Integration Runtime w wersji 2](../create-self-hosted-integration-runtime.md).

Jednym z wyzwań związanych z integracją nowoczesnych danych jest przeniesienie danych do i z lokalizacji lokalnej do chmury. Data Factory zapewnia tę integrację z bramą Zarządzanie danymi, która jest agentem, który można zainstalować lokalnie, aby umożliwić przenoszenie danych hybrydowych.

Zobacz następujące artykuły, aby uzyskać szczegółowe informacje na temat bramy Zarządzanie danymi i korzystania z niej:

*  [Brama zarządzania danymi](data-factory-data-management-gateway.md)
*  [Przenoszenie danych między środowiskiem lokalnym i chmurą przy użyciu Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>BIEŻĄCA WERSJA 
Nie utrzymujemy jeszcze informacji o wersji. Pobierz najnowsze informacje o wersji [tutaj](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Wcześniejsze wersje
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Usprawni
- Możesz dodać wpisy DNS, aby zezwolić na korzystanie z usługi Service Bus zamiast zezwalać na wszystkie adresy IP platformy Azure z zapory (jeśli to konieczne). Odpowiedni wpis DNS można znaleźć na Azure Portal (Tworzenie i wdrażanie bram "->" "->" w formacie JSON (Data Factory >).
- Łącznik HDFS obsługuje teraz certyfikat publiczny z podpisem własnym przez umożliwienie pominięcia weryfikacji TLS.
- Naprawiono: problem z bramą w trybie offline podczas aktualizacji (ze względu na przechylenie zegara)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Usprawni
-   Możesz dodać wpisy DNS, aby zezwalać Service Bus, a nie zezwalać na wszystkie adresy IP platformy Azure z zapory (jeśli to konieczne). Więcej informacji znajdziesz tutaj.
-   Teraz można kopiować dane do/z pojedynczego bloku obiektów BLOB o rozmiarze do 4,75 TB, czyli maksymalnego obsługiwanego rozmiaru blokowego obiektu BLOB. (wcześniejszy limit to 195 GB).
-   Rozwiązano: problem z brakiem pamięci podczas rozpakowywania kilku małych plików podczas działania kopiowania.
-   Naprawiono: problem z zakresem podczas kopiowania z bazy danych dokumentów do SQL Server za pomocą funkcji idempotentności.
-   Naprawiono: skrypt czyszczący SQL nie działa z SQL Server Kreatora kopiowania.
-   Naprawiono: Nazwa kolumny ze spacją nie działa w działaniu kopiowania.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Usprawni
- Naprawiono: problem z brakującymi poświadczeniami na ponownym uruchomieniu maszyny bramy.
- Naprawiono: problem z rejestracją podczas przywracania bramy przy użyciu pliku kopii zapasowej.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Usprawni
- Naprawiono: nieprawidłowy odczyt dziesiętnej wartości null z bazy danych Oracle jako źródło.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Co nowego
- Klienci mogą przekazać opinie dotyczące środowiska rejestracji bramy.
- Obsługa nowego formatu kompresji: ZIP (Wklęśnięcie)

### <a name="enhancements-"></a>Usprawni
- Poprawa wydajności dla usługi Oracle sink i systemu plików HDFS.
- Poprawka błędów automatycznej aktualizacji bramy, wydajność przetwarzania równoległego bramy.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Ulepszenia 
- Udoskonalone i bardziej niezawodne rejestracje bramy — teraz można śledzić stan postępu podczas procesu rejestracji bramy, co sprawia, że proces rejestracji jest większy.
- Poprawa procesu przywracania bramy — nadal możesz odzyskać bramę, nawet jeśli nie masz pliku kopii zapasowej bramy z tą aktualizacją. Wymaga to zresetowania poświadczeń połączonej usługi w portalu.
- Poprawka błędu.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Co nowego

- Teraz można przechowywać poświadczenia źródła danych lokalnie. Poświadczenia są szyfrowane. Poświadczenia źródła danych mogą być odzyskiwane i przywracane przy użyciu pliku kopii zapasowej, który można wyeksportować z istniejącej bramy, lokalnie.

### <a name="enhancements-"></a>Usprawni

- Udoskonalone i bardziej niezawodne środowisko rejestracji bramy.
- Obsługa automatycznego wykrywania konfiguracji QuoteChar dla formatu tekstu w Kreatorze kopiowania oraz poprawa dokładności wykrywania w całym formacie.

## <a name="2361002"></a>2.3.6100.2

- Obsługa automatycznego wykrywania użycia i SkipLineCount w Kreatorze kopiowania plików tekstowych w lokalnym systemie plików i HDFS.
- Zwiększ stabilność połączenia sieciowego między bramą a Service Bus
- Kilka poprawek błędów


## <a name="2260721"></a>2.2.6072.1

*  Obsługuje Ustawianie serwera proxy HTTP dla bramy przy użyciu Configuration Manager bramy. W przypadku skonfigurowania usługi Azure Blob, Azure Table, Azure Data Lake i Document DB są dostępne za pośrednictwem serwera proxy HTTP.
*  Obsługuje obsługę nagłówków dla TextFormat podczas kopiowania danych z/do obiektów blob platformy Azure, Azure Data Lake Store, lokalnego systemu plików i lokalnych systemów HDFS.
*  Obsługuje kopiowanie danych z dołączanego obiektu BLOB i stronicowego obiektu BLOB wraz z już obsługiwanym blokowym obiektem BLOB.
*  Wprowadza nowy stan bramy **online (z ograniczeniami)**, który wskazuje, że główne funkcje bramy działają z wyjątkiem obsługi interakcyjnej operacji Kreatora kopiowania.
*  Zwiększa niezawodność rejestracji bramy przy użyciu klucza rejestracji.

## <a name="216040"></a>2.1.6040.

*  Sterownik DB2 jest teraz dołączony do pakietu instalacyjnego bramy. Nie trzeba instalować jej oddzielnie.
*  Sterownik DB2 obsługuje teraz systemy z/OS i DB2 dla i (jako/400) wraz z już obsługiwanymi platformami (Linux, UNIX i Windows).
*  Obsługuje używanie Azure Cosmos DB jako źródła lub lokalizacji docelowej lokalnych magazynów danych
*  Obsługuje kopiowanie danych z/do zimnego/gorącego magazynu obiektów BLOB wraz z już obsługiwanym kontem magazynu ogólnego przeznaczenia.
*  Umożliwia nawiązanie połączenia z SQL Server za pośrednictwem bramy z uprawnieniami zdalnego logowania.  

## <a name="2060131"></a>2.0.6013.1

*  Podczas instalacji ręcznej można wybrać język/kulturę, która będzie używana przez bramę.

*  Gdy brama nie działa zgodnie z oczekiwaniami, można wysłać dzienniki bramy z ostatnich siedmiu dni do firmy Microsoft w celu ułatwienia rozwiązywania problemów. Jeśli Brama nie jest połączona z usługą w chmurze, możesz zapisać i zarchiwizować dzienniki bramy.  

*  Ulepszenia interfejsu użytkownika dla Menedżera konfiguracji bramy:

    *  Zwiększ widoczność stanu bramy na karcie Narzędzia główne.

    *  Reorganizowane i uproszczone kontrolki.

    *  Dane można kopiować z magazynu za pomocą narzędzia do [kopiowania bez kodu](data-factory-copy-data-wizard-tutorial.md). Więcej informacji o tej funkcji znajduje się w sekcji [przygotowane kopie](data-factory-copy-activity-performance.md#staged-copy) .
*  Za pomocą bramy Zarządzanie danymi można odbierać dane przychodzące bezpośrednio z SQL Serverj bazy danych w Azure Machine Learning.

*  Usprawnienia wydajności

    * Zwiększ wydajność wyświetlania schematu/podglądu w odniesieniu do SQL Server w narzędziu do kopiowania bez kodu.

## <a name="11259531"></a>1.12.5953.1

*  Poprawki błędów

## <a name="11159181"></a>1.11.5918.1

*  Maksymalny rozmiar dziennika zdarzeń bramy został zwiększony z 1 MB do 40 MB.

*  Wyświetli się okno dialogowe ostrzeżenia, jeśli podczas aktualizacji bramy jest wymagane ponowne uruchomienie komputera. Możesz wybrać opcję ponownego uruchomienia w prawo lub w późniejszym czasie.

*  W przypadku niepowodzenia automatycznego aktualizowania Instalator bramy ponawia próbę automatycznego zaktualizowania.

*  Usprawnienia wydajności

    * Zwiększ wydajność ładowania dużych tabel z serwera lokalnego w scenariuszu kopiowania bez kodu.

*  Poprawki błędów

## <a name="11058921"></a>1.10.5892.1

*  Usprawnienia wydajności

*  Poprawki błędów

## <a name="1958652"></a>1.9.5865.2

*  Możliwość zerowej aktualizacji dotykowej
*  Ikona nowego paska zadań ze wskaźnikami stanu bramy
*  Możliwość "aktualizacji teraz" z klienta
*  Możliwość ustawienia czasu harmonogramu aktualizacji
*  Skrypt programu PowerShell służący do przełączania lub wyłączania autoaktualizacji
*  Obsługa formatu JSON  
*  Usprawnienia wydajności
*  Poprawki błędów

## <a name="1858221"></a>1.8.5822.1

*  Ulepszanie środowiska rozwiązywania problemów
*  Usprawnienia wydajności
*  Poprawki błędów

### <a name="1757951"></a>1.7.5795.1

*  Usprawnienia wydajności
*  Poprawki błędów

### <a name="1757641"></a>1.7.5764.1

*  Usprawnienia wydajności
*  Poprawki błędów

### <a name="1657351"></a>1.6.5735.1

*  Obsługa lokalnego źródła/ujścia systemu plików HDFS
*  Usprawnienia wydajności
*  Poprawki błędów

### <a name="1656961"></a>1.6.5696.1

*  Usprawnienia wydajności
*  Poprawki błędów

### <a name="1656761"></a>1.6.5676.1

*  Obsługa narzędzi diagnostycznych na Configuration Manager
*  Obsługa kolumn tabeli dla tabelarycznych źródeł danych dla Azure Data Factory
*  Obsługa usługi Azure Synapse Analytics dla Azure Data Factory
*  Obsługa reclusive w BlobSource i FileSource dla Azure Data Factory
*  Obsługa CopyBehavior — MergeFiles, PreserveHierarchy i FlattenHierarchy in wartość blobsink i FileSink z kopią binarną dla Azure Data Factory
*  Obsługa postępu raportowania działania kopiowania dla Azure Data Factory
*  Obsługa weryfikacji łączności ze źródłem danych dla Azure Data Factory
*  Poprawki błędów

### <a name="1656721"></a>1.6.5672.1

*  Nazwa tabeli obsługi dla źródła danych ODBC dla Azure Data Factory
*  Usprawnienia wydajności
*  Poprawki błędów

### <a name="1656581"></a>1.6.5658.1

*  Obsługa ujścia plików dla Azure Data Factory
*  Obsługa zachowywania hierarchii w kopii binarnej dla Azure Data Factory
*  Obsługa idempotentności działania kopiowania dla Azure Data Factory
*  Poprawki błędów

### <a name="1656401"></a>1.6.5640.1

*  Obsługa 3 dodatkowych źródeł danych dla Azure Data Factory (ODBC, OData, HDFS)
*  Obsługa znaku cudzysłowu w analizatorze CSV dla Azure Data Factory
*  Obsługa kompresji (BZip2)
*  Poprawki błędów

### <a name="1556121"></a>1.5.5612.1

*  Obsługa pięciu relacyjnych baz danych dla Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata i Sybase)
*  Obsługa kompresji (gzip i Wklęśnięcie)
*  Usprawnienia wydajności
*  Poprawki błędów

### <a name="1455491"></a>1.4.5549.1

*  Dodaj obsługę programu Oracle Data Source dla Azure Data Factory
*  Usprawnienia wydajności
*  Poprawki błędów

### <a name="1454921"></a>1.4.5492.1

*  Ujednolicone dane binarne obsługujące Data Factory Microsoft Azure i usługi Power BI Office 365
*  Udoskonalanie interfejsu użytkownika konfiguracji i procesu rejestracji
*  Azure Data Factory — obsługa ruchu przychodzącego i wychodzącego platformy Azure dla SQL Server źródła danych

### <a name="1253031"></a>1.2.5303.1

*  Rozwiąż problem z limitem czasu, aby obsłużyć więcej czasochłonnych połączeń ze źródłami danych.

### <a name="1155268"></a>1.1.5526.8

*  Wymaga .NET Framework 4.5.1 jako warunek wstępny podczas instalacji.

### <a name="1051442"></a>1.0.5144.2

*  Brak zmian, które mają wpływ na Azure Data Factorye scenariusze.
