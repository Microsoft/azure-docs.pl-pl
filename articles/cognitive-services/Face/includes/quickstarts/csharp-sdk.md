---
title: Biblioteka klienta platformy .NET — szybki start
description: Użyj biblioteki klienta twarzy dla platformy .NET, aby wykrywać twarze, znajdować podobne (Wyszukiwanie twarzy według obrazu), identyfikować twarze (Wyszukiwanie twarzy) i migrować dane twarzy.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: ab99d574588989c84783e532fcf801dcaffdd54d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958310"
---
Wprowadzenie do rozpoznawania twarzy przy użyciu biblioteki klienta twarzy dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa twarzy zapewnia dostęp do zaawansowanych algorytmów służących do wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj biblioteki klienta programu Front for .NET, aby:

* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie elementu](#create-a-persongroup)
* [Identyfikowanie kroju](#identify-a-face)

[Dokumentacja](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Przykłady](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Wymagania wstępne


* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Środowisko IDE programu Visual Studio](https://visualstudio.microsoft.com/vs/) lub bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" Utwórz zasób czołowy "  target="_blank"> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z Face API. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Za pomocą programu Visual Studio Utwórz nową aplikację platformy .NET Core. 

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

Po utworzeniu nowego projektu Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie projektu w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet**. W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj**, zaznacz pozycję **Uwzględnij wersję wstępną** i Wyszukaj `Microsoft.Azure.CognitiveServices.Vision.Face` . Wybierz wersję `2.6.0-preview.1` , a następnie **Zainstaluj**. 

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `face-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program. cs*. 

```console
dotnet new console -n face-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

W katalogu aplikacji zainstaluj bibliotekę klienta programu Front for .NET przy użyciu następującego polecenia:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs), która zawiera przykłady kodu w tym przewodniku Szybki Start.


W katalogu projektu Otwórz plik *program. cs* i Dodaj następujące `using` dyrektywy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

W klasie **programu** aplikacji Utwórz zmienne dla klucza i punktu końcowego zasobu.


> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób czołowy utworzony w sekcji **wymagań wstępnych** został pomyślnie wdrożony, kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](../../../cognitive-services-security.md) Cognitive Services.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

W metodzie **głównej** aplikacji Dodaj wywołania metod używanych w tym przewodniku Szybki Start. Zostaną zaimplementowane później.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre z najważniejszych funkcji biblioteki klienckiej platformy .NET programu Front:

|Nazwa|Opis|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | Ta klasa reprezentuje autoryzację do korzystania z usługi twarz i jest potrzebna dla wszystkich funkcji funkcjonalnych. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas. |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonywać przy użyciu twarzy ludzkich. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej części obrazu. Można go użyć do pobierania szczegółowych informacji o kroju.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|Ta klasa zarządza opartymi na chmurze konstrukcjami **FaceList** , w których jest przechowywany asortyment zestawu twarzy. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| Ta klasa zarządza konstrukcjami **osób** , które są przechowywane w chmurze, które przechowują zestaw twarzy należących do jednej osoby.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| Ta klasa **zarządza konstrukcjami** , które są przechowywane w chmurze, przechowujących zestaw obiektów **osób** . |

## <a name="code-examples"></a>Przykłady kodu

Poniższe fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienckiej programu Front for .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie elementu](#create-a-persongroup)
* [Identyfikowanie kroju](#identify-a-face)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W nowej metodzie Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Zadeklaruj pola pomocnika

Następujące pola są potrzebne w przypadku kilku operacji, które zostaną później dodane. W katalogu głównym klasy **programu** Zdefiniuj następujący ciąg adresu URL. Ten adres URL wskazuje folder obrazów przykładowych.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

W metodzie **Main** Zdefiniuj ciągi w taki sposób, aby wskazywały różne typy modeli rozpoznawania. Później będzie można określić, który model rozpoznawania ma być używany do wykrywania kroju. Aby uzyskać informacje na temat tych opcji, zobacz temat [Określanie modelu rozpoznawania](../../Face-API-How-to-Topics/specify-recognition-model.md) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

### <a name="get-detected-face-objects"></a>Pobieranie wykrytych obiektów czołowych

Utwórz nową metodę wykrywania twarzy. `DetectFaceExtract`Metoda przetwarza trzy obrazy pod podanym adresem URL i tworzy listę obiektów **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** w pamięci programu. Lista wartości **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** określa funkcje do wyodrębnienia. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> Możesz również wykrywać twarze w obrazie lokalnym. Zobacz metody [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations) , takie jak **DetectWithStreamAsync**.

### <a name="display-detected-face-data"></a>Wyświetl wykryte dane dotyczące kroju

Pozostała część `DetectFaceExtract` metody analizuje i drukuje dane atrybutów dla każdej wykrytej cechy. Każdy atrybut musi być określony osobno w wywołaniu oryginalnego interfejsu API wykrywania kroju (na liście **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** ). Poniższy kod przetwarza każdy atrybut, ale prawdopodobnie trzeba będzie tylko użyć jednego lub kilku.

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

### <a name="create-a-persongroup"></a>Tworzenie elementu

Poniższy kod tworzy **odbiorcę** z sześciu różnych obiektów **osób** . Kojarzy każdej **osoby** z zestawem przykładowych obrazów, a następnie pociąga, aby rozpoznać każdą osobę według ich cech twarzy. Obiekty **osób** i **osób** są używane w operacjach sprawdzania, identyfikowania i grupowania.

Zadeklaruj zmienną ciągu w elemencie głównym klasy, aby reprezentować identyfikator grupy **osób** , którą utworzysz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

W nowej metodzie Dodaj następujący kod. Ta metoda spowoduje przeprowadzenie operacji identyfikacji. Pierwszy blok kodu kojarzy nazwy osób z przykładowymi obrazami.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Należy zauważyć, że ten kod definiuje zmienną `sourceImageFileName` . Ta zmienna odnosi się do obrazu źródła &mdash; obrazu zawierającego osoby do zidentyfikowania.

Następnie Dodaj następujący kod, aby utworzyć obiekt **osoby** dla każdej osoby w słowniku i dodać dane dotyczące kroju z odpowiednich obrazów. Każdy obiekt **osoby** jest skojarzony z tą samą **osobą** przy użyciu unikatowego ciągu identyfikatora. Pamiętaj, aby przekazać zmienne `client` , `url` i `RECOGNITION_MODEL1` do tej metody.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> Można również utworzyć **odbiorcę** z obrazów lokalnych. Zobacz metody [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson) , takie jak **AddFaceFromStreamAsync**.

### <a name="train-the-persongroup"></a>Szkolenie elementu PersonGroup

Po wyodrębnieniu danych kroju z obrazów i posortowaniu ich w różnych obiektach **osób** należy przeszkolić **osobę** , aby zidentyfikować funkcje wizualne skojarzone z poszczególnymi obiektami **osób** . Poniższy kod wywołuje metodę synchronicznego **pouczenia** i sonduje wyniki, drukując stan do konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> Face API jest uruchamiany na zestawie wstępnie skompilowanych modeli, które są statyczne według natury (wydajność modelu nie będzie przechodząca ani ulepszana, gdy usługa jest uruchamiana). Wyniki generowane przez model mogą ulec zmianie, jeśli firma Microsoft zaktualizuje zaplecze modelu bez migracji do zupełnie nowej wersji modelu. Aby skorzystać z nowszej wersji modelu, możesz ponownie przeprowadzić uczenie swojej **osoby**, określając nowszy model jako parametr z tymi samymi obrazami rejestracji.

Ta grupa **osób** i skojarzone z nią obiekty **osób** są teraz gotowe do użycia w operacjach sprawdzania, identyfikowania lub grupowania.

### <a name="identify-faces"></a>Identyfikowanie twarzy

Poniższy kod pobiera obraz źródłowy i tworzy listę wszystkich wykrytych twarzy na obrazie. Są to twarze, które zostaną zidentyfikowane względem **osoby**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Następny fragment kodu wywołuje operację **IdentifyAsync** i drukuje wyniki do konsoli. W tym miejscu usługa próbuje dopasować wszystkie powierzchnie z obrazu źródłowego do **osoby** w danej **osobie**. Spowoduje to zamknięcie metody identyfikacji.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Uruchom aplikację, klikając przycisk **Debuguj** w górnej części okna środowiska IDE.

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Jeśli utworzono **osobę** z tego przewodnika Szybki Start i chcesz ją usunąć, uruchom następujący kod w programie:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Zdefiniuj metodę usuwania przy użyciu następującego kodu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób korzystania z biblioteki klienta twarzy dla platformy .NET do wykonywania podstawowych zadań rozpoznawania twarzy. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie Face API (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)

* [Co to jest usługa rozpoznawania twarzy?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
