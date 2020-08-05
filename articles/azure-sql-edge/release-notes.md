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
ms.date: 07/27/2020
ms.openlocfilehash: 74e9772ada010d79e81ef36cae89ba586db73077
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553414"
---
# <a name="azure-sql-edge-release-notes"></a>Informacje o wersji usługi Azure SQL Edge 

W tym artykule opisano nowości i zmiany, które zostały zmienione wraz z każdą nową kompilacją usługi Azure SQL Edge.

## <a name="ctp-22"></a>CTP 2,2
### <a name="sql-engine-build-number---15020001546"></a>Numer kompilacji aparatu SQL — 15.0.2000.1546
### <a name="fixes"></a>Poprawki
1. Obsługa kontenerów innych niż główne 
2. Obsługa zbierania danych użycia i diagnostyki 
3. Aktualizacje przesyłania strumieniowego T-SQL
   - Obsługa znaków Unicode dla nazw obiektów strumienia
   - Ulepszenia oczyszczania procesów
   - Ulepszenia rejestrowania i diagnostyki
4. Poprawa wydajności na potrzeby pozyskiwania danych

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
