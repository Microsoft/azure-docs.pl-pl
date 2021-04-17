---
title: Co należy wziąć pod uwagę podczas korzystania z Video Indexer na dużą skalę — Azure
titleSuffix: Azure Media Services
description: W tym temacie wyjaśniono, jakie zagadnienia należy wziąć pod uwagę podczas Video Indexer na dużą skalę.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: f941d81df670f017d24a7c5011c55fcc4f082605
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531574"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Co należy wziąć pod uwagę podczas korzystania z Video Indexer na dużą skalę

Jeśli indeksator Azure Media Services wideo do indeksowania wideo, a Twoje archiwum wideo rośnie, rozważ skalowanie. 

W tym artykule znajdują się odpowiedzi na pytania, takie jak:

* Czy istnieją ograniczenia technologiczne, które należy wziąć pod uwagę?
* Czy istnieje inteligentny i wydajny sposób na to?
* Czy mogę zapobiec wydawaniu nadmiarowych pieniędzy w procesie?

Ten artykuł zawiera sześć najlepszych rozwiązań w zakresie używania Video Indexer na dużą skalę.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Podczas przekazywania wideo rozważ użycie adresu URL w tablicy bajtów

Video Indexer umożliwia przekazywanie filmów wideo z adresu URL lub wysyłanie pliku bezpośrednio jako tablicy bajtów, a druga z nich ma pewne ograniczenia. Aby uzyskać więcej informacji, [zobacz uploading considerations and limitations (Zagadnienia i ograniczenia dotyczące przekazywania)](upload-index-videos.md#uploading-considerations-and-limitations)

Po pierwsze ma ograniczenia rozmiaru pliku. Rozmiar pliku tablicy bajtów jest ograniczony do 2 GB w porównaniu do ograniczenia rozmiaru przekazywania 30 GB podczas korzystania z adresu URL.

Po drugie rozważ tylko niektóre problemy, które mogą mieć wpływ na wydajność, a tym samym możliwość skalowania:

* Wysyłanie plików przy użyciu wielu części oznacza wysoką zależność od sieci, 
* niezawodność usługi, 
* Łączność 
* szybkość przekazywania, 
* utracone pakiety w sieci World Wide Web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Pierwsze zagadnienia dotyczące używania Video Indexer na dużą skalę":::

Podczas przekazywania wideo przy użyciu adresu URL wystarczy podać ścieżkę do lokalizacji pliku multimedialnego, a usługa Video Indexer zajmie się resztą (zobacz pole w interfejsie API przekazywania `videoUrl` [wideo).](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)

> [!TIP]
> Użyj `videoUrl` opcjonalnego parametru interfejsu API przekazywania wideo.

Aby zobaczyć przykład przekazywania wideo przy użyciu adresu URL, zapoznaj się z [tym przykładem.](upload-index-videos.md#code-sample) Możesz też użyć narzędzia [AzCopy,](../../storage/common/storage-use-azcopy-v10.md) aby szybko i niezawodnie pobrać zawartość na konto magazynu, z którego można przesłać zawartość do usługi Video Indexer adresem [URL sygnatury dostępu współdzielonego.](../../storage/common/storage-sas-overview.md)

## <a name="increase-media-reserved-units-if-needed"></a>Zwiększ liczbę jednostek zarezerwowanych multimediów w razie potrzeby

Zazwyczaj na etapie weryfikacji koncepcji, gdy dopiero zaczynasz korzystać Video Indexer, nie potrzebujesz dużo mocy obliczeniowej. Po rozpoczęciu tworzenia większego archiwum filmów wideo, które należy indeksować, i chcesz, aby proces był w tempie dopasowanym do Twojego przypadku użycia, musisz skalować w górę użycie Video Indexer. Dlatego należy zastanowić się nad zwiększeniem liczby zasobów obliczeniowych, których używasz, jeśli bieżąca ilość mocy obliczeniowej po prostu nie wystarcza.

W Azure Media Services, aby zwiększyć moc obliczeniową i zrównoleglić, należy zwrócić uwagę na jednostki zarezerwowane [multimediów](../latest/concept-media-reserved-units.md)(RU). Jednostki RU to jednostki obliczeniowe, które określają parametry zadań przetwarzania multimediów. Liczba procesorów RU wpływa na liczbę zadań multimedialnych, które mogą być przetwarzane współbieżnie na każdym koncie, a ich typ określa szybkość przetwarzania, a jeden film wideo może wymagać więcej niż jednej rui, jeśli jego indeksowanie jest złożone. Gdy twoje zasoby są zajęte, nowe zadania będą przechowywane w kolejce do momentu, gdy inny zasób będzie dostępny.

Aby działać wydajnie i uniknąć sytuacji, w których zasoby pozostają bezczynne przez cały czas, program Video Indexer oferuje system automatycznego skalowania, który wiruje w dół, gdy potrzebne jest mniejsze przetwarzanie, i rozkręca się w czasie kilku godzin (maksymalnie w celu pełnego wykorzystania wszystkich procesorów). Tę funkcję można włączyć, [włączając autoskalowanie](manage-account-connected-to-azure.md#autoscale-reserved-units) w ustawieniach konta lub używając interfejsu [API Update-Paid-Account-Azure-Media-Services.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Druga zagadnienia dotyczące używania Video Indexer na dużą skalę":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Jednostki zarezerwowane ams":::

Aby zminimalizować czas trwania indeksowania i niską przepływność, zalecamy rozpoczęcie od 10 jednostki RU typu S3. Jeśli później przeskalujesz w górę w celu obsługi większej zawartości lub większej współbieżności, a potrzebujesz więcej zasobów, możesz skontaktować się z nami przy użyciu systemu pomocy technicznej [(tylko](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w przypadku płatnych kont), aby poprosić o alokację większej liczby zasobów.

## <a name="respect-throttling"></a>Przestrzegaj ograniczania przepustowości

Video Indexer jest zbudowany w celu radzenia sobie z indeksowaniem na dużą skalę, a jeśli chcesz jak najlepiej go użyć, musisz również wiedzieć o możliwościach systemu i odpowiednio zaprojektować integrację. Nie chcesz wysyłać żądania przekazania dla partii filmów wideo, aby dowiedzieć się, że niektóre filmy nie zostały przesłane i otrzymujesz kod odpowiedzi HTTP 429 (zbyt wiele żądań). Może się tak zdarzyć, ponieważ wysłano więcej żądań niż wynosi limit filmów [na minutę, które obsługujemy](upload-index-videos.md#uploading-considerations-and-limitations). Video Indexer dodaje nagłówek w odpowiedzi HTTP, nagłówek określa, kiedy `retry-after` należy spróbować ponowić kolejną próbę. Przed podjęciem następnego żądania upewnij się, że go przestrzegasz.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Projektowanie integracji z należytą przestrzeganiem ograniczania przepustowości":::

## <a name="use-callback-url"></a>Używanie adresu URL wywołania zwrotnego

Zalecamy, aby zamiast stale sondować stan żądania od sekundy wysłania żądania przekazania, dodać adres [URL](upload-index-videos.md#callbackurl)wywołania zwrotnego i poczekać na Video Indexer aktualizacji. Natychmiast po zmianie stanu żądania przekazania otrzymasz powiadomienie POST na podany adres URL.

Adres URL wywołania zwrotnego można dodać jako jeden z parametrów interfejsu [API przekazywania wideo.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) Zapoznaj się z przykładami kodu w [repozytorium GitHub.](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/) 

W przypadku adresu URL wywołania zwrotnego możesz również użyć Azure Functions, bez serwera sterowanego zdarzeniami, która może być wyzwalana przez protokół HTTP i implementować następujący przepływ.

### <a name="callback-url-definition"></a>callBack URL definition (Definicja adresu URL wywołania zwrotnego)

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Użyj odpowiednich parametrów indeksowania

Podczas podejmowania decyzji związanych z używaniem Video Indexer na dużą skalę przyjrzyj się, jak jak najlepiej z niego korzystać, używając odpowiednich parametrów do swoich potrzeb. Zastanów się nad swoim przypadekem użycia, definiując różne parametry, aby zaoszczędzić pieniądze i przyspieszyć proces indeksowania filmów wideo.

Przed przekazaniem i indeksowaniem filmu [](upload-index-videos.md)wideo zapoznaj się z tą krótką dokumentacją. Zapoznaj się z tematem [indexingPreset](upload-index-videos.md#indexingpreset) and streamingPreset (IndeksowaniePreset i [streamingPreset),](upload-index-videos.md#streamingpreset) aby lepiej zrozumieć dostępne opcje.

Na przykład nie należy ustawiać ustawienia wstępnego na przesyłanie strumieniowe, jeśli nie planujesz oglądać wideo, nie indeksuj szczegółowych informacji o wideo, jeśli potrzebujesz tylko szczegółowych informacji o dźwięku.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Indeks w optymalnej rozdzielczości, a nie najwyższej rozdzielczości

Być może pytasz, jaka jakość wideo jest potrzebna do indeksowania filmów wideo? 

W wielu przypadkach wydajność indeksowania nie ma prawie żadnej różnicy między filmami wideo w jakości HD (720P) i wideo 4K. Ostatecznie otrzymasz prawie te same szczegółowe informacje z takim samym poziomem pewności. Im wyższa jakość przesłanego filmu, tym większy rozmiar pliku, co prowadzi do większej mocy obliczeniowej i czasu potrzebnego do przekazania wideo.

Na przykład w przypadku funkcji wykrywania twarzy wyższa rozdzielczość może pomóc w scenariuszu, w którym istnieje wiele małych, ale kontekstowo ważnych twarzy. Jednak będzie to związane z kwadratowym wzrostem czasu wykonywania i zwiększonym ryzykiem wyników fałszywie dodatnich.

Dlatego zalecamy sprawdzenie, czy masz odpowiednie wyniki dla twojego przypadku użycia, i najpierw przetestowanie go lokalnie. Przekaż ten sam film wideo w 720P i 4K i porównaj wyniki, które otrzymasz.

## <a name="next-steps"></a>Następne kroki

[Badanie danych wyjściowych usługi Azure Video Indexer wytwarzanych przez interfejs API](video-indexer-output-json-v2.md)