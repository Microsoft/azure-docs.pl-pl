---
title: Wyszukiwanie i analiza tekstu przy użyciu interfejs API analizy tekstu platformy Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat wyszukiwania tekstu przy użyciu interfejs API analizy tekstu. Służy do analizy tonacji, wykrywania języka i innych form przetwarzania języka naturalnego.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/17/2020
ms.author: aahi
keywords: Wyszukiwanie tekstu, analiza tonacjiów, analizy tekstu
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 68f892fea01582b16dad5efd8c86dbf0b578e50f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353055"
---
# <a name="what-is-the-text-analytics-api"></a>Czym jest interfejs API analizy tekstu?

Interfejs API analizy tekstu to usługa oparta na chmurze, która zapewnia funkcje przetwarzania języka naturalnego (NLP) na potrzeby wyszukiwania tekstu i analizy tekstu, w tym: Analiza tonacji, wyszukiwanie opinii, wyodrębnianie kluczowych fraz, wykrywanie języka i rozpoznawanie nazw jednostek.

Interfejs API jest częścią usług [Azure Cognitive Services](../index.yml), które są zbiorem algorytmów uczenia maszynowego i sztucznej inteligencji w chmurze do wykorzystania w Twoich projektach programistycznych. Tych funkcji można używać z [interfejsem API REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)lub z [biblioteką klienta](quickstarts/text-analytics-sdk.md)programu.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

## <a name="sentiment-analysis"></a>Analiza tonacji

Korzystaj z [analizy tonacji](how-tos/text-analytics-how-to-sentiment-analysis.md) i Dowiedz się, jakie osoby uważają za swoją markę lub temat, wyszukując tekst pod kątem pozytywnych lub negatywnych tonacji. 

Funkcja udostępnia etykiety tonacji (na przykład "negatywna", "neutralna" i "pozytywne") w oparciu o najwyższy wynik zaufania znaleziony przez usługę na poziomie zdania i dokumentu. Ta funkcja zwraca również wyniki zaufania z zakresu od 0 do 1 dla każdego dokumentu & w nim zdania dla pozytywnych, neutralnych i ujemnych tonacji. Usługę można również uruchomić lokalnie [przy użyciu kontenera](how-tos/text-analytics-how-to-install-containers.md).

Począwszy od wersji zapoznawczej programu v 3.1, funkcja wyszukiwania jest funkcją analiza tonacji. Funkcja ta oferuje również bardziej szczegółowe informacje o opiniach związanych z aspektami (np. atrybutami produktów lub usług), nazywanymi analiza tonacji opartymi na aspektach.

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

Użyj [wyodrębniania kluczowych fraz](how-tos/text-analytics-how-to-keyword-extraction.md) , aby szybko identyfikować główne koncepcje w tekście. Na przykład w tekście "żywność została deliciousa i wystąpiło wspaniałe zatrudnienie", wyodrębnianie kluczowych fraz zwróci główne punkty rozmowy: "żywność" i "wspaniałe personel".

## <a name="language-detection"></a>Wykrywanie języka

Mechanizm wykrywania języka może [wykryć język, w którym wprowadzany jest tekst wejściowy](how-tos/text-analytics-how-to-language-detection.md) , i zgłosić jeden kod języka dla każdego dokumentu przesłanego na żądanie w szerokim zakresie języków, wariantów, dialektach i niektórych językach regionalnych/kulturowych. Kod języka jest sparowany z wynikiem pewności.

## <a name="named-entity-recognition"></a>Rozpoznawanie jednostek nazwanych

Rozpoznawanie jednostek nazwanych (NER) może [identyfikować i klasyfikować jednostki](how-tos/text-analytics-how-to-entity-linking.md) w tekście jako osoby, miejsca, organizacje, ilości, dobrze znane jednostki, które są także rozpoznawane i połączone z więcej informacji w sieci Web.

## <a name="deploy-on-premises-using-docker-containers"></a>Wdrażanie lokalnie przy użyciu kontenerów platformy Docker

[Używanie kontenerów analiza tekstu](how-tos/text-analytics-how-to-install-containers.md) do wdrażania funkcji interfejsu API w środowisku lokalnym. Te kontenery platformy Docker umożliwiają przełączenie usługi bliżej danych pod kątem zgodności, bezpieczeństwa lub innych przyczyn operacyjnych. Analiza tekstu oferuje następujące kontenery:

* Analiza tonacji
* Wyodrębnianie kluczowych fraz (wersja zapoznawcza)
* wykrywanie języka (wersja zapoznawcza)
* Analiza tekstu dla kondycji (wersja zapoznawcza)

## <a name="asynchronous-operations"></a>Operacje asynchroniczne

`/analyze`Punkt końcowy umożliwia korzystanie z funkcji select interfejs API analizy tekstu [asynchronicznie](how-tos/text-analytics-how-to-call-api.md), takich jak ner i wyodrębnianie kluczowych fraz.

## <a name="typical-workflow"></a>Typowy przepływ pracy

