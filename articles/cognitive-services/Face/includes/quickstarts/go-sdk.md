---
title: Przewodnik Szybki Start dotyczący biblioteki klienta
description: Użyj biblioteki klienta twarzy dla języka go, aby wykrywać twarze, znajdować podobne (Wyszukiwanie twarzy według obrazu), identyfikować twarze (Wyszukiwanie twarzy) i migrować dane twarzy.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 65f7af56e7f0042b8d4c312d17641a537f5fd908
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816773"
---
Wprowadzenie do rozpoznawania twarzy przy użyciu biblioteki klienta twarzy dla języka go. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa twarzy zapewnia dostęp do zaawansowanych algorytmów służących do wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Korzystanie z biblioteki klienta usługi Front Service dla języka go:

* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)

[Dokumentacja](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face)  |  źródłowy biblioteki [Pobieranie zestawu SDK](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja języka [go](https://golang.org/dl/)
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" Utwórz zasób czołowy "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API rozpoznawania twarzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Po otrzymaniu klucza i punktu końcowego [Utwórz zmienne środowiskowe](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego, `FACE_SUBSCRIPTION_KEY` odpowiednio nazwane i `FACE_ENDPOINT` .

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-go-project-directory"></a>Utwórz katalog projektu języka go

W oknie konsoli (cmd, PowerShell, Terminal, bash) Utwórz nowy obszar roboczy dla projektu go, nazwanego `my-app` i przejdź do niego.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Obszar roboczy będzie zawierać trzy foldery:

* **src** — ten katalog będzie zawierać kod źródłowy i pakiety. Wszystkie pakiety zainstalowane za pomocą `go get` polecenia będą znajdować się w tym folderze.
* **pkg** — ten katalog będzie zawierać skompilowane obiekty pakietu języka go. Wszystkie te pliki mają `.a` rozszerzenie.
* **bin** — ten katalog będzie zawierać binarne pliki wykonywalne, które są tworzone podczas uruchamiania programu `go install` .

> [!TIP]
> Aby dowiedzieć się więcej na temat struktury obszaru roboczego go, zobacz [dokumentację języka](https://golang.org/doc/code.html#Workspaces)go. Ten przewodnik zawiera informacje dotyczące ustawiania `$GOPATH` i `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Zainstaluj bibliotekę kliencką dla języka go

Następnie zainstaluj bibliotekę kliencką dla języka go:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

a jeśli używasz programu dep, w ramach repozytorium uruchom:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Tworzenie aplikacji języka go

Następnie utwórz plik w katalogu **src** o nazwie `sample-app.go` :

```bash
cd src
touch sample-app.go
```

Otwórz `sample-app.go` w preferowanym środowisku IDE lub edytorze tekstu. Następnie Dodaj nazwę pakietu i zaimportuj następujące biblioteki:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Następnie dowiesz się, jak dodać kod, aby wykonać różne operacje usługi kroju.

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta usługi Front Service.

|Nazwa|Opis|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Ta klasa reprezentuje autoryzację do korzystania z usługi twarz i jest potrzebna dla wszystkich funkcji funkcjonalnych. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas. |
|[Klient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonywać przy użyciu twarzy ludzkich. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej części obrazu. Można go użyć do pobierania szczegółowych informacji o kroju.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Ta klasa zarządza opartymi na chmurze konstrukcjami **FaceList** , w których jest przechowywany asortyment zestawu twarzy. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Ta klasa zarządza konstrukcjami **osób** , które są przechowywane w chmurze, które przechowują zestaw twarzy należących do jednej osoby.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Ta klasa **zarządza konstrukcjami** , które są przechowywane w chmurze, przechowujących zestaw obiektów **osób** . |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Ta klasa zarządza funkcją migawek. Można jej użyć do tymczasowego zapisania wszystkich danych platformy opartej na chmurze i przeprowadzenia migracji tych danych do nowej subskrypcji platformy Azure. |

## <a name="code-examples"></a>Przykłady kodu

Te przykłady kodu pokazują, jak wykonać podstawowe zadania przy użyciu biblioteki klienckiej usługi Front Service dla języka go:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE] 
> W tym przewodniku szybki start założono, że zostały [utworzone zmienne środowiskowe](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego, `FACE_SUBSCRIPTION_KEY` odpowiednio nazwane i `FACE_ENDPOINT` .

Utwórz **główną** funkcję i Dodaj do niej następujący kod, aby utworzyć wystąpienie klienta z punktem końcowym i kluczem. Utworzysz obiekt **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** z kluczem i użyjesz go w punkcie końcowym, aby utworzyć obiekt **[klienta](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** . Ten kod tworzy również obiekt kontekstu, który jest wymagany do tworzenia obiektów klienta. Definiuje również lokalizację zdalną, w której znajdują się niektóre przykładowe obrazy w tym przewodniku Szybki Start.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Dodaj następujący kod do metody **Main** . Ten kod definiuje zdalny obraz przykładowy i określa funkcje do wyodrębnienia z obrazu. Określa również, który model AI ma być używany do wyodrębniania danych z wykrytych. Aby uzyskać informacje na temat tych opcji, zobacz temat [Określanie modelu rozpoznawania](../../Face-API-How-to-Topics/specify-recognition-model.md) . Na koniec Metoda **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** wykonuje operację wykrywania kroju na obrazie i zapisuje wyniki w pamięci programu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

> [!TIP]
> Możesz również wykrywać twarze w obrazie lokalnym. Zobacz metody [klienta](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client) , takie jak **DetectWithStream**.

### <a name="display-detected-face-data"></a>Wyświetl wykryte dane dotyczące kroju

Następny blok kodu przyjmuje pierwszy element w tablicy obiektów **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** i drukuje jego atrybuty do konsoli. Jeśli używasz obrazu z wieloma twarzami, zamiast tego należy wykonać iterację tablicy.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedyncze wykryte buźki (Źródło) i przeszukuje zestaw innych twarzy (target), aby znaleźć dopasowania (Wyszukiwanie twarzy według obrazu). Po znalezieniu dopasowania program drukuje identyfikator dopasowanej do konsoli.

### <a name="detect-faces-for-comparison"></a>Wykrywanie twarzy do porównania

Najpierw Zapisz odwołanie do twarzy wykryte w sekcji wykrywaj twarze [w obrazie](#detect-faces-in-an-image) . To jest źródło.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Następnie wprowadź Poniższy kod w celu wykrycia zestawu twarzy w innym obrazie. Te powierzchnie będą elementem docelowym.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Znajdź dopasowania

Poniższy kod używa metody **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** , aby znaleźć wszystkie powierzchnie docelowe, które pasują do powierzchni źródłowej.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Drukuj dopasowania

Poniższy kod drukuje szczegóły dopasowania do konsoli.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Tworzenie i uczenie grupy osób

Aby wykonać czynności opisane w tym scenariuszu, należy zapisać następujące obrazy w katalogu głównym projektu: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Ta grupa obrazów zawiera trzy zestawy obrazów z jedną stroną, które odpowiadają trzem różnym osobom. Kod określi trzy obiekty **osoby** należącej do osoby i skojarzy je z plikami obrazów, które zaczynają się od `woman` , `man` i `child` .

### <a name="create-persongroup"></a>Utwórz osobę

Po pobraniu obrazów Dodaj poniższy kod na dole metody **Main** . Ten kod uwierzytelnia obiekt **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** , a następnie używa go do definiowania nowej **osoby**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Utwórz osoby

Następny blok kodu uwierzytelnia **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** i używa go do definiowania trzech nowych obiektów **osób** osoby. Każdy z tych obiektów reprezentuje pojedynczą osobę z zestawu obrazów.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Przypisywanie twarzy do osób

Poniższy kod sortuje obrazy według ich prefiksu, wykrywa twarze i przypisuje powierzchnie do każdego obiektu **osoby** osoby, na podstawie nazwy pliku obrazu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

> [!TIP]
> Można również utworzyć **odbiorcę** z obrazów zdalnych, do których odwołuje się adres URL. Zobacz metody [PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient) , takie jak **AddFaceFromURL**.

### <a name="train-persongroup"></a>Szkolenie zespołu

Po przypisaniu twarzy nauczysz się, że ta **osoba** będzie mogła identyfikować funkcje wizualne skojarzone z poszczególnymi obiektami **osób** . Poniższy kod wywołuje metodę synchronicznego **pouczenia** i sonduje wynik, drukując stan do konsoli.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

> [!TIP]
> Interfejs API rozpoznawania twarzy jest uruchamiany na zestawie wstępnie skompilowanych modeli, które są statyczne według natury (wydajność modelu nie będzie przechodząca ani ulepszana, gdy usługa jest uruchamiana). Wyniki generowane przez model mogą ulec zmianie, jeśli firma Microsoft zaktualizuje zaplecze modelu bez migracji do zupełnie nowej wersji modelu. Aby skorzystać z nowszej wersji modelu, możesz ponownie przeprowadzić uczenie swojej **osoby**, określając nowszy model jako parametr z tymi samymi obrazami rejestracji.

## <a name="identify-a-face"></a>Identyfikowanie kroju

Operacja identyfikowania pobiera obraz osoby (lub wielu osób) i szuka tożsamości każdej twarzy na obrazie (wyszukiwanie rozpoznawania twarzy). Porównuje każdą wykrytą twarzą z **osobą**, która jest bazą danych różnych obiektów **osób** , których funkcje twarzy są znane.

> [!IMPORTANT]
> Aby można było uruchomić ten przykład, należy najpierw uruchomić kod w temacie [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Pobierz obraz testowy

Poniższy kod szuka obrazu w folderze głównym projektu _test-image-person-group.jpg_ i ładuje go do pamięci programu. Ten obraz można znaleźć w tym samym repozytorium, co obrazy używane w [tworzeniu i uczeniu grupy osób](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Wykrywaj twarze źródłowe w obrazie testowym

Następny blok kodu jest zwykłym wykrywaniem twarzy na obrazie testu, aby pobrać wszystkie powierzchnie i zapisać je w tablicy.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identyfikowanie twarzy

Metoda **[Zidentyfikuj](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** pobiera tablicę wykrytych twarzy i porównuje je z daną **osobą** (zdefiniowaną i przeszkolony w poprzedniej sekcji). Jeśli może być zgodna z wykrytą stroną do **osoby** w grupie, zapisuje wynik.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Następnie ten kod drukuje szczegółowe wyniki dopasowania do konsoli.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Weryfikuj twarze

Operacja verify przyjmuje identyfikator kroju i inny obiekt ID lub **osobę** , a także określa, czy należą do tej samej osoby.

Poniższy kod wykrywa twarze w dwóch obrazach źródłowych, a następnie weryfikuje każdą z nich względem twarzy wykrytego z obrazu docelowego.

### <a name="get-test-images"></a>Pobierz obrazy testowe

Poniższy kod blokuje zmienne, które będą wskazywały docelowe i źródłowe obrazy dla operacji weryfikacji.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Wykrywanie twarzy do weryfikacji

Poniższy kod wykrywa twarze w obrazie źródłowym i docelowym i zapisuje je w zmiennych.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Pobierz wyniki weryfikacji

Poniższy kod porównuje każdy z obrazów źródłowych do obrazu docelowego i drukuje komunikat informujący o tym, czy należą do tej samej osoby.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację rozpoznawania aplikacji z katalogu aplikacji za pomocą `go run <app-name>` polecenia.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Jeśli w tym przewodniku szybki start utworzono **osobę** , która ma zostać usunięta, wywołaj metodę **[delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** .

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start pokazano, jak używać biblioteki klienta twarzy do wykonywania zadań rozpoznawania twarzy. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie interfejs API rozpoznawania twarzy (go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Co to jest usługa rozpoznawania twarzy?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).