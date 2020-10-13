---
title: Debuguj funkcje zdefiniowane przez użytkownika w Azure Stream Analytics
description: W tym artykule opisano sposób debugowania funkcji zdefiniowanych przez użytkownika w programie Azure Stream Analytics.
author: jenssuessmeyer
ms.author: jenss
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 567def7abaff3cd8d70eb56f0aa117d1eeb52a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91300432"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Debuguj funkcje zdefiniowane przez użytkownika w Azure Stream Analytics 

Gdy funkcje zdefiniowane przez użytkownika (UDF) nie działają zgodnie z oczekiwaniami, należy debugować je w celu znalezienia problemu. Można debugować UDF dla zadań Stream Analytics podczas lokalnego uruchamiania zadań przy użyciu [Visual Studio Code](visual-studio-code-local-run-live-input.md) lub [Visual Studio](stream-analytics-vs-tools-local-run.md).

Gdy zadanie zostanie uruchomione lokalnie na żywo strumienia wejściowego, naśladuje tylko wykonanie aparatu Azure Stream Analytics w chmurze na jednym węźle. Testowanie lokalne danych na żywo nie może zastąpić testowania wydajności i skalowalności w chmurze, ale zaoszczędzić czas podczas testowania funkcjonalnego, nie trzeba przesyłać zadania do chmury za każdym razem, gdy chcesz przetestować. Ponadto zasady czasu są wyłączone dla lokalnego uruchomienia z lokalnym lub przykładowymi danymi, ale zasady czasu są obsługiwane na potrzeby testowania danych na żywo.

## <a name="pick-your-language"></a>Wybierz język

