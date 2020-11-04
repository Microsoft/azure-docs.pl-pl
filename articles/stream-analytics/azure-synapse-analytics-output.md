---
title: Dane wyjściowe usługi Azure Synapse Analytics z Azure Stream Analytics
description: W tym artykule opisano usługę Azure Synapse Analytics jako dane wyjściowe dla Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 0b3bec9c4d4476b95279e35953ff89177f4488d4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305832"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Dane wyjściowe usługi Azure Synapse Analytics z Azure Stream Analytics

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (dawniej SQL Data Warehouse) to nieograniczona Usługa analityczna, która łączy magazyn danych w przedsiębiorstwie i analizę danych Big Data. 

Zadania Azure Stream Analytics mogą być wyprowadzane do dedykowanej tabeli puli SQL w usłudze Azure Synapse Analytics i mogą przetwarzać stawki przepływności do 200 MB/s. Obsługuje to najbardziej wymagające analiz w czasie rzeczywistym i wymaga przetwarzania danych na gorąco w przypadku obciążeń, takich jak raportowanie i pulpit nawigacyjny.  

Aby można było dodać dane wyjściowe do zadania Stream Analytics, musi istnieć dedykowana tabela puli SQL. Schemat tabeli musi pasować do pól i ich typów w danych wyjściowych zadania. 

Aby korzystać z usługi Azure Synapse jako danych wyjściowych, należy upewnić się, że konto magazynu zostało skonfigurowane. Przejdź do ustawień konta magazynu, aby skonfigurować konto magazynu. Dozwolone są tylko typy kont magazynu obsługujące tabele: ogólnego przeznaczenia w wersji 2 i ogólnego przeznaczenia w wersji 1. Wybierz tylko warstwę Standardowa. Warstwa Premium nie jest obsługiwana.

## <a name="output-configuration"></a>Konfiguracja wyjściowa

W poniższej tabeli wymieniono nazwy właściwości i ich opisy dotyczące tworzenia danych wyjściowych usługi Azure Synapse Analytics.

|Nazwa właściwości|Opis|
|-|-|
|Alias danych wyjściowych |Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do tej bazy danych. |
|baza danych |Nazwa dedykowanej puli SQL, do której wysyłane są dane wyjściowe. |
|Nazwa serwera |Nazwa serwera usługi Azure Synapse.  |
|Nazwa użytkownika |Nazwa użytkownika, która ma dostęp do zapisu w bazie danych. Stream Analytics obsługuje tylko uwierzytelnianie SQL. |
|Hasło |Hasło, aby połączyć się z bazą danych. |
|Tabela  | Nazwa tabeli, w której zapisano dane wyjściowe. W nazwie tabeli jest rozróżniana wielkość liter. Schemat tej tabeli powinien dokładnie pasować do liczby pól i ich typów generowanych przez dane wyjściowe zadania.|

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-azure-cli.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu szablonu ARM](quick-create-azure-resource-manager.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics w programie Visual Studio Code](quick-create-visual-studio-code.md)