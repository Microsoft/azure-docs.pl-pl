---
title: Utrwalaj dane wyjściowe zadania i zadania w magazynie danych
description: Dowiedz się więcej na temat różnych opcji utrwalania danych wyjściowych z zadań wsadowych. Możesz utrwalać dane w usłudze Azure Storage lub w innym magazynie danych.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ebe0b6d57225eff9f3f1251d5e491c95e9b7ffc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85965114"
---
# <a name="persist-job-and-task-output"></a>Utrwalanie danych wyjściowych zadań i zadań podrzędnych

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Typowe przykłady danych wyjściowych zadania obejmują:

- Pliki tworzone, gdy zadanie przetwarza dane wejściowe.
- Pliki dziennika skojarzone z wykonywaniem zadania.

W tym artykule opisano różne opcje utrwalania danych wyjściowych zadania.

## <a name="options-for-persisting-output"></a>Opcje utrwalania danych wyjściowych

W zależności od scenariusza istnieje kilka różnych metod, które można wykonać w celu utrwalenia danych wyjściowych zadania:

- [Użyj interfejsu API usługi Batch](batch-task-output-files.md).  
- [Użyj biblioteki Konwencji plików wsadowych dla platformy .NET](batch-task-output-file-conventions.md).  
- Zaimplementuj standardową konwencje plików wsadowych w aplikacji.
- Zaimplementuj niestandardowe rozwiązanie do przenoszenia plików.

W poniższych sekcjach krótko opisano każde podejście, a także ogólne zagadnienia projektowe dotyczące utrwalania danych wyjściowych.

### <a name="use-the-batch-service-api"></a>Korzystanie z interfejsu API usługi Batch

