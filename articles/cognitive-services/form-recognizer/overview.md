---
title: Co to jest rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Aparat rozpoznawania formularzy usług Azure Cognitive Services umożliwia identyfikowanie i wyodrębnianie par klucz/wartość i dane tabeli z dokumentów formularza.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5b7a060aa2313902e4644fdd15068e06816e3f58
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529896"
---
# <a name="what-is-form-recognizer"></a>Co to jest rozpoznawanie formularzy?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Aparat rozpoznawania to usługa kognitywna, która używa technologii uczenia maszynowego do identyfikowania i wyodrębniania tekstu, par kluczy/wartości i danych tabeli z dokumentów formularza. Pochylił tekst z formularzy i wyprowadza dane strukturalne, które zawierają relacje w oryginalnym pliku. Szybko otrzymujesz dokładne wyniki, które są dostosowane do konkretnych treści bez intensywnej interwencji ręcznej lub rozległej wiedzy na temat nauki o danych. Aparat rozpoznawania formularzy składa się z modeli niestandardowych, wstępnie utworzonego modelu potwierdzenia i interfejsu API układu. Modele rozpoznawania formularzy można wywołać przy użyciu interfejsu API REST, aby zmniejszyć złożoność i zintegrować go z przepływem pracy lub aplikacją.

Aparat rozpoznawania formularzy składa się z następujących usług:
* **Modele niestandardowe** — wyodrębnianie par kluczy/wartości i danych tabeli z formularzy. Te modele są szkolone z własnych danych, więc są one dostosowane do formularzy.
* **Wstępnie utworzony model przyjęcia** — wyodrębnianie danych z potwierdzeń sprzedaży w USA przy użyciu wstępnie utworzonego modelu.
* **Interfejs API układu** — wyodrębnianie struktur tekstu i tabeli wraz z ich współrzędnymi obwiedni z dokumentów.

<!-- add diagram -->

## <a name="custom-models"></a>Modele niestandardowe

Modele niestandardowe aparatu rozpoznawania formularzy trenują do własnych danych i potrzebujesz tylko pięciu przykładowych formularzy wejściowych do uruchomienia. Uczony model może wyprowadzać dane strukturalne, które zawierają relacje w oryginalnym dokumencie formularza. Po przeszkoleniu modelu można go przetestować i przeszkolić, a następnie użyć go do niezawodnego wyodrębniania danych z większej liczby formularzy zgodnie z potrzebami.

Podczas szkolenia modeli niestandardowych dostępne są następujące opcje: szkolenie z danymi oznaczonymi etykietami i bez danych oznaczonych etykietą.

### <a name="train-without-labels"></a>Trenuj bez etykiet

Domyślnie aparat rozpoznawania formularzy używa nienadzorowanego uczenia się, aby zrozumieć układ i relacje między polami i wpisami w formularzach. Podczas przesyłania formularzy wejściowych algorytm klastruje formularze według typu, odnajduje, jakie klucze i tabele są obecne, i kojarzy wartości z kluczami i wpisami do tabel. Nie wymaga to ręcznego etykietowania danych ani intensywnego kodowania i konserwacji, dlatego zalecamy, aby najpierw wypróbować tę metodę.

### <a name="train-with-labels"></a>Trenuj z etykietami

Podczas trenowania z oznaczonymi danymi model nadzoruje uczenie się wyodrębniania wartości zainteresowania przy użyciu etykietowanych formularzy, które podajesz. Powoduje to lepszą wydajność modeli i może tworzyć modele, które działają ze złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.

