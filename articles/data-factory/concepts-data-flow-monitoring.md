---
title: Monitorowanie wizualne przepływu danych mapowania
description: Jak wizualnie monitorować przepływy danych usługi Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: dea0f9a038958ea747147a179020545f2f6922a2
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605342"
---
# <a name="monitor-data-flows"></a>Monitorowanie przepływów danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po zakończeniu tworzenia i debugowania przepływu danych, należy zaplanować przepływ danych do wykonania zgodnie z harmonogramem w kontekście potoku. Potoku można zaplanować z usługi Azure Data Factory przy użyciu wyzwalaczy. Lub można użyć trigger now opcji z usługi Azure Data Factory Pipeline Builder do wykonania jednoestrunia do testowania przepływu danych w kontekście potoku.

Podczas wykonywania potoku, będzie można monitorować potoku i wszystkie działania zawarte w potoku, w tym działania przepływu danych. Kliknij ikonę monitora w panelu interfejsu użytkownika usługi Azure Factory po lewej stronie. Zobaczysz ekran podobny do poniższego. Wyróżnione ikony umożliwiają przechodzenie do szczegółów działań w potoku, w tym działania przepływu danych.

![Monitorowanie przepływu danych](media/data-flow/mon001.png "Monitorowanie przepływu danych")

Zobaczysz statystyki na tym poziomie, a także czasy i stan. Identyfikator uruchomienia na poziomie działania różni się od identyfikatora uruchom na poziomie potoku. Identyfikator uruchomienia na poprzednim poziomie jest dla potoku. Kliknięcie okularów daje szczegółowe informacje na temat wykonywania przepływu danych.

![Monitorowanie przepływu danych](media/data-flow/mon002.png "Monitorowanie przepływu danych")

Gdy znajdujesz się w widoku monitorowania węzła graficznego, zostanie wyświetlena uproszczona wersja wykresu przepływu danych tylko do widoku.

![Monitorowanie przepływu danych](media/data-flow/mon003.png "Monitorowanie przepływu danych")

## <a name="view-data-flow-execution-plans"></a>Wyświetlanie planów wykonania przepływu danych

Gdy przepływ danych jest wykonywany w programie Spark, usługa Azure Data Factory określa optymalne ścieżki kodu na podstawie całego przepływu danych. Ponadto ścieżki wykonywania mogą występować w różnych węzłach skalowanych w poziomie i partycjach danych. W związku z tym wykres monitorowania reprezentuje projekt przepływu, biorąc pod uwagę ścieżkę wykonywania przekształceń. Po kliknięciu na poszczególnych węzłów, zobaczysz "grupowania", które reprezentują kod, który został wykonany razem w klastrze. Czasy i liczby, które widzisz reprezentują te grupy, w przeciwieństwie do poszczególnych kroków w projekcie.

![Monitorowanie przepływu danych](media/data-flow/mon004.png "Monitorowanie przepływu danych")

* Po kliknięciu na otwartej przestrzeni w oknie monitorowania, statystyki w dolnym okienku będą wyświetlać czas i liczba wierszy dla każdego zlewu i przekształcenia, które doprowadziły do danych ujścia dla linii transformacji.

* Po wybraniu poszczególnych przekształceń, otrzymasz dodatkowe informacje zwrotne na panelu po prawej stronie, który pokazuje statystyki partycji, liczby kolumn, skośność (jak równomiernie jest dane rozproszone na partycjach) i kurtozy (jak kolczaste jest dane).

* Po kliknięciu na Sink w widoku węzła, zobaczysz linii kolumn. Istnieją trzy różne metody, które kolumny są gromadzone w całym przepływie danych do lądowania w zlewie. Oto one:

  * Obliczone: Kolumna służy do przetwarzania warunkowego lub w ramach wyrażenia w przepływie danych, ale nie wyląduj jej w zlewie
  * Pochodne: Kolumna jest nową kolumną, która została wygenerowana w przepływie, tj.
  * Mapowane: Kolumna pochodzi ze źródła i mapuje ją na pole ujścia
  * Stan przepływu danych: bieżący stan wykonania
  * Czas uruchamiania klastra: czas uzyskania środowiska obliczeniowego JIT Spark dla wykonywania przepływu danych
  * Liczba przekształceń: Ile kroków transformacji jest wykonywanych w przepływie
  
![Monitorowanie przepływu danych](media/data-flow/monitornew.png "Nowe monitorowanie przepływu danych")  
  
## <a name="monitor-icons"></a>Ikony monitora

Ta ikona oznacza, że dane transformacji zostały już zapisane w pamięci podręcznej klastra, więc czasy i ścieżka wykonywania zostały uwzględnione:

![Monitorowanie przepływu danych](media/data-flow/mon004.png "Monitorowanie przepływu danych")

Zobaczysz również zielone ikony kół w transformacji. Reprezentują one liczbę pochłaniaczy, do których przepływają dane.
