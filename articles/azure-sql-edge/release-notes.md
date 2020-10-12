---
title: Informacje o wersji usługi Azure SQL Edge
description: Informacje o wersji zawierające szczegółowe informacje o nowościach i zmianach w obrazach programu Azure SQL Edge
keywords: Informacje o wersji — Microsoft Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: afd78acadf133a9f128eec402eba9d0eed51b8e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91284486"
---
# <a name="azure-sql-edge-release-notes"></a>Informacje o wersji usługi Azure SQL Edge 

W tym artykule opisano nowości i zmiany, które zostały zmienione wraz z każdą nową kompilacją usługi Azure SQL Edge.

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge — 1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001552"></a>Numer kompilacji aparatu SQL — 15.0.2000.1552

### <a name="whats-new"></a>Co nowego?
1. Ubuntu 18,04 na podstawie obrazów kontenerów. 
2. Obsługa `IGNORE NULL` składni i `RESPECT NULL` funkcji oraz `LAST_VALUE()` `FIRST_VALUE()` . 
3. Udoskonalenia niezawodności dla przewidywania za pomocą ONNX.
4. Obsługa oczyszczania opartych na zasadach przechowywania danych.
   - Obsługa buforu pierścieniowego dla zadania oczyszczania przechowywania w celu rozwiązywania problemów.
5. Obsługa nowych funkcji 
   - Szybkie odzyskiwanie
   - Autodostrajanie zapytań
   - Włącz równoległe scenariusze wykonywania
