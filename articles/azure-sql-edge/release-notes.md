---
title: Informacje o wersji usługi Azure SQL Edge
description: Informacje o wersji opisujące nowości lub zmiany w obrazach programu Azure SQL Edge.
keywords: Informacje o wersji — Microsoft Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: c18cb1cef9b46b4b3cd2f614945f98abf9de7c58
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704354"
---
# <a name="azure-sql-edge-release-notes"></a>Informacje o wersji usługi Azure SQL Edge 

W tym artykule opisano nowości i zmiany, które zostały zmienione wraz z każdą nową kompilacją usługi Azure SQL Edge.

## <a name="azure-sql-edge-101"></a>Usługa Azure SQL Edge 1.0.1

15.0.2000.1553 kompilacji aparatu SQL

### <a name="whats-new"></a>Co nowego?

- Zezwalaj na wyrażenia Date_Bucket zdefiniowane w kolumnach obliczanych.

### <a name="fixes"></a>Poprawki

- Poprawka zasad przechowywania dla porzucania tabeli z włączonymi zasadami przechowywania z nieograniczonym limitem czasu
- Obsługa wdrażania DacFx na potrzeby funkcji przesyłania strumieniowego i funkcji przechowywania 
- DacFx wdrożenia w celu włączenia wdrożenia z folderu zagnieżdżonego w adresie URL sygnatury dostępu współdzielonego 
- PROGNOZowanie poprawek w celu obsługi długich nazw kolumn w komunikatach o błędach

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

15.0.2000.1552 kompilacji aparatu SQL

### <a name="whats-new"></a>Co nowego?
- Obrazy kontenerów oparte na Ubuntu 18,04 
- Obsługa `IGNORE NULL` składni i `RESPECT NULL` `LAST_VALUE()` `FIRST_VALUE()` funkcji i 
- Udoskonalenia niezawodności dla przewidywania za pomocą ONNX
- Obsługa oczyszczania na podstawie zasad przechowywania danych:
   - Obsługa buforu pierścieniowego dla zadania oczyszczania przechowywania w celu rozwiązywania problemów
- Obsługa nowych funkcji: 
   - Szybkie odzyskiwanie
   - Automatyczne dostrajanie zapytań
   - Scenariusze wykonywania równoległego