Można napisać UDF dla Azure Stream Analytics przy użyciu platformy .NET (C#) lub JavaScript. 

### <a name="functions-in-c"></a>Funkcje w języku C # 

[Pisząc UDF .NET przy użyciu programu Visual Studio](stream-analytics-edge-csharp-udf-methods.md), uzyskujesz taki sam poziom wsparcia jak dla dowolnego projektu klasy .NET. Ta obsługa obejmuje:

* Obsługa kompilacji, taka jak sprawdzanie składni i obsługa kompilatora.

* Możliwość dodawania, kompilowania i odwoływania się do projektu C# i artefaktów w rozwiązaniu Stream Analytics. 

* Łatwe ponowne użycie kodu hermetyzowane w projekcie, który można udostępniać. 

* Bezpośrednia obsługa debugowania w programie Visual Studio. Ustaw projekt Stream Analytics jako początkowy projekt i ustaw punkty przerwania w kodzie C#. Następnie naciśnij klawisz **F5** , aby debugować kod w języku c# tak jak w przypadku każdego innego projektu w języku c#. 

### <a name="functions-in-javascript"></a>Funkcje w języku JavaScript

Język JavaScript jest kolejną opcją do tworzenia funkcji w Stream Analytics. Kod JavaScript jest bezpośrednio umieszczony w obszarze funkcji projektu Stream Analytics, co sprawia, że udostępnianie między projektami jest trudniejsze.

Kompilacja występuje, gdy projekt Stream Analytics jest kompilowany lub wykonywany. Prawdopodobieństwo znalezienia problemu tylko w czasie wykonywania jest wyższe. Nie ma żadnej obsługi debugowania dla funkcji języka JavaScript w Stream Analytics bezpośrednio.

## <a name="debug-options-for-javascript"></a>Opcje debugowania dla języka JavaScript

Ze względu na to, że nie ma obsługi debugowania funkcji języka JavaScript w Stream Analytics bezpośrednio, można debugować przez hermetyzację funkcji w witrynie HTML i pobieranie danych wyjściowych.

Poniższy przykład pokazuje, jak debugować UDF JavaScript z pewnymi ograniczeniami w zintegrowanym środowisku uruchomieniowym w [Visual Studio Code](quick-create-visual-studio-code.md).

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że projekt Azure Stream Analytics zawiera następujące elementy:

* Dane wejściowe 
* Dane wyjściowe 
* Zapytanie Stream Analytics (. asaql) 
* Konfiguracja zadania Stream Analytics (JobConfig.jswłączona)
* UDF języka JavaScript

### <a name="prepare-files"></a>Przygotuj pliki

Na poniższej ilustracji plik zapytania *asaql* zawiera tylko wywołanie metody UDF, *fxcharCount*. Ta zmiana gwarantuje, że nadal można skompilować projekt po wprowadzeniu zmian.

> [!div class="mx-imgBorder"]
> ![Plik zapytania testowego Stream Analytics](./media/debug-user-defined-functions/asaql-file.png)

Utwórz dodatkowy folder w **testach** do hostowania pliku testowego, który jest wywoływany do wykonywania testu za pomocą funkcji JavaScript. W tym przykładzie nazwa folderu to *fxcharCount* , a nazwa testu jest *Test_UDF.js*. 

Poniższy obraz pokazuje kod w pliku testowym, który ładuje plik funkcji i wykonuje funkcję. Ten przykład jest prosty, ale można załadować dodatkowe pliki danych testowych i pętlę, używając dodatkowych testów do uzyskania danych wyjściowych. Notacja wywołania funkcji jest nieco inna od typowych wywołań, ponieważ plik jest przywoływany i nie został załadowany do środowiska uruchomieniowego, co umożliwia debugowanie. 

> [!div class="mx-imgBorder"]
> ![Plik testowy Stream Analytics](./media/debug-user-defined-functions/test-file.png)

W funkcji Dodaj następujące wiersze kodu do pliku, aby udostępnić metody. Nie wpływają one na możliwość kompilowania kodu w Visual Studio Code.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Stream Analytics UDF JavaScript](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>Zainstaluj obsługę debugowania

Aby debugować, należy [pobrać](https://nodejs.org/en/download/) i zainstalować **node.js**. Zainstaluj poprawną wersję zgodnie z platformą, z której korzystasz. Po zainstalowaniu środowiska uruchomieniowego node.js należy ponownie uruchomić Visual Studio Code w celu zaimplementowania zmian. 

Wybierz pozycję **Uruchom i Debuguj** lub naciśnij **klawisze CTRL + SHIFT + D** , aby rozpocząć debugowanie. Zostanie wyświetlone pole kombi, w którym można wybrać **node.js** jako środowisko uruchomieniowe. Jeśli zainstalowano tylko node.js, jest on używany domyślnie. W razie potrzeby za pomocą klawisza F11 powinno być możliwe przechodzenie przez kod i w pliku satelity. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics uruchamiania i debugowania UDF](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>Debuguj zdefiniowane przez użytkownika agregacje 

Możesz użyć metody Debug dla języka JavaScript UDF do debugowania agregacji zdefiniowanych przez użytkownika (UDA). W tym przykładzie UDA jest dodawany do pliku zapytania *. asaql* i pliku testowego.

Podobnie jak w przypadku formatu UDF, należy dołączyć wywołanie do UDA, aby upewnić się, że projekt zostanie skompilowany po wprowadzeniu zmian. 

> [!div class="mx-imgBorder"]
> ![Dodawanie uda do asaql](./media/debug-user-defined-functions/asaql-uda.png)

W pliku *Test_UDA.js* odwołujesz się do pliku uda, podobnie jak w przypadku formatu UDF. Ponadto należy wywołać `main()` , `init()` , i `accumulate()` . `accumulate()`Metoda jest wywoływana w pętli, aby umieścić wartości w stosie stanu. `computeresult()`Metoda jest wywoływana w celu utworzenia końcowego zapytania. 

> [!div class="mx-imgBorder"]
> ![Plik testowy UDA](./media/debug-user-defined-functions/uda-test.png)

Podobnie jak w przypadku przykładu UDF, należy dodać kod do samego UDA, aby uwidocznić odpowiednie metody.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Kod dodany do UDA](./media/debug-user-defined-functions/uda-expose-methods.png)

Wybierz pozycję **Uruchom i Debuguj** lub naciśnij **klawisze CTRL + SHIFT + D** , aby rozpocząć debugowanie. Zostanie wyświetlone pole kombi, w którym można wybrać **node.js** jako środowisko uruchomieniowe. Jeśli zainstalowano tylko node.js, jest on używany domyślnie. W razie potrzeby za pomocą klawisza F11 powinno być możliwe przechodzenie przez kod i w pliku satelity.

> [!div class="mx-imgBorder"]
> ![Stream Analytics uruchamianie i debugowanie uda](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>Następne kroki

* [Lokalne opracowywanie i debugowanie zadań Azure Stream Analytics](develop-locally.md)
* [Debuguj Azure Stream Analytics zapytań lokalnie przy użyciu diagramu zadań w programie Visual Studio](debug-locally-using-job-diagram.md)
* [Funkcje zdefiniowane przez użytkownika w Azure Stream Analytics](functions-overview.md)
 
