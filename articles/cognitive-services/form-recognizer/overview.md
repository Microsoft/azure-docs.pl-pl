---
title: Co to jest rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Aparat rozpoznawania formularzy Cognitive Services platformy Azure pozwala identyfikować i wyodrębniać pary klucz/wartość oraz dane tabeli z dokumentów formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 0df61c2ee42d468562efd67a2a66a90a5e4fda53
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723569"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Aparat rozpoznawania formularzy platformy Azure to usługa poznawczej, która korzysta z technologii uczenia maszynowego do identyfikowania i wyodrębniania par tekst, klucz/wartość i danych tabeli z dokumentów formularzy. Pobiera tekst z formularzy i wyprowadza dane ze strukturą, które zawierają relacje w oryginalnym pliku. Szybko uzyskuj dokładne wyniki, które są dostosowane do konkretnej zawartości, bez znacznej interwencji ręcznej lub szczegółowej wiedzy o nauce danych. Aparat rozpoznawania formularzy składa się z modeli niestandardowych, prekompilowanego modelu paragonu i interfejsu API układu. Można wywołać modele aparatu rozpoznawania formularzy przy użyciu interfejsu API REST, aby zmniejszyć złożoność i zintegrować go z przepływem pracy lub aplikacją.

Aparat rozpoznawania formularzy składa się z następujących usług:
* **Modele niestandardowe** — Wyodrębnij pary klucz/wartość i dane tabeli z formularzy. Te modele są przeszkolone przy użyciu własnych danych, dzięki czemu są dostosowane do Twoich formularzy.
* Wstępnie **utworzone modele** — Wyodrębnij dane z unikatowych typów formularzy przy użyciu wbudowanych modeli. Obecnie dostępne są wstępnie zbudowane modele dla paragonów sprzedaży i kart firmowych w języku angielskim.
* **Interfejs API układu** — Wyodrębnij strukturę tekstu i tabeli wraz z ich współrzędne pola ograniczenia, z dokumentów.

<!-- add diagram -->

## <a name="custom-models"></a>Modele niestandardowe

Modele niestandardowe aparatu rozpoznawania formularzy są pouczeni do własnych danych i wystarczy pięć przykładowych formularzy wejściowych do uruchomienia. Model przeszkolony może wyprowadzać dane strukturalne, które zawierają relacje w oryginalnym dokumencie formularza. Po przeprowadzeniu szkolenia modelu można testować i przeszkolić go i ostatecznie użyć do niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

Podczas uczenia modeli niestandardowych można korzystać z następujących opcji: uczenie z danymi z etykietami i bez etykiet.

### <a name="train-without-labels"></a>Uczenie bez etykiet

Domyślnie aparat rozpoznawania formularzy używa nienadzorowanej uczenia, aby zrozumieć układ i relacje między polami i pozycjami w formularzach. Po przesłaniu formularzy wejściowych algorytm tworzy klastry według typu, odnajduje, jakie klucze i tabele są obecne, i kojarzy wartości z kluczami i wpisami w tabelach. Nie wymaga to ręcznej etykietowania danych ani intensywnego kodowania i konserwacji, dlatego zalecamy wypróbowanie tej metody w pierwszej kolejności.

### <a name="train-with-labels"></a>Uczenie z etykietami

W przypadku uczenia się z danymi z etykietami model przeprowadza nadzorowane uczenie w celu wyodrębnienia interesujących wartości przy użyciu podanych formularzy z etykietami. Prowadzi to do lepszego wykonywania modeli i może generować modele, które współpracują z złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.

