---
title: 'Szybki start: biblioteka klienta twarzy dla języka Python'
description: Ten szybki start pomoże Ci rozpocząć pracę z biblioteką klienta twarzy dla języka Python, aby wykryć, znaleźć podobne, zidentyfikować, zweryfikować i nie tylko.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5ac36e47efd2e121126cd740dba0818feb39f5fb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402884"
---
# <a name="quickstart-face-client-library-for-python"></a>Szybki start: biblioteka klienta twarzy dla języka Python

Wprowadzenie do biblioteki klienta twarzy dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Usługa Face zapewnia dostęp do zaawansowanych algorytmów wykrywania i rozpoznawania ludzkich twarzy na obrazach.

Użyj biblioteki klienta twarzy dla języka Python, aby:

* Wykrywanie twarzy na obrazie
* Wyszukiwanie podobnych twarzy
* Tworzenie i szkolenie grupy osób
* Identyfikowanie twarzy
* Weryfikowanie ścian
* Tworzenie migawki do migracji danych

[Dokumentacja](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [referencyjna Przykłady pakietu kodu źródłowego](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [biblioteki](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-face-azure-resource"></a>Tworzenie zasobu platformy Face Azure

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla twarzy przy użyciu [witryny Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również otrzymać [klucz testowy](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się będzie on dostępny w [witrynie sieci Web platformy Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/)  

Po otrzymasz klucz z subskrypcji próbnej lub zasobu, [utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla klucza i punktu końcowego, odpowiednio i `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT`,.
 
### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy&mdash;skrypt Języka Python*quickstart-file.py*, na przykład. Następnie otwórz go w preferowanym edytorze lub IDE i zaimportuj następujące biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Następnie utwórz zmienne dla punktu końcowego i klucza platformy Azure zasobu.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Jeśli po uruchomieniu aplikacji utworzono zmienną środowiskową, należy zamknąć i ponownie otworzyć edytor, IDE lub powłokę uruchamianą ją, aby uzyskać dostęp do zmiennej.

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Bibliotekę klienta można zainstalować za pomocą:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Model obiektu

Następujące klasy i interfejsy obsługują niektóre z głównych funkcji SDK języka Face Python.

|Nazwa|Opis|
|---|---|
|[FaceClient (FaceClient)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Ta klasa reprezentuje autoryzację do korzystania z usługi Face i jest potrzebna dla wszystkich funkcji face. Tworzenie wystąpienia z informacjami o subskrypcji i używać go do tworzenia wystąpień innych klas. |
|[Operacji twarzy](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Ta klasa obsługuje podstawowe zadania wykrywania i rozpoznawania, które można wykonać z ludzkimi twarzami. |
|[Wykrytaface](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Ta klasa reprezentuje wszystkie dane, które zostały wykryte z pojedynczej twarzy na obrazie. Można go użyć do pobrania szczegółowych informacji o twarzy.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Ta klasa zarządza konstrukcjami **FaceList** przechowywanymi w chmurze, które przechowują różne zestaw twarzy. |
|[PersonGroupPersonOperacje](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Ta klasa zarządza konstrukcjami **person** przechowywanych w chmurze, które przechowują zestaw ścian należących do jednej osoby.|
|[Działania grupy person](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Ta klasa zarządza konstrukcjami **persongroup** przechowywanych w chmurze, które przechowują zestaw różnych **person** obiektów. |
|[ShapshotOperations (Operacji shapshot)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Ta klasa zarządza funkcje migawka; można go użyć do tymczasowego zapisywania wszystkich danych twarzy w chmurze i migracji tych danych do nowej subskrypcji platformy Azure. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki klienta face dla języka Python:

* [Uwierzytelnij klienta](#authenticate-the-client)
* [Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)
* [Wyszukiwanie podobnych twarzy](#find-similar-faces)
* [Tworzenie i szkolenie grupy osób](#create-and-train-a-person-group)
* [Identyfikowanie twarzy](#identify-a-face)
* [Weryfikowanie ścian](#verify-faces)
* [Tworzenie migawki do migracji danych](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

> [!NOTE]
> Ten przewodnik Szybki start zakłada utworzenie [zmiennej środowiskowej](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) `FACE_SUBSCRIPTION_KEY`dla klucza Twarz o nazwie .

Tworzenie wystąpienia klienta z punktem końcowym i kluczem. Utwórz [obiekt CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) za pomocą klucza i użyj go z punktem końcowym, aby utworzyć obiekt [FaceClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Wykrywanie twarzy na obrazie

Poniższy kod wykrywa twarz w obrazie zdalnym. Drukuje identyfikator wykrytej twarzy na konsoli, a także przechowuje ją w pamięci programu. Następnie wykrywa twarze na obrazie z wieloma osobami i drukuje ich identyfikatory na konsoli. Zmieniając parametry w [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) metody, można zwrócić różne informacje z każdego [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) obiektu.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Zobacz przykładowy kod w [usłudze GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) aby uzyskać więcej scenariuszy wykrywania.

### <a name="display-and-frame-faces"></a>Powierzchnie wyświetlania i ramki

Poniższy kod wyprowadza dany obraz do wyświetlania i rysuje prostokąty wokół twarzy, używając właściwości DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Młoda kobieta z czerwonym prostokątem narysowanym wokół twarzy](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Wyszukiwanie podobnych twarzy

Poniższy kod przyjmuje pojedynczą wykrytą twarz i przeszukuje zestaw innych twarzy, aby znaleźć dopasowania. Po znalezieniu dopasowania, drukuje współrzędne prostokąta dopasowanej ściany do konsoli. 

### <a name="find-matches"></a>Znajdź dopasowania

Najpierw uruchom kod w powyższej sekcji ([Wykrywanie twarzy na obrazie](#detect-faces-in-an-image)), aby zapisać odwołanie do pojedynczej ściany. Następnie uruchom następujący kod, aby uzyskać odwołania do kilku twarzy w obrazie grupy.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Następnie dodaj następujący blok kodu, aby znaleźć wystąpienia pierwszej ściany w grupie. Zobacz [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) metody, aby dowiedzieć się, jak zmodyfikować to zachowanie.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Drukuj dopasowania

Użyj następującego kodu, aby wydrukować szczegóły dopasowania na konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Tworzenie i szkolenie grupy osób

Poniższy kod tworzy **PersonGroup** z trzech różnych **Person** obiektów. Kojarzy każdą **osobę** z zestawem przykładowych obrazów, a następnie trenuje, aby móc rozpoznać każdą osobę. 

### <a name="create-persongroup"></a>Tworzenie grupy osób

Aby przejść przez ten scenariusz, należy zapisać następujące obrazy w katalogu https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesgłównym projektu: .

Ta grupa obrazów zawiera trzy zestawy obrazów twarzy odpowiadających trzem różnym osobom. Kod zdefiniuje trzy obiekty **Osoby** i skojarzy je z plikami obrazów rozpoczynającymi `woman`się od , `man`i `child`.

Po skonfigurowaniu obrazów zdefiniuj etykietę u góry skryptu dla utworzonego obiektu **PersonGroup.**

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Następnie dodaj następujący kod na dole skryptu. Ten kod tworzy **PersonGroup** i trzy **Person** obiektów.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Przypisywanie ścian do osób

Poniższy kod sortuje obrazy według ich prefiksu, wykrywa twarze i przypisuje ściany do każdego obiektu **Osoba.**

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Grupa osób pociągu

Po przypisaniu ścian należy przeszkolić **grupę osób,** aby mogła zidentyfikować obiekty wizualne skojarzone z każdym z jej obiektów **Person.** Poniższy kod wywołuje metodę **pociągu** asynchronicznej i sonduje wynik, drukując stan do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identyfikowanie twarzy

Poniższy kod wykonuje obraz z wieloma twarzami i wygląda, aby znaleźć tożsamość każdej osoby na obrazie. Porównuje każdą wykrytą twarz z **grupą osób**, bazą danych różnych obiektów **osoby,** których rysy twarzy są znane.

> [!IMPORTANT]
> Aby uruchomić ten przykład, należy najpierw uruchomić kod w [create i wyszkolić grupę osób](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Pobierz obraz testowy

Poniższy kod wygląda w katalogu głównym projektu dla obrazu _test-image-person-group.jpg_ i wykrywa twarze na obrazie. Ten obraz można znaleźć z obrazami używanymi do zarządzania **grupą persongroup:** https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identyfikowanie twarzy

Metoda **identyfikowania** przyjmuje tablicę wykrytych twarzy i porównuje je z **grupą persongroup**. Jeśli może dopasować wykrytą twarz do **osoby,** zapisuje wynik. Ten kod drukuje szczegółowe wyniki dopasowania do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Weryfikowanie ścian

Operacja Verify przyjmuje identyfikator twarzy i inny identyfikator twarzy lub obiekt **Person** i określa, czy należą one do tej samej osoby.

Poniższy kod wykrywa twarze w dwóch obrazach źródłowych, a następnie weryfikuje je względem twarzy wykrytej na obrazie docelowym.

### <a name="get-test-images"></a>Pobierz obrazy testowe

Następujące bloki kodu deklarują zmienne, które wskazują obrazy źródłowe i docelowe dla operacji weryfikacji.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Wykrywanie twarzy w celu weryfikacji

Poniższy kod wykrywa twarze w obrazach źródłowych i docelowych i zapisuje je w zmiennych.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Uzyskaj wyniki weryfikacji

Poniższy kod porównuje każdy z obrazów źródłowych do obrazu docelowego i drukuje komunikat wskazujący, czy należą one do tej samej osoby.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Tworzenie migawki do migracji danych

Funkcja Migawki umożliwia przenoszenie zapisanych danych twarzy, takich jak przeszkolony **persongroup,** do innej subskrypcji usługi Azure Cognitive Services Face. Możesz użyć tej funkcji, jeśli na przykład utworzono obiekt **PersonGroup** przy użyciu bezpłatnej subskrypcji próbnej, a teraz chcesz przeprowadzić migrację do płatnej subskrypcji. Zobacz [Migracja danych twarzy,](../Face-API-How-to-Topics/how-to-migrate-face-data.md) aby uzyskać ogólny przegląd funkcji Migawki.

W tym przykładzie zostanie migrowanie **grupy osób utworzonej** w [umień i szkolenie grupy osób](#create-and-train-a-person-group). Można najpierw ukończyć tę sekcję lub użyć własnej konstrukcji danych twarzy.

### <a name="set-up-target-subscription"></a>Konfigurowanie subskrypcji docelowej

Po pierwsze musisz mieć drugą subskrypcję platformy Azure z zasobem Face; Można to zrobić, wykonując czynności opisane w sekcji [Konfigurowanie.](#setting-up) 

Następnie należy utworzyć następujące zmienne w górnej części skryptu. Należy również utworzyć nowe zmienne środowiskowe dla identyfikatora subskrypcji konta platformy Azure, a także klucz, punkt końcowy i identyfikator subskrypcji nowego konta (docelowego). 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Uwierzytelnij klienta docelowego

W dalszej części skryptu zapisz bieżący obiekt klienta jako klienta źródłowego, a następnie uwierzytelnij nowy obiekt klienta dla subskrypcji docelowej. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Używanie migawki

Pozostałe operacje migawki odbywają się w ramach funkcji asynchroniczną. 

1. Pierwszym krokiem **jest,** aby zrobić migawkę, która zapisuje dane twarzy oryginalnej subskrypcji do tymczasowej lokalizacji w chmurze. Ta metoda zwraca identyfikator używany do wykonywania zapytań o stan operacji.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Następnie kwerendy identyfikator, dopóki operacja została zakończona.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Ten kod korzysta `wait_for_operation` z funkcji, którą należy zdefiniować oddzielnie:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Wróć do funkcji asynchronii. Użyj operacji **zastosuj,** aby zapisać dane twarzy do subskrypcji docelowej. Ta metoda zwraca również identyfikator.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Ponownie użyj `wait_for_operation` funkcji, aby zbadać identyfikator, dopóki operacja nie zostanie ukończona.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Po wykonaniu tych kroków będziesz mieć dostęp do konstrukcji danych twarzy z nowej subskrypcji (docelowej).

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `python` pomocą polecenia w pliku szybkiego startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie innych skojarzonych z nią zasobów.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Jeśli grupa **osób** została utworzona w tym przewodniku Szybki start i chcesz ją usunąć, uruchom w skrypcie następujący kod:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Jeśli dane zostały zmigrowane przy użyciu funkcji Migawka w tym przewodniku Szybki start, należy również usunąć **grupę persongroup** zapisaną w subskrypcji docelowej.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak używać biblioteki twarzy dla języka Python do wykonywania zadań bazowych. Następnie zapoznaj się z dokumentacją referencyjną, aby dowiedzieć się więcej o bibliotece.

> [!div class="nextstepaction"]
> [Odwołanie do interfejsu API twarzy (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Co to jest usługa rozpoznawania twarzy?](../overview.md)
* Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
