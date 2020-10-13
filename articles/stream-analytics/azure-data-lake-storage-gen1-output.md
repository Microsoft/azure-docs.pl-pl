---
title: Azure Data Lake Storage dane wyjściowe generacji 1 z Azure Stream Analytics
description: W tym artykule opisano Azure Data Lake Storage generacji 1 jako opcję wyjściową dla Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 59b4a04231df3b93b093750cc6c9d70982a418a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665348"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure Data Lake Storage dane wyjściowe generacji 1 z Azure Stream Analytics

Stream Analytics obsługuje dane wyjściowe [Azure Data Lake Storage generacji 1](../data-lake-store/data-lake-store-overview.md) . Azure Data Lake Storage to całe repozytorium w całym przedsiębiorstwie na potrzeby obciążeń analitycznych danych Big Data. Za pomocą Data Lake Storage można przechowywać dane dowolnego rozmiaru, typu i szybkość pozyskiwania dla analiz operacyjnych i poznawczych. Stream Analytics musi mieć autoryzację, aby uzyskać dostęp do Data Lake Storage.

Azure Data Lake Storage dane wyjściowe z Stream Analytics nie są dostępne w regionach platformy Azure w Chinach (Chiny) i Azure (Niemcy).

## <a name="output-configuration"></a>Konfiguracja wyjściowa

W poniższej tabeli wymieniono nazwy właściwości i ich opisy w celu skonfigurowania danych wyjściowych Data Lake Storage generacji 1.

| Nazwa właściwości | Opis |
| --- | --- |
| Alias danych wyjściowych | Przyjazna nazwa używana w zapytaniach do kierowania danych wyjściowych zapytania do Data Lake Store. |
| Subskrypcja | Subskrypcja, która zawiera konto Azure Data Lake Storage. |
| Nazwa konta | Nazwa konta Data Lake Store, do którego wysyłane są dane wyjściowe. Zostanie wyświetlona lista rozwijana kont Data Lake Store, które są dostępne w ramach subskrypcji. |
| Wzorzec prefiksu ścieżki | Ścieżka pliku, która jest używana do zapisywania plików w ramach określonego konta Data Lake Store. Można określić jedno lub więcej wystąpień zmiennych {date} i {Time}:<br /><ul><li>Przykład 1: Folder1/dzienniki/{Date}/{Time}</li><li>Przykład 2: Folder1/dzienniki/{Date}</li></ul><br />Sygnatura czasowa utworzonej struktury folderów następuje po UTC, a nie na czas lokalny.<br /><br />Jeśli wzorzec ścieżki pliku nie zawiera końcowego ukośnika (/), ostatni wzorzec w ścieżce pliku jest traktowany jako prefiks nazwy pliku. <br /><br />Nowe pliki są tworzone w następujących okolicznościach:<ul><li>Zmień w schemacie wyjściowym</li><li>Zewnętrzne lub wewnętrzne ponowne uruchomienie zadania</li></ul> |
| Format daty | Opcjonalny. Jeśli token daty jest używany w ścieżce prefiksu, można wybrać format daty, w którym są zorganizowane pliki. Przykład: RRRR/MM/DD |
|Format czasu | Opcjonalny. Jeśli token czasu jest używany w ścieżce prefiksu, określ format czasu, w którym są zorganizowane pliki. Obecnie jedyną obsługiwaną wartością jest HH. |
| Format serializacji zdarzeń | Format serializacji danych wyjściowych. Obsługiwane są kod JSON, CSV i Avro.|
| Encoding | Jeśli używasz formatu CSV lub JSON, należy określić kodowanie. W tym momencie jedynym obsługiwanym formatem kodowania jest UTF-8.|
| Ogranicznik | Dotyczy tylko serializacji woluminu CSV. Stream Analytics obsługuje wiele ograniczników na potrzeby serializowania danych woluminu CSV. Obsługiwane wartości to przecinek, średnik, spacja, tabulator i pionowy pasek.|
| Format | Dotyczy tylko serializacji JSON. **Linia rozdzielona** określa, że dane wyjściowe są formatowane przy użyciu każdego obiektu JSON oddzielonego przez nowy wiersz. W przypadku wybrania opcji **rozdzielone linią**kod JSON jest odczytywany po jednym obiekcie naraz. Cała zawartość nie będzie prawidłowym kodem JSON.  **Tablica** określa, że dane wyjściowe są formatowane jako tablica obiektów JSON. Ta tablica jest zamykana tylko wtedy, gdy zadanie zostało zatrzymane lub Stream Analytics zostało przeniesione do następnego przedziału czasu. Ogólnie rzecz biorąc, zalecane jest użycie kodu JSON rozdzielonego wierszem, ponieważ nie wymaga żadnej specjalnej obsługi, gdy plik wyjściowy jest nadal w trakcie zapisywania.|
| Tryb uwierzytelniania | Możesz autoryzować dostęp do konta Data Lake Storage przy użyciu [tożsamości zarządzanej](stream-analytics-managed-identities-adls.md) lub tokenu użytkownika. Po udzieleniu dostępu można odwołać dostęp poprzez zmianę hasła konta użytkownika, usunięcie Data Lake Storage danych wyjściowych dla tego zadania lub usunięcie zadania Stream Analytics. |

## <a name="partitioning"></a>Partycjonowanie

W przypadku klucza partycji Użyj tokenów {date} i {Time} w wzorcu prefiksu ścieżki. Wybierz format daty, na przykład RRRR/MM/DD, DD/MM/RRRR lub MM-DD-RRRR. Użyj HH dla formatu godziny. Liczba składników zapisywania danych wyjściowych następuje po partycjonowaniu danych wejściowych dla w [pełni działania równoległegoych zapytań](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Rozmiar partii wyjściowej

Maksymalny rozmiar komunikatu znajduje się w temacie [Data Lake Storage limitów](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits). Aby zoptymalizować rozmiar partii, użyj do 4 MB na operację zapisu.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-azure-cli.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu szablonu ARM](quick-create-azure-resource-manager.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Szybki Start: Tworzenie zadania Azure Stream Analytics w programie Visual Studio Code](quick-create-visual-studio-code.md)