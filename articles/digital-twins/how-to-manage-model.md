---
title: Zarządzanie modelami DTDL
titleSuffix: Azure Digital Twins
description: Zobacz jak tworzyć, edytować i usuwać model w usłudze Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 86efd7781c7353b4846c77eba01ed34fb63067ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433254"
---
# <a name="manage-azure-digital-twins-models"></a>Zarządzanie modelami Digital bliźniaczych reprezentacji na platformie Azure

Można zarządzać [modelami](concepts-models.md) , dla których wystąpienie usługi Azure Digital bliźniaczych reprezentacji wie o korzystaniu z [**interfejsów API DigitalTwinModels**](/rest/api/digital-twins/dataplane/models), [zestawu SDK platformy .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client)lub [interfejsu wiersza polecenia platformy Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md). 

Operacje zarządzania obejmują przekazywanie, sprawdzanie poprawności, pobieranie i usuwanie modeli. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-models"></a>Sposoby zarządzania modelami

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-models"></a>Tworzenie modeli

Modele dla usługi Azure Digital bliźniaczych reprezentacji są zapisywane w DTDL i zapisywane jako pliki *JSON* . Istnieje również [rozszerzenie DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) dostępne dla [Visual Studio Code](https://code.visualstudio.com/), które zapewnia weryfikację składni i inne funkcje do ułatwienia pisania dokumentów DTDL.

Rozważmy przykład, w którym Szpital chce cyfrowo reprezentować pokoje. Każde pomieszczenie zawiera inteligentny dozownik SOAP do monitorowania ręcznych i czujników do monitorowania ruchu w pokoju.

Pierwszym krokiem w kierunku rozwiązania jest utworzenie modeli do reprezentowania aspektów szpitala. Pokój pacjenta w tym scenariuszu może być opisany w następujący sposób:

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> Jest to Przykładowa treść pliku JSON, w którym model jest zdefiniowany i zapisany do przekazania w ramach projektu klienta. Wywołanie interfejsu API REST, z drugiej strony, pobiera tablicę definicji modelu, takich jak powyższa część (która jest mapowana na zestaw `IEnumerable<string>` SDK platformy .NET). Aby używać tego modelu bezpośrednio w interfejsie API REST, umieść go w nawiasach.

Ten model definiuje nazwę i unikatowy identyfikator pokoju pacjenta oraz właściwości przedstawiające liczbę odwiedzających i stan odmycia (te liczniki zostaną zaktualizowane z czujników ruchu i inteligentnych rozdzielaczy SOAP) i zostaną użyte razem w celu obliczenia *handwash wartości procentowej* . Model definiuje również relację *hasDevices*, która będzie używana do łączenia wszelkich [cyfrowych bliźniaczych reprezentacji](concepts-twins-graph.md) opartych na tym modelu *pokoju* na rzeczywistych urządzeniach.

Korzystając z tej metody, można wykonać Definiowanie modeli dla danych szpitalnych, stref lub samego szpitala.

### <a name="validate-syntax"></a>Weryfikuj składnię

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>Przekazywanie modeli

Po utworzeniu modeli można je przekazać do wystąpienia usługi Azure Digital bliźniaczych reprezentacji.

Gdy wszystko jest gotowe do przekazania modelu, można użyć następującego fragmentu kodu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Zwróć uwagę, że `CreateModels` Metoda akceptuje wiele plików w jednej pojedynczej transakcji. Oto przykład, który ilustruje:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

Pliki modelu mogą zawierać więcej niż jeden model. W takim przypadku modele muszą być umieszczone w tablicy JSON. Na przykład:

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

Przy przekazywaniu pliki modelu są sprawdzane przez usługę.

## <a name="retrieve-models"></a>Pobierz modele

Można wyświetlać i pobierać modele przechowywane w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. 

Poniżej przedstawiono następujące opcje:
* Pobieranie pojedynczego modelu
* Pobierz wszystkie modele
* Pobieranie metadanych i zależności dla modeli

Oto kilka przykładowych wywołań:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Interfejs API wywołuje pobieranie modeli wszystkich zwracanych `DigitalTwinsModelData` obiektów. `DigitalTwinsModelData` zawiera metadane dotyczące modelu przechowywanego w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, takie jak nazwa, DTMI i Data utworzenia modelu. `DigitalTwinsModelData`Obiekt również opcjonalnie zawiera sam model. W zależności od parametrów można w ten sposób użyć wywołań pobierania, aby pobrać tylko metadane (co jest przydatne w scenariuszach, w których chcesz wyświetlić listę dostępnych narzędzi, na przykład) lub cały model.

`RetrieveModelWithDependencies`Wywołanie zwraca nie tylko żądany model, ale również wszystkie modele, od których zależy żądany model.

Modele nie zawsze są zwracane w dokładnie formularzu dokumentu, w którym zostały przekazane. Usługa Azure Digital bliźniaczych reprezentacji gwarantuje, że formularz zwrotny będzie semantycznie równoważny. 

## <a name="update-models"></a>Aktualizowanie modeli

Po przekazaniu modelu do wystąpienia usługi Azure Digital bliźniaczych reprezentacji cały interfejs modelu jest niezmienny. Oznacza to, że nie ma tradycyjnego "edytowania" modeli. Usługa Azure Digital bliźniaczych reprezentacji również nie zezwala na ponowne przekazywanie tego samego modelu.

Zamiast tego, jeśli chcesz wprowadzić zmiany do modelu, na przykład aktualizację `displayName` lub — w tym celu należy `description` przekazać **nowszą wersję** modelu. 

### <a name="model-versioning"></a>Wersje modelu danych

Aby utworzyć nową wersję istniejącego modelu, Zacznij od DTDL oryginalnego modelu. Aktualizowanie, Dodawanie lub usuwanie pól, które chcesz zmienić.

Następnie oznacz ją jako nowszą wersję modelu, aktualizując `id` pole modelu. Ostatnia sekcja identyfikatora modelu, po `;` , reprezentuje numer modelu. Aby wskazać, że jest to teraz bardziej zaktualizowana wersja tego modelu, Zwiększ liczbę na końcu `id` wartości do dowolnej liczby większej niż bieżący numer wersji.

Na przykład jeśli poprzedni Identyfikator modelu wyglądał następująco:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

Wersja 2 tego modelu może wyglądać następująco:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Następnie Przekaż nową wersję modelu do wystąpienia. 

Ta wersja modelu będzie następnie dostępna w Twoim wystąpieniu do użycia na potrzeby cyfrowego bliźniaczych reprezentacji. Nie **zastępuje wcześniejszych** wersji modelu, więc wiele wersji modelu będzie współistnieć w wystąpieniu do momentu jego [usunięcia](#remove-models).

### <a name="impact-on-twins"></a>Wpływ na bliźniaczych reprezentacji

Po utworzeniu nowego sznurka, ponieważ nowa wersja modelu i stara wersja modelu współistnieć, nowe sznurki mogą korzystać z nowej wersji modelu lub starszej wersji.

Oznacza to również, że przekazywanie nowej wersji modelu nie wpływa automatycznie na istniejące bliźniaczych reprezentacji. Istniejący bliźniaczych reprezentacji będzie po prostu mieć wystąpienia starej wersji modelu.

Aby zaktualizować istniejące bliźniaczych reprezentacji do nowej wersji modelu, należy zastosować ich poprawki, zgodnie z opisem w sekcji [*aktualizowanie modelu cyfrowej sieci bliźniaczyej*](how-to-manage-twin.md#update-a-digital-twins-model) , how to *: Manage Digital bliźniaczych reprezentacji*. W ramach tej samej poprawki należy zaktualizować zarówno **Identyfikator modelu** (do nowej wersji), jak i **wszystkie pola, które muszą zostać zmienione na sznurze, aby były zgodne z nowym modelem**.

## <a name="remove-models"></a>Usuń modele

Modele można również usunąć z usługi, na jeden z dwóch sposobów:
* **Likwidowanie** : po zlikwidowaniu modelu nie można już używać go do tworzenia nowych bliźniaczych reprezentacji cyfrowych. Nie ma to wpływu na istniejące bliźniaczych reprezentacji cyfrowe, które już używają tego modelu, więc można je zaktualizować za pomocą elementów, takich jak zmiany właściwości i Dodawanie lub usuwanie relacji.
* **Usunięcie** : spowoduje to całkowite usunięcie modelu z rozwiązania. Wszystkie bliźniaczych reprezentacji korzystające z tego modelu nie są już skojarzone z żadnym prawidłowym modelem, dlatego są traktowane tak, jakby nie miały modelu. Nadal można odczytywać te bliźniaczych reprezentacji, ale nie będzie można wprowadzać żadnych aktualizacji, dopóki nie zostaną ponownie przypisane do innego modelu.

Są to osobne funkcje, które nie wpływają na siebie, ale mogą być używane razem w celu stopniowego usuwania modelu. 

### <a name="decommissioning"></a>Likwidowanie

Oto kod likwidowania modelu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

Stan likwidowania modelu jest uwzględniany w `ModelData` rekordach zwracanych przez interfejsy API pobierania modelu.

### <a name="deletion"></a>Usunięcie

Można usunąć wszystkie modele w wystąpieniu jednocześnie lub można wykonać je pojedynczo.

Aby zapoznać się z przykładem sposobu usuwania wszystkich modeli, należy pobrać przykładową aplikację używaną w [*samouczku: Poznaj podstawy za pomocą przykładowej aplikacji klienckiej*](tutorial-command-line-app.md). Plik *CommandLoop. cs* wykonuje tę `CommandDeleteAllModels` funkcję w funkcji.

Pozostała część tej sekcji przerywa usuwanie modelu w bardziej szczegółowy sposób i pokazuje, jak to zrobić dla pojedynczego modelu.

#### <a name="before-deletion-deletion-requirements"></a>Przed usunięciem: wymagania dotyczące usuwania

Ogólnie rzecz biorąc, modele można usuwać w dowolnym momencie.

Wyjątkiem są modele, od których zależą inne modele, z `extends` relacją lub składnikiem. Na przykład jeśli model *ConferenceRoom* rozszerza model *pokoju* i ma model *ACUnit* jako składnik, nie można usunąć *pokoju* ani *ACUnit* do momentu, gdy *ConferenceRoom* usunie te odpowiednie odwołania. 

Można to zrobić, aktualizując model zależny, aby usunąć zależności, lub całkowicie usuwając model zależny.

#### <a name="during-deletion-deletion-process"></a>Podczas usuwania: proces usuwania

Nawet jeśli model spełnia wymagania, aby natychmiast je usunąć, warto najpierw wykonać kilka kroków, aby uniknąć niezamierzonych konsekwencji bliźniaczych reprezentacji. Poniżej przedstawiono kilka kroków, które mogą ułatwić zarządzanie procesem:
1. Najpierw likwidowanie modelu
2. Poczekaj kilka minut, aby upewnić się, że usługa przetworzyła wszystkie żądania utworzenia łodzi z ostatniej minuty wysłane przed zlikwidowaniem
3. Bliźniaczych reprezentacji zapytania według modelu, aby zobaczyć wszystkie bliźniaczych reprezentacji, które używają obecnie likwidowanego modelu
4. Usuń bliźniaczych reprezentacji, jeśli nie są już potrzebne, lub w razie potrzeby poproś ich o nowy model. Możesz również zrezygnować z nich, a w takim przypadku stają się one bliźniaczych reprezentacji bez modeli po usunięciu modelu. Zapoznaj się z następną sekcją, aby poznać skutki tego stanu.
5. Poczekaj kilka minut, aby upewnić się, że zmiany zostały percolated przez
6. Usuń model 

Aby usunąć model, użyj tego wywołania:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

#### <a name="after-deletion-twins-without-models"></a>Po usunięciu: bliźniaczych reprezentacji bez modeli

Po usunięciu modelu wszelkie bliźniaczych reprezentacji cyfrowe, które były używane przez model, są teraz uważane za bez modelu. Należy zauważyć, że nie ma zapytania, które może dać listę wszystkich bliźniaczych reprezentacji w tym stanie — mimo że *można* nadal wysyłać zapytania do bliźniaczych reprezentacji przez usunięty model, aby wiedzieć, jakie bliźniaczych reprezentacji mają wpływ.

Poniżej znajdują się informacje o tym, co można zrobić i czego nie można zrobić z bliźniaczych reprezentacji, które nie mają modelu.

**Możliwe** czynności:
* Badaj sznurek
* Właściwości odczytu
* Odczytaj relacje wychodzące
* Dodawanie i usuwanie relacji przychodzących (podobnie jak w przypadku innych bliźniaczych reprezentacji można nadal tworzyć relacje *z* tym przędzą)
  - `target`W definicji relacji można nadal odzwierciedlić DTMI usuniętego modelu. Relacja bez zdefiniowanego obiektu docelowego może również być dostępna w tym miejscu.
* Usuń relacje
* Usuwanie sznurka

Elementy, których **nie** możesz wykonać:
* Edytuj relacje wychodzące (jak w programie, relacje *z* tego przędzy z innymi bliźniaczych reprezentacji)
* Edytuj właściwości

#### <a name="after-deletion-re-uploading-a-model"></a>Po usunięciu: przekazanie modelu

Po usunięciu modelu można później zdecydować się na przekazanie nowego modelu o takim samym IDENTYFIKATORze jak usunięty. Oto co się dzieje w tym przypadku.
* W perspektywie magazynu rozwiązań jest to taka sama jak w przypadku przekazywania zupełnie nowego modelu. Usługa nie pamięta, że stara została kiedykolwiek przekazana.   
* Jeśli w grafie istnieją jakiekolwiek pozostałe bliźniaczych reprezentacji, odwołujące się do usuniętego modelu, nie są już oddzielone; Ten identyfikator modelu jest ponownie ważny z nową definicją. Jeśli jednak Nowa definicja modelu różni się od definicji modelu, która została usunięta, te bliźniaczych reprezentacji mogą mieć właściwości i relacje, które pasują do usuniętej definicji i nie są prawidłowe dla nowej.

Usługa Azure Digital bliźniaczych reprezentacji nie uniemożliwia tego stanu, dlatego należy zachować ostrożność, aby odpowiednio zastosować poprawkę bliźniaczych reprezentacji w celu upewnienia się, że pozostaną one prawidłowe przez przełącznik definicji modelu.

## <a name="next-steps"></a>Następne kroki

Zobacz, jak tworzyć i zarządzać bliźniaczych reprezentacjiami cyfrowymi w oparciu o Twoje modele:
* [*Instrukcje: Zarządzanie bliźniaczych reprezentacji cyfrowym*](how-to-manage-twin.md)