---
title: Azure Functions dane wyjściowe z Azure Stream Analytics
description: W tym artykule opisano usługę Azure Functions jako dane wyjściowe dla Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e5ea7a1abbbd6ab4be32955179227fbd539cf641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019622"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Functions dane wyjściowe z Azure Stream Analytics

Azure Functions to bezserwerowa usługa obliczeniowa, która umożliwia uruchamianie kodu na żądanie bez konieczności jawnego udostępniania infrastruktury ani zarządzania nią. Umożliwia zaimplementowanie kodu wyzwalanego przez zdarzenia występujące na platformie Azure lub w usługach partnerskich. Azure Functions odpowiedzi na wyzwalacze są naturalnymi danymi wyjściowymi Azure Stream Analytics. Ta karta wyjściowa umożliwia użytkownikom łączenie Stream Analytics z Azure Functions i uruchamianie skryptu lub fragmentu kodu w odpowiedzi na różne zdarzenia.

Azure Functions dane wyjściowe z Stream Analytics nie są dostępne w regionach platformy Azure w Chinach (Chiny) i Azure (Niemcy). Połączenie z Azure Functions wewnątrz sieci wirtualnej (VNet) z zadania Stream Analytics, które działa w klastrze z wieloma dzierżawcami, również nie jest obsługiwane.

Azure Stream Analytics wywołuje Azure Functions za pośrednictwem wyzwalacza HTTP. Karta wyjściowa Azure Functions jest dostępna z następującymi konfigurowalnymi właściwościami:

| Nazwa właściwości | Opis |
| --- | --- |
| Aplikacja funkcji |Nazwa aplikacji Azure Functions. |
| Funkcja |Nazwa funkcji w aplikacji Azure Functions. |
| Klucz |Jeśli chcesz użyć funkcji platformy Azure z innej subskrypcji, możesz to zrobić, podając klucz, aby uzyskać dostęp do funkcji. |
| Maksymalny rozmiar wsadu |Właściwość, która umożliwia ustawienie maksymalnego rozmiaru dla każdej partii wyjściowej, która jest wysyłana do funkcji platformy Azure. Jednostka wejściowa jest w bajtach. Wartość domyślna to 262 144 bajtów (256 KB). |
| Maksymalna liczba partii  |Właściwość, która umożliwia określenie maksymalnej liczby zdarzeń w każdej partii, która jest wysyłana do Azure Functions. Wartość domyślna to 100. |

Azure Stream Analytics oczekuje stanu HTTP 200 z aplikacji Functions dla partii, które zostały przetworzone pomyślnie.

Gdy Azure Stream Analytics otrzymuje wyjątek 413 ("jednostka żądania HTTP zbyt duża") z funkcji platformy Azure, zmniejsza rozmiar partii wysyłanych do Azure Functions. W kodzie funkcji platformy Azure Użyj tego wyjątku, aby upewnić się, że Azure Stream Analytics nie wysyła zbyt dużych partii. Należy również upewnić się, że maksymalna liczba partii i wartości rozmiaru używane w funkcji są zgodne z wartościami wprowadzonymi w portalu Stream Analytics.

> [!NOTE]
> Podczas połączenia testowego Stream Analytics wysyła pustą partię do Azure Functions, aby przetestować, czy połączenie między nimi działa. Upewnij się, że aplikacja Functions obsługuje puste żądania usługi Batch, aby upewnić się, że połączenie testowe przebiega pomyślnie.

Ponadto, w sytuacji, gdy w przedziale czasowym nie ma wypełniania zdarzeń, nie jest generowane żadne wyjście. W związku z tym funkcja **computeResult** nie jest wywoływana. To zachowanie jest spójne z wbudowaną funkcją agregującą w oknie.

## <a name="partitioning"></a>Partycjonowanie

Klucz partycji jest oparty na klauzuli PARTITION BY w zapytaniu. Liczba składników zapisywania danych wyjściowych następuje po partycjonowaniu danych wejściowych dla w [pełni równoległych zapytań](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

Domyślny rozmiar wsadu to 262 144 bajtów (256 KB). Domyślna liczba zdarzeń na partię to 100. Rozmiar wsadu można skonfigurować i zwiększyć lub zmniejszyć w opcjach danych wyjściowych Stream Analytics.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-azure-cli.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu szablonu ARM](quick-create-azure-resource-manager.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics w programie Visual Studio Code](quick-create-visual-studio-code.md)