6. Udoskonalenia oszczędzania energii dla trybu niskiego zużycia energii
7. Obsługa przesyłania strumieniowego nowych funkcji 
   - [Okna migawek](https://docs.microsoft.com/stream-analytics-query/snapshot-window-azure-stream-analytics) : nowe okno typu pozwalające na grupowanie zdarzeń w tym samym czasie. 
   - Włącz [TopOne](https://docs.microsoft.com/stream-analytics-query/topone-azure-stream-analytics) i [CollectTop](https://docs.microsoft.com/stream-analytics-query/collecttop-azure-stream-analytics) jako funkcję analityczną, dzięki czemu będzie można zwracać rekordy uporządkowane według wybranej kolumny, bez konieczności części okna. 
   - Ulepszenia [MATCH_RECOGNIZE](https://docs.microsoft.com/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Poprawki
1. Dodatkowe komunikaty o błędach i szczegółowe informacje dotyczące rozwiązywania problemów z operacjami TSQL Streaming. 
2. Ulepszenia w celu zachowania czasu pracy baterii w trybie bezczynności. 
3. Poprawki aparatu przesyłania strumieniowego TSQL: 
   - Czyszczenie dla zatrzymywanego zadania przesyłania strumieniowego 
   - Poprawki dotyczące poprawy obsługi lokalizacji i formatu Unicode
   - Zwiększ możliwość debugowania dla programu Edge TSQL — przesyłanie strumieniowe, zezwól użytkownikom na wykonywanie zapytań o błędy błędów zadań z get_streaming_job.
4. Oczyszczanie oparte na zasadach przechowywania danych
   - Poprawki dotyczące scenariuszy tworzenia i oczyszczania zasad przechowywania.
5. Poprawki dotyczące zadań czasomierza w tle w celu zwiększenia oszczędności w trybie niskiego zużycia baterii.

### <a name="known-issues"></a>Znane problemy 
1. Nie można użyć funkcji T-SQL Date_Bucket w kolumnie obliczanej.


## <a name="ctp-23"></a>CTP 2,3
### <a name="sql-engine-build-number---15020001549"></a>Numer kompilacji aparatu SQL — 15.0.2000.1549
### <a name="whats-new"></a>Co nowego?
1. Obsługa niestandardowych źródeł w funkcji Date_Bucket (). 
2. Obsługa plików BacPac w ramach wdrożenia programu SQL Server.
3. Obsługa oczyszczania opartych na zasadach przechowywania danych.      
   - Obsługa języka DDL na potrzeby włączania zasad przechowywania 
   - Wyczyść procedury składowane i zadanie oczyszczania w tle
   - Rozszerzone zdarzenia do monitorowania zadań oczyszczania

### <a name="fixes"></a>Poprawki
1. Dodatkowe komunikaty o błędach i szczegółowe informacje dotyczące rozwiązywania problemów z operacjami TSQL Streaming. 
2. Ulepszenia w celu zachowania czasu pracy baterii w trybie bezczynności. 
3. Poprawki aparatu przesyłania strumieniowego TSQL: 
   - Problem z zablokowanym znakiem wodnym przy użyciu podstrumienia okna przeskoku 
   - Napraw obsługę wyjątków platformy, aby upewnić się, że jest ona zbierana jako błąd z możliwością wykonania przez użytkownika


## <a name="ctp-22"></a>CTP 2,2
### <a name="sql-engine-build-number---15020001546"></a>Numer kompilacji aparatu SQL — 15.0.2000.1546
### <a name="whats-new"></a>Co nowego?
1. Obsługa kontenerów innych niż główne 
2. Obsługa zbierania danych użycia i diagnostyki 
3. Aktualizacje przesyłania strumieniowego T-SQL
   - Obsługa znaków Unicode dla nazw obiektów strumienia

### <a name="fixes"></a>Poprawki
1. Aktualizacje przesyłania strumieniowego T-SQL
   - Ulepszenia oczyszczania procesów
   - Ulepszenia rejestrowania i diagnostyki
2. Poprawa wydajności na potrzeby pozyskiwania danych

## <a name="ctp-21"></a>CTP 2,1 
### <a name="sql-engine-build-number---15020001545"></a>Numer kompilacji aparatu SQL — 15.0.2000.1545
### <a name="fixes"></a>Poprawki
1. Popraw PROGNOZowanie dzięki modelom ONNX, aby obsłużyć problem CPUID w usłudze ARM 
2. Popraw, aby zwiększyć obsługę ścieżki niepowodzeń podczas uruchamiania TSQL Streaming 
3. W przypadku braku danych Popraw wartość opóźnienia wartości znaku wodnego w metrykach zadania. 
4. Usuń problem z adapterem wyjściowym, gdy karta zawiera zmienne schemat między partiami.  

## <a name="ctp-20"></a>CTP 2,0 
### <a name="sql-engine-build-number---15020001401"></a>Numer kompilacji aparatu SQL — 15.0.2000.1401
### <a name="whats-new"></a>Co nowego?
1.  Nazwa produktu została zaktualizowana do "Azure SQL Edge"
1.  Funkcja Date_bucket

    i.  Obsługa daty, godziny, typu DateTime
3.  PRZEWIDYWANie przy użyciu ONNX
    
    i.  Parametr środowiska uruchomieniowego wymagany przez ONNX 
    
4.  Obsługa przesyłania strumieniowego TSQL (ograniczona wersja zapoznawcza) 
 
### <a name="known-issues"></a>Znane problemy

1. <b>Problem:</b> Potencjalne błędy podczas stosowania dacpac przy uruchamianiu z powodu problemu z chronometrażem.

    <b>Obejście problemu:</b> Ponowne uruchomienie SQL Server lub kontenera spowoduje ponowne zastosowanie dacpac i rozwiązanie problemu
### <a name="request-support"></a>Żądaj obsługi
1. Możesz poprosić o pomoc techniczną na [stronie pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

4. Upewnij się, że wybrane są następujące pola: 
    * Typ problemu — techniczne 
    * Usługa — IoT Edge
    * Typ problemu — mój problem odnosi się do modułu IoT Edge
    * Podtyp problemu — Azure SQL Edge

   ![Przykładowy bilet pomocy technicznej](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>Numer kompilacji aparatu SQL — 15.0.2000.1331
### <a name="whats-new"></a>Co nowego?
1. Funkcja Date_bucket
    
    i. Obsługa typu DateTimeOffset
2. PRZEWIDYWANie przy użyciu modeli ONNX

    i. Obsługa nvarchar
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>Numer kompilacji aparatu SQL — 15.0.2000.1247
### <a name="whats-new"></a>Co nowego?
1.  PRZEWIDYWANie przy użyciu modeli ONNX
 
    i.  Obsługa varchar
    
    ii. Migracja do środowiska uruchomieniowego ONNX w wersji 1,0 
2.  Obsługa funkcji — włączone są następujące funkcje:

    i.   Obsługa przechwytywania zmian

    ii.  Tabela historii z kompresją

    iii. Wyższy współczynnik skalowania do odczytu dziennika w przód

    iv.  Przekazywanie filtru w trybie wsadowym

    v.   Optymalizacje z wyprzedzeniem
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>Numer kompilacji aparatu SQL — 15.0.2000.1147
### <a name="whats-new"></a>Co nowego?
1. Wdrożenie portalu Azure IOT 

    i.   Obsługa wdrażania obrazów AMD64 i ARM

    ii.  Obsługa tworzenia zadań przesyłania strumieniowego

    iii. Wdrożenie dacpac
2. PRZEWIDYWANie przy użyciu modeli ONNX

    i. Obsługa typu liczbowego
3. Obsługa funkcji — włączone są następujące funkcje:

    i.  Przekazywanie agregacji do magazynu kolumn

    ii. Skany z przechodzeniem do rundy
4. Zużycie i zmniejszenie zużycia pamięci
