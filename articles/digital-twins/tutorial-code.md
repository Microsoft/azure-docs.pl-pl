---
title: 'Samouczek: kod aplikacji klienckiej'
titleSuffix: Azure Digital Twins
description: Samouczek, w którym można napisać minimalny kod aplikacji klienckiej przy użyciu zestawu SDK platformy .NET (C#).
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: bd3ba88650161bd11a24697b4ff8575d307120e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102424465"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Samouczek: kodowanie za pomocą cyfrowych interfejsów API usługi Azure bliźniaczych reprezentacji

W przypadku deweloperów pracujących z usługą Azure Digital bliźniaczych reprezentacji można napisać aplikację kliencką do współdziałania z ich wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Ten samouczek ukierunkowany na dewelopera zawiera wprowadzenie do programowania w usłudze Azure Digital bliźniaczych reprezentacji Service przy użyciu [zestawu Azure Digital bliźniaczych reprezentacji SDK dla platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client). Przeprowadzi Cię przez proces pisania aplikacji klienckiej w języku C# krok po kroku, rozpoczynając od podstaw.

> [!div class="checklist"]
> * Konfigurowanie projektu
> * Wprowadzenie do kodu projektu   
> * Pełny przykładowy kod
> * Czyszczenie zasobów
> * Następne kroki

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku zostanie użyty wiersz polecenia dla Instalatora i pracy w projekcie. W związku z tym możesz użyć dowolnego edytora kodu, aby zapoznać się z ćwiczeniami.

Co należy zrobić:
* Dowolny edytor kodu
* **Platforma .NET Core 3,1** na komputerze deweloperskim. Tę wersję zestaw .NET Core SDK można pobrać z wielu platform [pobrać z programu .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

### <a name="prepare-an-azure-digital-twins-instance"></a>Przygotowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Konfigurowanie projektu

Gdy wszystko będzie gotowe do pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, Rozpocznij Konfigurowanie projektu aplikacji klienckiej. 

Otwórz wiersz polecenia lub inne okno konsoli na maszynie i Utwórz pusty katalog projektu, w którym chcesz przechowywać swoją służbę w trakcie tego samouczka. Nazwij katalog, którego chcesz dotyczyć (na przykład *DigitalTwinsCodeTutorial*).

Przejdź do nowego katalogu.

W katalogu projektu **Utwórz pusty projekt aplikacji konsoli .NET**. W oknie polecenia można uruchomić następujące polecenie, aby utworzyć minimalny projekt w języku C# dla konsoli programu:

```cmd/sh
dotnet new console
```

Spowoduje to utworzenie kilku plików znajdujących się w katalogu, w tym jednego o nazwie *program. cs* , w którym będzie można napisać większość kodu.

Pozostaw otwarte okno polecenia, ponieważ będziesz nadal korzystać z niego w całym samouczku.

Następnie **Dodaj dwie zależności do projektu** , które będą konieczne do pracy z usługą Azure Digital bliźniaczych reprezentacji. Pierwszy to pakiet [usługi Azure Digital bliźniaczych reprezentacji SDK dla platformy .NET](/dotnet/api/overview/azure/digitaltwins/client), a drugi zawiera narzędzia, które ułatwiają uwierzytelnianie na platformie Azure.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
```

## <a name="get-started-with-project-code"></a>Wprowadzenie do kodu projektu

W tej sekcji rozpocznie się pisanie kodu dla nowego projektu aplikacji do pracy z usługą Azure Digital bliźniaczych reprezentacji. Objęte są następujące działania:
* Uwierzytelnianie w usłudze
* Przekazywanie modelu
* Przechwytywanie błędów
* Tworzenie bliźniaczych reprezentacji cyfrowych
* Tworzenie relacji
* Wykonywanie zapytania dotyczącego cyfrowego bliźniaczych reprezentacji

Istnieje również sekcja przedstawiająca kompletny kod na końcu samouczka. Można jej użyć jako odwołania do sprawdzenia programu w trakcie pracy.

Aby rozpocząć, Otwórz plik File *. cs* w dowolnym edytorze kodu. Zobaczysz minimalny szablon kodu, który wygląda następująco:

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="Fragment przykładowego kodu. Istnieje jedna instrukcja &quot;using System;&quot;, przestrzeń nazw o nazwie DigitalTwinsCodeTutorial; Klasa w przestrzeni nazw o nazwie program; i Metoda Main w klasie ze standardowym podpisem &quot;static void Main (String [] args)&quot;. Metoda Main zawiera Hello world instrukcji Print." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Najpierw Dodaj wiersze w `using` górnej części kodu, aby ściągnąć w zależności od potrzeb.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

Następnie dodasz kod do tego pliku w celu wypełnienia niektórych funkcji. 

### <a name="authenticate-against-the-service"></a>Uwierzytelnianie w usłudze

W pierwszej kolejności aplikacja musi być uwierzytelniana w usłudze Azure Digital bliźniaczych reprezentacji. Następnie można utworzyć klasę klienta usługi, aby uzyskać dostęp do funkcji zestawu SDK.

Aby można było przeprowadzić uwierzytelnianie, potrzebna jest *Nazwa hosta* wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

W programie *program. cs* wklej następujący kod poniżej "Hello, World!" Linia wydruku w `Main` metodzie. Ustaw wartość `adtInstanceUrl` na *nazwę hosta* usługi Azure Digital bliźniaczych reprezentacji.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

Zapisz plik. 

W oknie polecenia Uruchom kod przy użyciu tego polecenia: 

```cmd/sh
dotnet run
```

Spowoduje to przywrócenie zależności od pierwszego uruchomienia, a następnie wykonanie programu. 
* Jeśli wystąpi błąd, program drukuje *klienta usługi, który został utworzony — gotowy do użycia*.
* Ponieważ w tym projekcie nie ma jeszcze żadnej obsługi błędów, w przypadku wystąpienia problemów zostanie wyświetlony wyjątek zgłoszony przez kod.

### <a name="upload-a-model"></a>Przekaż model

Usługa Azure Digital bliźniaczych reprezentacji nie ma słownika domeny wewnętrznej. Typy elementów w środowisku, które można reprezentować w usłudze Azure Digital bliźniaczych reprezentacji, są definiowane przez użytkownika przy użyciu **modeli**. [Modele](concepts-models.md) są podobne do klas w języku programowania zorientowanym na obiekty; udostępniają one szablony zdefiniowane przez użytkownika na potrzeby [cyfrowego bliźniaczych reprezentacji](concepts-twins-graph.md) i tworzenia wystąpienia w przyszłości. Są one zapisywane w języku podobnym do notacji JSON o nazwie **Digital bliźniaczych reprezentacji Definition Language (DTDL)**.

Pierwszym krokiem tworzenia rozwiązania Digital bliźniaczych reprezentacji na platformie Azure jest zdefiniowanie co najmniej jednego modelu w pliku DTDL.

W katalogu, w którym został utworzony projekt, Utwórz nowy plik *JSON* o nazwie *SampleModel.json*. Wklej w następującej treści pliku: 

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Jeśli używasz programu Visual Studio na potrzeby tego samouczka, możesz chcieć wybrać nowo utworzony plik JSON i ustawić właściwość *Kopiuj do katalogu wyjściowego* w Inspektorze właściwości, aby *skopiować w przypadku, gdy nowszy* lub *Kopiuj zawsze*. Umożliwi to programowi Visual Studio znalezienie pliku JSON z domyślną ścieżką podczas uruchamiania programu przy użyciu klawisza **F5** w pozostałej części tego samouczka.

> [!TIP] 
> Istnieje [przykład modułu sprawdzania poprawności](/samples/azure-samples/dtdl-validator/dtdl-validator) języka niezależny od DTDL, którego można użyć do sprawdzenia dokumentów modelu, aby upewnić się, że DTDL jest prawidłowy. Jest ona oparta na bibliotece analizatora DTDL, którą można dowiedzieć się więcej na temat postanowień [*: analizowanie i weryfikowanie modeli*](how-to-parse-models.md).

Następnie Dodaj kod do *programu. cs* , aby przekazać model, który został właśnie utworzony w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.

Najpierw Dodaj kilka `using` instrukcji na początku pliku:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Następnie przygotuj się do użycia metod asynchronicznych w zestawie SDK usługi C#, zmieniając `Main` sygnaturę metody, aby umożliwić wykonywanie asynchroniczne. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> Użycie `async` nie jest ściśle wymagane, ponieważ zestaw SDK udostępnia również synchroniczne wersje wszystkich wywołań. W tym samouczku przedstawiono sposób użycia programu `async` .

Następnie jest dostarczany pierwszy bit kodu, który współdziała z usługą Azure Digital bliźniaczych reprezentacji. Ten kod ładuje plik DTDL utworzony z dysku, a następnie przekazuje go do wystąpienia usługi Azure Digital bliźniaczych reprezentacji. 

Wklej następujący kod w obszarze kodu autoryzacji, który został dodany wcześniej.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs":::

W oknie polecenia Uruchom program za pomocą tego polecenia: 

```cmd/sh
dotnet run
```
"Przekaż model" zostanie wydrukowany w danych wyjściowych, wskazując, że ten kod został osiągnięty, ale nie ma jeszcze danych wyjściowych, aby wskazać, czy przekazywanie zakończyło się pomyślnie.

Aby dodać instrukcję Print, która przedstawia wszystkie modele, które zostały pomyślnie przekazane do wystąpienia, Dodaj następujący kod bezpośrednio po poprzedniej sekcji:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

**Przed ponownym uruchomieniem programu w celu przetestowania nowego kodu**, należy odwołać się do ostatniego uruchomienia programu, przekazano już swój model. Usługa Azure Digital bliźniaczych reprezentacji nie pozwoli na przekazanie tego samego modelu dwa razy, więc jeśli spróbujesz ponownie przekazać ten sam model, program powinien zgłosić wyjątek.

Z tego względu należy ponownie uruchomić program przy użyciu tego polecenia w oknie poleceń:

```cmd/sh
dotnet run
```

Program powinien zgłosić wyjątek. Podczas próby przekazania modelu, który został już przekazany, usługa zwraca błąd "złe żądanie" za pośrednictwem interfejsu API REST. W związku z tym zestaw SDK klienta Digital bliźniaczych reprezentacji platformy Azure będzie z kolei zgłaszał wyjątek dla każdego kodu powrotu usługi innego niż powodzenie. 

W następnej sekcji znajdują się informacje o wyjątkach, takich jak te i jak je obsłużyć w kodzie.

### <a name="catch-errors"></a>Błędy przechwytywania

Aby zapobiec awariom programu, można dodać kod wyjątku wokół kodu przekazanego przez model. Zawiń istniejące wywołanie klienta `await client.CreateModelsAsync(typeList)` w obsłudze try/catch, takie jak:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

Teraz, jeśli w oknie poleceń zostanie uruchomiony program z `dotnet run` teraz, zobaczysz kod błędu z powrotem. Dane wyjściowe z kodu tworzenia modelu przedstawiają ten błąd:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Dane wyjściowe programu, pokazujące komunikat &quot;409: żądanie usługi nie powiodło się. Stan: 409 (konflikt). ', po którym następuje wydruk o błędzie wskazujący, że dtmi: przykład: SampleModel; 1 już istnieje":::

Od tego momentu samouczek umieści wszystkie wywołania metod usługi w obsłudze try/catch.

### <a name="create-digital-twins"></a>Tworzenie cyfrowych reprezentacji bliźniaczych

Po przekazaniu modelu do usługi Azure Digital bliźniaczych reprezentacji można użyć tej definicji modelu do utworzenia **cyfrowego bliźniaczych reprezentacji**. [Digital bliźniaczych reprezentacji](concepts-twins-graph.md) to wystąpienia modelu i reprezentujące jednostki w środowisku biznesowym — takie jak czujniki w farmie, pokoje w budynku lub lampy w kabinie. Ta sekcja tworzy kilka bliźniaczych reprezentacji cyfrowych na podstawie przekazanego wcześniej modelu.

Dodaj następujący kod na końcu `Main` metody, aby utworzyć i zainicjować trzy bliźniaczych reprezentacji cyfrowe w oparciu o ten model.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

W oknie polecenia Uruchom program z `dotnet run` . W danych wyjściowych poszukaj wiadomości drukowania, które *sampleTwin-0*, *sampleTwin-1* i *sampleTwin-2* zostały utworzone. 

Następnie ponownie uruchom program. 

Należy zauważyć, że żaden błąd nie jest zgłaszany, gdy bliźniaczych reprezentacji jest tworzony drugi raz, nawet jeśli bliźniaczych reprezentacji już istnieje po pierwszym uruchomieniu. W przeciwieństwie do tworzenia modelu, tworzenie sznurka to, na poziomie REST, wywołanie *Put* z semantyką *upsert* . Oznacza to, że jeśli przędza już istnieje, próba utworzenia tego samego przędzy spowoduje jedynie zamienienie oryginalnego sznurka. Nie zgłoszono żadnego błędu.

### <a name="create-relationships"></a>Tworzenie relacji

Następnie można utworzyć **relacje** między utworzonym bliźniaczych reprezentacji, aby połączyć je z **wykresem bliźniaczym**. [Wykresy bliźniaczy](concepts-twins-graph.md) są używane do reprezentowania całego środowiska.

Dodaj **nową metodę statyczną** do `Program` klasy poniżej `Main` metody (kod ma teraz dwie metody):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Następnie Dodaj następujący kod na końcu `Main` metody, aby wywołać `CreateRelationship` metodę i użyć właśnie napisanego kodu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

W oknie polecenia Uruchom program z `dotnet run` . W danych wyjściowych Znajdź instrukcje Print informujące o tym, że dwie relacje zostały utworzone pomyślnie.

Należy pamiętać, że usługa Azure Digital bliźniaczych reprezentacji nie pozwala na utworzenie relacji, jeśli istnieje już inna relacja o takim samym IDENTYFIKATORze, więc jeśli program zostanie uruchomiony wielokrotnie, zobaczysz wyjątki podczas tworzenia relacji. Ten kod przechwytuje wyjątki i ignoruje je. 

### <a name="list-relationships"></a>Wyświetl relacje

Następny kod, który dodasz, umożliwia wyświetlenie listy utworzonych relacji.

Dodaj następującą **nową metodę** do `Program` klasy:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Następnie Dodaj następujący kod na końcu `Main` metody, aby wywołać `ListRelationships` kod:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

W oknie polecenia Uruchom program z `dotnet run` . Powinna zostać wyświetlona lista wszystkich relacji utworzonych w instrukcji wyjściowej, która wygląda następująco:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Dane wyjściowe programu, pokazujące komunikat &quot;przędza sampleTwin-0 jest połączona z: zawiera->sampleTwin-1,-Contains->sampleTwin-2&quot;" lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Bliźniaczych reprezentacji cyfrowe zapytania

Główną funkcją usługi Azure Digital bliźniaczych reprezentacji jest możliwość łatwego i wydajnego [wykonywania zapytań](concepts-query-language.md) na pytania dotyczące środowiska.

Ostatnia sekcja kodu do dodania w tym samouczku uruchamia zapytanie względem wystąpienia bliźniaczych reprezentacji Digital na platformie Azure. Zapytanie użyte w tym przykładzie zwraca wszystkie bliźniaczych reprezentacji cyfrowe w wystąpieniu.

Dodaj tę `using` instrukcję, aby umożliwić korzystanie z `JsonSerializer` klasy w celu uzyskania informacji o cyfrowej przędze:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Następnie Dodaj następujący kod na końcu `Main` metody:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

W oknie polecenia Uruchom program z `dotnet run` . Wszystkie bliźniaczych reprezentacji cyfrowe w tym wystąpieniu powinny być widoczne w danych wyjściowych.

## <a name="complete-code-example"></a>Pełny przykład kodu

W tym punkcie samouczka masz kompletną aplikację kliencką, która może wykonywać podstawowe działania związane z usługą Azure Digital bliźniaczych reprezentacji. W przypadku odwołania do kodu programu w *programie w programie w programie* zostanie wyświetlony następujący kod:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu tego samouczka możesz wybrać zasoby, które chcesz usunąć, w zależności od tego, co chcesz zrobić dalej.

* **Jeśli planujesz przejść do następnego samouczka**, wystąpienie użyte w tym samouczku może zostać ponownie użyte w następnym. Zasoby usługi Azure Digital bliźniaczych reprezentacji można przechowywać tutaj i pominąć pozostałe części tej sekcji.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Możesz również usunąć folder projektu z komputera lokalnego.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz aplikację kliencką programu .NET Console od podstaw. Zapisano kod dla tej aplikacji klienckiej w celu wykonywania podstawowych akcji w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji.

Przejdź do następnego samouczka, aby poznać czynności, które możesz wykonać za pomocą takiej przykładowej aplikacji klienckiej: 

> [!div class="nextstepaction"]
> [*Samouczek: Eksplorowanie podstaw za pomocą przykładowej aplikacji klienckiej*](tutorial-command-line-app.md)
