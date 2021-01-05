---
title: Co to jest rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Usługa aparat rozpoznawania formularzy platformy Azure pozwala identyfikować i wyodrębniać pary klucz/wartość oraz dane tabeli z dokumentów formularzy, a także wyodrębniać główne informacje z przyjęć sprzedaży i kart firmowych.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Automatyczne przetwarzanie danych, przetwarzanie dokumentów, automatyczne wprowadzanie danych, przetwarzanie formularzy
ms.openlocfilehash: ed940622f72271ef3e606c5068babcb6366c31b6
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845525"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Aparat rozpoznawania formularzy platformy Azure to usługa poznawcze, która umożliwia tworzenie automatycznego oprogramowania do przetwarzania danych przy użyciu technologii uczenia maszynowego. Zidentyfikuj i Wyodrębnij tekst, pary klucz/wartość, znaczniki wyboru, tabele i struktury z dokumentów &mdash; , usługa zwraca dane strukturalne, które obejmują relacje w oryginalnym pliku, pola związane z ograniczeniami, pewność i inne. Szybko uzyskuj dokładne wyniki, które są dostosowane do konkretnej zawartości, bez znacznej interwencji ręcznej lub szczegółowej wiedzy o nauce danych. Używaj aparatu rozpoznawania formularzy do automatyzowania wprowadzania danych w aplikacjach i wzbogacania możliwości wyszukiwania dokumentów.

Aparat rozpoznawania formularzy składa się z modeli przetwarzania dokumentów niestandardowych, wstępnie utworzonych modeli dla faktur, przyjęć i kart służbowych oraz modelu układu. Można wywołać modele aparatu rozpoznawania formularzy przy użyciu interfejsu API REST lub zestawów SDK biblioteki klienta, aby zmniejszyć złożoność i zintegrować ją z przepływem pracy lub aplikacją.

