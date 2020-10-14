---
title: Przesyłanie strumieniowe zawartości z integracją z usługą CDN
titleSuffix: Azure Media Services
description: Dowiedz się więcej na temat przesyłania strumieniowego zawartości z integracją usługi CDN, a także pobierania i Origin-Assist usługi CDN — pobieranie z wyprzedzeniem.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 6bdf6015ca5633c77280111a55055a7394cee5bd
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057658"
---
# <a name="stream-content-with-cdn-integration"></a>Przesyłanie strumieniowe zawartości z integracją z usługą CDN

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Usługa Azure Content Delivery Network (CDN) oferuje deweloperom globalne rozwiązanie umożliwiające szybkie dostarczanie użytkownikom zawartości wymagającej wysokiej przepustowości przez zapisywanie zawartości w pamięci podręcznej w węzłach fizycznych strategicznie rozmieszczonych na całym świecie.  

Usługa CDN buforuje zawartość przesyłaną strumieniowo z [punktu końcowego Media Services streaming (Origin)](streaming-endpoint-concept.md) na koder-dekoder, na protokół przesyłania strumieniowego, za szybkość transmisji bitów na format kontenera oraz na szyfrowanie/DRM. Dla każdej kombinacji kodera-strumieniowego protokołu-Stream — format kontenera — szybkość transmisji bitów — szyfrowanie będzie mieć osobną pamięć podręczną usługi CDN.

Popularna zawartość będzie obsługiwana bezpośrednio z pamięci podręcznej usługi CDN, o ile fragment wideo jest buforowany. Zawartość na żywo jest prawdopodobnie w pamięci podręcznej, ponieważ zazwyczaj masz wiele osób oglądających dokładnie te same rzeczy. Zawartość na żądanie może być trickier bitowym, ponieważ istnieje pewna zawartość, która jest najbardziej popularna, a niektóre z nich nie są. Jeśli masz miliony zasobów wideo, które nie są popularne (tylko jeden lub dwa osoby przeglądające w tygodniu), ale masz tysiące osób oglądających wszystkie różne wideo, Sieć CDN jest znacznie mniej skuteczna.

Należy również wziąć pod uwagę sposób działania adaptacyjnego przesyłania strumieniowego. Każdy pojedynczy fragment wideo jest buforowany jako jego własny obiekt. Na przykład załóżmy, że po raz pierwszy oglądasz film wideo. Jeśli podgląd pominie około zaledwie kilku sekund, a tylko fragmenty wideo skojarzone z tym, co ktoś ogląda w pamięci podręcznej w usłudze CDN. Dzięki adaptacyjnemu przesyłaniu strumieniowym zazwyczaj masz od 5 do 7 różnych szybkości transmisji wideo. Jeśli jedna osoba ogląda jedną szybkość transmisji bitów, a inna osoba ogląda inną szybkość transmisji bitów, to każda z nich jest buforowana osobno w sieci CDN. Nawet jeśli dwie osoby oglądają tę samą szybkość transmisji bitów, mogą one być przesyłane strumieniowo za pośrednictwem różnych protokołów. Każdy protokół (HLS, MPEG-KRESKa, Smooth Streaming) jest buforowany osobno. Dlatego każda szybkość transmisji bitów i protokół są buforowane oddzielnie, a tylko te fragmenty wideo, które zostały żądane, są buforowane.

Z wyjątkiem środowiska testowego zalecamy włączenie usługi CDN dla punktów końcowych przesyłania strumieniowego w warstwach Standardowa i Premium. Każdy typ punktu końcowego przesyłania strumieniowego ma inny obsługiwany limit przepływności.
Trudno jest precyzyjnie obliczyć maksymalną liczbę współbieżnych strumieni obsługiwanych przez punkt końcowy przesyłania strumieniowego, ponieważ istnieją różne czynniki, które należy wziąć pod uwagę. Należą do nich:

- Maksymalna szybkość transmisji bitów użyta na potrzeby przesyłania strumieniowego
- Zachowanie przed buforowaniem i przełączeniem odtwarzacza. Gracze próbują połączyć segmenty ze źródła i wykorzystać szybkość ładowania do obliczenia przełączania z adaptacyjną szybkością transmisji bitów. Jeśli punkt końcowy przesyłania strumieniowego zostanie zbliżony do nasycenia, czasy odpowiedzi mogą się różnić, a gracze zaczynają przełączać się do niższej jakości. Ponieważ zmniejsza to obciążenie odtwarzaczy punktów końcowych przesyłania strumieniowego, można skalować z powrotem do wyższej jakości, tworząc niepożądane wyzwalacze.
Ogólnie można bezpiecznie oszacować maksymalne współbieżne strumienie, przyjmując maksymalną przepływność punktów końcowych przesyłania strumieniowego i dzieląc ją przez maksymalną szybkość transmisji bitów (przy założeniu, że wszyscy gracze używają najwyższej szybkości transmisji bitów). Można na przykład mieć standardowy punkt końcowy przesyłania strumieniowego, który jest ograniczony do 600 MB/s i największą szybkość transmisji bitów 3Mbp. W takim przypadku na najwyższej szybkości transmisji bitów są obsługiwane około 200 współbieżnych strumieni. Pamiętaj, aby również uwzględnić czynniki w wymaganiach dotyczących przepustowości audio. Mimo że strumień audio może być przesyłany strumieniowo na 128 ochrony klucza, łączny czas przesyłania strumieniowego jest szybko dodawany, gdy zostanie pomnożony przez liczbę współbieżnych strumieni.

