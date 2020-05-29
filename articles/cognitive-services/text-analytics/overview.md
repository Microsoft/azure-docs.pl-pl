---
title: Czym jest interfejs API analizy tekstu? Możliwość
titleSuffix: Azure Cognitive Services
description: Użyj interfejs API analizy tekstu z usługi Azure Cognitive Services do analizy tonacji, wyodrębniania kluczowych fraz, wykrywania języka i rozpoznawania jednostek.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 1e9928359f314ed57004df0264ac843871083eba
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2020
ms.locfileid: "84141861"
---
# <a name="what-is-the-text-analytics-api"></a>Czym jest interfejs API analizy tekstu?

Interfejs API analizy tekstu to usługa oparta na chmurze, która zapewnia zaawansowane przetwarzanie języka naturalnego w przypadku nieprzetworzonego tekstu i obejmuje cztery główne funkcje: tonacji Analysis, wyodrębnianie kluczowych fraz, wykrywanie języka i rozpoznawanie jednostek nazwanych.

Interfejs API jest częścią usług [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), które są zbiorem algorytmów uczenia maszynowego i sztucznej inteligencji w chmurze do wykorzystania w Twoich projektach programistycznych.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Analiza tekstu może oznaczać różne elementy, ale w Cognitive Services interfejs API analizy tekstu oferuje cztery typy analiz, jak opisano poniżej. Tych funkcji można używać z [interfejsem API REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)lub z [biblioteką klienta](quickstarts/text-analytics-sdk.md)programu.

## <a name="sentiment-analysis"></a>Analiza tonacji
Użyj [analizy tonacji](how-tos/text-analytics-how-to-sentiment-analysis.md) , aby dowiedzieć się, co klienci uważają za swoją markę lub temat, analizując nieprzetworzony tekst w celu uzyskania wskazówek dotyczących pozytywnych lub negatywnych tonacji. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację.<br /> Modele analizy są wstępnie szkolone przy użyciu rozbudowanych technologii z zakresu treści tekstu oraz naturalnego języka firmy Microsoft. W przypadku [wybranych języków](text-analytics-supported-languages.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej.

## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz
Automatycznie [Wyodrębniaj kluczowe frazy](how-tos/text-analytics-how-to-keyword-extraction.md) , aby szybko identyfikować główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”.

## <a name="language-detection"></a>Wykrywanie języka
Możesz [wykryć język, w którym wprowadzany jest tekst wejściowy](how-tos/text-analytics-how-to-language-detection.md) , i zgłosić jeden kod języka dla każdego dokumentu przesłanego na żądanie w szerokim zakresie języków, wariantów, dialektach i niektórych językach regionalnych/kulturowych. Kod języka jest powiązany z oceną, co wskazuje siłę oceny.

## <a name="named-entity-recognition"></a>Rozpoznawanie jednostek nazwanych
[Zidentyfikuj i Kategoryzuj jednostki](how-tos/text-analytics-how-to-entity-linking.md) w tekście jako osoby, miejsca, organizacje, daty/godziny, ilości, wartości procentowe, waluty i nie tylko. Dobrze znane jednostki są również rozpoznawane i łączone z większą ilością informacji w Internecie.

## <a name="use-containers"></a>Korzystanie z kontenerów

Po zainstalowaniu standardowych kontenerów platformy Docker blisko danych można lokalnie wyodrębniać kluczowe frazy, wykrywać język i analizować tonację, korzystając z [kontenerów analizy tekstu](how-tos/text-analytics-how-to-install-containers.md).

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
    * [Użyj interfejs API analizy tekstu i usługi MS Flow, aby zidentyfikować tonacji komentarzy w grupie usługi Yammer](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integruj Power BI z interfejs API analizy tekstu, aby analizować Opinie klientów](tutorials/tutorial-power-bi-key-phrases.md)
* Zalecane środowisko programistyczne:
    * [Analiza tonacji na strumieniu danych przy użyciu usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Tworzenie aplikacji kolby do tłumaczenia tekstu, analizowanie tonacji i synteza mowy](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Obsługiwane języki

Ta sekcja została przeniesiona do oddzielnego artykułu, aby zapewnić lepszą czytelność. Zapoznaj się z [obsługiwanymi językami w interfejs API analizy tekstu](text-analytics-supported-languages.md) tej zawartości.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limity danych

Wszystkie punkty końcowe interfejsu API analizy tekstu akceptują dane w postaci nieprzetworzonego tekstu. Aktualne ograniczenie to 5120 znaków dla każdego dokumentu; jeśli chcesz przeanalizować większe dokumenty, możesz podzielić je na mniejsze części.

| Limit | Wartość |
|------------------------|---------------|
| Maksymalny rozmiar pojedynczego dokumentu | 5 120 znaków mierzonych przez [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) . |
| Maksymalny rozmiar całego żądania | 1 MB |
| Maksymalna liczba dokumentów w żądaniu | Do 1 000 dokumentów ([różne dla każdej funkcji](concepts/data-limits.md)) |

Twoje limity szybkości różnią się w zależności od warstwy cenowej.

| Warstwa          | Żądania na sekundę | Liczba żądań na minutę |
|---------------|---------------------|---------------------|
| S/wiele usług | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Żądania są mierzone osobno dla każdej funkcji analiza tekstu. Na przykład można wysłać maksymalną liczbę żądań dla warstwy cenowej do każdej funkcji w tym samym czasie.      

## <a name="unicode-encoding"></a>Kodowanie Unicode

Interfejs API analizy tekstu używa kodowania Unicode na potrzeby przedstawiania tekstu oraz obliczeń w zakresie liczby znaków. Żądania można przesyłać w kodowaniu UTF-8 oraz UTF-16, bez żadnych mierzalnych różnic w liczbie znaków. Punkty kodu Unicode są używane jako heurystyka dla długości znaków i są uznawane za równoważne dla celów związanych z limitami danych analizy tekstu. Jeśli używasz [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) , aby uzyskać liczbę znaków, używana jest ta sama metoda, która służy do mierzenia rozmiaru danych.

## <a name="next-steps"></a>Następne kroki

+ [Utwórz zasób platformy Azure](../cognitive-services-apis-create-account.md) dla analiza tekstu, aby uzyskać klucz i punkt końcowy dla aplikacji.

+ Użyj [przewodnika Szybki](quickstarts/text-analytics-sdk.md) Start, aby rozpocząć wysyłanie wywołań interfejsu API. Dowiedz się, jak przesyłać tekst, wybierać analizę oraz wyświetlać wyniki przy użyciu minimalnej ilości kodu.

+ Aby uzyskać informacje o nowych wersjach i funkcjach [, zobacz co nowego w interfejs API analizy tekstu](whats-new.md) .

+ Dig w nieco bardziej szczegółowym [samouczku dotyczącym analizy tonacji](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) przy użyciu Azure Databricks.

+ Zapoznaj się z naszą listą wpisów w blogu i Dowiedz się więcej na temat korzystania z interfejs API analizy tekstu z innymi narzędziami i technologiami na naszej [stronie zawartości społeczności zewnętrznej &](text-analytics-resource-external-community.md).
