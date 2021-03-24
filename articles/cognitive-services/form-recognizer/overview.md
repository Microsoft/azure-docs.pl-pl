---
title: Co to jest rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Usługa aparat rozpoznawania formularzy platformy Azure pozwala identyfikować i wyodrębniać pary klucz/wartość oraz dane tabeli z dokumentów formularzy, a także wyodrębniać główne informacje z przyjęć sprzedaży i kart firmowych.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Automatyczne przetwarzanie danych, przetwarzanie dokumentów, automatyczne wprowadzanie danych, przetwarzanie formularzy
ms.openlocfilehash: 4465f88e3b0ccab8eace1936f426af8dd32af27b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872255"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Aparat rozpoznawania formularzy platformy Azure to usługa poznawcze, która umożliwia tworzenie automatycznego oprogramowania do przetwarzania danych przy użyciu technologii uczenia maszynowego. Zidentyfikuj i Wyodrębnij tekst, pary klucz/wartość, znaczniki wyboru, tabele i struktury z dokumentów &mdash; , usługa zwraca dane strukturalne, które obejmują relacje w oryginalnym pliku, pola związane z ograniczeniami, pewność i inne. Szybko uzyskuj dokładne wyniki, które są dostosowane do konkretnej zawartości, bez znacznej interwencji ręcznej lub szczegółowej wiedzy o nauce danych. Używaj aparatu rozpoznawania formularzy do automatyzowania wprowadzania danych w aplikacjach i wzbogacania możliwości wyszukiwania dokumentów.

Aparat rozpoznawania formularzy składa się z niestandardowych modeli przetwarzania dokumentów, wstępnie utworzonych modeli dla faktur, przyjęć, identyfikatorów i kart służbowych oraz modelu układu. Można wywołać modele aparatu rozpoznawania formularzy przy użyciu interfejsu API REST lub zestawów SDK biblioteki klienta, aby zmniejszyć złożoność i zintegrować ją z przepływem pracy lub aplikacją.

Ta dokumentacja zawiera następujące typy artykułów:  

* Przewodniki [**Szybki Start**](quickstarts/client-library.md) to instrukcje umożliwiające wykonywanie żądań do usługi.  
* [**Przewodniki z**](build-training-data-set.md) instrukcjami dotyczącymi używania usługi w bardziej specyficzny lub dostosowany sposób.  
* [**Koncepcje**](concept-layout.md) zawierają szczegółowe wyjaśnienia funkcji i funkcji usługi.  
* [**Samouczki**](tutorial-bulk-processing.md) są więcej przewodnikami, które pokazują, jak korzystać z usługi jako składnika w szerszym zakresie rozwiązań dla biznesu.  

## <a name="form-recognizer-features"></a>Funkcje aparatu rozpoznawania formularzy

Dzięki aparatowi rozpoznawania formularzy możesz łatwo wyodrębniać i analizować dane formularzy przy użyciu następujących funkcji:

