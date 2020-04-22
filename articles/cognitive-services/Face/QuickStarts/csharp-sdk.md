---
title: 'Szybki start: biblioteka klienta twarzy dla platformy .NET'
description: Wprowadzenie do biblioteki klienta Face dla platformy .NET za pomocą tego przewodnika Szybki start.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 89ef221fce9aed3f9e2c948e89933b8650bb4b4b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769825"
---
# <a name="quickstart-face-client-library-for-net"></a>Szybki start: biblioteka klienta twarzy dla platformy .NET

Wprowadzenie do biblioteki klienta Face dla platformy .NET. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa Face zapewnia dostęp do zaawansowanych algorytmów wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj biblioteki klienta face dla platformy .NET, aby:

* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Wyszukiwanie podobnych twarzy](#find-similar-faces)
* [Tworzenie i szkolenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie twarzy](#identify-a-face)
* [Tworzenie migawki do migracji danych](#take-a-snapshot-for-data-migration)

[Przykłady](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | referencyjnej[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [Samples](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* Bieżąca wersja programu [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-face-azure-resource"></a>Tworzenie zasobu platformy Face Azure

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla twarzy przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również otrzymać [klucz testowy](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  

Po otrzymasz klucz z subskrypcji próbnej lub zasobu, [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i adresu URL punktu końcowego, odpowiednio `FACE_SUBSCRIPTION_KEY` i `FACE_ENDPOINT`, odpowiednio.

### <a name="create-a-new-c-application"></a>Tworzenie nowej aplikacji języka C#

Utwórz nową aplikację .NET Core w preferowanym edytorze lub ide. 

W oknie konsoli (takim jak cmd, PowerShell `dotnet new` lub Bash) użyj polecenia, `face-quickstart`aby utworzyć nową aplikację konsoli o nazwie . To polecenie tworzy prosty projekt "Hello World" C# z jednym plikiem źródłowym: *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można utworzyć za pomocą:

```dotnetcli
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać żadnych ostrzeżeń ani błędów. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

W katalogu projektu otwórz plik *Program.cs* w preferowanym edytorze lub w programie IDE. Dodaj następujące `using` dyrektywy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

W `Main` metodzie aplikacji utwórz zmienne dla punktu końcowego i klucza platformy Azure zasobu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W katalogu aplikacji zainstaluj bibliotekę klienta Face dla platformy .NET za pomocą następującego polecenia:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Jeśli używasz środowiska IDE programu Visual Studio, biblioteka klienta jest dostępna jako pakiet NuGet do pobrania.

## <a name="object-model"></a>Model obiektu

Następujące klasy i interfejsy obsługują niektóre z głównych funkcji SDK Face .NET:

|Nazwa|Opis|
|---|---|
|[FaceClient (FaceClient)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Ta klasa reprezentuje autoryzację do korzystania z usługi Face i jest potrzebna dla wszystkich funkcji face. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do tworzenia wystąpień innych klas. |
|[Operacji twarzy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonać z ludzkimi twarzami. |
|[Wykrytaface](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej twarzy na obrazie. Można go użyć do pobrania szczegółowych informacji o twarzy.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Ta klasa zarządza konstrukcjami **FaceList** przechowywanymi w chmurze, które przechowują różne zestaw twarzy. |
|[PersonGroupPersonExtensions (OsobyGrupoweWysekcje)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Ta klasa zarządza konstrukcjami **person** przechowywanych w chmurze, które przechowują zestaw ścian należących do jednej osoby.|
|[Działania grupy person](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Ta klasa zarządza konstrukcjami **persongroup** przechowywanych w chmurze, które przechowują zestaw różnych **person** obiektów. |
|[ShapshotOperations (Operacji shapshot)](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Ta klasa zarządza funkcje migawka. Można go użyć do tymczasowego zapisania wszystkich danych twarzy w chmurze i migracji tych danych do nowej subskrypcji platformy Azure. |

## <a name="code-examples"></a>Przykłady kodu

Poniższe fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta Face dla platformy .NET:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Wyszukiwanie podobnych twarzy](#find-similar-faces)
* [Tworzenie i szkolenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie twarzy](#identify-a-face)
* [Tworzenie migawki do migracji danych](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> Ten przewodnik Szybki start zakłada, że [utworzono zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza twarzy i punktu końcowego o nazwie `FACE_SUBSCRIPTION_KEY` i `FACE_ENDPOINT`.

W nowej metodzie wystąpienia klienta z punktem końcowym i kluczem. Utwórz **[obiekt ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** za pomocą klucza i użyj go z punktem końcowym, aby utworzyć obiekt **[FaceClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

Prawdopodobnie będziesz chciał wywołać tę `Main` metodę w metodzie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_client)]

### <a name="declare-helper-fields"></a>Zadeklaruj pola pomocnika

Poniższe pola są potrzebne dla kilku operacji Ściana, które zostaną dodane później. W katalogu głównym klasy zdefiniuj następujący ciąg adresu URL. Ten adres URL wskazuje folder przykładowych obrazów.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Zdefiniuj ciągi wskazujące różne typy modeli rozpoznawania. Później będzie można określić model rozpoznawania, którego chcesz użyć do wykrywania twarzy. Zobacz [Określanie modelu rozpoznawania,](../Face-API-How-to-Topics/specify-recognition-model.md) aby uzyskać informacje na temat tych opcji.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Dodaj następujące wywołanie metody do **metody głównej.** Następnie zdefiniujesz metodę. Ostateczna operacja wykrywania zajmie **[Obiekt FaceClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** adres URL obrazu i model rozpoznawania.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Wykrywanie obiektów twarzy

W następnym bloku kodu `DetectFaceExtract` metoda wykrywa twarze w trzech obrazów pod danym adresem URL i tworzy listę **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** obiektów w pamięci programu. Lista wartości **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** określa, które funkcje należy wyodrębnić. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Wyświetlanie danych wykrytej twarzy

Reszta `DetectFaceExtract` metody analizuje i drukuje dane atrybutu dla każdej wykrytej twarzy. Każdy atrybut musi być określony oddzielnie w oryginalnym wywołaniu interfejsu API wykrywania twarzy (na liście **[FaceAttributeType).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** Poniższy kod przetwarza każdy atrybut, ale prawdopodobnie trzeba będzie użyć tylko jednego lub kilku.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedynczą wykrytą twarz (źródło) i przeszukuje zestaw innych twarzy (docelowych), aby znaleźć dopasowania. Po znalezieniu dopasowania, drukuje identyfikator dopasowanej ściany do konsoli.

### <a name="detect-faces-for-comparison"></a>Wykrywanie twarzy do porównania

Najpierw zdefiniuj drugą metodę wykrywania twarzy. Przed ich porównaniem należy wykryć twarze na obrazach, a ta metoda wykrywania jest zoptymalizowana pod kątem operacji porównania. Nie wyodrębnia szczegółowych atrybutów twarzy, jak w powyższej sekcji i używa innego modelu rozpoznawania.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Znajdź dopasowania

Poniższa metoda wykrywa twarze w zestawie obrazów docelowych i w jednym obrazie źródłowym. Następnie porównuje je i znajduje wszystkie obrazy docelowe, które są podobne do obrazu źródłowego.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Drukuj dopasowania

Następujący kod drukuje szczegóły dopasowania do konsoli:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Identyfikowanie twarzy

Operacja Identyfikuj pobiera obraz osoby (lub wielu osób) i wygląda, aby znaleźć tożsamość każdej twarzy na obrazie. Porównuje każdą wykrytą twarz z **grupą osób**, bazą danych różnych obiektów **osoby,** których rysy twarzy są znane. Aby wykonać operację Identyfikowanie, należy najpierw utworzyć i wyszkolić **grupę osób**

### <a name="create-and-train-a-person-group"></a>Tworzenie i szkolenie grupy osób

Poniższy kod tworzy **PersonGroup** z sześciu różnych **Person** obiektów. Kojarzy każdą **osobę** z zestawem przykładowych obrazów, a następnie trenuje, aby rozpoznać każdą osobę według ich cech twarzy. **Obiekty Person** i **PersonGroup** są używane w operacjach Weryfikuj, identyfikuj i Grupuj.

#### <a name="create-persongroup"></a>Tworzenie grupy osób

Zadeklaruj zmienną ciągu w katalogu głównym klasy do reprezentowania **identyfikatora persongroup,** który utworzysz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

W nowej metodzie dodaj następujący kod. Ta metoda przeprowadzi identify operacji. Pierwszy blok kodu kojarzy nazwiska osób z ich przykładowymi obrazami.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Następnie dodaj następujący kod, aby utworzyć **person** obiektu dla każdej osoby w słowniku i dodać dane twarzy z odpowiednich obrazów. Każdy **obiekt Person** jest skojarzony z tą samą grupą **persongroup** za pośrednictwem unikatowego ciągu identyfikatora. Pamiętaj, aby przekazać `client` `url`zmienne `RECOGNITION_MODEL1` , i do tej metody.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

#### <a name="train-persongroup"></a>Grupa osób pociągu

Po wyodrębnieniu danych twarzy z obrazów i posortowaniu ich w różne **obiekty Person** należy przeszkolić **grupę persongroup** w celu zidentyfikowania funkcji wizualnych skojarzonych z każdym z jej obiektów **Person.** Poniższy kod wywołuje metodę **pociągu** asynchronicznej i sonduje wyniki, drukując stan do konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

Ta grupa **osoby** i skojarzone z nią obiekty **Person** są teraz gotowe do użycia w operacjach Weryfikuj, identyfikuj lub Grupuj.

### <a name="get-a-test-image"></a>Pobierz obraz testowy

Należy zauważyć, że kod tworzenia i szkolenia `sourceImageFileName`grupy [osób](#create-and-train-a-person-group) definiuje zmienną . Ta zmienna odpowiada obrazowi źródłowego obrazu,&mdash;który zawiera osoby do zidentyfikowania.

### <a name="identify-faces"></a>Identyfikowanie twarzy

Poniższy kod pobiera obraz źródłowy i tworzy listę wszystkich twarzy wykrytych na obrazie. Są to twarze, które zostaną zidentyfikowane względem **persongroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

Następny fragment kodu wywołuje **identifyAsync** operacji i drukuje wyniki do konsoli. W tym miejscu usługa próbuje dopasować każdą twarz z obrazu źródłowego do **osoby** w danej **grupie osób**. Spowoduje to zamknięcie metody Identify.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Tworzenie migawki do migracji danych

Funkcja Migawki umożliwia przeniesienie zapisanych danych twarzy, takich jak przeszkolony **persongroup,** do innej subskrypcji usługi Azure Cognitive Services Face. Możesz użyć tej funkcji, jeśli na przykład utworzono obiekt **PersonGroup** przy użyciu bezpłatnej subskrypcji próbnej i chcesz przeprowadzić migrację do płatnej subskrypcji. Zobacz [Migrowanie danych twarzy,](../Face-API-How-to-Topics/how-to-migrate-face-data.md) aby uzyskać omówienie funkcji Migawki.

W tym przykładzie zostanie migrowanie **grupy osób utworzonej** w [umień i szkolenie grupy osób](#create-and-train-a-person-group). Można najpierw ukończyć tę sekcję lub utworzyć własną konstrukcję danych twarzy do migracji.

### <a name="set-up-target-subscription"></a>Konfigurowanie subskrypcji docelowej

Po pierwsze musisz mieć drugą subskrypcję platformy Azure z zasobem Face; Można to zrobić, wykonując czynności opisane w sekcji [Konfigurowanie.](#setting-up) 

Następnie zdefiniuj następujące `Main` zmienne w metodzie programu. Musisz utworzyć nowe zmienne środowiskowe dla identyfikatora subskrypcji konta platformy Azure, a także klucz, punkt końcowy i identyfikator subskrypcji nowego konta (docelowego). 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

W tym przykładzie zadeklarować zmienną dla identyfikatora obiektu **persongroup**&mdash;docelowego, który należy do nowej subskrypcji, do której skopiujesz dane.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Uwierzytelnij klienta docelowego

Następnie dodaj kod, aby uwierzytelnić dodatkową subskrypcję face.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Używanie migawki

Pozostałe operacje migawki musi odbywać się w ramach metody asynchroniczną. 

1. Pierwszym krokiem **jest,** aby zrobić migawkę, która zapisuje dane twarzy oryginalnej subskrypcji do tymczasowej lokalizacji w chmurze. Ta metoda zwraca identyfikator używany do wykonywania zapytań o stan operacji.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take)]

1. Następnie kwerendy identyfikator, dopóki operacja została zakończona.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_take_wait)]

1. Następnie użyj operacji **zastosuj,** aby zapisać dane twarzy do subskrypcji docelowej. Ta metoda zwraca również wartość identyfikatora.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Ponownie kwerendy nowego identyfikatora, dopóki operacja została zakończona.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_apply)]

1. Na koniec należy ukończyć blok try/catch i zakończyć metodę.

    [!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_snapshot_trycatch)]

W tym momencie nowy **obiekt PersonGroup** powinien mieć te same dane co oryginalny i powinien być dostępny z nowej subskrypcji usługi Azure Face.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji `dotnet run` za pomocą polecenia.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Jeśli grupa **osób** została utworzona w tym przewodniku Szybki start i chcesz ją usunąć, uruchom w programie następujący kod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Zdefiniuj metodę usuwania za pomocą następującego kodu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

Ponadto w przypadku migracji danych przy użyciu funkcji Migawka w tym przewodniku Szybki start należy również usunąć **grupę persongroup** zapisaną w subskrypcji docelowej.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak używać biblioteki Twarzy dla platformy .NET do wykonywania zadań bazowych. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie do interfejsu API rozpoznawania twarzy (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Co to jest usługa rozpoznawania twarzy?](../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
