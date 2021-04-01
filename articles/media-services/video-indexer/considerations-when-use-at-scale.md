---
title: Kwestie, które należy wziąć pod uwagę podczas korzystania z Video Indexer w skali — Azure
titleSuffix: Azure Media Services
description: W tym temacie wyjaśniono, jakie kwestie należy wziąć pod uwagę podczas korzystania z Video Indexer na dużą skalę.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: b955c0f494b757fd29c400194ef8b11314a89a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96483614"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Kwestie, które należy wziąć pod uwagę podczas korzystania z Video Indexer na dużą skalę

W przypadku korzystania z Azure Media Services indeksatora wideo do indeksowania wideo, a archiwum wideo rośnie, należy rozważyć skalowanie. 

W tym artykule znajdują się odpowiedzi na pytania, takie jak:

* Czy istnieją jakiekolwiek ograniczenia technologiczne, które należy wziąć pod uwagę?
* Czy istnieje inteligentny i wydajny sposób, aby to zrobić?
* Czy mogę zapobiec nadmiernemu wykorzystaniu wydatków w procesie?

Artykuł zawiera sześć najlepszych rozwiązań dotyczących używania Video Indexer w odpowiedniej skali.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Podczas przekazywania filmów wideo Rozważ użycie adresu URL w tablicy bajtowej

Video Indexer daje możliwość przekazania wideo z adresu URL lub bezpośrednio przez wysłanie pliku jako tablicę bajtową, a drugie z pewnymi ograniczeniami. Aby uzyskać więcej informacji, zobacz temat [przekazywanie zagadnień i ograniczeń)](upload-index-videos.md#uploading-considerations-and-limitations)

Po pierwsze ma ograniczenia dotyczące rozmiaru plików. Rozmiar pliku tablicy bajtów jest ograniczony do 2 GB w porównaniu z limitem rozmiaru przekazywania 30 GB podczas używania adresu URL.

Po drugie należy wziąć pod uwagę tylko niektóre problemy, które mogą mieć wpływ na wydajność, a tym samym możliwości skalowania:

* Wysyłanie plików przy użyciu wiele części oznacza wysoką zależność w sieci, 
* niezawodność usługi, 
* łączność 
* szybkość przekazywania, 
* utracone pakiety w sieci World Wide Web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Pierwsze zagadnienie dotyczące używania Video Indexer na dużą skalę":::

Po przekazaniu filmów wideo przy użyciu adresu URL wystarczy podać ścieżkę do lokalizacji pliku multimedialnego, a Video Indexer zajmie się resztą (zobacz `videoUrl` pole w interfejsie API [przekazywania wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) ).

> [!TIP]
> Użyj `videoUrl` opcjonalnego parametru interfejsu API przekazywania wideo.

Aby zobaczyć przykład sposobu przekazywania filmów wideo przy użyciu adresu URL, zapoznaj się z [tym przykładem](upload-index-videos.md#code-sample). Możesz też użyć [AzCopy](../../storage/common/storage-use-azcopy-v10.md) , aby szybko i niezawodnie uzyskać zawartość na konto magazynu, z którego można przesłać do Video Indexer przy użyciu [adresu URL sygnatury dostępu współdzielonego](../../storage/common/storage-sas-overview.md).

## <a name="increase-media-reserved-units-if-needed"></a>W razie konieczności Zwiększ jednostki zarezerwowane multimediów

Zwykle w fazie weryfikacji koncepcji, gdy dopiero zaczynasz korzystać z Video Indexer, nie potrzebujesz dużej mocy obliczeniowej. Po rozpoczęciu pracy nad większym archiwum wideo potrzebne do indeksowania i chcesz, aby proces znajdował się w tempie, który pasuje do Twojego przypadku użycia, musisz skalować w górę użycie Video Indexer. W związku z tym należy zastanowić się nad zwiększeniem liczby używanych zasobów obliczeniowych, jeśli Bieżąca ilość mocy obliczeniowej jest za mała.

W Azure Media Services, jeśli chcesz zwiększyć moc obliczeniową i przetwarzanie równoległe, musisz zwrócić uwagę na [jednostki zarezerwowane](../latest/concept-media-reserved-units.md)multimediów (jednostek ru). Jednostek ru są jednostkami obliczeniowymi, które określają parametry zadań przetwarzania multimediów. Liczba jednostek ru ma wpływ na liczbę zadań multimedialnych, które mogą być przetwarzane współbieżnie na poszczególnych kontach, a ich typ określa prędkość przetwarzania, a jedno wideo może wymagać więcej niż jednego obiektu RU, jeśli jego indeksowanie jest złożone. Gdy jednostek ru są zajęte, nowe zadania będą przechowywane w kolejce do momentu udostępnienia innego zasobu.

Aby efektywnie działać wydajnie i uniknąć braku zasobów, które pozostają w stanie bezczynności, Video Indexer oferuje system automatycznego skalowania, który jednostek RU w dół, gdy potrzebne jest mniejsze przetwarzanie i jednostek ru się w czasie w godzinach szczytu (do pełnego użycia jednostek ru). Tę funkcję można włączyć, [włączając funkcję skalowania automatycznego](manage-account-connected-to-azure.md#autoscale-reserved-units) w ustawieniach konta lub korzystając z funkcji [aktualizacji płatnych-Account-Azure-Media-Services](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update).

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Drugie zagadnienie dotyczące używania Video Indexer na dużą skalę":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Jednostki zarezerwowane AMS":::

Aby zminimalizować czas trwania indeksowania i niewielką przepływność, zalecamy rozpoczęcie od 10 jednostek ru typu S3. Później w przypadku skalowania w górę w celu zapewnienia obsługi większej ilości zawartości lub większej współbieżności, aby uzyskać więcej zasobów, możesz [skontaktować się z nami przy użyciu systemu pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (tylko na płatne konta), aby zażądać większej ilości alokacji jednostek ru.

## <a name="respect-throttling"></a>Poszanowanie ograniczenia przepustowości

Video Indexer jest oparta na rozpoczęciu indeksowania na dużą skalę, a jeśli chcesz, aby maksymalnie wykorzystać możliwości tego systemu, należy również wiedzieć, jak można się zaprojektować, i odpowiednio Zaprojektuj integrację. Nie chcesz wysyłać żądania przekazywania dla partii wideo tylko w celu ustalenia, że niektóre filmy nie zostały przekazane i otrzymujesz kod odpowiedzi HTTP 429 (zbyt wiele żądań). Przyczyną może być fakt, że wysłano więcej żądań niż [Limit filmów na minutę](upload-index-videos.md#uploading-considerations-and-limitations). Video Indexer dodaje `retry-after` nagłówek w odpowiedzi HTTP, nagłówek określa, kiedy należy podjąć kolejną ponowną próbę. Upewnij się, że jest to poszanowanie przed podjęciem próby następnego żądania.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Zaprojektuj swoją integrację i Uwzględnij ograniczenie przepustowości":::

## <a name="use-callback-url"></a>Użyj adresu URL wywołania zwrotnego

Firma Microsoft zaleca, aby zamiast sondowania stanu żądania stały się stale od drugiego wysłanego żądania przekazywania, można dodać [adres URL wywołania zwrotnego](upload-index-videos.md#callbackurl)i poczekać na video Indexer, aby zaktualizować użytkownika. Po zmianie stanu żądania przekazywania otrzymasz powiadomienie o podanym adresie URL.

Możesz dodać adres URL wywołania zwrotnego jako jeden z parametrów [interfejsu API przekazywania wideo](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload). Zapoznaj się z przykładami kodu w [repozytorium GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/). 

W przypadku adresu URL wywołania zwrotnego można również użyć Azure Functions, bezserwerowej platformy opartej na zdarzeniach, która może być wyzwalana przez protokół HTTP i implementować następujący przepływ.

### <a name="callback-url-definition"></a>Definicja adresu URL wywołania zwrotnego

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Korzystanie z prawidłowych parametrów indeksowania

Przy podejmowaniu decyzji związanych z używaniem Video Indexer na dużą skalę zapoznaj się z tym, jak najlepiej wykorzystać go do odpowiednich parametrów. Zastanów się nad przypadkiem użycia, definiując różne parametry, możesz zaoszczędzić pieniądze i przyspieszyć proces indeksowania wideo.

Przed przekazaniem i indeksowaniem wideo Przeczytaj tę krótką [dokumentację](upload-index-videos.md), sprawdź [indexingPreset](upload-index-videos.md#indexingpreset) i [streamingPreset](upload-index-videos.md#streamingpreset) , aby lepiej zrozumieć, jakie są dostępne opcje.

Na przykład nie należy ustawiać ustawienia wstępnego do przesyłania strumieniowego, jeśli nie planujesz oglądać wideo, nie Indeksuj szczegółowych informacji wideo, jeśli potrzebujesz tylko szczegółowych informacji o dźwięku.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Indeks w optymalnej rozdzielczości, a nie o najwyższej rozdzielczości

Może się pojawić pytanie, jaka jakość wideo jest potrzebna do indeksowania wideo? 

W wielu przypadkach wydajność indeksowania nie ma prawie żadnych różnic między plikami wideo HD (720) i 4 KB. Wreszcie uzyskasz niemal te same informacje o tym samym poziomie zaufania. Im wyższa jakość przekazywanego filmu, tym większy rozmiar pliku i to prowadzi do większej mocy obliczeniowej i czasu potrzebnego do przekazania wideo.

Na przykład w przypadku funkcji wykrywania twarzy wyższa rozdzielczość może pomóc w scenariuszu, w którym istnieje wiele małych i ważnych twarzy. Będzie to jednak miało wzrost kwadratowy środowiska uruchomieniowego i zwiększone ryzyko fałszywych dodatnich.

W związku z tym zalecamy sprawdzenie, czy otrzymujesz odpowiednie wyniki dla przypadku użycia i najpierw Przetestuj je lokalnie. Przekaż to samo wideo w 720 i w 4K i porównaj informacje, które otrzymujesz.

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie danych wyjściowych platformy Azure Video Indexer utworzonych przez interfejs API](video-indexer-output-json-v2.md)