Aparat rozpoznawania formularzy używa [interfejsu API układu,](#layout-api) aby poznać oczekiwane rozmiary i położenie drukowanych i odręcznych elementów tekstowych. Następnie używa etykiet określonych przez użytkownika, aby dowiedzieć się skojarzenia klucz/wartość w dokumentach. Zaleca się użycie pięciu ręcznie oznakowanych form tego samego typu, aby rozpocząć szkolenie nowego modelu i dodać więcej danych oznaczonych etykietą w razie potrzeby, aby poprawić dokładność modelu.

## <a name="prebuilt-receipt-model"></a>Wstępnie utworzony model paragonu

Rozpoznawanie formularzy zawiera również model do czytania angielskich wpływów ze sprzedaży ze Stanów Zjednoczonych&mdash;typu używanego przez restauracje, stacje benzynowe, handel detaliczny i tak dalej[(paragon próbki).](./media/contoso-receipt-small.png) Ten model wyodrębnia kluczowe informacje, takie jak godzina i data transakcji, informacje o handlowcach, kwoty podatków i sum i inne. Ponadto wstępnie utworzony model przyjęcia jest przeszkolony w rozpoznawaniu i zwracaniu całego tekstu na potwierdzeniu.

## <a name="layout-api"></a>Interfejs API układu

Aparat rozpoznawania formularzy może również wyodrębniać strukturę tekstu i tabeli (numery wierszy i kolumn skojarzone z tekstem) za pomocą optycznego rozpoznawania znaków o wysokiej rozdzielczości (OCR).

## <a name="get-started"></a>Wprowadzenie

Postępuj zgodnie z przewodnikiem Szybki start, aby rozpocząć wyodrębnianie danych z formularzy. Zalecamy korzystanie z bezpłatnej usługi podczas nauki technologii. Pamiętaj, że liczba darmowych stron jest ograniczona do 500 miesięcznie.

* Niestandardowe - trenuj model do formularzy
  * Trenuj bez etykiet
    * [Szybki start: szkolenie modelu rozpoznawania formularzy i wyodrębnianie danych formularza przy użyciu interfejsu API REST z cURL](quickstarts/curl-train-extract.md)
    * [Szybki start: trenuj model aparatu rozpoznawania formularzy i wyodrębniaj dane formularza przy użyciu interfejsu API REST z programem Python](quickstarts/python-train-extract.md)
  * Trenuj z etykietami
    * [Szkolenie modelu aparatu rozpoznawania formularzy z etykietami przy użyciu przykładowego narzędzia do etykietowania](quickstarts/label-tool.md)
    * [Trenuj model aparatu rozpoznawania formularzy z etykietami przy użyciu interfejsu API REST i języka Python](quickstarts/python-labeled-data.md)
* Wstępnie utworzone przyjęcia - wyodrębnianie danych z wpływów ze sprzedaży w USA
  * [Szybki start: wyodrębnianie danych o paragonach przy użyciu cURL](quickstarts/curl-receipts.md)
  * [Szybki start: wyodrębnianie danych o potwierdzeniu przy użyciu języka Python](quickstarts/python-receipts.md)
* Układ — wyodrębnianie tekstu i struktury tabeli z formularzy
  * [Szybki start: wyodrębnianie danych układu przy użyciu języka Python](quickstarts/python-layout.md)

### <a name="review-the-rest-apis"></a>Przegląd interfejsów API REST

Za pomocą następujących interfejsów API należy szkolić modele i wyodrębniać dane strukturalne z formularzy.

|Nazwa |Opis |
|---|---|
| **Model niestandardowy pociągu**| Trenuj nowy model do analizowania formularzy przy użyciu pięciu form tego samego typu. Ustaw _parametr useLabelFile,_ aby trenować `true` z danymi ręcznie oznaczonymi etykietą. |
| **Analizowanie formularza** |Analizowanie pojedynczego dokumentu przekazywanego jako strumień w celu wyodrębnienia tekstu, par klucz/wartości i tabel z formularza za pomocą modelu niestandardowego.  |
| **Analiza potwierdzenia** |Analizowanie pojedynczego dokumentu przyjęcia w celu wyodrębnienia informacji o kluczu i innego tekstu przyjęcia.|
| **Analizowanie układu** |Analizowanie układu formularza w celu wyodrębnienia tekstu i struktury tabeli.|

Zapoznaj się z [dokumentacją referencyjną interfejsu API REST,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) aby dowiedzieć się więcej. Jeśli znasz poprzednią wersję interfejsu API, zobacz [Co nowego artykułu,](./whats-new.md) aby dowiedzieć się więcej o ostatnich zmianach.

## <a name="input-requirements"></a>Wymagania wejściowe
### <a name="custom-model"></a>Model niestandardowy

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Wstępnie utworzony model paragonu

Wymagania wejściowe dla modelu przyjęcia są nieco inne.

* Format musi być JPEG, PNG, PDF (tekst lub skanowane) lub TIFF.
* Rozmiar pliku musi być mniejszy niż 20 MB.
* Wymiary obrazu muszą wynosić od 50 x 50 pikseli do 10000 x 10000 pikseli.
* Wymiary PDF muszą mieć wymiary co najwyżej 17 x 17 cali, co odpowiada rozmiarom papieru Legal lub A3 i mniejszym.
* W przypadku plików PDF i TIFF przetwarzane są tylko pierwsze 200 stron (z bezpłatną subskrypcją warstwy przetwarzane są tylko dwie pierwsze strony).

## <a name="data-privacy-and-security"></a>Prywatność i zabezpieczenia danych

Ta usługa jest oferowana jako [wersja zapoznawcza](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) usługi platformy Azure zgodnie z [Warunkami usługi online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Podobnie jak w przypadku wszystkich usług kognitywnych, deweloperzy korzystający z usługi rozpoznawania formularzy powinni być świadomi zasad firmy Microsoft dotyczących danych klientów. Zobacz [stronę usług Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) w Centrum zaufania firmy Microsoft, aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Ukończ [przewodnik Szybki start,](quickstarts/curl-train-extract.md) aby rozpocząć korzystanie z [interfejsów API aparatu rozpoznawania formularzy.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
