---
title: Azure Cosmos DB dane wyjściowe z Azure Stream Analytics
description: W tym artykule opisano sposób wyprowadzania danych z Azure Stream Analytics do Azure Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e322135cfdb7aaff331367e84c603e8344436528
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906263"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>Azure Cosmos DB dane wyjściowe z Azure Stream Analytics

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) to globalnie dystrybuowana usługa bazy danych, która oferuje nieograniczoną skalę elastyczną wokół świata, zaawansowanego zapytania i automatycznego indeksowania dla modeli danych niezależny od schematu. Aby dowiedzieć się więcej o Azure Cosmos DB opcjach kontenera Stream Analytics, zobacz artykuł [Stream Analytics with Azure Cosmos dB jako dane wyjściowe](stream-analytics-documentdb-output.md) .

Azure Cosmos DB dane wyjściowe z Stream Analytics nie są obecnie dostępne w regionach platformy Azure w Chinach (Chiny) i Azure (Niemcy).

> [!Note]
> Azure Stream Analytics obsługuje tylko połączenie z Azure Cosmos DB przy użyciu interfejsu API SQL.
> Inne interfejsy API Azure Cosmos DB nie są jeszcze obsługiwane. Jeśli użytkownik wskaże Azure Stream Analytics kont Azure Cosmos DB utworzonych z innymi interfejsami API, dane mogą nie być prawidłowo przechowywane.

W poniższej tabeli opisano właściwości tworzenia Azure Cosmos DB danych wyjściowych.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Alias do odwoływania danych wyjściowych w kwerendzie Stream Analytics. |
| Ujście | Azure Cosmos DB. |
| Opcja importu | Wybierz opcję **wybierz Cosmos DB z subskrypcji** lub **podaj ręcznie ustawienia Cosmos DB**.
| Identyfikator konta | Nazwa lub identyfikator URI punktu końcowego konta Azure Cosmos DB. |
| Klucz konta | Współużytkowany klucz dostępu dla konta Azure Cosmos DB. |
| baza danych | Nazwa bazy danych Azure Cosmos DB. |
| Nazwa kontenera | Nazwa kontenera do użycia, która musi istnieć w Cosmos DB. Przykład:  <br /><ul><li> Obiekt _Webcontainerer_: kontener o nazwie "The containerer" musi istnieć.</li>|
| Identyfikator dokumentu |Opcjonalny. Nazwa pola w zdarzeniach wyjściowych, które służy do określania klucza podstawowego, na którym bazują operacje wstawiania lub aktualizacji.

## <a name="partitioning"></a>Partycjonowanie

Klucz partycji jest oparty na klauzuli PARTITION BY w zapytaniu. Liczba składników zapisywania danych wyjściowych następuje po partycjonowaniu danych wejściowych dla w [pełni równoległych zapytań](stream-analytics-scale-jobs.md). Stream Analytics konwertuje klucz partycji wyjściowej Cosmos DB na ciąg. Na przykład jeśli masz klucz partycji o wartości 1 typu bigint, jest on konwertowany na "1" typu ciąg.

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

Maksymalny rozmiar komunikatu znajduje się w temacie [Azure Cosmos DB limitów](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits). Rozmiar partii i częstotliwość zapisu są dostosowywane dynamicznie na podstawie Azure Cosmos DB odpowiedzi. Nie ma żadnych wstępnie zdefiniowanych ograniczeń z Stream Analytics.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-azure-cli.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu szablonu ARM](quick-create-azure-resource-manager.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics w programie Visual Studio Code](quick-create-visual-studio-code.md)