Usługa Batch obsługuje określanie plików wyjściowych w usłudze Azure Storage na potrzeby danych zadań po [dodaniu zadania do zadania](/rest/api/batchservice/add-a-task-to-a-job) lub [dodaniu kolekcji zadań do zadania](/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Aby uzyskać więcej informacji na temat utrwalania danych wyjściowych zadań za pomocą interfejsu API usługi Batch, zobacz [trwałe dane zadania do usługi Azure Storage za pomocą interfejsu API usług Batch](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Korzystanie z biblioteki Konwencji plików wsadowych dla platformy .NET

Usługa Batch definiuje opcjonalny zestaw Konwencji do nazewnictwa plików wyjściowych zadań w usłudze Azure Storage. [Standardowe konwencje plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) opisują te konwencje. Standard Konwencji plików określa nazwy kontenera docelowego i ścieżkę obiektu BLOB w usłudze Azure Storage dla danego pliku wyjściowego na podstawie nazw zadania i zadania.

Jest to możliwe niezależnie od tego, czy użytkownik zdecyduje się użyć standardu Konwencji plików do nazewnictwa plików danych wyjściowych. Możesz również nadać nazwę kontenerowi docelowemu i obiektowi BLOB. Jeśli używasz standardu Konwencji plików do nazewnictwa plików wyjściowych, pliki wyjściowe są dostępne do wyświetlania w [Azure Portal][portal].

Deweloperzy tworzący rozwiązania wsadowe w językach C# i .NET mogą używać [biblioteki Konwencji plików dla platformy .NET][nuget_package] do utrwalania danych zadań na koncie usługi Azure Storage, zgodnie z [standardem Konwencji plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions). Biblioteka Konwencji plików obsługuje przeniesienie plików wyjściowych do usługi Azure Storage i Określanie nazw kontenerów docelowych i obiektów BLOB w dobrze znany sposób.

Aby uzyskać więcej informacji na temat utrwalania danych wyjściowych zadań przy użyciu biblioteki Konwencji plików dla platformy .NET, zobacz [trwałe dane zadania i zadania do usługi Azure Storage za pomocą biblioteki Konwencji plików wsadowych dla platformy .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementuj Standard Konwencji plików wsadowych

W przypadku korzystania z języka innego niż .NET można zaimplementować [standardową konwencje plików wsadowych](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) we własnej aplikacji.

Możesz chcieć wdrożyć konwencje nazewnictwa w standardowym czasie, gdy chcesz, aby sprawdzony schemat nazewnictwa lub gdy chcesz wyświetlić dane wyjściowe zadania w Azure Portal.

### <a name="implement-a-custom-file-movement-solution"></a>Implementowanie niestandardowego rozwiązania do przenoszenia plików

Możesz również zaimplementować własne kompletne rozwiązanie do przenoszenia plików. Użyj tej metody, gdy:

- Dane zadania mają być utrwalane w magazynie danych innym niż usługa Azure Storage. Aby przekazać pliki do magazynu danych, takiego jak Azure SQL lub Azure datalake, możesz utworzyć niestandardowy skrypt lub plik wykonywalny do przekazania do tej lokalizacji. Następnie można wywołać go w wierszu polecenia po uruchomieniu podstawowego pliku wykonywalnego. Na przykład w węźle systemu Windows można wywołać te dwa polecenia: `doMyWork.exe && uploadMyFilesToSql.exe`
- Chcesz wykonać sprawdzenie lub wczesne przekazywanie początkowych wyników.
- Chcesz zachować szczegółową kontrolę nad obsługą błędów. Na przykład możesz chcieć wdrożyć własne rozwiązanie, jeśli chcesz użyć akcji zależności zadań, aby wykonać pewne akcje przekazywania na podstawie określonych kodów zakończenia zadania. Aby uzyskać więcej informacji o akcjach zależności zadań, zobacz [Tworzenie zależności zadań w celu uruchamiania zadań zależnych od innych zadań](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Zagadnienia projektowe dotyczące utrwalania danych wyjściowych

Podczas projektowania rozwiązania do przetwarzania wsadowego należy wziąć pod uwagę następujące czynniki związane z zadaniami i zadaniami wyjściowymi zadań.

- **Okres istnienia węzła obliczeniowego**: węzły obliczeniowe są często przejściowe, szczególnie w pulach z obsługą skalowania automatycznego. Dane wyjściowe zadania uruchomionego w węźle są dostępne tylko wtedy, gdy węzeł istnieje i tylko w okresie przechowywania pliku ustawionym dla zadania. Jeśli zadanie generuje dane wyjściowe, które mogą być wymagane po zakończeniu zadania, zadanie musi przekazać pliki wyjściowe do magazynu trwałego, takiego jak usługa Azure Storage.

- **Magazyn wyjściowy**: usługa Azure Storage jest zalecana jako magazyn danych dla danych wyjściowych zadania, ale można użyć dowolnego trwałego magazynu. Zapisywanie danych wyjściowych zadań w usłudze Azure Storage jest zintegrowane z interfejsem API usługi Batch. W przypadku korzystania z innej formy magazynu trwałego należy napisać logikę aplikacji w celu utrwalenia danych wyjściowych zadania.

- **Pobieranie danych wyjściowych**: możesz pobrać dane wyjściowe zadania bezpośrednio z węzłów obliczeniowych w puli lub z usługi Azure Storage lub innego magazynu danych, jeśli masz utrwalone dane wyjściowe zadania. Aby pobrać dane wyjściowe zadania bezpośrednio z węzła obliczeniowego, należy mieć nazwę pliku i jego lokalizację wyjściową w węźle. W przypadku utrwalania danych wyjściowych zadań w usłudze Azure Storage potrzebna jest pełna ścieżka do pliku w usłudze Azure Storage w celu pobrania plików wyjściowych za pomocą zestawu SDK usługi Azure Storage.

- **Wyświetlanie danych wyjściowych**: po przejściu do zadania wsadowego w Azure Portal i wybraniu **plików w węźle**zostaną wyświetlone wszystkie pliki skojarzone z zadaniem, a nie tylko pliki wyjściowe, które Cię interesują. Ponownie pliki w węzłach obliczeniowych są dostępne tylko wtedy, gdy węzeł istnieje i tylko w czasie przechowywania pliku ustawionym dla zadania. Aby wyświetlić dane wyjściowe zadania, które zostały utrwalone w usłudze Azure Storage, możesz użyć Azure Portal lub aplikacji klienckiej usługi Azure Storage, takiej jak [Eksplorator usługi Azure Storage][storage_explorer]. Aby wyświetlić dane wyjściowe w usłudze Azure Storage przy użyciu portalu lub innego narzędzia, należy znać lokalizację pliku i przejść bezpośrednio do niego.

## <a name="next-steps"></a>Następne kroki

- Skorzystaj z nowych funkcji w interfejsie API usługi Batch, aby utrwalać dane zadań w usłudze [Azure Storage za pomocą interfejsu API usługi Batch](batch-task-output-files.md).
- Dowiedz się więcej o korzystaniu z biblioteki Konwencji plików wsadowych dla platformy .NET w temacie [utrwalanie danych zadań i zadań w usłudze Azure Storage za pomocą biblioteki Konwencji plików wsadowych dla platformy .NET](batch-task-output-file-conventions.md).
- Zobacz przykładowy projekt [PersistOutputs][github_persistoutputs] w witrynie GitHub, w którym pokazano, jak używać biblioteki klienta usługi Batch dla platformy .NET i biblioteki Konwencji plików dla platformy .NET do utrwalania danych wyjściowych zadań w trwałej pamięci masowej.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