W tym temacie omówiono Włączanie integracji z siecią [CDN](#enable-azure-cdn-integration). Opisano w nim również wstępne pobieranie (aktywne buforowanie) i koncepcję z [wyprzedzeniem](#origin-assist-cdn-prefetch) dotyczącej usługi CDN.

## <a name="considerations"></a>Istotne zagadnienia

- [Punkt końcowy przesyłania strumieniowego](streaming-endpoint-concept.md) `hostname` i adres URL przesyłania strumieniowego pozostają takie same niezależnie od tego, czy jest włączona sieć CDN.
- Jeśli potrzebujesz możliwości testowania zawartości z użyciem usługi CDN lub bez niej, Utwórz inny punkt końcowy przesyłania strumieniowego, który nie jest włączony w sieci CDN.

## <a name="enable-azure-cdn-integration"></a>Włącz integrację Azure CDN

> [!IMPORTANT]
> Nie można włączyć usługi CDN dla kont platformy Azure z wersji próbnej lub ucznia.
>
> Integracja z usługą CDN jest włączona we wszystkich centrach danych platformy Azure z wyjątkiem regionów federalnych i w Chinach.

Po udostępnieniu punktu końcowego przesyłania strumieniowego z włączoną usługą CDN jest zdefiniowany czas oczekiwania na Media Services przed aktualizacją usługi DNS w celu zamapowania punktu końcowego przesyłania strumieniowego na punkt końcowy usługi CDN.

Jeśli zechcesz później wyłączyć/włączyć usługę CDN, punkt końcowy przesyłania strumieniowego musi znajdować się w stanie **zatrzymanym** . Gdy punkt końcowy przesyłania strumieniowego zostanie uruchomiony, może upłynąć do czterech godzin, aby można było włączyć integrację Azure CDN i aby zmiany były aktywne we wszystkich punktach pop usługi CDN. Można jednak uruchomić punkt końcowy przesyłania strumieniowego i strumień bez przerw w punkcie końcowym przesyłania strumieniowego. Po zakończeniu integracji strumień jest dostarczany z sieci CDN. W trakcie okresu aprowizacji punkt końcowy przesyłania strumieniowego będzie w stanie **uruchomienia** i może wystąpić spadek wydajności.

Po utworzeniu standardowego punktu końcowego przesyłania strumieniowego jest on konfigurowany domyślnie przy użyciu standardowego Verizon. Korzystając z interfejsów API REST, można skonfigurować Verizon Premium lub standardowego dostawcy Akamai.

Integracja Azure Media Services z Azure CDN jest zaimplementowana w **Azure CDN z Verizon** dla standardowych punktów końcowych przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego w warstwie Premium można skonfigurować przy użyciu wszystkich **Azure CDN warstw cenowych i dostawców**.

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat Azure CDN, zobacz [Omówienie usługi CDN](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Ustal, czy wprowadzono zmianę w systemie DNS

Można określić, czy zmiana DNS została wprowadzona w punkcie końcowym przesyłania strumieniowego (ruch jest kierowany do Azure CDN) za pomocą programu <https://www.digwebinterface.com> . Jeśli w wynikach pojawią się azureedge.net nazwy domen, ruch jest teraz wskazywany do sieci CDN.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

Buforowanie sieci CDN jest procesem reaktywnym. Jeśli sieć CDN będzie mogła przewidzieć żądanie następnego obiektu, Sieć CDN może proaktywnie zażądać i przechować następny obiekt w pamięci podręcznej. Dzięki temu procesowi można uzyskać trafienie pamięci podręcznej dla wszystkich (lub większości) obiektów, co zwiększa wydajność.

Koncepcja pobierania z wyprzedzeniem dąży do położenia obiektów na "krawędzi Internetu" w przewidywaniu, że zostaną one zlecone przez odtwarzacz, co skraca czas na dostarczenie tego obiektu do odtwarzacza.

Aby osiągnąć ten cel, punkt końcowy przesyłania strumieniowego (Źródło) i sieć CDN muszą współpracować z ręką na kilka sposobów:

- Pochodzenie Media Services musi mieć "inteligencję" (Źródło), aby poinformować sieć CDN o następnym obiekcie do pobrania z wyprzedzeniem.
- Usługa CDN umożliwia pobieranie z wyprzedzeniem i buforowanie (część usługi CDN — pobieranie z wyprzedzeniem). Usługa CDN musi również mieć "inteligencję", aby poinformować źródło o tym, czy jest to pobieranie z wyprzedzeniem czy regularne pobieranie, obsługiwać odpowiedzi 404 i sposób unikania nieskończonej pętli pobierania z wyprzedzeniem.

### <a name="benefits"></a>Korzyści

Zalety funkcji pobierania z *wyprzedzeniem usługi CDN* to:

- Pobieranie z wyprzedzeniem poprawia jakość odtwarzania wideo przez wstępne pozycjonowanie przewidzianych segmentów wideo na krawędzi podczas odtwarzania, zmniejszanie opóźnień w podglądzie i poprawianie czasów pobierania segmentów wideo. Powoduje to przyspieszenie czasu uruchamiania wideo i zmniejszenie wystąpień ponownego buforowania.
- Pojęcie to jest stosowane do ogólnego scenariusza sieci CDN i nie jest ograniczone do nośników.
- Akamai dodał tę funkcję do usługi [Akamai Cloud embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Ta funkcja nie ma jeszcze zastosowania do usługi CDN Akamai zintegrowanej z punktem końcowym usługi Media Services Streaming. Jest to jednak dostępne dla klientów Media Services, którzy mają już istniejący kontrakt Akamai i wymagają niestandardowej integracji między Akamai CDN i pochodzeniem Media Services.

### <a name="how-it-works"></a>Jak to działa

Obsługa sieci CDN dla `Origin-Assist CDN-Prefetch` nagłówków (zarówno w przypadku przesyłania strumieniowego na żywo, jak i wideo na żądanie) jest dostępna dla klientów z bezpośrednią umową z usługą AKAMAI CDN. Funkcja obejmuje następujące wymiany nagłówka HTTP między Akamai CDN i pochodzeniem Media Services:

|Nagłówek HTTP|Wartości|Nadawca|Odbiornik|Przeznaczenie|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (wartość domyślna) lub 0 |CDN|Origin|Aby wskazać, że Usługa CDN jest włączona z wyprzedzeniem.|
|`CDN-Origin-Assist-Prefetch-Path`| Przykład: <br/>Fragmenty (wideo = 1400000000, format = MPD-Time-CMAF)|Origin|CDN|W celu zapewnienia ścieżki pobierania z wyprzedzeniem do sieci CDN.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (żądanie pobrania z wyprzedzeniem) lub 0 (regularne żądanie)|CDN|Origin|Aby wskazać żądanie z usługi CDN, należy wykonać pobieranie z wyprzedzeniem.|

Aby wyświetlić część działania wymiany nagłówka, możesz spróbować wykonać następujące czynności:

1. Użyj programu Poster lub Zwinięciea, aby wydać żądanie do pochodzenie Media Services dla segmentu audio lub wideo lub fragmentu. Upewnij się, że `CDN-Origin-Assist-Prefetch-Enabled: 1` w żądaniu dodano nagłówek.
2. W odpowiedzi powinien zostać wyświetlony nagłówek `CDN-Origin-Assist-Prefetch-Path` z ścieżką względną jako wartość.

### <a name="supported-streaming-protocols"></a>Obsługiwane protokoły przesyłania strumieniowego

`Origin-Assist CDN-Prefetch`Funkcja obsługuje następujące protokoły przesyłania strumieniowego do przesyłania strumieniowego na żywo i na żądanie:

* HLS v3
* HLS v4
* HLS CMAF
* KRESKA (CSF)
* KRESKA (CMAF)
* Płynne przesyłanie strumieniowe

### <a name="faqs"></a>Często zadawane pytania

* Co zrobić, jeśli adres URL ścieżki pobierania z wyprzedzeniem jest nieprawidłowy, dzięki czemu pobieranie z wyprzedzeniem przez usługę CDN jest 404?

    Usługa CDN będzie buforować w pamięci podręcznej odpowiedź 404 przez 10 sekund (lub inną skonfigurowaną wartość).

* Załóżmy, że masz wideo na żądanie. W przypadku włączenia usługi CDN — pobieranie z wyprzedzeniem oznacza, że po zażądaniu pierwszego segmentu wideo przez klienta w ramach pobierania z wyprzedzeniem zostanie uruchomiona pętla obejmująca wszystkie kolejne segmenty wideo z tą samą szybkością transmisji bitów?

    Nie, Usługa CDN — pobieranie z wyprzedzeniem odbywa się tylko po zainicjowaniu żądania/odpowiedzi przez klienta. Usługa CDN — pobieranie z wyprzedzeniem nigdy nie jest wyzwalane przez pobieranie z wyprzedzeniem, aby uniknąć pętli pobierania z wyprzedzeniem.

* Czy funkcja jest Origin-Assist CDN-Prefetch zawsze włączona? Jak można ją włączyć/wyłączyć?

    Ta funkcja jest domyślnie wyłączona. Klienci muszą ją włączyć za pośrednictwem interfejsu API Akamai.

* W przypadku przesyłania strumieniowego na żywo, co się stanie Origin-Assist, jeśli następny segment lub fragment nie jest jeszcze dostępny?

    W takim przypadku źródłem Media Services nie będzie udostępnić `CDN-Origin-Assist-Prefetch-Path` nagłówka i sieci CDN — pobieranie z wyprzedzeniem nie zostanie przeprowadzone.

* Jak `Origin-Assist CDN-Prefetch` działa z filtrami dynamicznego manifestu?

    Ta funkcja działa niezależnie od filtru manifestu. Gdy Następny fragment jest poza oknem filtru, jego adres URL będzie nadal znajdować się w manifeście pierwotnego klienta, a następnie zwracany jako nagłówek odpowiedzi pobierania z wyprzedzeniem usługi CDN. W związku z tym Usługa CDN pobierze adres URL fragmentu odfiltrowanego z ŁĄCZNIKa/HLS/Wygładź manifest. Jednak odtwarzacz nigdy nie wyśle żądania GET do sieci CDN w celu pobrania tego fragmentu, ponieważ ten fragment nie jest uwzględniony w delitera/HLS/gładkim manifeście przechowywanym przez odtwarzacz (gracz nie wie o istnieniu fragmentu).

* Czy jest możliwe pobieranie z listy odtwarzania PAUZ/HLS/gładkiego manifestu?

    Nie dodano ŁĄCZNIKa "No", "HLS Master Playlist", lista odtwarzania HLS Variant lub gładki adres URL manifestu nie został dodany do nagłówka z wyprzedzeniem.

* Czy adresy URL pobierania z wyprzedzeniem względne czy bezwzględne?

    Mimo Media Services że Usługa CDN Akamai umożliwia obu, pierwotne adresy URL dla ścieżki pobierania z wyprzedzeniem, ponieważ nie ma żadnej widocznej korzyści przy użyciu bezwzględnych adresów URL.

* Czy ta funkcja działa z zawartością chronioną za pomocą technologii DRM?

    Tak, ponieważ ta funkcja działa na poziomie HTTP, nie dekoduje ani nie analizuje żadnego segmentu/fragmentu. Nie należy zadbać o to, czy zawartość jest zaszyfrowana, czy nie.

* Czy ta funkcja działa wraz z wstawianiem AD po stronie serwera (SSAI)?
    
    W przypadku zawartości oryginalnej/głównej (oryginalna zawartość wideo przed wstawieniem AD) działa, ponieważ SSAI nie zmienia sygnatury czasowej zawartości źródłowej ze źródła Media Services. Czy ta funkcja współpracuje z zawartością usługi AD, zależy od tego, czy pochodzenie usługi AD nie obsługuje pochodzenie. Na przykład, jeśli zawartość usługi AD jest również hostowana w Azure Media Services (tego samego lub oddzielnego pochodzenia), zawartość usługi AD również zostanie pobrana.

* Czy ta funkcja działa z zawartością UHD/HEVC?

    Tak.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

* Pamiętaj, aby przejrzeć dokument [punktu końcowego przesyłania strumieniowego](streaming-endpoint-concept.md) .
* W przykładzie [w tym repozytorium](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) przedstawiono sposób uruchamiania domyślnego punktu końcowego przesyłania strumieniowego przy użyciu platformy .NET.