- Ulepszenia oszczędzania energii dla trybu niskiego zużycia energii
- Obsługa przesyłania strumieniowego nowych funkcji: 
   - Okna [migawek](/stream-analytics-query/snapshot-window-azure-stream-analytics): nowy typ okna umożliwia grupowanie zdarzeń, które docierają w tym samym czasie.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) i [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) można włączyć jako funkcje analityczne. Można zwrócić rekordy uporządkowane według wybranej kolumny. Nie muszą one być częścią okna. 
   - Ulepszenia [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Poprawki
- Dodatkowe komunikaty o błędach i szczegółowe informacje dotyczące rozwiązywania problemów z operacjami przesyłania strumieniowego T-SQL 
- Udoskonalenia zachowywania czasu pracy baterii w trybie bezczynności 
- Poprawki aparatu przesyłania strumieniowego T-SQL: 
   - Czyszczenie dla zatrzymanych zadań przesyłania strumieniowego 
   - Poprawki dotyczące lokalizacji 
   - Ulepszona obsługa formatu Unicode 
   - Ulepszone debugowanie dla programu SQL Edge T-SQL streaming, co pozwala użytkownikom na wykonywanie zapytań o błędy niepowodzeń zadań z get_streaming_job
- Czyszczenie na podstawie zasad przechowywania danych: 
    - Poprawki dotyczące scenariuszy tworzenia i oczyszczania zasad
- Poprawki dla zadań czasomierza w tle w celu zwiększenia oszczędności w trybie niskiego zużycia mocy

### <a name="known-issues"></a>Znane problemy 
- Nie można użyć Date_Bucket funkcji T-SQL w kolumnie obliczanej.


## <a name="ctp-23"></a>CTP 2,3
15.0.2000.1549 kompilacji aparatu SQL
### <a name="whats-new"></a>Co nowego?
- Obsługa niestandardowych źródeł w funkcji Date_Bucket () 
- Obsługa plików BACPAC w ramach wdrożenia programu SQL
- Obsługa oczyszczania na podstawie zasad przechowywania danych:      
   - Obsługa języka DDL na potrzeby włączania zasad przechowywania 
   - Czyszczenie procedur składowanych i zadania oczyszczania w tle
   - Rozszerzone zdarzenia do monitorowania zadań oczyszczania

### <a name="fixes"></a>Poprawki
- Dodatkowe komunikaty o błędach i szczegółowe informacje dotyczące rozwiązywania problemów z operacjami przesyłania strumieniowego T-SQL 
- Udoskonalenia zachowywania czasu pracy baterii w trybie bezczynności 
- Aparat przesyłania strumieniowego T-SQL: 
   - Poprawka dla zablokowanego znaku wodnego w podstrumieńowym oknie przeskoku 
   - Poprawka dla obsługi wyjątków platformy, aby upewnić się, że jest ona zbierana jako błąd z możliwością działania użytkownika


## <a name="ctp-22"></a>CTP 2,2
15.0.2000.1546 kompilacji aparatu SQL
### <a name="whats-new"></a>Co nowego?
- Obsługa kontenerów niegłównych 
- Obsługa zbierania danych użycia i diagnostyki 
- Aktualizacje przesyłania strumieniowego T-SQL:
   - Obsługa znaków Unicode dla nazw obiektów strumienia

### <a name="fixes"></a>Poprawki
- Aktualizacje przesyłania strumieniowego T-SQL:
   - Ulepszenia oczyszczania procesów
   - Ulepszenia rejestrowania i diagnostyki
- Poprawa wydajności na potrzeby pozyskiwania danych

## <a name="ctp-21"></a>CTP 2,1 
15.0.2000.1545 kompilacji aparatu SQL
### <a name="fixes"></a>Poprawki
- Dozwolone modele PREDYKCYJNe-with-ONNX do obsługi problemu CPUID w usłudze ARM 
- Ulepszona obsługa ścieżki niepowodzeń podczas uruchamiania przesyłania strumieniowego T-SQL
- Skorygowana wartość opóźnienia znaku wodnego w metrykach zadania, gdy nie ma żadnych danych.
- Rozwiązanie problemu z kartą wyjściową, gdy karta zawiera schemat zmiennej między partiami  

## <a name="ctp-20"></a>CTP 2,0 
15.0.2000.1401 kompilacji aparatu SQL
### <a name="whats-new"></a>Co nowego?
-   Nazwa produktu została zaktualizowana do *usługi Azure SQL Edge*
-  Funkcja Date_Bucket:
    - Obsługa typów dat, godzin i DateTime
- PRZEWIDYWANie przy użyciu ONNX:
    - ONNX wymagania dotyczące parametru środowiska uruchomieniowego  
- Obsługa przesyłania strumieniowego T-SQL (ograniczona wersja zapoznawcza) 
 
### <a name="known-issues"></a>Znane problemy

- Problem: potencjalne błędy przy zastosowaniu DACPAC podczas uruchamiania z powodu problemu z chronometrażem.
- Obejście: Uruchom ponownie SQL Server. W przeciwnym razie kontener podejmie ponowną próbę zastosowania DACPAC.

### <a name="request-support"></a>Żądaj obsługi
Na [stronie pomocy](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)technicznej możesz poprosić o pomoc techniczną. Wybierz następujące pola: 
- **Typ wydania**: *Techniczne* 
- **Usługa**: *IoT Edge*
- **Typ problemu**: *mój problem odnosi się do modułu IoT Edge*
- **Podtyp problemu**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Zrzut ekranu przedstawiający przykładowy bilet pomocy technicznej.":::

## <a name="ctp-15"></a>CTP 1,5
15.0.2000.1331 kompilacji aparatu SQL
### <a name="whats-new"></a>Co nowego?
- Funkcja Date_Bucket:
    - Obsługa typu DateTimeOffset
- PRZEWIDYWANie przy użyciu modeli ONNX:
  - Obsługa NVARCHAR
 
## <a name="ctp-14"></a>CTP 1,4
15.0.2000.1247 kompilacji aparatu SQL
### <a name="whats-new"></a>Co nowego?
-   PRZEWIDYWANie przy użyciu modeli ONNX:
    - Obsługa VARCHAR
    - Migracja do środowiska uruchomieniowego ONNX w wersji 1,0 

- Dostępne są następujące funkcje:
  - Obsługa przechwytywania zmian
  - Tabela historii z kompresją
  - Współczynnik wyższego poziomu do odczytu dziennika
  - Przekazywanie filtru w trybie wsadowym
  - Optymalizacje z wyprzedzeniem
 
## <a name="ctp-13"></a>CTP 1,3
15.0.2000.1147 kompilacji aparatu SQL
### <a name="whats-new"></a>Co nowego?
- Wdrożenie portalu Azure IoT: 
    - Obsługa wdrażania obrazów AMD64 i ARM
    - Obsługa tworzenia zadań przesyłania strumieniowego
    - Wdrożenie DACPAC
- PRZEWIDYWANie przy użyciu modeli ONNX:
    - Obsługa typu liczbowego
- Dostępne są następujące funkcje:
    - Przekazywanie agregacji do magazynu kolumn
    - Skany z przechodzeniem do rundy
- Ilość pracy i zmniejszenie zużycia pamięci
