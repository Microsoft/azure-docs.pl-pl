---
title: 'Szybki Start: formularze etykiet, uczenie modelu i analizowanie formularzy przy użyciu narzędzia do etykietowania przykładowego'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start będziesz używać narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy do ręcznego etykietowania dokumentów. Następnie nauczysz model przetwarzania dokumentu niestandardowego z oznaczonymi dokumentami i użyj modelu, aby wyodrębnić pary klucz/wartość.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Przetwarzanie dokumentu
ms.openlocfilehash: 89de0752b3015fb8132bfa50c7dbdce174061bcc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467297"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Uczenie modelu aparatu rozpoznawania formularzy z etykietami przy użyciu narzędzia do etykietowania przykładowego

W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy z przykładowym narzędziem do etykietowania do uczenia niestandardowego modelu przetwarzania dokumentów z ręcznie oznaczonymi danymi. Zapoznaj się z sekcją [uczenie z etykietami](../overview.md#train-with-labels) , aby dowiedzieć się więcej na temat nadzorowanej nauki z aparatem rozpoznawania formularzy.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Utwórz zasób aparatu rozpoznawania formularzy "  target="_blank"> Utwórz zasób aparatu rozpoznawania formularza </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu wybierz pozycję **Przejdź do zasobu**.
  * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API rozpoznawania formularzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
  * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Zestaw składający się z co najmniej sześciu formularzy tego samego typu. Te dane będą używane do uczenia modelu i testowania formularza. Możesz użyć [przykładowego zestawu danych](https://go.microsoft.com/fwlink/?linkid=2090451) (Pobierz i wyodrębnij *sample_data.zip*) dla tego przewodnika Szybki Start. Przekaż pliki szkoleniowe do katalogu głównego kontenera magazynu obiektów BLOB na koncie usługi Azure Storage w warstwie Standardowa wydajność.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="try-it-out"></a>Czas to wypróbować

Aby wypróbować narzędzie do etykietowania próbek aparatu rozpoznawania w trybie online, przejdź do [witryny sieci Web FOTT](https://fott-preview.azurewebsites.net/).

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

> [!div class="nextstepaction"]
> [Wypróbuj wbudowane modele](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Wypróbuj wbudowane modele](https://fott.azurewebsites.net/)

---

Potrzebna jest subskrypcja platformy Azure ([Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)) i punkt końcowy [zasobu aparatu rozpoznawania formularzy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oraz klucz służący do wypróbowania usługi rozpoznawania formularzy.

## <a name="set-up-the-sample-labeling-tool"></a>Konfigurowanie przykładowego narzędzia do etykietowania

Użyjesz aparatu platformy Docker, aby uruchomić przykładowe narzędzie do etykietowania. Wykonaj następujące kroki, aby skonfigurować kontener platformy Docker. Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).

> [!TIP]
> Narzędzie do etykietowania formularza OCR jest również dostępne jako projekt Open Source w witrynie GitHub. Narzędzie to aplikacja sieci Web języka TypeScript skompilowana przy użyciu reakcji i Redux. Aby dowiedzieć się więcej lub współtworzyć, zobacz repozytorium [narzędzi do etykietowania formularzy OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application) . Aby wypróbować narzędzie w trybie online, przejdź do [witryny sieci Web FOTT](https://fott.azurewebsites.net/).

1. Najpierw zainstaluj platformę Docker na komputerze-hoście. W tym przewodniku pokazano, jak używać komputera lokalnego jako hosta. Jeśli chcesz używać usługi hostingu platformy Docker na platformie Azure, zobacz Przewodnik dotyczący [wdrażania przykładowego narzędzia do etykietowania](../deploy-label-tool.md) .

   Komputer hosta musi spełniać następujące wymagania sprzętowe:

    | Kontener | Minimum | Zalecane|
    |:--|:--|:--|
    |Przykładowe narzędzie do etykietowania|2 rdzeń, 4 GB pamięci|4 rdzenie, 8 GB pamięci|

   Zainstaluj platformę Docker na maszynie, postępując zgodnie z odpowiednimi instrukcjami dla danego systemu operacyjnego:

   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Pobierz kontener narzędzia do etykietowania przykładowego za pomocą `docker pull` polecenia.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```console
 docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```console
docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
```

---
</br>
  3. Teraz możesz przystąpić do uruchamiania kontenera za pomocą `docker run` .

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

```console
 docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview eula=accept
```

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

```console
docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
```

---

   To polecenie spowoduje udostępnienie przykładowego narzędzia do etykietowania za pomocą przeglądarki sieci Web. Przejdź do witryny `http://localhost:3000`.

> [!NOTE]
> Możesz także oznaczyć dokumenty i pouczenie modeli przy użyciu interfejsu API REST aparatu rozpoznawania formularzy. Aby nauczyć się i analizować za pomocą interfejsu API REST, zobacz [uczenie się z etykietami przy użyciu interfejsu API REST i języka Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="set-up-input-data"></a>Konfigurowanie danych wejściowych

Najpierw upewnij się, że wszystkie dokumenty szkoleniowe mają ten sam format. Jeśli masz formularze w wielu formatach, podziel je na podfoldery w oparciu o wspólny format. Podczas szkolenia będziesz kierować interfejs API do podfolderu.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Konfigurowanie udostępniania zasobów między domenami (CORS)

Włącz funkcję CORS na koncie magazynu. Wybierz konto magazynu w Azure Portal a następnie wybierz kartę **CORS** w okienku po lewej stronie. W dolnej linii Wypełnij poniższe wartości. Wybierz pozycję **Zapisz** u góry.

* Dozwolone źródła = *
* Dozwolone metody = \[ Zaznacz wszystko\]
* Dozwolone nagłówki = *
* Uwidocznione nagłówki = *
* Maksymalny wiek = 200

> [!div class="mx-imgBorder"]
> ![Konfiguracja mechanizmu CORS w Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Połącz z przykładowym narzędziem do etykietowania

 Przykładowe narzędzie do etykietowania łączy się ze źródłem (oryginalnie przekazanymi formularzami) i obiektem docelowym (utworzonymi etykietami i danymi wyjściowymi).

Połączenia można skonfigurować i udostępnić między projektami. Korzystają one z rozszerzalnego modelu dostawcy, dzięki czemu można łatwo dodawać nowych dostawców źródła/obiektu docelowego.

Aby utworzyć nowe połączenie, wybierz ikonę **nowe połączenia** (plug) na pasku nawigacyjnym po lewej stronie.

Wypełnij pola następującymi wartościami:

* **Nazwa wyświetlana** — nazwa wyświetlana połączenia.
* **Opis** — opis projektu.
* **Adres URL** sygnatury dostępu współdzielonego (SAS) dla kontenera BLOB Storage platformy Azure. [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="Pobieranie adresu URL SAS":::

:::image type="content" source="../media/label-tool/connections.png" alt-text="Ustawienia połączenia przykładowego narzędzia do etykietowania.":::

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przykładowym narzędziu do etykietowania projekty przechowują konfiguracje i ustawienia. Utwórz nowy projekt i wypełnij pola następującymi wartościami:

* **Nazwa wyświetlana** — nazwa wyświetlana projektu
* **Token zabezpieczający** — niektóre ustawienia projektu mogą zawierać wartości poufne, takie jak klucze interfejsu API lub inne wspólne klucze tajne. Każdy projekt generuje token zabezpieczający, który może służyć do szyfrowania/odszyfrowywania poufnych ustawień projektu. Tokeny zabezpieczające można znaleźć w ustawieniach aplikacji, wybierając ikonę koła zębatego u dołu lewego paska nawigacyjnego.
* **Połączenie źródłowe** — połączenie z usługą Azure Blob Storage utworzone w poprzednim kroku, którego chcesz użyć dla tego projektu.
* **Ścieżka folderu** — opcjonalne — Jeśli Twoje formularze źródłowe znajdują się w folderze kontenera obiektów blob, określ tutaj nazwę folderu
* **Identyfikator URI usługi rozpoznawania formularza** — adres URL punktu końcowego aparatu rozpoznawania formularza.
* **Klucz interfejsu API** — klucz subskrypcji aparatu rozpoznawania formularza.
* **Opis** — opcjonalnie — opis projektu

:::image type="content" source="../media/label-tool/new-project.png" alt-text="Nowa strona projektu na przykładowym narzędziu do etykietowania.":::

## <a name="label-your-forms"></a>Etykiety formularzy

Po utworzeniu lub otwarciu projektu zostanie otwarte okno edytora tagów głównych. Edytor tagów składa się z trzech części:

* Okienko podglądu o zmiennym rozmiarze, które zawiera przewijaną listę formularzy ze źródła połączenia.
* Główne okienko edytora, które pozwala na stosowanie tagów.
* Okienko edytora tagów, które pozwala użytkownikom modyfikować, blokować, zmieniać kolejność i usuwać Tagi.

### <a name="identify-text-and-tables"></a>Identyfikowanie tekstu i tabel 

Wybierz opcję **Uruchom OCR dla wszystkich plików** w okienku po lewej stronie, aby uzyskać informacje o układzie tekstu i tabeli dla każdego dokumentu. Narzędzie do etykietowania rysuje ramki ograniczenia wokół każdego elementu tekstowego.

Narzędzie do etykietowania również pokazuje, które tabele zostały wyodrębnione automatycznie. Wybierz ikonę tabela/siatka z lewej strony dokumentu, aby wyświetlić wyodrębnioną tabelę. W tym przewodniku Szybki Start, ponieważ zawartość tabeli jest wyodrębniana automatycznie, nie będziemy etykietować zawartości tabeli, ale raczej polegają na zautomatyzowanej ekstrakcji.

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Wizualizacja tabeli w przykładowym narzędziu do etykietowania.":::

W wersji 2.1, jeśli dokument szkoleniowy nie ma wypełnionej wartości, możesz narysować pole, w którym wartość powinna być. Użyj **rysowania regionu** w lewym górnym rogu okna, aby uczynić region taggable.

### <a name="apply-labels-to-text"></a>Zastosuj etykiety do tekstu

Następnie utworzysz Tagi (etykiety) i zastosujemy je do elementów tekstowych, które mają być analizowane przez model.

### <a name="v20"></a>[Wersja 2.0](#tab/v2-1)  

1. Najpierw użyj okienka edytora tagów, aby utworzyć Tagi, które chcesz zidentyfikować.
   1. Wybierz **+** , aby utworzyć nowy tag.
   1. Wprowadź nazwę tagu.
   1. Naciśnij klawisz ENTER, aby zapisać tag.
1. W edytorze głównym wybierz słowa z wyróżnionych elementów tekstowych lub regionu, w którym nastąpiło narysowane.
1. Wybierz tag, który chcesz zastosować, lub naciśnij odpowiedni klawisz klawiatury. Klucze liczb są przypisywane jako klawisze dostępu dla pierwszych 10 tagów. Można zmienić kolejność tagów przy użyciu ikon strzałek w górę i w dół w okienku Edytora tagów.
    > [!Tip]
    > Podczas etykietowania formularzy należy pamiętać o następujących wskazówkach:
    >
    > * Do każdego zaznaczonego elementu tekstowego można zastosować tylko jeden tag.
    > * Każdy tag można zastosować tylko raz na stronę. Jeśli wartość pojawia się wiele razy w tym samym formularzu, Utwórz różne Tagi dla każdego wystąpienia. Na przykład: "Invoice nr 1", "Invoice nr 2" i tak dalej.
    > * Znaczniki nie mogą obejmować między stronami.
    > * Etykiety wartości w postaci, w jakiej są wyświetlane w formularzu; nie próbuj podzielić wartości na dwie części z dwoma różnymi tagami. Na przykład pole adresu powinno mieć etykietę z pojedynczym tagiem nawet wtedy, gdy obejmuje wiele wierszy.
    > * Nie dołączaj kluczy w oznakowanych polach &mdash; tylko wartości.
    > * Dane tabeli powinny być wykrywane automatycznie i będą dostępne w końcowym wyjściowym pliku JSON. Jeśli jednak model nie wykryje wszystkich danych tabeli, możesz również ręcznie oznaczyć te pola. Oznacz każdą komórkę w tabeli inną etykietą. Jeśli formularze zawierają tabele o różnej liczbie wierszy, upewnij się, że tag zawiera co najmniej jeden formularz o największej możliwej tabeli.
    > * Użyj przycisków z prawej strony, **+** Aby przeszukać, zmienić nazwę, Zmień kolejność i usunąć Tagi.
    > * Aby usunąć zastosowany tag bez usuwania samego tagu, zaznacz znacznik oznakowany w widoku dokumentu i naciśnij klawisz Delete.
    >

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

1. Najpierw użyj okienka edytora tagów, aby utworzyć Tagi, które chcesz zidentyfikować.
   1. Wybierz **+** , aby utworzyć nowy tag.
   1. Wprowadź nazwę tagu.
   1. Naciśnij klawisz ENTER, aby zapisać tag.
1. W edytorze głównym wybierz pozycję słowa z wyróżnionych elementów tekstowych.
1. Wybierz tag, który chcesz zastosować, lub naciśnij odpowiedni klawisz klawiatury. Klucze liczb są przypisywane jako klawisze dostępu dla pierwszych 10 tagów. Można zmienić kolejność tagów przy użyciu ikon strzałek w górę i w dół w okienku Edytora tagów.
    > [!Tip]
    > Podczas etykietowania formularzy należy pamiętać o następujących wskazówkach:
    >
    > * Do każdego zaznaczonego elementu tekstowego można zastosować tylko jeden tag.
    > * Każdy tag można zastosować tylko raz na stronę. Jeśli wartość pojawia się wiele razy w tym samym formularzu, Utwórz różne Tagi dla każdego wystąpienia. Na przykład: "Invoice nr 1", "Invoice nr 2" i tak dalej.
    > * Znaczniki nie mogą obejmować między stronami.
    > * Etykiety wartości w postaci, w jakiej są wyświetlane w formularzu; nie próbuj podzielić wartości na dwie części z dwoma różnymi tagami. Na przykład pole adresu powinno mieć etykietę z pojedynczym tagiem nawet wtedy, gdy obejmuje wiele wierszy.
    > * Nie dołączaj kluczy w oznakowanych polach &mdash; tylko wartości.
    > * Dane tabeli powinny być wykrywane automatycznie i będą dostępne w końcowym wyjściowym pliku JSON. Jeśli jednak model nie wykryje wszystkich danych tabeli, możesz również ręcznie oznaczyć te pola. Oznacz każdą komórkę w tabeli inną etykietą. Jeśli formularze zawierają tabele o różnej liczbie wierszy, upewnij się, że tag zawiera co najmniej jeden formularz o największej możliwej tabeli.
    > * Użyj przycisków z prawej strony, **+** Aby przeszukać, zmienić nazwę, Zmień kolejność i usunąć Tagi.
    > * Aby usunąć zastosowany tag bez usuwania samego tagu, zaznacz znacznik oznakowany w widoku dokumentu i naciśnij klawisz Delete.
>

---
---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="Główne okno edytora przykładowego narzędzia do etykietowania.":::

Postępuj zgodnie z powyższymi krokami, aby oznaczyć co najmniej pięć formularzy.

### <a name="specify-tag-value-types"></a>Określanie typów wartości tagów

Można ustawić oczekiwany typ danych dla każdego tagu. Otwórz menu kontekstowe z prawej strony znacznika i wybierz typ z menu. Ta funkcja umożliwia algorytmowi wykrywania wykonywanie założeń, które spowodują poprawienie dokładności wykrywania tekstu. Zapewnia również, że wykryte wartości będą zwracane w formacie standardowym w końcowym danych wyjściowych JSON. Informacje o typie wartości są zapisywane w **fields.js** w pliku w tej samej ścieżce co pliki etykiet.

> [!div class="mx-imgBorder"]
> ![Wybór typu wartości z przykładowym narzędziem do etykietowania](../media/whats-new/value-type.png)

Następujące typy wartości i różnice są obecnie obsługiwane:

* `string`
  * domyślne, `no-whitespaces` , `alphanumeric`

* `number`
  * wartooć `currency`

* `date`
  * domyślne, `dmy` , `mdy` , `ymd`

* `time`
* `integer`
* `selectionMark` — _Nowość w wersji 2.1 — wersja zapoznawcza. 1!_

> [!NOTE]
> Zobacz te reguły formatowania dat:
>
> `dmy`Aby formatowanie daty działało, należy określić format (, `mdy` , `ymd` ).
>
> Jako ograniczników dat można używać następujących znaków: `, - / . \` . Nie można użyć odstępu jako ogranicznika. Na przykład:
>
> * 01, 01, 2020
> * 01-01-2020
> * 01/01/2020
>
> Każdy dzień i miesiąc mogą być zapisywane jako jedną lub dwie cyfry, a rok może składać się z dwóch lub czterech cyfr:
>
> * 1-1-2020
> * 1-01-20
>
> Jeśli ciąg daty ma osiem cyfr, ogranicznik jest opcjonalny:
>
> * 01012020
> * 01 01 2020
>
> Miesiąc można również zapisać jako jego pełną lub krótką nazwę. Jeśli nazwa jest używana, znaki ogranicznika są opcjonalne. Jednak ten format może być rozpoznawany mniej więcej niż inne.
>
> * 01/Jan/2020
> * 01Jan2020
> * 01 stycznia 2020

### <a name="label-tables-v21-only"></a>Tabele etykiet (tylko wersja 2.1)

Czasami dane mogą okazać się lepszym rozwiązaniem do etykietowania jako tabela, a nie par klucz-wartość. W takim przypadku można utworzyć tag tabeli, klikając pozycję "Dodaj nowy znacznik tabeli", określ, czy tabela będzie miała stałą liczbę wierszy lub zmienną liczbę wierszy w zależności od dokumentu, i zdefiniuj schemat.

:::image type="content" source="../media/label-tool/table-tag.png" alt-text="Konfigurowanie tagu tabeli.":::

Po zdefiniowaniu znacznika tabeli Oznacz wartości komórek.

:::image type="content" source="../media/table-labeling.png" alt-text="Etykietowanie tabeli.":::

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

Wybierz ikonę pouczenia w okienku po lewej stronie, aby otworzyć stronę szkolenia. Następnie wybierz przycisk **uczenie** , aby rozpocząć uczenie modelu. Po zakończeniu procesu szkolenia zostaną wyświetlone następujące informacje:

* **Identyfikator modelu** — Identyfikator modelu, który został utworzony i przeszkolony. Każde wywołanie szkoleniowe tworzy nowy model z własnym IDENTYFIKATORem. Skopiuj ten ciąg do bezpiecznej lokalizacji; będzie ona potrzebna, jeśli chcesz wykonywać wywołania prognoz za pomocą [interfejsu API REST](./client-library.md?pivots=programming-language-rest-api) lub [biblioteki klienckiej](./client-library.md).
* **Średnia dokładność** — średnia dokładność modelu. Możesz poprawić dokładność modelu, dodając etykiety do dodatkowych formularzy i przeszkolenia, aby utworzyć nowy model. Zalecamy rozpoczęcie od etykietowania pięciu formularzy i dodanie większej liczby formularzy zgodnie z wymaganiami.
* Lista tagów i Szacowana dokładność na tag.


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="Widok szkoleń.":::

Po zakończeniu szkolenia Przejrzyj wartość **średnia dokładność** . Jeśli jest to niska, należy dodać więcej dokumentów wejściowych i powtórz powyższe kroki. Dokumenty, które zostały już oznaczone etykietami, pozostaną w indeksie projektu.

> [!TIP]
> Możesz również uruchomić proces szkolenia przy użyciu wywołania interfejsu API REST. Aby dowiedzieć się, jak to zrobić, zobacz [uczenie się z etykietami przy użyciu języka Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="compose-trained-models"></a>Twórz przeszkolone modele

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

Funkcja Redagowanie modelu umożliwia utworzenie nawet 100 modeli w ramach jednego identyfikatora modelu. Po wywołaniu metody Analizuj przy użyciu programu `modelID` , aparat rozpoznawania formularza najpierw klasyfikuje przesłany formularz, wybierze najlepiej pasujący model, a następnie zwraca wyniki dla tego modelu. Ta operacja jest przydatna, gdy formularze przychodzące mogą należeć do jednego z kilku szablonów.

Aby zredagować modele w narzędziu przykładowego etykietowania, wybierz ikonę redagowanie modelu (Scalanie strzałki) po lewej stronie. Po lewej stronie wybierz modele, które chcesz utworzyć razem. Modele o ikonie strzałek mają już modele.
Wybierz **przycisk Utwórz**. W oknie podręcznym Nadaj nazwę nowemu modelowi złożonemu i wybierz pozycję **Utwórz**. Po zakończeniu operacji, nowo utworzony model powinien pojawić się na liście.

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="Widok redagowania modelu.":::

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

Ta funkcja jest obecnie dostępna w wersji 2.1. przeglądania.

---

## <a name="analyze-a-form"></a>Analizowanie formularza

Wybierz ikonę Predykcyjności (żarówki) po lewej stronie, aby przetestować model. Przekaż dokument formularza, który nie był używany w procesie szkoleniowym. Następnie wybierz przycisk **predykcyjny** po prawej stronie, aby uzyskać prognozy klucza/wartości dla formularza. Narzędzie zastosuje znaczniki w obwiedniach i zgłosi zaufanie każdego tagu.

> [!TIP]
> Można również uruchomić analizowanie interfejsu API z wywołaniem REST. Aby dowiedzieć się, jak to zrobić, zobacz [uczenie się z etykietami przy użyciu języka Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="improve-results"></a>Popraw wyniki

W zależności od raportowanej dokładności możesz chcieć przeprowadzić dalsze szkolenia, aby usprawnić model. Po zakończeniu przewidywania przejrzyj wartości zaufania dla każdego z zastosowanych tagów. Jeśli średnia wartość szkoleniowa jest wysoka, ale wyniki pewności są niskie (lub wyniki są niedokładne), należy dodać plik przewidywania do zestawu szkoleniowego, oznaczyć go etykietą i ponownie przeprowadzić uczenie.

Raportowane średnia dokładność, wyniki pewności i rzeczywista dokładność mogą być niespójne, gdy analizowane dokumenty różnią się od dokumentów używanych w szkoleniu. Należy pamiętać, że niektóre dokumenty wyglądają podobnie, gdy są wyświetlane przez osoby, ale mogą odróżnić się od modelu AI. Na przykład można pouczenie się typu formularza, który ma dwie odmiany, gdzie zestaw szkoleniowy składa się z 20% zmian A i 80% wariacji B. W czasie przewidywania Wyniki pewności dotyczące dokumentów odmiany A mogą być niższe.

## <a name="save-a-project-and-resume-later"></a>Zapisz projekt i Wznów później

Aby wznowić projekt w innym czasie lub w innej przeglądarce, musisz zapisać token zabezpieczający projektu i ponownie wprowadzić go później.

### <a name="get-project-credentials"></a>Pobierz poświadczenia projektu

Przejdź do strony ustawień projektu (ikona suwaka) i zanotuj nazwę tokenu zabezpieczającego. Następnie przejdź do ustawień aplikacji (ikony koła zębatego), które pokazują wszystkie tokeny zabezpieczające w bieżącym wystąpieniu przeglądarki. Znajdź token zabezpieczający projektu i skopiuj jego nazwę i wartość klucza do bezpiecznej lokalizacji.

### <a name="restore-project-credentials"></a>Przywróć poświadczenia projektu

Gdy chcesz wznowić projekt, musisz najpierw utworzyć połączenie z tym samym kontenerem usługi BLOB Storage. Aby to zrobić, powtórz powyższe kroki. Następnie przejdź do strony ustawień aplikacji (ikony koła zębatego) i sprawdź, czy jest tam używany token zabezpieczający projektu. Jeśli tak nie jest, Dodaj nowy token zabezpieczający i skopiuj go przy użyciu nazwy i klucza tokenu z poprzedniego kroku. Wybierz pozycję **Zapisz** , aby zachować ustawienia..

### <a name="resume-a-project"></a>Wznów projekt

Na koniec przejdź do strony głównej (ikona domu) i wybierz pozycję **Otwórz projekt w chmurze**. Następnie wybierz połączenie usługi BLOB Storage i wybierz plik **. fott** projektu. Aplikacja będzie ładować wszystkie ustawienia projektu, ponieważ ma token zabezpieczający.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób użycia narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy do uczenia modelu z ręcznymi etykietami danych. Jeśli chcesz utworzyć własne narzędzie do etykietowania danych szkoleniowych, Użyj interfejsów API REST, które zajmują się szkoleniem dotyczącym danych z etykietami.

> [!div class="nextstepaction"]
> [Uczenie z etykietami przy użyciu języka Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [Co to jest rozpoznawanie formularzy?](../overview.md)
* [Szybki Start dla aparatu rozpoznawania formularzy](client-library.md)
