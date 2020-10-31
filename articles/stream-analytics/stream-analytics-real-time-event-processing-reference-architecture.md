---
title: Przetwarzanie zdarzeń w czasie rzeczywistym przy użyciu Azure Stream Analytics
description: W tym artykule opisano architekturę referencyjną do osiągnięcia przetwarzania zdarzeń w czasie rzeczywistym i analizy przy użyciu Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 0aa489e1732a8be564b3cefe1ed74d7aaacb1456
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124428"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architektura referencyjna: przetwarzanie zdarzeń w czasie rzeczywistym za pomocą Microsoft Azure Stream Analytics
Architektura referencyjna dla przetwarzania zdarzeń w czasie rzeczywistym z Azure Stream Analytics ma na celu zapewnienie ogólnego planu wdrażania rozwiązania do przetwarzania strumieniowego platformy jako usługi (PaaS) w czasie rzeczywistym z Microsoft Azure.

## <a name="summary"></a>Podsumowanie
Tradycyjnie rozwiązania analityczne zostały oparte na funkcjach, takich jak ETL (wyodrębnianie, przekształcanie, ładowanie) i magazynowanie danych, gdzie dane są przechowywane przed analizą. Zmiana wymagań, w tym szybszego przybycia danych, powoduje wypychanie tego istniejącego modelu do limitu. Możliwość analizowania danych w obrębie przenoszonych strumieni przed magazynem to jedno rozwiązanie, a chociaż nie jest to nowa funkcja, podejście nie zostało szeroko przyjęte we wszystkich branżach pionowych. 

Microsoft Azure udostępnia obszerny katalog technologii analitycznych, które mogą obsługiwać tablicę różnych scenariuszy i wymagań rozwiązań. Wybór usług platformy Azure, które mają zostać wdrożone na potrzeby kompleksowego rozwiązania, może być wyzwaniem z uwzględnieniem szerokiej oferty. Ten dokument jest przeznaczony do opisywania możliwości i współdziałania różnych usług platformy Azure, które obsługują rozwiązanie przesyłania strumieniowego zdarzeń. Wyjaśniono również niektóre scenariusze, w których klienci mogą korzystać z tego typu podejścia.

## <a name="contents"></a>Zawartość
* Streszczenie
* Wprowadzenie do analizy Real-Time
* Propozycja wartości Real-Time danych na platformie Azure
* Typowe scenariusze analizy Real-Time
* Architektura i składniki
  * Źródła danych
  * Warstwa Data-Integration
  * Warstwa analizy w czasie rzeczywistym
  * Warstwa magazynowania danych
  * Warstwa prezentacji/zużycia
* Podsumowanie

**Autor:** Charles Feddersen, architekt rozwiązań, centrum danych usługi Insights, firma Microsoft Corporation

**Opublikowano:** Styczeń 2015

**Poprawka:** 1,0

**Pobieranie:** [przetwarzanie zdarzeń w czasie rzeczywistym za pomocą Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Uzyskaj pomoc
Aby uzyskać dalszą pomoc, wypróbuj&stronie pytań i odpowiedzi [dla Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)