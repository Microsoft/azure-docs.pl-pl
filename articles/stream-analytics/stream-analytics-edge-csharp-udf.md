---
title: Samouczek — pisanie funkcji zdefiniowanych przez użytkownika w języku C# dla zadań Azure Stream Analytics w programie Visual Studio (wersja zapoznawcza)
description: W tym samouczku przedstawiono sposób pisania funkcji zdefiniowanych przez użytkownika w języku c# dla zadań Stream Analytics w programie Visual Studio.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 851229e441aa2fbdf7b6eec05390c0ce2b149da2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020472"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Samouczek: pisanie funkcji zdefiniowanej przez użytkownika w języku C# dla zadania Azure Stream Analytics (wersja zapoznawcza)

Zdefiniowane przez użytkownika funkcje w języku C# w programie Visual Studio pozwalają rozszerzyć język zapytań usługi Azure Stream Analytics przy użyciu własnych funkcji. Możesz ponownie używać istniejącego kodu (w tym bibliotek DLL), a także korzystać z matematycznej lub złożonej logiki języka C#. Istnieją trzy sposoby implementowania funkcji zdefiniowanych przez użytkownika: pliki CodeBehind w projekcie usługi Stream Analytics, funkcje zdefiniowane przez użytkownika z lokalnego projektu języka C# lub funkcje zdefiniowane przez użytkownika z istniejącego pakietu z konta magazynu. W tym samouczku do zaimplementowania podstawowej funkcji języka C# jest używana metoda CodeBehind. Funkcja UDF dla Stream Analytics zadań jest obecnie w wersji zapoznawczej i nie powinna być używana w obciążeniach produkcyjnych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie funkcji zdefiniowanej przez użytkownika w języka C# przy użyciu metody CodeBehind.
> * Przetestuj zadanie Stream Analytics lokalnie.
> * Opublikuj zadanie na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że następujące wymagania wstępne zostały spełnione:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj narzędzia [Stream Analytics Tools for Visual Studio](stream-analytics-tools-for-visual-studio-install.md) oraz obciążenia **Programowanie na platformie Azure** i **Magazynowanie i przetwarzanie danych**.
* Zapoznaj się z istniejącym [przewodnikiem projektowania Stream Analytics Edge](stream-analytics-tools-for-visual-studio-edge-jobs.md) , jeśli tworzysz zadanie IoT Edge.

## <a name="create-a-container-in-your-azure-storage-account"></a>Tworzenie kontenera na koncie usługi Azure Storage

Tworzony kontener będzie używany do przechowywania skompilowanego pakietu języka C#. Jeśli utworzysz zadanie brzegowe, to konto magazynu zostanie również użyte do wdrożenia pakietu na urządzeniu IoT Edge. Dla każdego zadania usługi Stream Analytics użyj dedykowanego kontenera. Ponowne używanie tego samego kontenera dla wielu zadań usługi Stream Analytics Edge nie jest obsługiwane. Jeśli masz już konto magazynu z istniejącymi kontenerami, możesz go użyć. Jeśli nie, musisz [utworzyć nowy kontener](../storage/blobs/storage-quickstart-blobs-portal.md). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Tworzenie projektu Stream Analytics w programie Visual Studio

1. Uruchom program Visual Studio.

2. Wybierz pozycję **Plik > Nowy > Projekt**.

3. Na liście szablony po lewej stronie wybierz pozycję **Stream Analytics**, a następnie wybierz pozycję **Azure Stream Analytics aplikacja brzegowa** lub **aplikacja Azure Stream Analytics**.

