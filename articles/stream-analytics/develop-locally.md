---
title: Lokalne opracowywanie i debugowanie zadań Azure Stream Analytics
description: Dowiedz się, jak opracowywać i testować Azure Stream Analytics zadania na komputerze lokalnym przed ich uruchomieniem w Azure Portal.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 797577ce976f71f41ece0d103bd0145775d48b56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885588"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Lokalne opracowywanie i debugowanie zadań Azure Stream Analytics

Mimo że można tworzyć i testować Azure Stream Analytics zadania w Azure Portal, wielu deweloperów preferuje lokalne środowisko programistyczne. Stream Analytics ułatwia korzystanie z ulubionego edytora kodu i narzędzi programistycznych do tworzenia i testowania zadań z strumieńami zdarzeń na żywo z usługi Azure Event Hub, IoT Hub i Blob Storage przy użyciu w pełni działającego lokalnego środowiska uruchomieniowego jednego węzła. Możesz również przesyłać zadania na platformę Azure bezpośrednio z lokalnego środowiska deweloperskiego.

## <a name="local-development-environments"></a>Lokalne środowiska programistyczne

Sposób tworzenia zadań Stream Analytics na komputerze lokalnym zależy od preferencji narzędzi i dostępności funkcji. Zobacz [porównanie funkcji Azure Stream Analytics](feature-comparison.md) , aby sprawdzić, jakie funkcje są obsługiwane w poszczególnych środowiskach deweloperskich.

Środowiska w poniższej tabeli obsługują programowanie lokalne:

|Środowisko                              |Opis    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| [Rozszerzenie narzędzi Azure Stream Analytics Tools](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) for Visual Studio Code umożliwia tworzenie i testowanie zadań usługi Stream Analytics zarówno lokalnie, jak i w chmurze dzięki rozbudowanej funkcji IntelliSense i natywnej kontroli źródła. Obsługuje programowanie w systemach Linux, MacOS i Windows. Aby dowiedzieć się więcej, zobacz [Tworzenie zadania Azure Stream Analytics w Visual Studio Code](quick-create-visual-studio-code.md). Rozszerzenie obsługuje również [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) , który jest środowiskiem deweloperskim hostowanym w chmurze.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Narzędzia Stream Analytics są częścią rozwiązań do tworzenia i przetwarzania danych na platformie Azure w programie Visual Studio. Za pomocą programu Visual Studio można pisać niestandardowe funkcje i deserializacji zdefiniowane przez użytkownika w języku C#. Aby dowiedzieć się więcej, zobacz [Tworzenie zadania Azure Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md).|
|[Wiersz polecenia lub terminal](stream-analytics-tools-for-visual-studio-cicd.md)|Pakiet NuGet Azure Stream Analytics/CD zapewnia narzędzia dla kompilacji projektu programu Visual Studio, testowanie lokalne na dowolnym komputerze. Pakiet npm (ciągłej integracji/ciągłego wdrażania) udostępnia narzędzia dla Visual Studio Code kompilacje projektu (które generuje szablon Azure Resource Manager) na dowolnym komputerze. Azure Stream Analytics|

## <a name="next-steps"></a>Następne kroki

* [Testowanie Stream Analytics zapytań lokalnie z przykładowymi danymi przy użyciu Visual Studio Code](visual-studio-code-local-run.md)
* [Przetestuj Stream Analytics zapytań lokalnie względem danych wejściowych strumienia na żywo za pomocą Visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Testowanie Stream Analytics zapytań lokalnie za pomocą programu Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Lokalne testowanie danych na żywo przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio](stream-analytics-live-data-local-testing.md)
