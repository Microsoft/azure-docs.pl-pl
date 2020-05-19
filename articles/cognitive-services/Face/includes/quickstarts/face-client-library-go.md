---
title: Przewodnik Szybki Start dotyczący biblioteki klienta
description: Rozpocznij pracę z biblioteką klienta programu Front for.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 4a96f0e887bb04aea6d451e08bd5d26d1cc6edca
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82587836"
---
Rozpocznij pracę z biblioteką klienta programu Front for. Wykonaj następujące kroki, aby zainstalować bibliotekę i wypróbuj nasze przykłady dla podstawowych zadań. Usługa twarzy zapewnia dostęp do zaawansowanych algorytmów służących do wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Korzystanie z biblioteki klienta usługi Front Service dla języka go:

* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Wyszukiwanie podobnych twarzy](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)
* [Utwórz migawkę migracji danych](#take-a-snapshot-for-data-migration)

[Dokumentacja](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face)  |  źródłowy biblioteki [Pobieranie zestawu SDK](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Najnowsza wersja języka [go](https://golang.org/dl/)

## <a name="set-up"></a>Konfiguruj

### <a name="create-a-face-azure-resource"></a>Tworzenie zasobu platformy Azure w postaci czołowej 

Rozpocznij korzystanie z usługi kroju i utwórz zasób platformy Azure. Wybierz odpowiedni typ zasobu:

* [Zasób próbny](https://azure.microsoft.com/try/cognitive-services/#decision) (nie jest wymagana subskrypcja platformy Azure): 
    * Ważne przez siedem dni bezpłatnie. Po zarejestrowaniu się w [witrynie sieci Web platformy Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/)będzie dostępny klucz wersji próbnej i punkt końcowy. 
    * Jest to świetna opcja, jeśli chcesz wypróbować usługę, ale nie masz subskrypcji platformy Azure.
* [Zasób usługi kroju](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace):
    * Dostępne w Azure Portal do momentu usunięcia zasobu.
    * Skorzystaj z warstwy cenowej bezpłatna do wypróbowania usługi i przeprowadź uaktualnienie później do warstwy płatnej dla środowiska produkcyjnego.
* Zasób obejmujący [wiele usług](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne):
    * Dostępne w Azure Portal do momentu usunięcia zasobu.  
    * Użyj tego samego klucza i punktu końcowego dla aplikacji w wielu Cognitive Services.

### <a name="create-an-environment-variable"></a>Utwórz zmienną środowiskową

>[!NOTE]
> Punkty końcowe dla zasobów nieprzeznaczonych dla wersji próbnej utworzonych po 1 lipca 2019 używają niestandardowego formatu poddomen pokazanego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Przy użyciu klucza i punktu końcowego z utworzonego zasobu Utwórz dwa zmienne środowiskowe do uwierzytelnienia:
* `FACE_SUBSCRIPTION_KEY`— Klucz zasobu do uwierzytelniania żądań.
* `FACE_ENDPOINT`— Punkt końcowy zasobu do wysyłania żądań interfejsu API. Będzie wyglądać następująco: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Skorzystaj z instrukcji dotyczących systemu operacyjnego.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

Po dodaniu zmiennej środowiskowej Uruchom ponownie okno konsoli.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source ~/.bashrc` z okna konsoli, aby zmiany zostały uwzględnione.

#### <a name="macos"></a>[macOS](#tab/unix)

Edytuj `.bash_profile` i Dodaj zmienną środowiskową:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Po dodaniu zmiennej środowiskowej uruchom polecenie `source .bash_profile` z okna konsoli, aby zmiany zostały uwzględnione.
***

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
* [Wyszukiwanie podobnych twarzy](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)
* [Utwórz migawkę migracji danych](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE] 
> W tym przewodniku szybki start założono, że zostały [utworzone zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego, `FACE_SUBSCRIPTION_KEY` odpowiednio nazwane i `FACE_ENDPOINT` .

Utwórz **główną** funkcję i Dodaj do niej następujący kod, aby utworzyć wystąpienie klienta z punktem końcowym i kluczem. Utworzysz obiekt **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** z kluczem i użyjesz go w punkcie końcowym, aby utworzyć obiekt **[klienta](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** . Ten kod tworzy również obiekt kontekstu, który jest wymagany do tworzenia obiektów klienta. Definiuje również lokalizację zdalną, w której znajdują się niektóre przykładowe obrazy w tym przewodniku Szybki Start.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Dodaj następujący kod do metody **Main** . Ten kod definiuje zdalny obraz przykładowy i określa funkcje do wyodrębnienia z obrazu. Określa również, który model AI ma być używany do wyodrębniania danych z wykrytych. Aby uzyskać informacje na temat tych opcji, zobacz temat [Określanie modelu rozpoznawania](../../Face-API-How-to-Topics/specify-recognition-model.md) . Na koniec Metoda **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** wykonuje operację wykrywania kroju na obrazie i zapisuje wyniki w pamięci programu.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Wyświetl wykryte dane dotyczące kroju

Następny blok kodu przyjmuje pierwszy element w tablicy obiektów **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** i drukuje jego atrybuty do konsoli. Jeśli używasz obrazu z wieloma twarzami, zamiast tego należy wykonać iterację tablicy.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedyncze wykryte twarze (Źródło) i przeszukuje zestaw innych twarzy (element docelowy) w celu znalezienia dopasowań. Po znalezieniu dopasowania program drukuje identyfikator dopasowanej do konsoli.

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

### <a name="train-persongroup"></a>Szkolenie zespołu

Po przypisaniu twarzy nauczysz się, że ta **osoba** będzie mogła identyfikować funkcje wizualne skojarzone z poszczególnymi obiektami **osób** . Poniższy kod wywołuje metodę synchronicznego **pouczenia** i sonduje wynik, drukując stan do konsoli.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identyfikowanie kroju

Poniższy kod pobiera obraz z wieloma twarzami i szuka tożsamości każdej osoby w obrazie. Porównuje każdą wykrytą twarzą z **osobą**, która jest bazą danych różnych obiektów **osób** , których funkcje twarzy są znane.

> [!IMPORTANT]
> Aby można było uruchomić ten przykład, należy najpierw uruchomić kod w temacie [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Pobierz obraz testowy

Poniższy kod wyszukuje w katalogu głównym projektu obraz _test-Image-Person-Group. jpg_ i ładuje go do pamięci programu. Ten obraz można znaleźć w tym samym repozytorium, co obrazy używane w [tworzeniu i uczeniu grupy osób](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

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


## <a name="take-a-snapshot-for-data-migration"></a>Utwórz migawkę migracji danych

Funkcja migawek pozwala przenosić zapisane dane, takie jak wyszkolone **osoby**, do innej subskrypcji platformy Azure Cognitive Services. Ta funkcja może być używana, jeśli na przykład utworzono obiekt obiektu **osoby** przy użyciu subskrypcji bezpłatnej wersji próbnej i teraz zechcesz przeprowadzić migrację go do płatnej subskrypcji. Więcej informacji na temat funkcji migawek można znaleźć w temacie [Migrowanie danych własnych](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) .

W tym przykładzie przeprowadzisz migrację grupy **osób** utworzonych w temacie [Tworzenie i uczenie](#create-and-train-a-person-group)użytkownika. Możesz najpierw wykonać tę sekcję lub użyć własnych konstrukcji danych czołowych.

### <a name="set-up-target-subscription"></a>Skonfiguruj subskrypcję docelową

Najpierw musisz mieć drugą subskrypcję platformy Azure z zasobem czołowym; można to zrobić przez powtórzenie kroków z sekcji [Konfiguracja](#set-up) . 

Następnie Utwórz następujące zmienne w górnej części metody **Main** . Należy również utworzyć nowe zmienne środowiskowe dla identyfikatora subskrypcji Twojego konta platformy Azure, a także klucz, punkt końcowy i Identyfikator subskrypcji nowego konta (docelowego).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Następnie wprowadź wartość identyfikatora subskrypcji do tablicy w celu wykonania następnych kroków.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Uwierzytelnianie klienta docelowego

Później w skrypcie Zapisz oryginalny obiekt Client jako klienta źródłowego, a następnie Uwierzytelnij nowy obiekt klienta dla subskrypcji docelowej. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Tworzenie migawki

Następnym krokiem **[jest wykonanie migawki z użyciem](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)**, co spowoduje zapisanie danych pierwotnych subskrypcji w tymczasowej lokalizacji w chmurze. Ta metoda zwraca identyfikator używany do wykonywania zapytań dotyczących stanu operacji.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Następnie wykonaj zapytanie o identyfikator do momentu zakończenia operacji.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Zastosuj migawkę

Użyj operacji **[Zastosuj](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** , aby zapisać nowo przekazane dane do subskrypcji docelowej. Ta metoda zwraca również identyfikator.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Ponownie wykonaj zapytanie o identyfikator do momentu zakończenia operacji.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Po wykonaniu tych kroków możesz uzyskać dostęp do swoich konstrukcji danych własnych z nowej subskrypcji (docelowej).

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację go za pomocą `go run [arguments]` polecenia z katalogu aplikacji.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Jeśli w tym przewodniku szybki start utworzono **osobę** , która ma zostać usunięta, wywołaj metodę **[delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** . W przypadku migrowania danych przy użyciu funkcji Snapshot w tym przewodniku Szybki Start należy również usunąć **osobę** , która została zapisana do subskrypcji docelowej.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób korzystania z biblioteki rozpoznawania jako zadań do wykonania. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie interfejs API rozpoznawania twarzy (go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Co to jest usługa rozpoznawania twarzy?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