Aparat rozpoznawania formularzy składa się z następujących usług:
* **[Interfejs API układu](#layout-api)** — Wyodrębnianie tekstu, znaczników wyboru i struktur tabel wraz ze współrzędnymi pola ograniczenia, z dokumentów.
* **[Modele niestandardowe](#custom-models)** — Wyodrębnij pary tekstu, par klucz/wartość, znaczniki wyboru i dane tabeli z formularzy. Te modele są przeszkolone przy użyciu własnych danych, dzięki czemu są dostosowane do Twoich formularzy.
* Wstępnie **[utworzone modele](#prebuilt-models)** — Wyodrębnij dane z unikatowych typów formularzy przy użyciu wbudowanych modeli. Obecnie dostępne są następujące modele wbudowane
    * [Faktury](./concept-invoices.md)
    * [Przyjęcia sprzedaży](./concept-receipts.md)
    * [Wizytówki](./concept-business-cards.md)


## <a name="try-it-out"></a>Czas to wypróbować

Aby wypróbować usługę aparat rozpoznawania formularzy, przejdź do narzędzia przykładowego interfejsu użytkownika w trybie online:


# <a name="v20"></a>[Wersja 2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [Wypróbuj wbudowane modele](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)
> [!div class="nextstepaction"]
> [Wypróbuj wbudowane modele](https://fott-preview.azurewebsites.net/)

---

Potrzebna jest subskrypcja platformy Azure ([Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)) i punkt końcowy [zasobu aparatu rozpoznawania formularzy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oraz klucz służący do wypróbowania usługi rozpoznawania formularzy.

## <a name="layout-api"></a>Interfejs API układu

Aparat rozpoznawania formularzy może wyodrębnić tekst, znaczniki wyboru i strukturę tabeli (numery wierszy i kolumn skojarzonych z tekstem) przy użyciu optycznego rozpoznawania znaków (OCR) o wysokiej rozdzielczości i zaawansowanego modelu uczenia głębokiego z dokumentów. Więcej informacji można znaleźć w przewodniku koncepcyjnym [układu](./concept-layout.md) .

:::image type="content" source="./media/tables-example.jpg" alt-text="przykład tabel" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Modele niestandardowe

Modele niestandardowe aparatu rozpoznawania formularzy są pouczeni do własnych danych i wystarczy pięć przykładowych formularzy wejściowych do uruchomienia. Model przetwarzania dokumentów przeszkolonych może wyprowadzać dane strukturalne, które zawierają relacje w oryginalnym dokumencie formularza. Po przeprowadzeniu szkolenia modelu można testować i przeszkolić go i ostatecznie użyć do niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

Podczas uczenia modeli niestandardowych można korzystać z następujących opcji: uczenie z danymi z etykietami i bez etykiet.

### <a name="train-without-labels"></a>Uczenie bez etykiet

Domyślnie aparat rozpoznawania formularzy używa nienadzorowanej uczenia, aby zrozumieć układ i relacje między polami i pozycjami w formularzach. Po przesłaniu formularzy wejściowych algorytm tworzy klastry według typu, odnajduje, jakie klucze i tabele są obecne, i kojarzy wartości z kluczami i wpisami w tabelach. Nie wymaga to ręcznej etykietowania danych ani intensywnego kodowania i konserwacji, dlatego zalecamy wypróbowanie tej metody w pierwszej kolejności.

Zobacz [Tworzenie zestawu danych szkoleniowych,](./build-training-data-set.md) Aby uzyskać porady dotyczące sposobu zbierania dokumentów szkoleniowych.

### <a name="train-with-labels"></a>Uczenie z etykietami

W przypadku uczenia się z danymi z etykietami model przeprowadza nadzorowane uczenie w celu wyodrębnienia interesujących wartości przy użyciu podanych formularzy z etykietami. Prowadzi to do lepszego wykonywania modeli i może generować modele, które współpracują z złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.

Aparat rozpoznawania formularzy używa [interfejsu API układu](#layout-api) , aby poznać oczekiwane rozmiary i położenia elementów tekstu wydrukowanych i odręcznych. Następnie używa etykiet określonych przez użytkownika, aby poznać skojarzenia klucz/wartość w dokumentach. Zalecamy używanie pięciu ręcznie oznaczonych etykiet tego samego typu (tej samej struktury), aby rozpocząć pracę w przypadku uczenia nowego modelu i dodania do niego większej ilości danych, aby poprawić dokładność modelu.

[Wprowadzenie do uczenia się z etykietami](./quickstarts/label-tool.md)


> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


## <a name="prebuilt-models"></a>Wstępnie utworzone modele

Aparat rozpoznawania formularzy zawiera również wstępnie zbudowane modele do automatycznego przetwarzania danych unikatowych typów.

### <a name="prebuilt-invoice-model"></a>Wstępnie zbudowany model faktury
Wstępnie skompilowany model faktury wyodrębnia dane z faktur w różnych formatach i zwraca dane strukturalne. Ten model wyodrębnia kluczowe informacje, takie jak identyfikator faktury, szczegóły klienta, szczegóły dostawcy, dostawa do, rachunek do, suma, podatek, suma częściowa i inne. Ponadto prekompilowany model faktury jest szkolony do rozpoznawania i zwracania całego tekstu i tabel na fakturze. Więcej informacji można znaleźć w przewodniku koncepcyjnym dotyczącym [faktur](./concept-invoices.md) .

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Przykładowa faktura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Wstępnie utworzony model przyjęcia

Wstępnie utworzony model przyjęcia jest używany do odczytywania z angielskiego zakupu sprzedaży z Australii, Kanady, Wielkiej Brytanii, Indii i Stany Zjednoczone &mdash; typu używanego przez Restauracje, stacje gazu, sprzedaż detaliczna i tak dalej. Ten model wyodrębnia kluczowe informacje, takie jak godzina i Data transakcji, informacje o sprzedawcy, kwoty podatków, pozycje wiersza, sumy itd. Ponadto wstępnie utworzony model paragonów jest szkolony do rozpoznawania i zwracania całego tekstu w paragonie. Więcej informacji można znaleźć w przewodniku koncepcyjnym dotyczącym [potwierdzeń](./concept-receipts.md) .

:::image type="content" source="./media/overview-receipt.jpg" alt-text="Przykładowe potwierdzenie" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-business-cards-model"></a>Model prebudowanych kart roboczych

Model wizytówek umożliwia wyodrębnienie informacji, takich jak imię i nazwisko osoby, stanowiska, adres e-mail, firma i numery telefonów z kart służbowych w języku angielskim. Więcej informacji można znaleźć w przewodniku po pojęciach dotyczących [kart służbowych](./concept-business-cards.md) .

:::image type="content" source="./media/overview-business-card.jpg" alt-text="Przykładowa karta biznesowa" lightbox="./media/overview-business-card.jpg":::


## <a name="get-started"></a>Rozpoczęcie pracy

Skorzystaj z [przykładowego narzędzia rozpoznawania formularzy](https://fott.azurewebsites.net/) lub Skorzystaj z przewodnika Szybki Start, aby rozpocząć wyodrębnianie danych z formularzy. Zalecamy korzystanie z bezpłatnej usługi podczas nauki technologii. Należy pamiętać, że liczba bezpłatnych stron jest ograniczona do 500 miesięcznie.

* [Biblioteka klienta/interfejs API REST — szybki start](./quickstarts/client-library.md) (wszystkie języki, wiele scenariuszy)
* Przewodnik Szybki Start dotyczący interfejsu użytkownika sieci Web
  * [Szkolenie z etykietami — przykładowe narzędzie do etykietowania](quickstarts/label-tool.md)
* Przykłady REST (GitHub)
 * Wyodrębnij tekst, znaczniki wyboru i strukturę tabeli z dokumentów
    * [Wyodrębnij dane układu — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
  * Uczenie modeli niestandardowych i wyodrębnianie danych formularza
    * [Uczenie bez etykiet — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
    * [Uczenie z etykietami — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
  * Wyodrębnij dane z faktur
    * [Wyodrębnij dane faktury — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
  * Wyodrębnij dane z przyjęć sprzedaży
    * [Wyodrębnij dane przyjęcia — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
  * Wyodrębnianie danych z wizytówek
    * [Wyodrębnianie danych z kart służbowych — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>Przeglądanie interfejsów API REST

Poniższe interfejsy API służą do uczenia modeli i wyodrębnienia danych strukturalnych z formularzy.

|Nazwa |Opis |
|---|---|
| **Analizowanie układu** | Analiza dokumentu przeniesiona jako strumień służący do wyodrębniania tekstu, znaczników wyboru, tabel i struktury z dokumentu |
| **Uczenie modelu niestandardowego**| Uczenie nowego modelu do analizowania formularzy przy użyciu pięciu postaci tego samego typu. Ustaw parametr _useLabelFile_ , aby `true` można było nauczyć się z ręcznie oznaczonymi danymi. |
| **Analiza formularza** |Analizuj formularz przekazaną jako strumień, aby wyodrębnić pary tekstu, klucza/wartości i tabele z formularza z modelem niestandardowym.  |
| **Analizowanie faktury** | Analizuj fakturę, aby wyodrębnić informacje o kluczu, tabelach i innych tekstach faktury.|
| **Analizuj potwierdzenie** | Analizuj dokument paragonu w celu wyodrębnienia informacji o kluczu i innego tekstu paragonu.|
| **Analizuj wizytówkę** | Analizuj kartę biznesową, aby wyodrębnić informacje o kluczu i tekst.|


# <a name="v20"></a>[Wersja 2.0](#tab/v2-0)
Zapoznaj się z [dokumentacją interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) , aby dowiedzieć się więcej. Jeśli znasz poprzednią wersję interfejsu API, zapoznaj się z artykułem [co nowego](./whats-new.md) , aby dowiedzieć się więcej o najnowszych zmianach.

# <a name="v21"></a>[Wersja 2.1](#tab/v2-1)
Zapoznaj się z [dokumentacją interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeWithCustomForm) , aby dowiedzieć się więcej. Jeśli znasz poprzednią wersję interfejsu API, zapoznaj się z artykułem [co nowego](./whats-new.md) , aby dowiedzieć się więcej o najnowszych zmianach.

---

## <a name="input-requirements"></a>Wymagania wejściowe

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Wdrażanie lokalnie przy użyciu kontenerów platformy Docker

[Używanie kontenerów aparatu rozpoznawania formularzy (wersja zapoznawcza)](form-recognizer-container-howto.md) do wdrażania funkcji interfejsu API w środowisku lokalnym. Ten kontener platformy Docker umożliwia przełączenie usługi do danych pod kątem zgodności, zabezpieczeń lub innych przyczyn operacyjnych.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich usług poznawczych, deweloperzy korzystający z usługi rozpoznawania formularzy powinni mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Ukończ [Przewodnik Szybki Start](quickstarts/client-library.md) , aby rozpocząć pisanie aplikacji przetwarzania formularzy przy użyciu aparatu rozpoznawania formularzy w wybranym języku.