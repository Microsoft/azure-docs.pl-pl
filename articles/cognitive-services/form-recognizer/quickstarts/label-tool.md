---
title: 'Szybki start: etykietuj formularze, trenuj model i analizuj formularz za pomocą przykładowego narzędzia do etykietowania — Aparat rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz przykładowego narzędzia do etykietowania aparatów rozpoznawania formularzy, aby ręcznie oznaczyć dokumenty formularza. Następnie będziesz trenować model niestandardowy z etykietami dokumentów i użyć modelu do wyodrębnienia par klucz/wartość.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 790e2a148385f9da54df82f597c2ca52124dc2be
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529874"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Szkolenie modelu aparatu rozpoznawania formularzy z etykietami przy użyciu przykładowego narzędzia do etykietowania

W tym przewodniku Szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy z przykładowym narzędziem do etykietowania, aby wyszkolić model niestandardowy z danymi oznaczonymi ręcznie. Zobacz [Train with labels](../overview.md#train-with-labels) sekcji przeglądu, aby dowiedzieć się więcej o tej funkcji.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, musisz mieć:

- Zestaw co najmniej sześciu form tego samego typu. Użyjesz tych danych do szkolenia modelu i przetestowania formularza. Do tego szybkiego startu można użyć [przykładowego zestawu danych.](https://go.microsoft.com/fwlink/?linkid=2090451) Przekaż pliki szkoleniowe do katalogu głównego kontenera magazynu obiektów blob na koncie usługi Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularzy

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>Konfigurowanie przykładowego narzędzia do etykietowania

Aparat platformy Docker użyjesz do uruchomienia przykładowego narzędzia do etykietowania. Wykonaj następujące kroki, aby skonfigurować kontener platformy Docker. Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).

> [!TIP]
> Narzędzie do etykietowania formularzy OCR jest również dostępne jako projekt open source w usłudze GitHub. Narzędzie jest aplikacją internetową stworzoną przy użyciu React + Redux i jest napisane w TypeScript. Aby dowiedzieć się więcej lub przyczynić się, zobacz [Narzędzie do etykietowania formularzy OCR](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application).

1. Najpierw zainstaluj program Docker na komputerze-hoście. W tym przewodniku pokazano, jak używać komputera lokalnego jako hosta. Jeśli chcesz użyć usługi hostingu platformy Docker na platformie Azure, zobacz [przewodnik Wdrażanie przykładowego narzędzia etykietowania.](../deploy-label-tool.md) 

   Komputer-host musi spełniać następujące wymagania sprzętowe:

    | Kontener | Minimalne | Zalecane|
    |:--|:--|:--|
    |Przykładowe narzędzie do etykietowania|2-rdzeniowa pamięć 4 GB|4-rdzeniowa pamięć 8 GB|

   Zainstaluj platformę Docker na komputerze, postępując zgodnie z odpowiednimi instrukcjami dotyczącymi systemu operacyjnego: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Pobierz przykładowy kontener narzędzi `docker pull` do etykietowania za pomocą polecenia.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Teraz możesz przystąpić do uruchamiania kontenera za pomocą `docker run`pliku .
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   To polecenie udostępni przykładowe narzędzie do etykietowania za pośrednictwem przeglądarki internetowej. Przejdź [http://localhost:3000](http://localhost:3000)do .

> [!NOTE]
> Można również oznaczyć dokumenty i szkolić modele za pomocą interfejsu API REST aparatu rozpoznawania formularzy. Aby trenować i analizować za pomocą interfejsu API REST, zobacz [Trenuj z etykietami przy użyciu interfejsu API REST i Języka Python](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Konfigurowanie danych wejściowych

Najpierw upewnij się, że wszystkie dokumenty szkoleniowe są w tym samym formacie. Jeśli masz formularze w wielu formatach, zorganizuj je w podfoldery na podstawie wspólnego formatu. Podczas szkolenia, należy skierować interfejs API do podfolderu.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Konfigurowanie współużytku zasobów między domenami (CORS)

Włącz usługę CORS na koncie magazynu. Wybierz swoje konto magazynu w witrynie Azure portal i kliknij kartę **CORS** w lewym okienku. W dolnej linii wypełnij następujące wartości. Następnie kliknij przycisk **Zapisz** u góry.

* Dozwolone początki = * 
* Dozwolone metody \[= wybierz wszystkie\]
* Dozwolone nagłówki = *
* Odsłonięte nagłówki = * 
* Maksymalny wiek = 200

> [!div class="mx-imgBorder"]
> ![Konfiguracja cors w witrynie Azure portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Łączenie się z przykładowym narzędziem do etykietowania

Przykładowe narzędzie do etykietowania łączy się ze źródłem (gdzie znajdują się oryginalne formularze) i obiektem docelowym (w którym eksportuje utworzone etykiety i dane wyjściowe).

Połączenia można dzielić i udostępniać między projektami. Używają rozszerzalnego modelu dostawcy, dzięki czemu można łatwo dodać nowych dostawców źródłowych/docelowych.

Aby utworzyć nowe połączenie, kliknij ikonę **Nowe połączenia** (wtyczka) na lewym pasku nawigacyjnym.

Wypełnij pola następującymi wartościami:

* **Nazwa wyświetlana** — nazwa wyświetlana połączenia.
* **Opis** - Opis projektu.
* **Adres URL sygnatury dostępu** Współdzielonego (SAS) kontenera usługi Azure Blob Storage. Aby pobrać adres URL sygnatury dostępu Współdzielonego, otwórz Eksploratora magazynu Platformy Microsoft Azure, kliknij prawym przyciskiem myszy kontener i wybierz pozycję **Pobierz podpis dostępu współdzielonego**. Ustaw czas wygaśnięcia na jakiś czas po tym, jak będziesz korzystać z usługi. Upewnij się, że zaznaczone są uprawnienia **Odczyt,** **Zapis,** **Usuń**i **Lista,** a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość w sekcji **ADRES URL.** Powinien mieć formularz: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

![Ustawienia połączenia przykładowego narzędzia do etykietowania](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Tworzenie nowego projektu

W przykładowym narzędziu do etykietowania projekty przechowują konfiguracje i ustawienia. Utwórz nowy projekt i wypełnij pola następującymi wartościami:

* **Nazwa wyświetlana** - nazwa wyświetlana projektu
* **Token zabezpieczający** — niektóre ustawienia projektu mogą zawierać poufne wartości, takie jak klucze interfejsu API lub inne udostępnione wpisy tajne. Każdy projekt wygeneruje token zabezpieczający, który może służyć do szyfrowania/odszyfrowywania poufnych ustawień projektu. Tokeny zabezpieczające można znaleźć w ustawieniach aplikacji, klikając ikonę koła zębatego u dołu lewego paska nawigacyjnego.
* **Źródło połączenia** — połączenie usługi Azure Blob Storage utworzone w poprzednim kroku, który chcesz użyć dla tego projektu.
* **Ścieżka folderu** — opcjonalnie — jeśli formularze źródłowe znajdują się w folderze w kontenerze obiektów blob, określ tutaj nazwę folderu
* **Identyfikator rozpoznawania formularzy Uri** — adres URL punktu końcowego rozpoznawania formularzy.
* **Klucz INTERFEJSU API** — klucz subskrypcji rozpoznawania formularzy.
* **Opis** — opcjonalnie — opis projektu

![Nowa strona projektu w przykładowym narzędziu do etykietowania](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Oznaczanie formularzy etykietami

Podczas tworzenia lub otwierania projektu zostanie otwarte okno edytora tagów głównych. Edytor tagów składa się z trzech części:

* Okienko podglądu o zmiennym rozmiarze, które zawiera przewijaną listę formularzy z połączenia źródłowego.
* Główne okienko edytora, które umożliwia stosowanie tagów.
* Okienko edytora tagów, które umożliwia użytkownikom modyfikowanie, blokowanie, zmienianie kolejności i usuwanie tagów. 

### <a name="identify-text-elements"></a>Identyfikowanie elementów tekstowych

Kliknij **pozycję Uruchom ocr we wszystkich plikach** w lewym okienku, aby uzyskać informacje o układzie tekstu dla każdego dokumentu. Narzędzie do etykietowania będzie rysować obwiednie wokół każdego elementu tekstowego.

### <a name="apply-labels-to-text"></a>Stosowanie etykiet do tekstu

Następnie utworzysz znaczniki (etykiety) i zastosujesz je do elementów tekstowych, które mają rozpoznawać model.

1. Najpierw użyj okienka edytora tagów, aby utworzyć znaczniki, które chcesz zidentyfikować.
   1. Kliknij, **+** aby utworzyć nowy znacznik.
   1. Wprowadź nazwę znacznika.
   1. Naciśnij klawisz Enter, aby zapisać znacznik.
1. W edytorze głównym kliknij i przeciągnij, aby zaznaczyć jeden lub wiele wyrazów z wyróżnionych elementów tekstowych.
1. Kliknij znacznik, który chcesz zastosować, lub naciśnij odpowiedni klawisz klawiatury. Klawisze numeryczne są przypisywane jako skróty klawiszowe dla pierwszych 10 tagów. Możesz zamiecą kolejność znaczników za pomocą ikon strzałek w górę i w dół w okienku edytora tagów.
    > [!Tip]
    > Podczas etykietowania formularzy należy pamiętać o poniższych wskazówkach.
    > * Do każdego zaznaczonego elementu tekstowego można zastosować tylko jeden znacznik.
    > * Każdy znacznik można zastosować tylko raz na stronie. Jeśli wartość pojawia się wiele razy w tym samym formularzu, utwórz różne znaczniki dla każdego wystąpienia. Na przykład: "faktura# 1", "faktura# 2" i tak dalej.
    > * Znaczniki nie mogą obejmować między stronami.
    > * Etykiety wartości, które pojawiają się w formularzu; nie próbuj podzielić wartości na dwie części z dwoma różnymi znacznikami. Na przykład pole adresu powinno być oznaczone pojedynczym znacznikiem, nawet jeśli obejmuje wiele wierszy.
    > * Nie dołączaj kluczy do&mdash;oznaczonych pól tylko wartości.
    > * Dane tabeli powinny być wykrywane automatycznie i będą dostępne w wyjściowym pliku JSON. Jeśli jednak model nie wykryje wszystkich danych tabeli, można również ręcznie oznaczyć te pola. Oznacz każdą komórkę w tabeli inną etykietą. Jeśli formularze mają tabele z różna liczba wierszy, upewnij się, że tag co najmniej jeden formularz z największą możliwą tabelę.

![Okno edytora głównego przykładowego narzędzia do etykietowania](../media/label-tool/main-editor.png)

Wykonaj powyższe czynności, aby oznaczyć co najmniej pięć formularzy.

### <a name="specify-tag-value-types"></a>Określanie typów wartości znacznika

Opcjonalnie można ustawić typ oczekiwanych danych dla każdego tagu. Otwórz menu kontekstowe po prawej stronie znacznika i wybierz typ z menu. Ta funkcja umożliwia algorytmowi wykrywania, aby pewne założenia, które poprawią dokładność wykrywania tekstu. Zapewnia również, że wykryte wartości zostaną zwrócone w znormalizowanym formacie w ostatecznym wyjściu JSON. 

> [!div class="mx-imgBorder"]
> ![Wybór typu wartości za pomocą przykładowego narzędzia do etykietowania](../media/whats-new/formre-value-type.png)

Następujące typy wartości i odmiany są obecnie obsługiwane:
* `string`
    * domyślnie, `no-whitespaces`,`alphanumeric`
* `number`
    * Domyślny`currency`
* `date` 
    * `dmy`domyślnie, `mdy`,`ymd`
* `time`
* `integer`

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

Kliknij ikonę Pociąg w lewym okienku, aby otworzyć stronę Szkolenie. Następnie kliknij przycisk **Pociąg,** aby rozpocząć szkolenie modelu. Po zakończeniu procesu szkolenia zobaczysz następujące informacje:

* **Identyfikator modelu** — identyfikator modelu, który został utworzony i przeszkolony. Każde wywołanie szkolenia tworzy nowy model z własnym identyfikatorem. Skopiuj ten ciąg do bezpiecznej lokalizacji; będziesz go potrzebować, jeśli chcesz zrobić przewidywanie wywołania za pośrednictwem interfejsu API REST.
* **Średnia dokładność** — średnia dokładność modelu. Można poprawić dokładność modelu, etykietując dodatkowe formularze i ponownie trenując, aby utworzyć nowy model. Zalecamy rozpoczęcie od etykietowania pięciu formularzy i dodanie większej liczby formularzy w razie potrzeby.
* Lista tagów i szacowana dokładność na znacznik.

![widok szkolenia](../media/label-tool/train-screen.png)

Po zakończeniu szkolenia sprawdź wartość **średnia dokładność.** Jeśli jest niski, należy dodać więcej dokumentów wejściowych i powtórzyć powyższe kroki. Dokumenty, które zostały już oznaczone, pozostaną w indeksie projektu.

> [!TIP]
> Proces szkolenia można również uruchomić za pomocą wywołania interfejsu API REST. Aby dowiedzieć się, jak to zrobić, zobacz [Trenuj z etykietami przy użyciu języka Python](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Analizowanie formularza

Kliknij ikonę Predict (żarówka) po lewej stronie, aby przetestować model. Przekaż dokument formularza, który nie był używany w procesie szkolenia. Następnie kliknij przycisk **Predict** po prawej stronie, aby uzyskać prognozy klucz/wartość dla formularza. Narzędzie zastosuje znaczniki w obwiedniach i zgłosi zaufanie każdego tagu.

> [!TIP]
> Można również uruchomić analizowanie interfejsu API za pomocą wywołania REST. Aby dowiedzieć się, jak to zrobić, zobacz [Trenuj z etykietami przy użyciu języka Python](./python-labeled-data.md).

## <a name="improve-results"></a>Poprawa wyników

W zależności od zgłoszonej dokładności można wykonać dalsze szkolenia w celu ulepszenia modelu. Po wykonaniu prognozowania sprawdź wartości zaufania dla każdego z zastosowanych tagów. Jeśli średnia dokładność szkolenia wartość była wysoka, ale wyniki zaufania są niskie (lub wyniki są niedokładne), należy dodać plik używany do przewidywania do zestawu szkoleniowego, etykiety go i trenować ponownie.

Zgłoszona średnia dokładność, wyniki zaufania i rzeczywista dokładność mogą być niespójne, gdy analizowane dokumenty różnią się od tych używanych w szkoleniu. Pamiętaj, że niektóre dokumenty wyglądają podobnie, gdy są oglądane przez osoby, ale mogą wyglądać inaczej niż model SI. Na przykład można trenować z typem formularza, który ma dwie odmiany, gdzie zestaw szkoleniowy składa się z 20% odmiany A i 80% odmiany B. Podczas przewidywania wyniki zaufania dla dokumentów odmiany A mogą być niższe.

## <a name="save-a-project-and-resume-later"></a>Zapisywanie projektu i wznawianie później

Aby wznowić projekt w innym czasie lub w innej przeglądarce, należy zapisać token zabezpieczający projektu i ponownie wnieść go później. 

### <a name="get-project-credentials"></a>Uzyskaj poświadczenia projektu
Przejdź do strony ustawień projektu (ikona suwaka) i zanotuj nazwę tokenu zabezpieczającego. Następnie przejdź do ustawień aplikacji (ikona koła zębatego), która pokazuje wszystkie tokeny zabezpieczające w bieżącym wystąpieniu przeglądarki. Znajdź token zabezpieczający projektu i skopiuj jego nazwę i wartość klucza do bezpiecznej lokalizacji.

### <a name="restore-project-credentials"></a>Przywracanie poświadczeń projektu
Jeśli chcesz wznowić projekt, najpierw należy utworzyć połączenie z tym samym kontenerem magazynu obiektów blob. W tym celu powtórz powyższe czynności. Następnie przejdź do strony ustawień aplikacji (ikona koła zębatego) i sprawdź, czy istnieje token zabezpieczający projektu. Jeśli tak nie jest, dodaj nowy token zabezpieczający i skopiuj nazwę i klucz tokenu z poprzedniego kroku. Następnie kliknij pozycję Zapisz ustawienia. 

### <a name="resume-a-project"></a>Wznawianie projektu

Na koniec przejdź do strony głównej (ikona domu) i kliknij przycisk Otwórz projekt w chmurze. Następnie wybierz połączenie magazynu obiektów blob i wybierz plik *vott* projektu. Aplikacja załaduje wszystkie ustawienia projektu, ponieważ ma token zabezpieczający.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak użyć narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy do szkolenia modelu z danymi oznaczonymi ręcznie. Jeśli chcesz zintegrować narzędzie etykietowania z własną aplikacją, użyj interfejsów API REST, które zajmują się szkoleniem danych oznaczonych etykietami.

> [!div class="nextstepaction"]
> [Trenuj z etykietami za pomocą Pythona](./python-labeled-data.md)