4.  Wprowadź **nazwę**, **lokalizację** oraz **nazwę rozwiązania** dla projektu i wybierz przycisk **OK**.

    ![Tworzenie projektu usługi Azure Stream Analytics Edge w programie Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Konfigurowanie ścieżki pakietu zestawu

1. Otwórz program Visual Studio i przejdź do **Eksploratora rozwiązań**.

2. Kliknij dwukrotnie plik konfiguracji zadania `EdgeJobConfig.json`.

3. Rozwiń sekcję **Konfiguracja kodu skonfigurowana przez użytkownika**, a następnie wypełnij konfigurację za pomocą następujących sugerowanych wartości:

   |**Ustawienie**|**Sugerowana wartość**|
   |-------|---------------|
   |Zasób ustawień magazynu globalnego|Wybierz źródło danych z bieżącego konta|
   |Subskrypcja ustawień magazynu globalnego| < subskrypcję >|
   |Konto magazynu ustawień globalnych magazynu| < konta magazynu >|
   |Zasób ustawień niestandardowego magazynu kodu|Wybierz źródło danych z bieżącego konta|
   |Konto magazynu ustawień niestandardowych magazynu kodu|< konta magazynu >|
   |Kontener ustawień niestandardowego magazynu kodu|< kontener magazynu >|


## <a name="write-a-c-udf-with-codebehind"></a>Pisanie funkcji zdefiniowanej przez użytkownika w języku C# przy użyciu metody CodeBehind
Plik CodeBehind jest plikiem języka C# skojarzonym z pojedynczym skryptem zapytania ASA. Narzędzia programu Visual Studio będą automatycznie pakować plik CodeBehind i przekazywać go do konta usługi Azure Storage po przesłaniu. Wszystkie klasy muszą być zdefiniowane jako *publiczne*, a wszystkie obiekty — jako *statyczne publiczne*.

1. W **Eksploratorze rozwiązań** rozwiń węzeł **Script.asql**, aby znaleźć plik CodeBehind **Script.asaql.cs**.

2. Zastąp kod następującym przykładem:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Implementowanie funkcji zdefiniowanej przez użytkownika

1. W **Eksploratorze rozwiązań** otwórz plik **Script.asaql**.

2. Zastąp istniejące zapytanie następującym:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Testowanie lokalne

1. Pobierz [przykładowy plik danych symulatora temperatury](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

2. W **Eksploratorze rozwiązań** rozwiń węzeł **Dane wejściowe**, kliknij prawym przyciskiem myszy plik **Input.json** i wybierz pozycję **Dodaj lokalne dane wejściowe**.

   ![Dodawanie lokalnych danych wejściowych do zadania usługi Stream Analytics w programie Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Określ ścieżkę pliku lokalnych danych wejściowych dla przykładowych pobranych danych i wybierz pozycję **Zapisz**.

    ![Konfiguracja lokalnych danych wejściowych dla zadania usługi Stream Analytics w programie Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Kliknij pozycję **Uruchom lokalnie** w Edytorze skryptów. Gdy uruchomienie lokalne spowoduje pomyślne zapisanie wyników danych wyjściowych, naciśnij dowolny klawisz, aby wyświetlić wyniki w formacie tabeli. 

    ![Lokalne uruchamianie zadania usługi Azure Stream Analytics przy użyciu programu Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Można również wybrać pozycję **Otwórz folder wyników**, aby wyświetlić nieprzetworzone pliki w formacie JSON i CSV.

    ![Wyświetlanie wyników lokalnego zadania usługi Azure Stream Analytics przy użyciu programu Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Debugowanie funkcji zdefiniowanej przez użytkownika
Funkcję zdefiniowaną przez użytkownika w języku C# można debugować lokalnie, tak samo jak standardowy kod języka C#. 

1. Dodaj punkty przerwania w funkcji języka C#.

    ![Dodawanie punktów przerwania do zdefiniowanej przez użytkownika funkcji usługi Stream Analytics w programie Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Naciśnij klawisz **F5**, aby uruchomić debugowanie. Zgodnie z oczekiwaniami program będzie zatrzymywać się w punktach przerwania.

    ![Wyświetlanie wyników debugowania zdefiniowanej przez użytkownika funkcji usługi Stream Analytics](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publikowanie zadania na platformie Azure
Po przetestowaniu zapytania w środowisku lokalnym wybierz pozycję **Prześlij na platformę Azure** w Edytorze skryptów, aby opublikować zadanie na platformie Azure.

![Przesyłanie zadania usługi Stream Analytics Edge na platformę Azure z programu Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Wdrażanie na urządzeniach usługi IoT Edge
Jeśli zdecydujesz się na utworzenie zadania Stream Analytics Edge, można je wdrożyć jako moduł IoT Edge. Postępuj zgodnie z instrukcjami z [przewodnika Szybki start dotyczącego usługi IoT Edge](../iot-edge/quickstart.md), aby utworzyć centrum IoT Hub, zarejestrować urządzenie usługi IoT Edge, a następnie zainstaluj i uruchom środowisko uruchomieniowe usługi IoT Edge na urządzeniu. Następnie postępuj zgodnie z instrukcjami z samouczka dotyczącego [wdrażania zadania](../iot-edge/tutorial-deploy-stream-analytics.md#deploy-the-job), aby wdrożyć zadanie usługi Stream Analytics jako moduł usługi IoT Edge. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz prostą funkcję zdefiniowaną przez użytkownika w języku C# za pomocą CodeBehind, opublikowano zadanie na platformie Azure i wdrożono zadanie na platformie Azure lub na IoT Edge urządzeniu. 

Aby dowiedzieć się więcej na temat różnych sposobów używania funkcji zdefiniowanych przez użytkownika w języku C# do Stream Analytics zadań, przejdź do tego artykułu:

> [!div class="nextstepaction"]
> [Pisanie w języku C# funkcji dla usługi Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)