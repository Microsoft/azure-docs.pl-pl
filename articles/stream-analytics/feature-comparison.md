---
title: Porównanie funkcji Azure Stream Analytics
description: W tym artykule porównano funkcje obsługiwane w przypadku zadań w chmurze i IoT Edge Azure Stream Analytics w Azure Portal, Visual Studio i Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 2fbaaac0095622f14d5bc0033b7787bec8ec2fa2
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018291"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porównanie funkcji Azure Stream Analytics

Za pomocą Azure Stream Analytics można tworzyć rozwiązania przesyłania strumieniowego w chmurze i IoT Edge przy użyciu [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)i [Visual Studio Code](quick-create-visual-studio-code.md). W tabelach w tym artykule przedstawiono, które funkcje są obsługiwane przez każdą platformę dla obu typów zadań.

> [!NOTE]
> Narzędzia Visual Studio i Visual Studio Code Tools nie obsługują zadań w regionach Chiny Wschodnie, Chiny Północne, Niemczech centralnych i Niemczech północnych.

## <a name="cloud-job-features"></a>Funkcje zadania w chmurze


|Cecha  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Wiele platform     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Tworzenie skryptu     |Yes         |Yes         |Yes         |
|Skrypt IntelliSense     |Podświetlanie składni         |Podświetlanie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |Podświetlanie składni</br>Uzupełnianie kodu</br>Znacznik błędu         |
|Zdefiniuj wszystkie typy danych wejściowych, wyjść i konfiguracji zadań     |Yes         |Yes         |Yes         |
|Kontrola źródła     |Nie         |Yes         |Yes         |
|Obsługa ciągłej integracji/ciągłego wdrażania     |Częściowe         |Yes         |Yes         |
|Udostępnianie danych wejściowych i wyjściowych w wielu zapytaniach     |Nie         |Yes         |Yes         |
|Testowanie zapytań przy użyciu pliku przykładowego     |Yes         |Yes        |Yes         |
|Lokalne testowanie danych na żywo     |Nie         |Yes       |Yes      |
|Wyświetlanie listy zadań i przeglądanie jednostek zadań     |Yes         |Yes        |Yes         |
|Eksportowanie zadania do projektu lokalnego     |Nie         |Yes         |Yes         |
|Przesyłanie, uruchamianie i zatrzymywanie zadań     |Yes         |Yes         |Yes         |
|Wyświetlanie metryk i diagramów zadań     |Yes         |Yes         |Otwórz w portalu         |
|Wyświetl błędy środowiska uruchomieniowego zadania     |Yes         |Yes         |Nie         |
|Dzienniki zasobów     |Yes         |Nie         |Nie         |
|Niestandardowe właściwości komunikatów     |Yes         |Yes         |Nie       |
|Funkcja niestandardowego kodu w języku C# i deserializacja|Tryb tylko do odczytu|Yes|Nie|
|Java UDF i UDA     |Yes         |Yes         |Tylko Windows         |
|Usługa Machine Learning     |Yes        |Yes         |Nie         |
|Azure Machine Learning Studio (klasyczny)|Tak, ale nie można przetestować zapytania        |Yes |Nie         |
|Poziom zgodności     |1.0</br>1,1</br>1,2 (wartość domyślna)         |1.0</br>1,1</br>1,2 (wartość domyślna)           |1.0</br>1,1</br>1,2 (wartość domyślna)           |
|Wbudowane funkcje wykrywania anomalii opartej na ML     |Yes         |Yes         |Yes         |
|Wbudowane funkcje geograficzne     |Yes         |Yes         |Yes         |



## <a name="iot-edge-job-features"></a>Funkcje zadania IoT Edge

|Cecha  |Portal  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Tworzenie zadań     |Yes         |Yes         |Nie         |
|Kontrola źródła     |Nie         |Yes         |Nie         |
|Eksportowanie zadania do projektu lokalnego     |Nie         |Yes         |Nie         |
|Testowanie zapytań przy użyciu pliku przykładowego     |Yes         |Yes         |Nie         |
|Udostępnianie danych wejściowych i wyjściowych w wielu zapytaniach     |Nie         |Yes         |Nie         |
|Funkcja zdefiniowana przez użytkownika w języku C#     |Nie         |Yes         |Nie         |
|Prześlij zadania     |Yes         |Yes         |Nie         |
|Wyświetlanie listy zadań i przeglądanie jednostek zadań     |Yes         |Yes         |Nie         |
|Wyświetlanie metryk i diagramów zadań     |Yes         |Częściowe         |Nie         |
|Wyświetl błędy środowiska uruchomieniowego zadania     |Yes         |Częściowe         |Nie         |
|Obsługa ciągłej integracji/ciągłego wdrażania     |Nie         |Nie         |Nie         |


## <a name="next-steps"></a>Następne kroki

* [Azure Stream Analytics na urządzeniach IoT Edge](stream-analytics-edge.md)
* [Samouczek: pisanie funkcji zdefiniowanej przez użytkownika w języku C# dla zadania Azure Stream Analytics IoT Edge (wersja zapoznawcza)](stream-analytics-edge-csharp-udf.md)
* [Opracowywanie zadań IoT Edge Stream Analytics przy użyciu narzędzi Visual Studio Tools](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Eksplorowanie Azure Stream Analytics z Visual Studio Code (wersja zapoznawcza)](visual-studio-code-explore-jobs.md)


