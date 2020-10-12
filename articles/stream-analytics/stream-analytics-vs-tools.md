---
title: Wyświetlanie Azure Stream Analytics zadań w programie Visual Studio
description: Dowiedz się, jak wyświetlać, uruchamiać i zatrzymywać, testować połączenia, sprawdzać wyniki oraz eksportować Azure Stream Analytics zadania przy użyciu programu Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: 95abf403dfd4abd4c0533b6e8b4a2f83b33fa18b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045556"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)

Narzędzia Azure Stream Analytics Tools for Visual Studio ułatwiają deweloperom zarządzanie swoimi Stream Analytics zadaniami bezpośrednio z poziomu środowiska IDE. Za pomocą narzędzi Azure Stream Analytics można:
- [Utwórz nowe zadania](stream-analytics-quick-create-vs.md)
- Uruchamianie, zatrzymywanie i [monitorowanie zadań](stream-analytics-monitor-jobs-use-vs.md)
- Sprawdź wyniki zadania
- Eksportowanie istniejących zadań do projektu
- Testowanie połączeń wejściowych i wyjściowych
- [Uruchom zapytania lokalnie](stream-analytics-vs-tools-local-run.md)

Dowiedz się, jak [zainstalować narzędzia Azure Stream Analytics Tools for Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Eksploruj widok zadania

Widok zadania służy do współpracy z zadaniami Azure Stream Analytics w programie Visual Studio.

### <a name="open-the-job-view"></a>Otwórz widok zadania

1. W **Eksplorator serwera**wybierz pozycję **Stream Analytics zadania** , a następnie wybierz pozycję **Odśwież**. Zadanie powinno pojawić się w obszarze **Stream Analytics zadania**.

    ![Lista Eksploratora serwera Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Rozwiń węzeł zadania, a następnie kliknij dwukrotnie węzeł **widok zadania** , aby otworzyć widok zadań.
    
   ![Rozwinięty węzeł zadania](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Uruchamianie i zatrzymywanie zadań

Zadania Azure Stream Analytics mogą być w pełni zarządzane z poziomu widoku zadań w programie Visual Studio. Użyj kontrolek, aby uruchomić, zatrzymać lub usunąć zadanie.
    
   ![Kontrolki zadania Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Sprawdź wyniki zadania

Narzędzia Stream Analytics Tools for Visual Studio obecnie obsługują Podgląd danych wyjściowych dla Azure Data Lake Storage i magazynu obiektów BLOB. Aby wyświetlić wyniki, wystarczy dwukrotnie kliknąć węzeł dane wyjściowe diagramu zadania w **widoku zadania** i wprowadzić odpowiednie poświadczenia.

   ![Dane wyjściowe obiektu BLOB zadania Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Eksportowanie zadań do projektu

Istnieją dwa sposoby eksportowania istniejącego zadania do projektu.

1. W **Eksplorator serwera**w węźle zadania Stream Analytics kliknij prawym przyciskiem myszy węzeł zadanie. Wybierz pozycję **Eksportuj do nowego projektu Stream Analytics**.
    
   ![Eksportowanie zadania do projektu](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Wygenerowany projekt zostanie wyświetlony w **Eksplorator rozwiązań**.
    
   ![Eksplorator rozwiązań](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. W widoku zadania wybierz pozycję **Generuj projekt**.
    
   ![Generuj projekt na podstawie widoku zadania](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Testuj połączenia

W **widoku zadania** można testować połączenia wejściowe i wyjściowe, wybierając opcję z listy rozwijanej **połączenie testowe** .

   ![Lista rozwijana połączenia testowego](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Wyniki **połączenia testowego** są wyświetlane w oknie **dane wyjściowe** .

   ![Wyniki połączenia testowego](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Następne kroki

* [Monitoruj Azure Stream Analytics zadania i zarządzaj nimi za pomocą programu Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Samouczek: wdrażanie zadania usługi Azure Stream Analytics z ciągłą integracją/ciągłym wdrażaniem przy użyciu usługi Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Ciągła integracja i ciągłe opracowywanie za pomocą narzędzi usługi Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