* **[Interfejs API układu](#layout-api)** — Wyodrębnianie tekstu, znaczników wyboru i struktur tabel wraz ze współrzędnymi pola ograniczenia, z dokumentów.
* **[Modele niestandardowe](#custom-models)** — Wyodrębnij pary tekstu, par klucz/wartość, znaczniki wyboru i dane tabeli z formularzy. Te modele są przeszkolone przy użyciu własnych danych, dzięki czemu są dostosowane do Twoich formularzy.

* Wstępnie **[utworzone modele](#prebuilt-models)** — Wyodrębnij dane z unikatowych typów dokumentów przy użyciu wbudowanych modeli. Obecnie dostępne są następujące modele wbudowane

  * [Faktury](./concept-invoices.md)
  * [Przyjęcia sprzedaży](./concept-receipts.md)
  * [Wizytówki](./concept-business-cards.md)
  * [Karty identyfikacyjne (ID)](./concept-identification-cards.md)


## <a name="get-started"></a>Rozpoczęcie pracy

Skorzystaj z przykładowego narzędzia rozpoznawania formularzy do wypróbowania układu, wstępnie skompilowanych modeli i uczenia modelu niestandardowego dla dokumentów. Potrzebna jest subskrypcja platformy Azure ([**Utwórz ją bezpłatnie**](https://azure.microsoft.com/free/cognitive-services)) i punkt końcowy [**zasobu aparatu rozpoznawania formularzy**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oraz klucz służący do wypróbowania usługi rozpoznawania formularzy.

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

> [!div class="nextstepaction"]
> [Wypróbuj aparat rozpoznawania formularzy](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Wypróbuj aparat rozpoznawania formularzy](https://fott.azurewebsites.net/)

---
Postępuj zgodnie z [biblioteką klienta/przewodnikiem Szybki start interfejsu API REST](./quickstarts/client-library.md) , aby rozpocząć wyodrębnianie danych z dokumentów. Zalecamy korzystanie z bezpłatnej usługi podczas nauki technologii. Należy pamiętać, że liczba bezpłatnych stron jest ograniczona do 500 miesięcznie.

Aby rozpocząć, możesz również użyć przykładów REST (GitHub). 

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
| **Identyfikator analizy** | Analizuj dokument identyfikatora karty, aby wyodrębnić informacje o kluczu i inny tekst karty identyfikacyjnej.|
| **Analizuj wizytówkę** | Analizuj kartę biznesową, aby wyodrębnić informacje o kluczu i tekst.|

### <a name="v21-preview"></a>[wersja zapoznawcza wersji 2.1](#tab/v2-1)

Zapoznaj się z [dokumentacją interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) , aby dowiedzieć się więcej. Jeśli znasz poprzednią wersję interfejsu API, zapoznaj się z artykułem [co nowego](./whats-new.md) , aby dowiedzieć się więcej o najnowszych zmianach.

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

Zapoznaj się z [dokumentacją interfejsu API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) , aby dowiedzieć się więcej. Jeśli znasz poprzednią wersję interfejsu API, zapoznaj się z artykułem [co nowego](./whats-new.md) , aby dowiedzieć się więcej o najnowszych zmianach.

---

## <a name="layout-api"></a>Interfejs API układu

Aparat rozpoznawania formularzy może wyodrębnić tekst, znaczniki wyboru i strukturę tabeli (numery wierszy i kolumn skojarzonych z tekstem) przy użyciu optycznego rozpoznawania znaków (OCR) o wysokiej rozdzielczości i zaawansowanego modelu uczenia głębokiego z dokumentów. Więcej informacji można znaleźć w przewodniku koncepcyjnym [układu](./concept-layout.md) .

:::image type="content" source="./media/tables-example.jpg" alt-text="przykład tabel" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Modele niestandardowe

Modele niestandardowe aparatu rozpoznawania formularzy są pouczeni do własnych danych i wystarczy pięć przykładowych formularzy wejściowych do uruchomienia. Model przetwarzania dokumentów przeszkolonych może wyprowadzać dane strukturalne, które zawierają relacje w oryginalnym dokumencie formularza. Po przeprowadzeniu szkolenia modelu można testować i przeszkolić go i ostatecznie użyć do niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

Podczas uczenia modeli niestandardowych można korzystać z następujących opcji: uczenie z danymi z etykietami i bez etykiet.

### <a name="train-without-labels"></a>Uczenie bez etykiet

Aparat rozpoznawania formularzy używa nienadzorowanej uczenia, aby zrozumieć układ i relacje między polami i pozycjami w formularzach. Po przesłaniu formularzy wejściowych algorytm tworzy klastry według typu, odnajduje, jakie klucze i tabele są obecne, i kojarzy wartości z kluczami i wpisami w tabelach. Szkolenia bez etykiet nie wymagają ręcznego etykietowania danych ani intensywnego kodowania i konserwacji, a zalecamy wypróbowanie tej metody w pierwszej kolejności.

Zobacz [Tworzenie zestawu danych szkoleniowych,](./build-training-data-set.md) Aby uzyskać porady dotyczące sposobu zbierania dokumentów szkoleniowych.

### <a name="train-with-labels"></a>Uczenie z etykietami

W przypadku uczenia się z danymi z etykietami model używa nadzorowanej uczenia do wyodrębnienia wartości zainteresowania przy użyciu podanego formularza z etykietami. Dane z etykietami są wynikiem lepszych modeli i mogą generować modele, które współpracują z złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.

Aparat rozpoznawania formularzy używa [interfejsu API układu](#layout-api) , aby poznać oczekiwane rozmiary i położenia elementów tekstu wydrukowanych i odręcznych oraz wyodrębniania tabel. Następnie używa etykiet określonych przez użytkownika, aby poznać skojarzenia klucz/wartość i tabele w dokumentach. Zalecamy używanie pięciu ręcznie oznaczonych etykiet tego samego typu (tej samej struktury), aby rozpocząć pracę w przypadku uczenia nowego modelu i dodania do niego większej ilości danych, aby poprawić dokładność modelu. Aparat rozpoznawania formularzy umożliwia uczenie modelu w celu wyodrębnienia par wartości klucza i tabel przy użyciu nadzorowanych możliwości uczenia. 

[Wprowadzenie do uczenia się z etykietami](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Wstępnie utworzone modele

Aparat rozpoznawania formularzy zawiera również wstępnie zbudowane modele do automatycznego przetwarzania danych unikatowych typów.

### <a name="prebuilt-invoice-model"></a>Wstępnie zbudowany model faktury

Wstępnie skompilowany model faktury wyodrębnia dane z faktur w różnych formatach i zwraca dane strukturalne. Ten model wyodrębnia kluczowe informacje, takie jak identyfikator faktury, szczegóły klienta, szczegóły dostawcy, dostawa do, rachunek do, suma, podatek, suma częściowa, elementy wiersza itd. Ponadto prekompilowany model faktur jest szkolony, aby analizować i zwracać cały tekst i tabele na fakturze. Więcej informacji można znaleźć w przewodniku koncepcyjnym dotyczącym [faktur](./concept-invoices.md) .

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Przykładowa faktura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Wstępnie utworzony model przyjęcia

Wstępnie utworzony model przyjęcia jest używany do odczytywania z angielskiego zakupu sprzedaży z Australii, Kanady, Wielkiej Brytanii, Indii i Stany Zjednoczone &mdash; typu używanego przez Restauracje, stacje gazu, sprzedaż detaliczna i tak dalej. Ten model wyodrębnia kluczowe informacje, takie jak godzina i Data transakcji, informacje o sprzedawcy, kwoty podatków, pozycje wiersza, sumy itd. Ponadto wstępnie utworzony model paragonów jest szkolony, aby analizować i zwracać cały tekst w paragonie. Więcej informacji można znaleźć w przewodniku koncepcyjnym dotyczącym [potwierdzeń](./concept-receipts.md) .

:::image type="content" source="./media/overview-receipt.jpg" alt-text="Przykładowe potwierdzenie" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Model wbudowanych kart identyfikacyjnych (ID)

Model kart identyfikacyjnych (ID) umożliwia wyodrębnienie najważniejszych informacji z paszportów opartych na całym świecie i licencji sterowników w Stanach Zjednoczonych. Dane takie są wyodrębniane, takie jak identyfikator dokumentu, Data wygaśnięcia urodzenia, Data wygaśnięcia, nazwa, kraj, region, strefa do odczytu maszynowego i wiele innych. Aby uzyskać więcej informacji, zobacz Przewodnik dotyczący [kart identyfikacyjnych (ID)](./concept-identification-cards.md) .

:::image type="content" source="./media/overview-id.jpg" alt-text="Przykładowa karta identyfikacyjna" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Model prebudowanych kart roboczych

Model wizytówek umożliwia wyodrębnienie informacji, takich jak imię i nazwisko osoby, stanowiska, adres e-mail, firma i numery telefonów z kart służbowych w języku angielskim. Więcej informacji można znaleźć w przewodniku po pojęciach dotyczących [kart służbowych](./concept-business-cards.md) .

:::image type="content" source="./media/overview-business-card.jpg" alt-text="Przykładowa karta biznesowa" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Wymagania wejściowe

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Wdrażanie lokalnie przy użyciu kontenerów platformy Docker

[Używanie kontenerów aparatu rozpoznawania formularzy (wersja zapoznawcza)](form-recognizer-container-howto.md) do wdrażania funkcji interfejsu API w środowisku lokalnym. Ten kontener platformy Docker umożliwia przełączenie usługi do danych pod kątem zgodności, zabezpieczeń lub innych przyczyn operacyjnych.

## <a name="service-availability-and-redundancy"></a>Dostępność i nadmiarowość usługi

### <a name="is-form-recognizer-service-zone-resilient"></a>Czy usługa aparat rozpoznawania formularzy jest odporna na błędy?

Tak. Usługa aparat rozpoznawania formularzy domyślnie jest odporna na strefy.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Jak mogę skonfigurować usługę aparat rozpoznawania formularzy, aby była odporna na strefy?

W celu włączenia odporności stref nie trzeba wykonywać żadnych czynności konfiguracyjnych. Odporność stref na zasoby dla aparatu rozpoznawania formularzy jest domyślnie dostępna i zarządzana przez samą usługę.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich usług poznawczych, deweloperzy korzystający z usługi rozpoznawania formularzy powinni mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z naszego narzędzia online i przewodnika Szybki Start, aby dowiedzieć się więcej na temat usługi aparat rozpoznawania formularzy.

* [**Narzędzie do rozpoznawania formularzy**](https://fott-preview.microsoft.com/)
* [**Biblioteka klienta i interfejs API REST — Szybki Start**](quickstarts/client-library.md)
