---
title: Porównanie funkcji Azure Stream Analytics
description: W tym artykule porównano funkcje obsługiwane w przypadku zadań w chmurze i IoT Edge Azure Stream Analytics w Azure Portal, Visual Studio i Visual Studio Code.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 037bd8bc823cd8c77241d0ca25174e29d25149b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020540"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porównanie funkcji Azure Stream Analytics

Za pomocą Azure Stream Analytics można tworzyć rozwiązania przesyłania strumieniowego w chmurze i IoT Edge przy użyciu [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)i [Visual Studio Code](quick-create-visual-studio-code.md). W tabelach w tym artykule przedstawiono, które funkcje są obsługiwane przez każdą platformę dla obu typów zadań.

> [!NOTE]
> Narzędzia Visual Studio i Visual Studio Code Tools nie obsługują zadań w regionach Chiny Wschodnie, Chiny Północne, Niemczech centralnych i Niemczech północnych.

## <a name="cloud-job-features"></a>Funkcje zadania w chmurze


|Cecha  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Wiele platform     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Tworzenie skryptu     |Tak         |Tak         |Tak         |
|Skrypt IntelliSense     |Podświetlanie składni         |Podświetlanie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |Podświetlanie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |
|Zdefiniuj wszystkie typy danych wejściowych, wyjść i konfiguracji zadań     |Tak         |Tak         |Tak         |
|Kontrola źródła     |Nie         |Tak         |Tak         |
|Obsługa ciągłej integracji/ciągłego wdrażania     |Częściowe         |Tak         |Tak         |
|Udostępnianie danych wejściowych i wyjściowych w wielu zapytaniach     |Nie         |Tak         |Tak         |
|Testowanie zapytań przy użyciu pliku przykładowego     |Tak         |Tak        |Tak         |
|Lokalne testowanie danych na żywo     |Nie         |Tak       |Tak      |
|Wyświetlanie listy zadań i przeglądanie jednostek zadań     |Tak         |Tak        |Tak         |
|Eksportowanie zadania do projektu lokalnego     |Nie         |Tak         |Tak         |
|Przesyłanie, uruchamianie i zatrzymywanie zadań     |Tak         |Tak         |Tak         |
|Wyświetlanie metryk i diagramów zadań     |Tak         |Tak         |Otwórz w portalu         |
|Wyświetl błędy środowiska uruchomieniowego zadania     |Tak         |Tak         |Nie         |
|Dzienniki zasobów     |Tak         |Nie         |Nie         |
|Niestandardowe właściwości komunikatów     |Tak         |Tak         |Nie       |
|Funkcja niestandardowego kodu w języku C# i deserializacja|Tryb tylko do odczytu|Tak|Nie|
|Java UDF i UDA     |Tak         |Tak         |Tylko Windows         |
|Usługa Machine Learning     |Tak        |Tak         |Nie         |
|Azure Machine Learning Studio (klasyczny)|Tak, ale nie można przetestować zapytania        |Tak |Nie         |
|Poziom zgodności     |1.0</br>1.1</br>1,2 (wartość domyślna)         |1.0</br>1.1</br>1,2 (wartość domyślna)           |1.0</br>1.1</br>1,2 (wartość domyślna)           |
|Wbudowane funkcje wykrywania anomalii opartej na ML     |Tak         |Tak         |Tak         |
|Wbudowane funkcje geograficzne     |Tak         |Tak         |Tak         |



## <a name="iot-edge-job-features"></a>Funkcje zadania IoT Edge

|Cecha  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Tworzenie zadań     |Tak         |Tak         |Nie         |
|Kontrola źródła     |Nie         |Tak         |Nie         |
|Eksportowanie zadania do projektu lokalnego     |Nie         |Tak         |Nie         |
|Testowanie zapytań przy użyciu pliku przykładowego     |Tak         |Tak         |Nie         |
|Udostępnianie danych wejściowych i wyjściowych w wielu zapytaniach     |Nie         |Tak         |Nie         |
|Funkcja zdefiniowana przez użytkownika w języku C#     |Nie         |Tak         |Nie         |
|Prześlij zadania     |Tak         |Tak         |Nie         |
|Wyświetlanie listy zadań i przeglądanie jednostek zadań     |Tak         |Tak         |Nie         |
|Wyświetlanie metryk i diagramów zadań     |Tak         |Częściowe         |Nie         |
|Wyświetl błędy środowiska uruchomieniowego zadania     |Tak         |Częściowe         |Nie         |
|Obsługa ciągłej integracji/ciągłego wdrażania     |Nie         |Nie         |Nie         |


## <a name="next-steps"></a>Następne kroki

* [Azure Stream Analytics na urządzeniach IoT Edge](stream-analytics-edge.md)
* [Samouczek: pisanie funkcji zdefiniowanej przez użytkownika w języku C# dla zadania Azure Stream Analytics IoT Edge (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Opracowywanie zadań IoT Edge Stream Analytics przy użyciu narzędzi Visual Studio Tools](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Eksplorowanie Azure Stream Analytics z Visual Studio Code (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)


