---
title: Biblioteka klienta języka Python — Szybki Start
description: Użyj biblioteki klienta twarzy dla języka Python, aby wykrywać twarze, znajdować podobne (Wyszukiwanie twarzy według obrazu), identyfikować twarze (Wyszukiwanie twarzy) i migrować dane twarzy.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 09/17/2020
ms.author: pafarley
ms.openlocfilehash: f746a61850567014ce216c47df472d035f1ae123
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91322981"
---
Wprowadzenie do rozpoznawania twarzy przy użyciu biblioteki klienta twarzy dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa twarzy zapewnia dostęp do zaawansowanych algorytmów służących do wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj biblioteki klienta programu Front dla języka Python, aby:

* Wykrywanie twarzy na obrazie
* Wyszukiwanie podobnych twarzy
* Tworzenie i uczenie grupy osób
* Identyfikowanie kroju
* Weryfikuj twarze
* Utwórz migawkę migracji danych

[Dokumentacja](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face)  |  źródłowy biblioteki [Pakiet (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/)  |  [Przykłady](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" Utwórz zasób czołowy "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API rozpoznawania twarzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Po otrzymaniu klucza i punktu końcowego [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego, `FACE_SUBSCRIPTION_KEY` odpowiednio nazwane i `FACE_ENDPOINT` .

## <a name="setting-up"></a>Konfigurowanie
 
### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy skrypt w języku Python &mdash; , na przykład*QuickStart-File.py*. Następnie otwórz go w preferowanym edytorze lub środowisku IDE i zaimportuj poniższe biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Następnie utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę, na których jest uruchomiona, aby uzyskać dostęp do zmiennej.

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Bibliotekę kliencką można zainstalować za pomocą programu:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienckiej języka Python programu.

|Nazwa|Opis|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Ta klasa reprezentuje autoryzację do korzystania z usługi twarz i jest potrzebna dla wszystkich funkcji funkcjonalnych. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonywać przy użyciu twarzy ludzkich. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej części obrazu. Można go użyć do pobierania szczegółowych informacji o kroju.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Ta klasa zarządza opartymi na chmurze konstrukcjami **FaceList** , w których jest przechowywany asortyment zestawu twarzy. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Ta klasa zarządza konstrukcjami **osób** , które są przechowywane w chmurze, które przechowują zestaw twarzy należących do jednej osoby.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Ta klasa **zarządza konstrukcjami** , które są przechowywane w chmurze, przechowujących zestaw obiektów **osób** . |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Ta klasa zarządza funkcją migawek; można jej użyć do tymczasowego zapisania wszystkich danych platformy opartej na chmurze i przeprowadzenia migracji tych danych do nowej subskrypcji platformy Azure. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienckiej Front dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Znajdź podobne twarze](#find-similar-faces)
* [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie kroju](#identify-a-face)
* [Weryfikuj twarze](#verify-faces)
* [Utwórz migawkę migracji danych](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

> [!NOTE]
> W tym przewodniku szybki start założono, że [utworzono zmienną środowiskową](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) dla klucza czołowego o nazwie `FACE_SUBSCRIPTION_KEY` .

Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Poniższy kod wykrywa czołową w obrazie zdalnym. Spowoduje to wydrukowanie z konsoli programu wykrytego identyfikatora, a także zapisanie jej w pamięci w programie. Następnie wykrywa twarze w obrazie z wieloma osobami i drukuje ich identyfikatory również w konsoli. Zmieniając parametry w metodzie [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) , można zwrócić różne informacje z każdym obiektem [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) , aby poznać więcej scenariuszy wykrywania.

### <a name="display-and-frame-faces"></a>Wyświetlacz i twarze z ramkami

Poniższy kod wyprowadza dany obraz do ekranu i Rysuje prostokąty wokół twarzy przy użyciu właściwości DetectedFace. faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Młoda kobieta z czerwonym prostokątem narysowanym wokół twarzy](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedyncze wykryte buźki (Źródło) i przeszukuje zestaw innych twarzy (target), aby znaleźć dopasowania (Wyszukiwanie twarzy według obrazu). Po znalezieniu dopasowania program drukuje identyfikator dopasowanej do konsoli.

### <a name="find-matches"></a>Znajdź dopasowania

Najpierw należy uruchomić kod w powyższej sekcji ([wykrywaj twarze na obrazie](#detect-faces-in-an-image)), aby zapisać odwołanie do jednej twarzy. Następnie uruchom następujący kod, aby uzyskać odwołania do kilku twarzy w obrazie grupy.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Następnie Dodaj poniższy blok kodu, aby znaleźć wystąpienia pierwszej czołowej w grupie. Zobacz metodę [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) , aby dowiedzieć się, jak zmodyfikować to zachowanie.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Drukuj dopasowania

Użyj poniższego kodu, aby wydrukować szczegóły dopasowania do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Tworzenie i uczenie grupy osób

Poniższy kod tworzy obiekt **Person** z trzema różnymi obiektami **osób** . Kojarzy każdej **osoby** z zestawem przykładowych obrazów, a następnie pociąga za sobą możliwość rozpoznawania każdej osoby. 

### <a name="create-persongroup"></a>Utwórz osobę

Aby wykonać czynności opisane w tym scenariuszu, należy zapisać następujące obrazy w katalogu głównym projektu: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

Ta grupa obrazów zawiera trzy zestawy obrazów kroju odpowiadających trzem różnym osobom. Kod określi trzy obiekty **osób** i skojarzy je z plikami obrazów, które zaczynają się od `woman` , `man` i `child` .

Po skonfigurowaniu obrazów Zdefiniuj etykietę w górnej części skryptu **dla obiektu, który utworzysz** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Następnie Dodaj następujący kod w dolnej części skryptu. Ten kod tworzy **osobę** i trzy obiekty **osób** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Przypisywanie twarzy do osób

Poniższy kod sortuje obrazy według ich prefiksu, wykrywa twarze i przypisuje powierzchnie do każdego obiektu **osoby** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Szkolenie zespołu

Po przypisaniu twarzy należy przeszkolić **osobę** , aby mogła ona identyfikować funkcje wizualne powiązane z poszczególnymi obiektami **osób** . Poniższy kod wywołuje metodę synchronicznego **pouczenia** i sonduje wynik, drukując stan do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identyfikowanie kroju

Operacja identyfikowania pobiera obraz osoby (lub wielu osób) i szuka tożsamości każdej twarzy na obrazie (wyszukiwanie rozpoznawania twarzy). Porównuje każdą wykrytą twarzą z **osobą**, która jest bazą danych różnych obiektów **osób** , których funkcje twarzy są znane.

> [!IMPORTANT]
> Aby można było uruchomić ten przykład, należy najpierw uruchomić kod w temacie [Tworzenie i uczenie grupy osób](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Pobierz obraz testowy

Poniższy kod wygląda w folderze głównym projektu dla obrazu _test-image-person-group.jpg_ i wykrywa twarze na obrazie. Ten obraz można znaleźć za pomocą obrazów używanych do zarządzania **osobami** : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identyfikowanie twarzy

Metoda **Zidentyfikuj** pobiera tablicę wykrytych twarzy i porównuje je z tą **osobą**. Jeśli może być zgodna z wykrytą stroną do **osoby**, zapisuje wynik. Ten kod drukuje szczegółowe wyniki dopasowania do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Weryfikuj twarze

Operacja verify przyjmuje identyfikator kroju i inny obiekt ID lub **osobę** , a także określa, czy należą do tej samej osoby.

Poniższy kod wykrywa twarze w dwóch obrazach źródłowych, a następnie weryfikuje je pod kątem twarzy wykrytego z obrazu docelowego.

### <a name="get-test-images"></a>Pobierz obrazy testowe

Poniższy kod blokuje zmienne, które będą wskazywały na obrazy źródłowe i docelowe dla operacji weryfikacji.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Wykrywanie twarzy do weryfikacji

Poniższy kod wykrywa twarze w obrazie źródłowym i docelowym i zapisuje je w zmiennych.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Pobierz wyniki weryfikacji

Poniższy kod porównuje każdy z obrazów źródłowych do obrazu docelowego i drukuje komunikat informujący o tym, czy należą do tej samej osoby.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Utwórz migawkę migracji danych

Funkcja migawek pozwala przenosić zapisane dane, takie jak wyszkolone **osoby**, do innej subskrypcji platformy Azure Cognitive Services. Możesz chcieć użyć tej funkcji, jeśli na przykład utworzysz obiekt obiektu **osoby** przy użyciu bezpłatnej subskrypcji, a teraz chcesz przeprowadzić migrację go do płatnej subskrypcji. Więcej informacji na temat funkcji migawek można znaleźć w temacie [Migrowanie danych własnych](../../Face-API-How-to-Topics/how-to-migrate-face-data.md) .

W tym przykładzie zostanie przeprowadzona migracja grupy **osób** utworzonych w temacie [Tworzenie i uczenie osoby](#create-and-train-a-person-group). Możesz najpierw wykonać tę sekcję lub użyć własnych konstrukcji danych czołowych.

### <a name="set-up-target-subscription"></a>Skonfiguruj subskrypcję docelową

Najpierw musisz mieć drugą subskrypcję platformy Azure z zasobem czołowym; można to zrobić, wykonując czynności opisane w sekcji [Konfigurowanie](#setting-up) . 

Następnie Utwórz następujące zmienne w górnej części skryptu. Należy również utworzyć nowe zmienne środowiskowe dla identyfikatora subskrypcji Twojego konta platformy Azure, a także klucz, punkt końcowy i Identyfikator subskrypcji nowego konta (docelowego). 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Uwierzytelnianie klienta docelowego

Później w skrypcie Zapisz bieżący obiekt klienta jako klienta źródłowego, a następnie Uwierzytelnij nowy obiekt klienta dla subskrypcji docelowej. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Użyj migawki

Pozostałe operacje tworzenia migawek odbywają się w ramach funkcji asynchronicznej. 

1. Pierwszym krokiem jest **wykonanie** migawki, która zapisuje dane pierwotnej subskrypcji w tymczasowej lokalizacji w chmurze. Ta metoda zwraca identyfikator używany do wykonywania zapytań dotyczących stanu operacji.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Następnie wykonaj zapytanie o identyfikator do momentu zakończenia operacji.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Ten kod wykorzystuje `wait_for_operation` funkcję, którą należy zdefiniować osobno:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Wróć do funkcji asynchronicznej. Użyj operacji **Zastosuj** , aby zapisać swoje dane z Twojej subskrypcji docelowej. Ta metoda zwraca również identyfikator.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Ponownie Użyj funkcji, `wait_for_operation` aby zbadać identyfikator do momentu zakończenia operacji.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Po wykonaniu tych kroków będziesz mieć możliwość uzyskania dostępu do konstrukcji danych własnych z nowej subskrypcji (docelowej).

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację rozpoznawania aplikacji z katalogu aplikacji za pomocą `python` polecenia.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Jeśli utworzono **osobę** z tego przewodnika Szybki Start i chcesz ją usunąć, uruchom następujący kod w skrypcie:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

W przypadku migrowania danych przy użyciu funkcji Snapshot w tym przewodniku Szybki Start należy również usunąć **osobę** , która została zapisana do subskrypcji docelowej.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start pokazano, jak używać biblioteki klienta twarzy dla języka Python, aby wykonywać podstawowe zadania rozpoznawania twarzy. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie interfejs API rozpoznawania twarzy (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Co to jest usługa rozpoznawania twarzy?](../../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
