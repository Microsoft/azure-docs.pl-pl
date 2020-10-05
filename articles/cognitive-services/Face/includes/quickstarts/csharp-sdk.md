---
title: Biblioteka klienta platformy .NET — szybki start
description: Użyj biblioteki klienta twarzy dla platformy .NET, aby wykrywać twarze, znajdować podobne (Wyszukiwanie twarzy według obrazu), identyfikować twarze (Wyszukiwanie twarzy) i migrować dane twarzy.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 09/17/2020
ms.author: pafarley
ms.openlocfilehash: 80255790129468857e1115f3034516f04bc86d26
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91322980"
---
Wprowadzenie do rozpoznawania twarzy przy użyciu biblioteki klienta twarzy dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa twarzy zapewnia dostęp do zaawansowanych algorytmów służących do wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj biblioteki klienta programu Front for .NET, aby:

* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)
* [Utwórz migawkę migracji danych](#take-a-snapshot-for-data-migration)

[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Przykłady](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Wymagania wstępne

* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" Utwórz zasób czołowy "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API rozpoznawania twarzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Po otrzymaniu klucza i punktu końcowego [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla adresu URL klucza i punktu końcowego, `FACE_SUBSCRIPTION_KEY` odpowiednio nazwane i `FACE_ENDPOINT` .

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

Utwórz nową aplikację platformy .NET Core w preferowanym edytorze lub środowisku IDE. 

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `face-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```dotnetcli
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Dodaj następujące `using` dyrektywy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

W `Main` metodzie aplikacji Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj bibliotekę klienta programu Front for .NET przy użyciu następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre z najważniejszych funkcji biblioteki klienckiej platformy .NET programu Front:

|Nazwa|Opis|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Ta klasa reprezentuje autoryzację do korzystania z usługi twarz i jest potrzebna dla wszystkich funkcji funkcjonalnych. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonywać przy użyciu twarzy ludzkich. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej części obrazu. Można go użyć do pobierania szczegółowych informacji o kroju.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Ta klasa zarządza opartymi na chmurze konstrukcjami **FaceList** , w których jest przechowywany asortyment zestawu twarzy. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Ta klasa zarządza konstrukcjami **osób** , które są przechowywane w chmurze, które przechowują zestaw twarzy należących do jednej osoby.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Ta klasa **zarządza konstrukcjami** , które są przechowywane w chmurze, przechowujących zestaw obiektów **osób** . |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Ta klasa zarządza funkcją migawek. Można jej użyć do tymczasowego zapisania wszystkich danych platformy opartej na chmurze i przeprowadzenia migracji tych danych do nowej subskrypcji platformy Azure. |

## <a name="code-examples"></a>Przykłady kodu

Poniższe fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienckiej programu Front for .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)
* [Utwórz migawkę migracji danych](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start założono, że [utworzono zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla Twojego klucza i punktu końcowego, o nazwie `FACE_SUBSCRIPTION_KEY` i `FACE_ENDPOINT` .

W nowej metodzie Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

Prawdopodobnie chcesz wywołać tę metodę w `Main` metodzie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>Zadeklaruj pola pomocnika

Następujące pola są potrzebne w przypadku kilku operacji, które zostaną później dodane. W katalogu głównym klasy Zdefiniuj następujący ciąg adresu URL. Ten adres URL wskazuje folder obrazów przykładowych.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Zdefiniuj ciągi w taki sposób, aby wskazywały różne typy modeli rozpoznawania. Później będzie można określić, który model rozpoznawania ma być używany do wykrywania kroju. Aby uzyskać informacje na temat tych opcji, zobacz temat [Określanie modelu rozpoznawania](../../Face-API-How-to-Topics/specify-recognition-model.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Dodaj następujące wywołanie metody do metody **Main** . Należy zdefiniować kolejną metodę. Ostatnia Operacja wykrywania zajmie obiektem **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** , adresem URL obrazu i modelem rozpoznawania.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Pobieranie wykrytych obiektów czołowych

W następnym bloku kodu `DetectFaceExtract` Metoda wykrywa twarze w trzech obrazach pod podanym adresem URL i tworzy listę obiektów **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** w pamięci programu. Lista wartości **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** określa funkcje do wyodrębnienia. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Wyświetl wykryte dane dotyczące kroju

Pozostała część `DetectFaceExtract` metody analizuje i drukuje dane atrybutów dla każdej wykrytej cechy. Każdy atrybut musi być określony osobno w wywołaniu oryginalnego interfejsu API wykrywania kroju (na liście **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** ). Poniższy kod przetwarza każdy atrybut, ale prawdopodobnie trzeba będzie tylko użyć jednego lub kilku.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedyncze wykryte buźki (Źródło) i przeszukuje zestaw innych twarzy (target), aby znaleźć dopasowania (Wyszukiwanie twarzy według obrazu). Po znalezieniu dopasowania program drukuje identyfikator dopasowanej do konsoli.

### <a name="detect-faces-for-comparison"></a>Wykrywanie twarzy do porównania

Najpierw Zdefiniuj metodę wykrywania drugiej czołowej. Należy wykryć twarze w obrazach, zanim będzie można je porównać, a ta metoda wykrywania jest zoptymalizowana pod kątem operacji porównania. Nie wyodrębnimy szczegółowych atrybutów kroju, takich jak w powyższej sekcji, i używa innego modelu rozpoznawania.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Znajdź dopasowania

Poniższa metoda wykrywa twarze w zestawie obrazów docelowych i w jednym obrazie źródłowym. Następnie porównuje je i odnajduje wszystkie obrazy docelowe podobne do obrazu źródłowego.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Drukuj dopasowania

Poniższy kod drukuje szczegóły dopasowania do konsoli programu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Identyfikowanie kroju

Operacja identyfikowania pobiera obraz osoby (lub wielu osób) i szuka tożsamości każdej twarzy na obrazie (wyszukiwanie rozpoznawania twarzy). Porównuje każdą wykrytą twarzą z **osobą**, która jest bazą danych różnych obiektów **osób** , których funkcje twarzy są znane. Aby wykonać operację identyfikowania, należy najpierw utworzyć i przeszkolić **odbiorcę**

### <a name="create-and-train-a-person-group"></a>Tworzenie i uczenie grupy osób

Poniższy kod tworzy **odbiorcę** z sześciu różnych obiektów **osób** . Kojarzy każdej **osoby** z zestawem przykładowych obrazów, a następnie pociąga, aby rozpoznać każdą osobę według ich cech twarzy. Obiekty **osób** i **osób** są używane w operacjach sprawdzania, identyfikowania i grupowania.

#### <a name="create-persongroup"></a>Utwórz osobę

Zadeklaruj zmienną ciągu w elemencie głównym klasy, aby reprezentować identyfikator grupy **osób** , którą utworzysz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

W nowej metodzie Dodaj następujący kod. Ta metoda spowoduje przeprowadzenie operacji identyfikacji. Pierwszy blok kodu kojarzy nazwy osób z przykładowymi obrazami.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Następnie Dodaj następujący kod, aby utworzyć obiekt **osoby** dla każdej osoby w słowniku i dodać dane dotyczące kroju z odpowiednich obrazów. Każdy obiekt **osoby** jest skojarzony z tą samą **osobą** przy użyciu unikatowego ciągu identyfikatora. Pamiętaj, aby przekazać zmienne `client` , `url` i `RECOGNITION_MODEL1` do tej metody.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>Szkolenie zespołu

Po wyodrębnieniu danych kroju z obrazów i posortowaniu ich w różnych obiektach **osób** należy przeszkolić **osobę** , aby zidentyfikować funkcje wizualne skojarzone z poszczególnymi obiektami **osób** . Poniższy kod wywołuje metodę synchronicznego **pouczenia** i sonduje wyniki, drukując stan do konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

Ta grupa **osób** i skojarzone z nią obiekty **osób** są teraz gotowe do użycia w operacjach sprawdzania, identyfikowania lub grupowania.

### <a name="get-a-test-image"></a>Pobierz obraz testowy

Zwróć uwagę, że kod służący do [tworzenia i uczenia grupy osób](#create-and-train-a-person-group) definiuje zmienną `sourceImageFileName` . Ta zmienna odnosi się do obrazu źródła &mdash; obrazu zawierającego osoby do zidentyfikowania.

### <a name="identify-faces"></a>Identyfikowanie twarzy

Poniższy kod pobiera obraz źródłowy i tworzy listę wszystkich wykrytych twarzy na obrazie. Są to twarze, które zostaną zidentyfikowane względem **osoby**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Następny fragment kodu wywołuje operację **IdentifyAsync** i drukuje wyniki do konsoli. W tym miejscu usługa próbuje dopasować wszystkie powierzchnie z obrazu źródłowego do **osoby** w danej **osobie**. Spowoduje to zamknięcie metody identyfikacji.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Utwórz migawkę migracji danych

Funkcja migawek pozwala przenosić zapisane dane, takie jak wyszkolone **osoby**, do innej subskrypcji platformy Azure Cognitive Services. Możesz chcieć użyć tej funkcji, jeśli na przykład utworzono obiekt obiektu **osoby** przy użyciu bezpłatnej subskrypcji i chcesz przeprowadzić migrację go do płatnej subskrypcji. Aby zapoznać się z omówieniem funkcji migawek, zobacz [Migrowanie danych](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) z obszaru.

W tym przykładzie zostanie przeprowadzona migracja grupy **osób** utworzonych w temacie [Tworzenie i uczenie osoby](#create-and-train-a-person-group). Możesz najpierw wykonać tę sekcję lub utworzyć własne konstrukcje danych (-y) do migracji.

### <a name="set-up-target-subscription"></a>Skonfiguruj subskrypcję docelową

Najpierw musisz mieć drugą subskrypcję platformy Azure z zasobem czołowym; można to zrobić, wykonując czynności opisane w sekcji [Konfigurowanie](#setting-up) . 

Następnie Zdefiniuj następujące zmienne w `Main` metodzie programu. Należy utworzyć nowe zmienne środowiskowe dla identyfikatora subskrypcji Twojego konta platformy Azure, a także klucz, punkt końcowy i Identyfikator subskrypcji nowego konta (docelowego). 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

Na potrzeby tego przykładu Zadeklaruj zmienną dla identyfikatora obiektu **docelowego,** &mdash; który należy do nowej subskrypcji, do której zostaną skopiowane dane.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Uwierzytelnianie klienta docelowego

Następnie Dodaj kod w celu uwierzytelnienia pomocniczej subskrypcji programu Marketo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Użyj migawki

Pozostałe operacje migawek muszą mieć miejsce w metodzie asynchronicznej. 

1. Pierwszym krokiem jest **wykonanie** migawki, która zapisuje dane pierwotnej subskrypcji w tymczasowej lokalizacji w chmurze. Ta metoda zwraca identyfikator używany do wykonywania zapytań dotyczących stanu operacji.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take)]

1. Następnie wykonaj zapytanie o identyfikator do momentu zakończenia operacji.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take_wait)]

1. Następnie użyj operacji **Zastosuj** , aby zapisać swoje dane z Twojej subskrypcji docelowej. Ta metoda zwraca również wartość identyfikatora.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Ponownie wykonaj zapytanie o nowy identyfikator do momentu zakończenia operacji.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Na koniec wykonaj blok try/catch i Zakończ metodę.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_trycatch)]

W tym momencie nowy obiekt obiektu **osoby** powinien mieć te same dane co oryginał i powinny być dostępne z nowej subskrypcji (docelowej) platformy Azure.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację rozpoznawania aplikacji z katalogu aplikacji za pomocą `dotnet run` polecenia.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Jeśli utworzono **osobę** z tego przewodnika Szybki Start i chcesz ją usunąć, uruchom następujący kod w programie:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Zdefiniuj metodę usuwania przy użyciu następującego kodu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

Ponadto w przypadku migrowania danych przy użyciu funkcji Snapshot w tym przewodniku Szybki Start należy również usunąć **osobę** , która została zapisana do subskrypcji docelowej.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start pokazano, jak używać biblioteki klienckiej twarzy dla platformy .NET, aby wykonywać podstawowe zadania rozpoznawania twarzy. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie interfejs API rozpoznawania twarzy (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Co to jest usługa rozpoznawania twarzy?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
