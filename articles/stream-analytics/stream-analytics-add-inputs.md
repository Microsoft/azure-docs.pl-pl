---
title: Informacje o danych wejściowych dla Azure Stream Analytics
description: W tym artykule opisano koncepcję danych wejściowych w zadaniu Azure Stream Analytics, porównując dane wejściowe przesyłania strumieniowego z danymi wejściowymi.
author: jseb225
ms.author: krishmam
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: b344e9e24d15189b805f586227c7253395e8448e
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348988"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Informacje o danych wejściowych dla Azure Stream Analytics

Azure Stream Analytics zadania łączą się z jednym lub wieloma danymi wejściowymi. Każde wejście definiuje połączenie z istniejącym źródłem danych. Stream Analytics akceptuje dane przychodzące z kilku rodzajów źródeł zdarzeń, w tym Event Hubs, IoT Hub i magazynu obiektów BLOB. Dane wejściowe są przywoływane przez nazwę w kwerendzie SQL przesyłania strumieniowego, które należy napisać dla każdego zadania. W zapytaniu można sprzęgać wiele danych wejściowych z danymi mieszanymi lub porównywać dane przesyłane strumieniowo z wyszukiwaniem w dane referencyjne i przekazywać wyniki do danych wyjściowych. 

Stream Analytics ma integrację pierwszej klasy z czterema rodzajami zasobów jako danymi wejściowymi:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

Te zasoby wejściowe mogą być aktywne w ramach tej samej subskrypcji platformy Azure co zadanie Stream Analytics lub z innej subskrypcji.

Za pomocą [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-input),  [Azure PowerShell](/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [interfejsu API platformy .NET](/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [interfejsu API REST](/rest/api/streamanalytics/2016-03-01/inputs)i [programu Visual Studio](stream-analytics-tools-for-visual-studio-install.md) można tworzyć, edytować i testować dane wejściowe zadań Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Dane wejściowe strumienia i odwołania
Gdy dane są wypychane do źródła danych, są używane przez zadanie Stream Analytics i przetwarzane w czasie rzeczywistym. Istnieją dwa typy danych wejściowych: dane strumienia danych i dane referencyjne.

### <a name="data-stream-input"></a>Dane wejściowe strumienia danych
Strumień danych to nieograniczona sekwencja zdarzeń w czasie. Zadania usługi Stream Analytics musza zawierać co najmniej jedne dane wejściowe strumienia danych. Event Hubs, IoT Hub, Azure Data Lake Storage Gen2 oraz magazyn obiektów BLOB są obsługiwane jako źródła danych wejściowych strumienia. Event Hubs są używane do zbierania strumieni zdarzeń z wielu urządzeń i usług. Te strumienie mogą obejmować źródła działań mediów społecznościowych, informacje o handlu giełdowe lub dane z czujników. Centra IoT są zoptymalizowane pod kątem zbierania danych z połączonych urządzeń w scenariuszach Internet rzeczy (IoT).  Magazyn obiektów BLOB może być używany jako źródło danych wejściowych do pozyskiwania zbiorczych plików jako strumień, takich jak pliki dziennika.  

Aby uzyskać więcej informacji na temat danych wejściowych przesyłania strumieniowego, zobacz [przesyłanie strumieniowe danych jako dane wejściowe do Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Dane wejściowe odwołania
Stream Analytics obsługuje również dane wejściowe znane jako *informacje referencyjne*. Dane referencyjne są całkowicie statyczne lub zmieniają się powoli. Jest zazwyczaj używany do przeprowadzenia korelacji i wyszukiwania. Na przykład można przyłączyć dane danych wejściowych strumienia danych do danych w danych referencyjnych, podobnie jak w przypadku wykonywania sprzężenia SQL w celu wyszukania wartości statycznych. Magazyn obiektów blob platformy Azure, Azure Data Lake Storage Gen2 i Azure SQL Database są obecnie obsługiwane jako źródła danych wejściowych. Źródłowe źródła danych referencyjne mają limit równy 300 MB, w zależności od złożoności zapytania i przydzieloną liczbę jednostek przesyłania strumieniowego (zobacz sekcję [ograniczenie rozmiaru](stream-analytics-use-reference-data.md#size-limitation) w dokumentacji danych referencyjnych, aby uzyskać więcej informacji).

Aby uzyskać więcej informacji na temat danych wejściowych referencyjnych, zobacz [Korzystanie z danych referencyjnych dla odnośników w Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](stream-analytics-quick-create-portal.md)