Aparat rozpoznawania formularzy używa [interfejsu API układu](#layout-api) , aby poznać oczekiwane rozmiary i położenia elementów tekstu wydrukowanych i odręcznych. Następnie używa etykiet określonych przez użytkownika, aby poznać skojarzenia klucz/wartość w dokumentach. Zalecamy używanie pięciu etykiet oznaczonych ręcznie z tym samym typem, aby rozpocząć pracę w przypadku szkolenia nowego modelu i dodać więcej etykiet danych w miarę potrzeby, aby poprawić dokładność modelu.

## <a name="prebuilt-models"></a>Wstępnie utworzone modele

Aparat rozpoznawania formularzy zawiera również wstępnie skompilowane modele dla unikatowych typów formularzy.
### <a name="prebuilt-receipt-model"></a>Wstępnie utworzony model przyjęcia
Wstępnie utworzony model przyjęcia jest używany do odczytywania z angielskiego zakupu sprzedaży z Australii, Kanady, Wielkiej Brytanii, Indii i Stany Zjednoczone &mdash; typu używanego przez Restauracje, stacje gazu, sprzedaż detaliczna i tak dalej. Ten model wyodrębnia kluczowe informacje, takie jak godzina i Data transakcji, informacje o sprzedawcy, kwoty podatków, pozycje wiersza, sumy itd. Ponadto wstępnie utworzony model paragonów jest szkolony do rozpoznawania i zwracania całego tekstu w paragonie. 

![Przykładowe potwierdzenie](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Model prebudowanych kart roboczych
Model wizytówek umożliwia wyodrębnienie informacji, takich jak imię i nazwisko osoby, stanowiska, adres e-mail, firma i numery telefonów z kart służbowych w języku angielskim. 

![Przykładowa karta biznesowa](./media/business-card-english.jpg)

## <a name="layout-api"></a>Interfejs API układu

Aparat rozpoznawania formularzy może również wyodrębnić strukturę tekstu i tabeli (numery wierszy i kolumn skojarzonych z tekstem) za pomocą optycznego rozpoznawania znaków (OCR) o wysokiej rozdzielczości.

## <a name="get-started"></a>Wprowadzenie

Skorzystaj z przewodnika Szybki Start, aby rozpocząć wyodrębnianie danych z formularzy. Zalecamy korzystanie z bezpłatnej usługi podczas nauki technologii. Należy pamiętać, że liczba bezpłatnych stron jest ograniczona do 500 miesięcznie.

* [Biblioteka kliencka — szybki start](./quickstarts/client-library.md) (wszystkie języki, wiele scenariuszy)
* Przewodnik Szybki Start dotyczący interfejsu użytkownika sieci Web
  * [Szkolenie z etykietami — przykładowe narzędzie do etykietowania](quickstarts/label-tool.md)
* Przewodniki Szybki Start
  * Uczenie modeli niestandardowych i wyodrębnianie danych formularza
    * [Uczenie bez etykiet — zwinięcie](quickstarts/curl-train-extract.md)
    * [Uczenie bez etykiet — Python](quickstarts/python-train-extract.md)
    * [Uczenie z etykietami — Python](quickstarts/python-labeled-data.md)
  * Wyodrębnij dane z przyjęć sprzedaży w USA
    * [Wyodrębnij dane przyjęcia — zwinięcie](quickstarts/curl-receipts.md)
    * [Wyodrębnij dane przyjęcia — Python](quickstarts/python-receipts.md)
  * Wyodrębnij strukturę tekstu i tabeli z formularzy
    * [Wyodrębnij dane układu — Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>Przeglądanie interfejsów API REST

Poniższe interfejsy API służą do uczenia modeli i wyodrębnienia danych strukturalnych z formularzy.

|Nazwa |Opis |
|---|---|
| **Uczenie modelu niestandardowego**| Uczenie nowego modelu do analizowania formularzy przy użyciu pięciu postaci tego samego typu. Ustaw parametr _useLabelFile_ , aby `true` można było nauczyć się z ręcznie oznaczonymi danymi. |
| **Analiza formularza** |Analizuj pojedynczy dokument, który został przesłany jako strumień, aby wyodrębnić pary klucz-wartość i tabele z formularza z modelem niestandardowym.  |
| **Analizuj potwierdzenie** |Analizuj pojedynczy dokument paragonu w celu wyodrębnienia informacji o kluczu i innego tekstu paragonu.|
| **Analizowanie układu** |Analizuj układ formularza, aby wyodrębnić strukturę tekstu i tabeli.|

Zapoznaj się z [dokumentacją interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) , aby dowiedzieć się więcej. Jeśli znasz poprzednią wersję interfejsu API, zapoznaj się z artykułem [co nowego](./whats-new.md) , aby dowiedzieć się więcej o najnowszych zmianach.

## <a name="input-requirements"></a>Wymagania wejściowe
### <a name="custom-model"></a>Model niestandardowy

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt"></a>Wstępnie utworzonych

Wymagania dotyczące danych wejściowych dla modelu paragonu są nieco inne.

* Format musi być JPEG, PNG, PDF (tekst lub skanowany) lub TIFF.
* Rozmiar pliku musi być mniejszy niż 20 MB.
* Wymiary obrazu muszą zawierać się w przedziale od 50 x 50 pikseli i 10000 x 10000 pikseli.
* Wymiary PDF muszą mieć co najwyżej 17 x 17 cali, odpowiadające rozmiarowi papieru legalnego lub A3 i mniejszemu.
* W przypadku plików PDF i TIFF tylko pierwsze 200 stron jest przetwarzanych (z subskrypcją warstwy Bezpłatna, są przetwarzane tylko dwie pierwsze strony).

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Podobnie jak w przypadku wszystkich usług poznawczych, deweloperzy korzystający z usługi rozpoznawania formularzy powinni mieć świadomość zasad firmy Microsoft dotyczących danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Ukończ [Przewodnik Szybki Start](quickstarts/curl-train-extract.md) , aby rozpocząć pracę z [interfejsami API aparatu rozpoznawania formularzy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm).