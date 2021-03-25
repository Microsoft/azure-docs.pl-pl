---
title: Content Moderator przewodniku szybki start dla biblioteki klienta języka Python
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start dowiesz się, jak rozpocząć pracę z biblioteką kliencką Content Moderator platformy Azure dla języka Python. Twórz oprogramowanie do filtrowania zawartości w aplikacji, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.custom: cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 06ff04d8615b7ebdda07e993a3fc560d44fbf702
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104647"
---
Zacznij korzystać z biblioteki klienta Content Moderator platformy Azure dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet PiPy i wypróbować przykładowy kod dla podstawowych zadań. 

Content Moderator to usługa AI, która umożliwia obsługę zawartości potencjalnie obraźliwej, ryzykownej lub niepożądanej. Użyj usługi moderowania zawartości opartej na AI do skanowania tekstu, obrazów i filmów wideo i automatycznego stosowania flag zawartości. Następnie Zintegruj swoją aplikację za pomocą narzędzia do przeglądu, środowiska moderatora online dla zespołu osób przeglądających ludzi. Twórz oprogramowanie do filtrowania zawartości w aplikacji, aby zachować zgodność z przepisami lub zachować zamierzone środowisko dla użytkowników.

Użyj biblioteki klienta Content Moderator dla języka Python, aby:

* Tekst umiarkowany
* Używanie listy warunków niestandardowych
* Obrazy umiarkowane
* Używanie niestandardowej listy obrazów
* Utwórz recenzję

[Dokumentacja](/python/api/overview/azure/cognitiveservices/contentmoderator)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator)  |  źródłowy biblioteki [Pakiet (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * Instalacja języka Python powinna obejmować [PIP](https://pip.pypa.io/en/stable/). Aby sprawdzić, czy jest zainstalowany program PIP, należy uruchomić `pip --version` polecenie w wierszu polecenia. Pobierz narzędzie PIP, instalując najnowszą wersję środowiska Python.
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" Utwórz zasób Content moderator "  target="_blank"> utwórz zasób Content Moderator </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Zaczekaj na jego wdrożenie i kliknij przycisk **Przejdź do zasobu** .
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć swoją aplikację z Content Moderator. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.


## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Po zainstalowaniu języka Python można zainstalować bibliotekę kliencką Content Moderator przy użyciu następującego polecenia:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy skrypt w języku Python i otwórz go za pomocą preferowanego edytora lub środowiska IDE. Następnie Dodaj poniższe `import` instrukcje na początku pliku.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py), która zawiera przykłady kodu w tym przewodniku Szybki Start.

Następnie utwórz zmienne dla lokalizacji i klucza punktu końcowego zasobu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli pomyślnie wdrożono zasób Content Moderator w sekcji **wymagania wstępne** , kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [Magazyn kluczy platformy Azure](../../../../key-vault/general/overview.md).

## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje biblioteki klienta języka Python Content Moderator.

|Nazwa|Opis|
|---|---|
|[ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient)|Ta klasa jest wymagana dla wszystkich funkcji Content Moderator. Tworzysz wystąpienie z informacjami o subskrypcji i używasz ich do tworzenia wystąpień innych klas.|
|[ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations)|Ta klasa udostępnia funkcje do analizowania obrazów na potrzeby zawartości dla dorosłych, danych osobowych lub osób ludzkich.|
|[TextModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations)|Ta klasa udostępnia funkcje do analizowania tekstu dla języka, niewulgarności, błędów i informacji osobistych.|
[ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations)|Ta klasa udostępnia funkcje interfejsów API przeglądu, w tym metody tworzenia zadań, niestandardowych przepływów pracy i przeglądów ludzkich.|

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienta Content Moderator dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Tekst umiarkowany](#moderate-text)
* [Używanie listy warunków niestandardowych](#use-a-custom-terms-list)
* [Obrazy umiarkowane](#moderate-images)
* [Używanie niestandardowej listy obrazów](#use-a-custom-image-list)
* [Utwórz recenzję](#create-a-review)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie klienta z punktem końcowym i kluczem. Utwórz obiekt [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) z kluczem i użyj go w punkcie końcowym, aby utworzyć obiekt [ContentModeratorClient](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient) .

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Tekst umiarkowany

Poniższy kod używa klienta Content Moderator, aby analizować treść tekstu i drukować wyniki w konsoli programu. Najpierw Utwórz **text_files/** folder w katalogu głównym projektu i dodaj plik *content_moderator_text_moderation.txt* . Dodaj własny tekst do tego pliku lub użyj następującego przykładowego tekstu:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Dodaj odwołanie do nowego folderu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Następnie Dodaj następujący kod do skryptu w języku Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Używanie listy warunków niestandardowych

Poniższy kod pokazuje, jak zarządzać listą niestandardowych terminów na potrzeby moderowania tekstu. Możesz użyć klasy [ListManagementTermListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations) , aby utworzyć listę warunków, zarządzać indywidualnymi postanowieniami i na ekranie inne treści tekstu.

### <a name="get-sample-text"></a>Pobierz przykładowy tekst

Aby użyć tego przykładu, należy utworzyć **text_files/** folder w katalogu głównym projektu i dodać plik *content_moderator_term_list.txt* . Ten plik powinien zawierać tekst organiczny, który zostanie sprawdzony względem listy warunków. Możesz użyć następującego przykładowego tekstu:

```
This text contains the terms "term1" and "term2".
```

Dodaj odwołanie do folderu, jeśli jeszcze nie został zdefiniowany.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Utwórz listę

Dodaj następujący kod do skryptu języka Python, aby utworzyć niestandardową listę warunków i zapisać jej wartość identyfikatora.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definiuj szczegóły listy

Aby edytować nazwę i opis, można użyć identyfikatora listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Dodawanie terminu do listy

Poniższy kod dodaje warunki `"term1"` i `"term2"` do listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Pobierz wszystkie warunki na liście

Możesz użyć identyfikatora listy, aby zwrócić wszystkie warunki z listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Odśwież indeks listy

Po każdym dodaniu lub usunięciu warunków z listy należy odświeżyć indeks, aby można było użyć zaktualizowanej listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Tekst ekranu na liście

Główną funkcją listy warunków niestandardowych jest porównanie treści tekstu z listą i Dowiedz się, czy istnieją odpowiednie warunki. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Usuwanie terminu z listy

Poniższy kod usuwa okres `"term1"` z listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Usuń wszystkie warunki z listy

Użyj poniższego kodu, aby wyczyścić listę wszystkich jej warunków.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-a-list"></a>Usuwanie listy

Użyj poniższego kodu, aby usunąć listę niestandardowych warunków.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Obrazy umiarkowane

Poniższy kod używa klienta Content Moderator, wraz z obiektem [ImageModerationOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations) , do analizowania obrazów pod kątem treści dla dorosłych i erotycznej.

### <a name="get-sample-images"></a>Pobierz przykładowe obrazy

Zdefiniuj odwołanie do niektórych obrazów do przeanalizowania.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Następnie Dodaj następujący kod, aby wykonać iterację obrazów. Reszta kodu w tej sekcji zostanie przestawiona w tej pętli.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Sprawdź zawartość dla dorosłych/erotycznej

Poniższy kod sprawdza obraz pod podanym adresem URL dla zawartości dla dorosłych lub erotycznej, a następnie drukuje wyniki do konsoli. Aby uzyskać informacje na temat tego, co oznaczają te warunki, zobacz Przewodnik dotyczący [pojęć związanych z moderowaniem obrazu](../../image-moderation-api.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Wyszukaj widoczny tekst

Poniższy kod sprawdza obraz pod kątem widocznej zawartości tekstowej i drukuje wyniki do konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sprawdź dostępność twarzy

Poniższy kod sprawdza obraz dla ludzkich twarzy i drukuje wyniki w konsoli programu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Używanie niestandardowej listy obrazów

Poniższy kod przedstawia sposób zarządzania niestandardową listą obrazów na potrzeby moderowania obrazu. Ta funkcja jest przydatna, jeśli platforma często odbiera wystąpienia tego samego zestawu obrazów, które chcesz wypróbować. Dzięki utrzymywaniu listy tych konkretnych obrazów można poprawić wydajność. Klasa [ListManagementImageListsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations) umożliwia tworzenie listy obrazów, zarządzanie poszczególnymi obrazami na liście i porównywanie innych obrazów.

Utwórz następujące zmienne tekstowe do przechowywania adresów URL obrazów, które będą używane w tym scenariuszu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Nie jest to właściwa lista, ale nieformalnej listy obrazów, które zostaną dodane w `add images` sekcji kodu.


### <a name="create-an-image-list"></a>Tworzenie listy obrazów

Dodaj następujący kod, aby utworzyć listę obrazów i zapisać odwołanie do jego identyfikatora.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Dodawanie obrazów do listy

Poniższy kod dodaje wszystkie obrazy do listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Zdefiniuj funkcję pomocnika **add_images** w innym miejscu w skrypcie.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Pobierz obrazy z listy

Poniższy kod drukuje nazwy wszystkich obrazów na liście.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Aktualizuj szczegóły listy

Aby zaktualizować nazwę i opis listy, można użyć identyfikatora listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Pobierz szczegóły listy

Użyj poniższego kodu, aby wydrukować bieżące szczegóły listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Odśwież indeks listy

Po dodaniu lub usunięciu obrazów należy odświeżyć indeks listy, aby można było użyć go do wyświetlania innych obrazów.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Dopasuj obrazy do listy

Główną funkcją list obrazów jest porównanie nowych obrazów i sprawdzenie, czy istnieją jakieś dopasowania.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Usuwanie obrazu z listy

Poniższy kod usuwa element z listy. W tym przypadku jest to obraz, który nie jest zgodny z kategorią listy.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Usuń wszystkie obrazy z listy

Użyj poniższego kodu, aby wyczyścić listę obrazów.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Usuń listę obrazów

Użyj poniższego kodu, aby usunąć daną listę obrazów.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Utwórz recenzję

Korzystając z biblioteki klienta języka Python Content Moderator, można uzyskać zawartość do [Narzędzia do przeglądu](https://contentmoderator.cognitive.microsoft.com) , aby umożliwić moderatorom ludzkim sprawdzenie. Aby dowiedzieć się więcej na temat narzędzia do przeglądu, zobacz [Przewodnik dotyczący koncepcyjnego narzędzia do przeglądu](../../review-tool-user-guide/human-in-the-loop.md).

Poniższy kod używa klasy [ReviewsOperations](/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations) w celu utworzenia przeglądu, pobrania jego identyfikatora i sprawdzenia jego szczegółów po odebraniu danych ludzkich za pośrednictwem portalu internetowego narzędzia do przeglądu.

### <a name="get-review-credentials"></a>Pobierz poświadczenia przeglądu

Najpierw Zaloguj się do narzędzia przeglądu i Pobierz swoją nazwę zespołu. Następnie przypisz go do odpowiedniej zmiennej w kodzie. Opcjonalnie można skonfigurować punkt końcowy wywołania zwrotnego, aby otrzymywać aktualizacje dotyczące działania przeglądu.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Utwórz przegląd obrazu

Dodaj następujący kod, aby utworzyć i opublikować przegląd dla danego adresu URL obrazu. Kod zapisuje odwołanie do identyfikatora przeglądu. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Pobierz szczegóły przeglądu

Użyj poniższego kodu, aby sprawdzić szczegóły danego przeglądu. Po utworzeniu recenzji możesz samodzielnie przejść do narzędzia do przeglądu i korzystać z zawartości. Aby dowiedzieć się, jak to zrobić, zobacz [Przewodnik dotyczący przeglądów](../../review-tool-user-guide/review-moderated-images.md). Po zakończeniu możesz ponownie uruchomić ten kod i pobrać wyniki procesu recenzowania.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Jeśli w tym scenariuszu użyto punktu końcowego wywołania zwrotnego, powinien on otrzymać zdarzenie w tym formacie:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `python` polecenia w pliku szybkiego startu.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób korzystania z biblioteki języka Python Content Moderator w celu wykonywania zadań moderowania. Następnie Dowiedz się więcej o moderowaniu obrazów lub innych nośnikach, odczytując Przewodnik koncepcyjny.

> [!div class="nextstepaction"]
>[Pojęcia związane z moderowaniem obrazu](../../image-moderation-api.md)
