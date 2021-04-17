---
title: Co to jest rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Usługa Azure Rozpoznawanie formularzy umożliwia identyfikowanie i wyodrębnianie par klucz/wartość oraz danych tabeli z dokumentów formularzy, a także wyodrębnianie najważniejszych informacji z paragonów sprzedaży i wizytówek.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: zautomatyzowane przetwarzanie danych, przetwarzanie dokumentów, automatyczne wprowadzanie danych, przetwarzanie formularzy
ms.openlocfilehash: 680bb612546aaffc167970c1c48a44159ef9af6f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518244"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Rozpoznawanie formularzy to usługa poznawczą, która umożliwia tworzenie zautomatyzowanego oprogramowania do przetwarzania danych przy użyciu technologii uczenia maszynowego. Identyfikowanie i wyodrębnianie tekstu, par klucz/wartość, znaczników wyboru, tabel i struktury z dokumentów usługa wyprowadza dane ustrukturyzowane, które obejmują relacje w oryginalnym pliku, pola granic, zaufanie &mdash; i inne. Możesz szybko uzyskać dokładne wyniki dostosowane do określonej zawartości bez intensywnej interwencji ręcznej lub rozległej wiedzy z zakresu nauki o danych. Użyj Rozpoznawanie formularzy, aby zautomatyzować wprowadzanie danych w aplikacjach i wzbogacić możliwości wyszukiwania dokumentów.

Rozpoznawanie formularzy składa się z niestandardowych modeli przetwarzania dokumentów, wstępnie utworzonych modeli faktur, paragonów, identyfikatorów i wizytówek oraz modelu układu. Możesz wywołać modele Rozpoznawanie formularzy przy użyciu interfejsu API REST lub zestawów SDK biblioteki klienta, aby zmniejszyć złożoność i zintegrować je z przepływem pracy lub aplikacją.

Ta dokumentacja zawiera następujące typy artykułów:  

* [**Przewodniki Szybki start to**](quickstarts/client-library.md) instrukcje z wprowadzeniem, które pokierują Cię przez proces tworzenia żądań do usługi.  
* [**Przewodniki z instrukcjami**](build-training-data-set.md) zawierają instrukcje dotyczące korzystania z usługi w bardziej szczegółowe lub dostosowane sposoby.  
* [**Pojęcia**](concept-layout.md) zawierają szczegółowe wyjaśnienia dotyczące funkcjonalności i funkcji usługi.  
* [**Samouczki**](tutorial-bulk-processing.md) to dłuższe przewodniki, które pokazują, jak używać usługi jako składnika w szerszych rozwiązaniach biznesowych.  

## <a name="form-recognizer-features"></a>Rozpoznawanie formularzy funkcji

Dzięki Rozpoznawanie formularzy można łatwo wyodrębniać i analizować dane formularza za pomocą tych funkcji:

* **[Interfejs API układu](#layout-api)** — wyodrębnianie tekstu, znaczników wyboru i struktur tabel wraz z ich współrzędnymi pola granicznego z dokumentów.
* **[Modele niestandardowe](#custom-models)** — wyodrębnianie tekstu, par klucz/wartość, znaczników wyboru i danych tabeli z formularzy. Te modele są trenowane przy użyciu własnych danych, dzięki czemu są dostosowane do Twoich formularzy.

* **[Wstępnie utworzone modele — wyodrębnianie](#prebuilt-models)** danych z unikatowych typów dokumentów przy użyciu wstępnie utworzonych modeli. Obecnie dostępne są następujące wstępnie utworzone modele

  * [Faktury](./concept-invoices.md)
  * [Paragony sprzedaży](./concept-receipts.md)
  * [Wizytówki](./concept-business-cards.md)
  * [Karty identyfikacyjne (IDENTYFIKATOR)](./concept-identification-cards.md)


## <a name="get-started"></a>Rozpoczęcie pracy

Użyj narzędzia Przykładowe Rozpoznawanie formularzy, aby wypróbować układ, wstępnie sbudowaną modele i wytszkolić niestandardowy model dla dokumentów. Aby wypróbować usługę azure, musisz mieć [](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) subskrypcję platformy Azure [**(utwórz**](https://azure.microsoft.com/free/cognitive-services)Rozpoznawanie formularzy Rozpoznawanie formularzy bezpłatnie) oraz punkt końcowy Rozpoznawanie formularzy zasobów i klucz.

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

> [!div class="nextstepaction"]
> [Spróbuj Rozpoznawanie formularzy](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Spróbuj Rozpoznawanie formularzy](https://fott.azurewebsites.net/)

---
Postępuj zgodnie z przewodnika Szybki start [biblioteka klienta/interfejs API REST,](./quickstarts/client-library.md) aby rozpocząć wyodrębnianie danych z dokumentów. Zalecamy korzystanie z bezpłatnej usługi podczas nauki technologii. Pamiętaj, że liczba bezpłatnych stron jest ograniczona do 500 na miesiąc.

Aby rozpocząć pracę, możesz również użyć przykładów REST (GitHub) — 

* Wyodrębnianie tekstu, znaczników wyboru i struktury tabeli z dokumentów
  * [Wyodrębnianie danych układu — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Szkolenie modeli niestandardowych i wyodrębnianie danych formularza
  * [Trenuj bez etykiet — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Trenuj przy użyciu etykiet — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Wyodrębnianie danych z faktur
  * [Wyodrębnianie danych faktury — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Wyodrębnianie danych z paragonów sprzedaży
  * [Wyodrębnianie danych paragonu — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Wyodrębnianie danych z wizytówek
  * [Wyodrębnianie danych wizytówki — Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>Przeglądanie interfejsów API REST

Użyjesz następujących interfejsów API do trenowania modeli i wyodrębniania ustrukturyzowanych danych z formularzy.

|Nazwa |Opis |
|---|---|
| **Analizowanie układu** | Analizowanie dokumentu przekazanego jako strumień w celu wyodrębnienia tekstu, znaczników wyboru, tabel i struktury z dokumentu |
| **Trenowanie modelu niestandardowego**| Wytrenuj nowy model do analizowania formularzy przy użyciu pięciu form tego samego typu. Ustaw parametr _useLabelFile_ na wartość `true` , aby trenować z ręcznie oznaczonymi danymi. |
| **Formularz analizy** |Przeanalizuj formularz przekazany jako strumień, aby wyodrębnić tekst, pary klucz/wartość i tabele z formularza przy użyciu modelu niestandardowego.  |
| **Analizowanie faktury** | Analizowanie faktury w celu wyodrębnienia kluczowych informacji, tabel i innego tekstu faktury.|
| **Analizowanie paragonu** | Przeanalizuj dokument potwierdzenia, aby wyodrębnić kluczowe informacje i inny tekst potwierdzenia.|
| **Analizowanie identyfikatora** | Analizowanie dokumentu z kartą identyfikatorów w celu wyodrębnienia informacji o kluczu i innego tekstu karty identyfikacyjnej.|
| **Analizowanie wizytówki** | Analizowanie wizytówki w celu wyodrębnienia kluczowych informacji i tekstu.|

### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/v2-1)

Zapoznaj się z [dokumentacją referencyjną interfejsu API REST,](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) aby dowiedzieć się więcej. Jeśli znasz poprzednią wersję interfejsu API, [](./whats-new.md) zobacz artykuł Co nowego, aby dowiedzieć się więcej o ostatnich zmianach.

### <a name="v20"></a>[Wersja 2.0](#tab/v2-0)

Zapoznaj się z [dokumentacją referencyjną interfejsu API REST,](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync) aby dowiedzieć się więcej. Jeśli znasz poprzednią wersję interfejsu API, [](./whats-new.md) zobacz artykuł Co nowego, aby dowiedzieć się więcej o ostatnich zmianach.

---

## <a name="layout-api"></a>Interfejs API układu

Rozpoznawanie formularzy wyodrębniać tekst, znaczniki wyboru i strukturę tabeli (numery wierszy i kolumn skojarzone z tekstem) przy użyciu optycznego rozpoznawania znaków wysokiej rozdzielczości (OCR) i ulepszonego modelu uczenia głębokiego z dokumentów. Aby uzyskać więcej informacji, zobacz [Przewodnik](./concept-layout.md) koncepcyjny dotyczący układu.

:::image type="content" source="./media/tables-example.jpg" alt-text="przykład tabel" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Modele niestandardowe

Rozpoznawanie formularzy niestandardowe trenują na własnych danych, a do uruchomienia potrzebnych jest tylko pięć przykładowych formularzy wejściowych. Wytrenowany model przetwarzania dokumentów może wyprowadzać dane ustrukturyzowane, które obejmują relacje w oryginalnym dokumencie formularza. Po wytrenowania modelu można go przetestować i ponownie wytrenować, a następnie użyć go do niezawodnego wyodrębniania danych z większej liczby formularzy zgodnie z potrzebami.

Podczas trenowania modeli niestandardowych dostępne są następujące opcje: szkolenie z danymi oznaczonymi etykietami i bez danych oznaczonych etykietami.

### <a name="train-without-labels"></a>Trenuj bez etykiet

Rozpoznawanie formularzy uczenia bez nadzoru do zrozumienia układu i relacji między polami i wpisami w formularzach. Podczas przesyłania formularzy wejściowych algorytm grupuje formularze według typu, wykrywa, jakie klucze i tabele są obecne, oraz kojarzy wartości z kluczami i wpisami w tabelach. Trenowanie bez etykiet nie wymaga ręcznego etykietowania danych ani intensywnego kodowania i konserwacji, dlatego zalecamy wypróbowanie tej metody w pierwszej kolejności.

Aby uzyskać porady dotyczące sposobu zbierania dokumentów szkoleniowych, zobacz Build [a training data set](./build-training-data-set.md) (Tworzenie zestawu danych szkoleniowych).

### <a name="train-with-labels"></a>Trenuj przy użyciu etykiet

Podczas trenowania przy użyciu danych oznaczonych etykietami model wyodrębnia interesujące wartości przy użyciu formularzy oznaczonych etykietami za pomocą uczenia nadzorowanego. Dane oznaczone etykietami dają lepsze wyniki w modelach i mogą tworzyć modele, które działają ze złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.

Rozpoznawanie formularzy [interfejsu API układu,](#layout-api) aby poznać oczekiwane rozmiary i położenia drukowanych i odręcznych elementów tekstowych oraz wyodrębnić tabele. Następnie używa etykiet określonych przez użytkownika, aby poznać skojarzenia klucz/wartość i tabele w dokumentach. Zalecamy używanie pięciu ręcznie oznaczonych formularzy tego samego typu (tej samej struktury), aby rozpocząć trenowanie nowego modelu i dodać więcej danych oznaczonych etykietami w razie potrzeby w celu zwiększenia dokładności modelu. Rozpoznawanie formularzy umożliwia trenowanie modelu w celu wyodrębniania par klucz-wartość i tabel przy użyciu funkcji uczenia nadzorowanego. 

[Wprowadzenie do szkolenia z etykietami](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Wstępnie utworzone modele

Rozpoznawanie formularzy obejmuje również wstępnie utworzone modele do automatycznego przetwarzania danych unikatowych typów formularzy.

### <a name="prebuilt-invoice-model"></a>Wstępnie utworzony model faktur

Wstępnie utworzony model faktur wyodrębnia dane z faktur w różnych formatach i zwraca dane ustrukturyzowane. Ten model wyodrębnia kluczowe informacje, takie jak identyfikator faktury, szczegóły klienta, szczegóły dostawcy, wysyłka do, rachunek, suma, podatek, suma częściowa, pozycje wiersza i inne. Ponadto wstępnie utworzony model faktur jest trenowany w celu analizowania i zwracania całego tekstu i tabel na fakturze. Aby uzyskać więcej [informacji,](./concept-invoices.md) zobacz przewodnik koncepcyjny dotyczący faktur.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="przykładowa faktura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Wstępnie utworzony model paragonu

Wstępnie utworzony model paragonu służy do odczytywania paragonów sprzedaży w języku angielskim z Australii, Kanady, Wielkiej Brytanii, Indiach i Stany Zjednoczone typu używanego przez restauracje, stacje paliw, handel detaliczny &mdash; i tak dalej. Ten model wyodrębnia kluczowe informacje, takie jak godzina i data transakcji, informacje o sprzedawcy, kwoty podatków, pozycje wiersza, sumy i inne. Ponadto wstępnie utworzony model paragonu jest trenowany w celu analizowania i zwracania całego tekstu na paragonie. Aby uzyskać więcej [informacji,](./concept-receipts.md) zobacz Przewodnik koncepcyjny dotyczący paragonów.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="przykładowy paragon" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Wstępnie utworzony model kart identyfikacji (ID)

Model kart identyfikacji (ID) umożliwia wyodrębnianie kluczowych informacji z paszportów na całym świecie i licencji na prawo jazdy w USA. Wyodrębnia dane, takie jak identyfikator dokumentu, data wygaśnięcia urodzenia, data wygaśnięcia, nazwa, kraj, region, strefa czytelna dla komputera i inne. Aby uzyskać [więcej informacji,](./concept-identification-cards.md) zobacz przewodnik koncepcyjny dotyczący kart identyfikacji (ID).

:::image type="content" source="./media/overview-id.jpg" alt-text="przykładowa karta identyfikacyjna" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Wstępnie utworzony model wizytówek

Model wizytówek umożliwia wyodrębnianie z wizytówek informacji, takich jak imię i nazwisko osoby, stanowisko, adres, adres e-mail, firma i numery telefonów, z wizytówek w języku angielskim. Aby uzyskać [więcej informacji,](./concept-business-cards.md) zobacz przewodnik koncepcyjny Dotyczący wizytówek.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="przykładowa wizytówka" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Wymagania dotyczące danych wejściowych

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Wdrażanie lokalne przy użyciu kontenerów platformy Docker

[Użyj Rozpoznawanie formularzy (wersja zapoznawcza),](form-recognizer-container-howto.md) aby wdrożyć funkcje interfejsu API w środowisku lokalnym. Ten kontener platformy Docker umożliwia przybliżanie usługi do danych ze względu na zgodność, zabezpieczenia lub z innych powodów operacyjnych.

## <a name="service-availability-and-redundancy"></a>Dostępność i nadmiarowość usługi

### <a name="is-form-recognizer-service-zone-resilient"></a>Czy Rozpoznawanie formularzy usługa jest odporna na strefy?

Tak. Usługa Rozpoznawanie formularzy domyślnie jest odporna na strefy.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Jak mogę skonfigurować usługę Rozpoznawanie formularzy, aby była odporna na strefy?

Do zapewnienia odporności strefy nie jest konieczna żadna konfiguracja klienta. Odporność strefowa dla Rozpoznawanie formularzy zasobów jest domyślnie dostępna i zarządzana przez samą usługę.

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich usług Cognitive Services, deweloperzy korzystający z usługi Rozpoznawanie formularzy powinni wiedzieć o zasadach firmy Microsoft dotyczących danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Wypróbuj nasze narzędzie online i przewodnik Szybki start, aby dowiedzieć się więcej o Rozpoznawanie formularzy online.

* [**Rozpoznawanie formularzy narzędziu**](https://fott-preview.azurewebsites.net/)
* [**Biblioteka klienta i interfejs API REST szybki start**](quickstarts/client-library.md)