Przepływ pracy jest prosty: przesyłasz dane do analizy i obsługujesz dane wyjściowe w swoim kodzie. Analizatory są używane tak, jak jest — bez dodatkowej konfiguracji czy dostosowywania.

1. [Utwórz zasób platformy Azure](../cognitive-services-apis-create-account.md) dla analiza tekstu. Następnie należy [uzyskać klucz](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) wygenerowany dla Ciebie, aby uwierzytelnić Twoje żądania.

2. [Sformułuj żądanie](how-tos/text-analytics-how-to-call-api.md#json-schema) zawierające dane jako nieprzetworzony tekst bez struktury, w formacie JSON.

3. Wyślij żądanie do punktu końcowego ustanowionego podczas rejestracji, dołączając żądany zasób: tonacji Analysis, wyodrębnianie kluczowych fraz, wykrywanie języka lub rozpoznawanie jednostek nazwanych.

4. Prześlij odpowiedź strumieniowo lub przechowaj ją lokalnie. W zależności od żądania wyniki są oceną tonacji, kolekcją wyodrębnionych kluczowych fraz lub kodem języka.

Dane wyjściowe są zwracane w jednym dokumencie JSON, z wynikami dla każdego opublikowanego dokumentu tekstowego, w oparciu o identyfikator. Możesz następnie przeanalizować, zwizualizować lub sklasyfikować wyniki, przekształcając je w szczegółowe informacje umożliwiające podejmowanie działań.

Dane nie są przechowywane na koncie. Operacje wykonywane przez interfejs API analizy tekstu są bezstanowe, co oznacza, że podany tekst jest przetwarzany, a wyniki są zwracane natychmiast.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>analiza tekstu dla wielu poziomów doświadczenia programistycznego

Możesz rozpocząć korzystanie z interfejs API analizy tekstu w swoich procesach, nawet jeśli nie masz dużo doświadczenia w programowaniu. Skorzystaj z tych samouczków, aby dowiedzieć się, jak za pomocą interfejsu API analizować tekst na różne sposoby, aby dopasować go do poziomu środowiska. 

* Wymagane jest minimalne programowanie:
    * [Wyodrębnij informacje w programie Excel przy użyciu analiza tekstu i automatyzacji](tutorials/extract-excel-information.md)
    * [Użyj interfejs API analizy tekstu i usługi MS Flow, aby zidentyfikować tonacji komentarzy w grupie usługi Yammer](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Integruj Power BI z interfejs API analizy tekstu, aby analizować Opinie klientów](tutorials/tutorial-power-bi-key-phrases.md)
* Zalecane środowisko programistyczne:
    * [Analiza tonacji na strumieniu danych przy użyciu usługi Azure Databricks](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Tworzenie aplikacji kolby do tłumaczenia tekstu, analizowanie tonacji i synteza mowy](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Obsługiwane języki

Ta sekcja została przeniesiona do oddzielnego artykułu, aby zapewnić lepszą czytelność. Zapoznaj się z [obsługiwanymi językami w interfejs API analizy tekstu](./language-support.md) tej zawartości.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limity danych

Wszystkie punkty końcowe interfejsu API analizy tekstu akceptują dane w postaci nieprzetworzonego tekstu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [limitów danych](concepts/data-limits.md) .

## <a name="unicode-encoding"></a>Kodowanie Unicode

Interfejs API analizy tekstu używa kodowania Unicode na potrzeby przedstawiania tekstu oraz obliczeń w zakresie liczby znaków. Żądania można przesyłać w kodowaniu UTF-8 oraz UTF-16, bez żadnych mierzalnych różnic w liczbie znaków. Punkty kodu Unicode są używane jako heurystyka dla długości znaków i są uznawane za równoważne dla celów związanych z limitami danych analizy tekstu. Jeśli używasz [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) , aby uzyskać liczbę znaków, używana jest ta sama metoda, która służy do mierzenia rozmiaru danych.

## <a name="next-steps"></a>Następne kroki

+ [Utwórz zasób platformy Azure](../cognitive-services-apis-create-account.md) dla analiza tekstu, aby uzyskać klucz i punkt końcowy dla aplikacji.

+ Użyj [przewodnika Szybki](quickstarts/text-analytics-sdk.md) Start, aby rozpocząć wysyłanie wywołań interfejsu API. Dowiedz się, jak przesyłać tekst, wybierać analizę oraz wyświetlać wyniki przy użyciu minimalnej ilości kodu.

+ Aby uzyskać informacje o nowych wersjach i funkcjach [, zobacz co nowego w interfejs API analizy tekstu](whats-new.md) .

+ Dig w nieco bardziej szczegółowym [samouczku dotyczącym analizy tonacji](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services) przy użyciu Azure Databricks.

+ Zapoznaj się z naszą listą wpisów w blogu i Dowiedz się więcej na temat korzystania z interfejs API analizy tekstu z innymi narzędziami i technologiami na naszej [stronie zawartości społeczności zewnętrznej &](text-analytics-resource-external-community.md).