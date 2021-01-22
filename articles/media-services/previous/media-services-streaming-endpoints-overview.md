---
title: Omówienie punktu końcowego przesyłania strumieniowego Azure Media Services | Microsoft Docs
description: Ten artykuł zawiera omówienie Azure Media Services punktów końcowych przesyłania strumieniowego.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f5e3d7e61b6c2a6ad3c121da9c0198c95ac24850
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694575"
---
# <a name="streaming-endpoints-overview"></a>Omówienie punktów końcowych przesyłania strumieniowego  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

W Microsoft Azure Media Services (AMS) **punkt końcowy przesyłania strumieniowego** reprezentuje usługę przesyłania strumieniowego, która umożliwia dostarczanie zawartości bezpośrednio do aplikacji odtwarzacza klienta lub Content Delivery Network (CDN) w celu dalszej dystrybucji. Media Services również zapewnia bezproblemowe Azure CDN integrację. Strumień wychodzący z usługi StreamingEndpoint może być strumieniem na żywo, wideo na żądanie lub pobieraniem progresywnym zasobu na koncie Media Services. Każde konto Azure Media Services obejmuje domyślny StreamingEndpoint. Dodatkowe StreamingEndpoints można utworzyć w ramach konta. Istnieją dwie wersje StreamingEndpoints, 1,0 i 2,0. Począwszy od 10 stycznia 2017 wszystkie nowo utworzone konta AMS będą zawierać **domyślne** StreamingEndpoint w wersji 2,0. Dodatkowe punkty końcowe przesyłania strumieniowego dodawane do tego konta również będą w wersji 2,0. Ta zmiana nie wpłynie na istniejące konta. istniejący StreamingEndpoints będzie w wersji 1,0 i można go uaktualnić do wersji 2,0. W przypadku tej zmiany zostanie zachowane zachowanie, rozliczenia i zmiany funkcji (Aby uzyskać więcej informacji, zobacz sekcję **Typy przesyłania strumieniowego i wersje** opisane poniżej).

Azure Media Services dodano następujące właściwości do jednostki punktu końcowego przesyłania strumieniowego: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Szczegółowe omówienie tych właściwości [można znaleźć w](/rest/api/media/operations/streamingendpoint)temacie. 

Podczas tworzenia konta Azure Media Services jest tworzony domyślny standardowy punkt końcowy przesyłania strumieniowego w stanie **zatrzymany** . Nie można usunąć domyślnego punktu końcowego przesyłania strumieniowego. W zależności od Azure CDN dostępności w regionie wskazywanym domyślnie nowo utworzony domyślny punkt końcowy przesyłania strumieniowego obejmuje również integrację z dostawcą usługi CDN "StandardVerizon". 
                
> [!NOTE]
> Integrację Azure CDN można wyłączyć przed uruchomieniem punktu końcowego przesyłania strumieniowego. `hostname`I adres URL przesyłania strumieniowego pozostaje taki sam niezależnie od tego, czy jest włączona sieć CDN.

Ten temat zawiera omówienie głównych funkcji udostępnianych przez punkty końcowe przesyłania strumieniowego.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Dla domyślnego punktu końcowego: `{AccountName}.streaming.mediaservices.windows.net`

Dla wszystkich dodatkowych punktów końcowych: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Typy i wersje przesyłania strumieniowego

### <a name="standardpremium-types-version-20"></a>Typy standardowe/Premium (wersja 2,0)

Począwszy od wersji 2017 stycznia Media Services, istnieją dwa typy przesyłania strumieniowego: **Standard** (wersja zapoznawcza) i **Premium**. Te typy są częścią wersji punktu końcowego przesyłania strumieniowego "2,0".


|Typ|Opis|
|--------|--------|  
|**Standardowa**|Domyślny punkt końcowy przesyłania strumieniowego jest typem **standardowym** , można go zmienić na typ Premium przez dostosowanie jednostek przesyłania strumieniowego.|
|**Premium** |Ta opcja jest odpowiednia dla profesjonalnych scenariuszy, które wymagają wyższego poziomu skalowania lub kontroli. Przechodzenie do typu **Premium** przez dostosowanie jednostek przesyłania strumieniowego.<br/>Dedykowane punkty końcowe przesyłania strumieniowego na żywo w środowisku izolowanym i nie konkurują o zasoby.|

W przypadku klientów chcących dostarczyć zawartość do dużych odbiorców internetowych zalecamy włączenie usługi CDN w punkcie końcowym przesyłania strumieniowego.

Aby uzyskać bardziej szczegółowe informacje, zobacz sekcję [Porównanie typów przesyłania strumieniowego](#comparing-streaming-types) poniżej.

### <a name="classic-type-version-10"></a>Typ klasyczny (wersja 1,0)

W przypadku użytkowników, którzy utworzyli konta usługi AMS przed wydaniem 10 2017 stycznia, masz **klasyczny** typ punktu końcowego przesyłania strumieniowego. Ten typ jest częścią wersji punktu końcowego przesyłania strumieniowego "1,0".

Jeśli w **wersji "1,0"** punkt końcowy przesyłania strumieniowego ma >= 1 Premium Streaming units (Su), będzie to punkt końcowy przesyłania strumieniowego w warstwie Premium i będzie on zawierał wszystkie funkcje AMS (podobnie jak w przypadku typu **Standard/Premium** ) bez dodatkowych kroków konfiguracji.

>[!NOTE]
>**Klasyczne** punkty końcowe przesyłania strumieniowego (wersja "1,0" i 0 Su) zapewniają ograniczoną liczbę funkcji i nie obejmują umowy SLA. Zaleca się migrację do typu **standardowego** , aby uzyskać lepsze środowisko i korzystać z funkcji, takich jak dynamiczne pakowanie lub szyfrowanie i inne funkcje, które są dostarczane z typem **standardowym** . Aby przeprowadzić migrację do typu **standardowego** , przejdź do [Azure Portal](https://portal.azure.com/) i wybierz pozycję **zgoda na Standard**. Aby uzyskać więcej informacji na temat migracji, zobacz sekcję [migracja](#migration-between-types) .
>
>Uważaj, że tej operacji nie można cofnąć i ma wpływ na cenę.
>
 
## <a name="comparing-streaming-types"></a>Porównywanie typów przesyłania strumieniowego

### <a name="versions"></a>Wersje

|Typ|StreamingEndpointVersion|ScaleUnits|CDN|Rozliczenia|
|--------------|----------|-----------------|-----------------|-----------------|
|Klasyczny|1.0|0|NA|Bezpłatna|
|Standardowy punkt końcowy przesyłania strumieniowego (wersja zapoznawcza)|2.0|0|Tak|Płatna|
|Jednostki przesyłania strumieniowego w warstwie Premium|1.0|>0|Tak|Płatna|
|Jednostki przesyłania strumieniowego w warstwie Premium|2.0|>0|Tak|Płatna|

### <a name="features"></a>Funkcje

Cechy|Standardowa (Standard)|Premium
---|---|---
Przepływność |Do 600 MB/s i może zapewnić znacznie wyższą skuteczną przepływność w przypadku użycia sieci CDN.|200 MB/s na jednostkę przesyłania strumieniowego (SU). W przypadku korzystania z sieci CDN można zapewnić znacznie wyższą skuteczną przepływność.
CDN|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.|Azure CDN, Sieć CDN innej firmy lub brak sieci CDN.
Opłaty są naliczane proporcjonalnie| Codziennie|Codziennie
Szyfrowanie dynamiczne|Tak|Tak
Dynamiczne tworzenie pakietów|Tak|Tak
Skalowanie|Automatycznie Skaluj do dostosowanej przepływności.|Dodatkowe jednostki przesyłania strumieniowego.
Filtrowanie/G20 IP/Host niestandardowy <sup>1</sup>|Tak|Tak
Pobieranie progresywne|Tak|Tak
Zalecane użycie |Zalecane w przypadku większości scenariuszy przesyłania strumieniowego.|Profesjonalne użycie. 

<sup>1</sup> używany bezpośrednio w punkcie końcowym przesyłania strumieniowego, gdy sieć CDN nie jest włączona w punkcie końcowym.<br/>

Aby uzyskać informacje o umowie SLA, zobacz [Cennik i Umowa SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migracja między typami

Źródło | Działanie | Akcja
---|---|---
Wdrożenie klasyczne|Standardowa (Standard)|Trzeba się zadecydować
Klasyczny|Premium| Skalowanie (dodatkowe jednostki przesyłania strumieniowego)
Standard/Premium|Klasyczny|Niedostępne (Jeśli wersja punktu końcowego przesyłania strumieniowego to 1,0). Można zmienić na klasyczny z ustawieniem scaleunits na "0".
Standardowa (z usługą CDN/bez)|Premium z tymi samymi konfiguracjami|Dozwolone w stanie **uruchomienia** . (za pośrednictwem Azure Portal)
Premium (z usługą CDN/bez)|Standardowa z tymi samymi konfiguracjami|Dozwolone w stanie **uruchomienia** (za pośrednictwem Azure Portal)
Standardowa (z usługą CDN/bez)|Premium z inną konfiguracją|Dozwolone w stanie **zatrzymania** (za pośrednictwem Azure Portal). Niedozwolone w stanie uruchomienia.
Premium (z usługą CDN/bez)|Standardowa z inną konfiguracją|Dozwolone w stanie **zatrzymania** (za pośrednictwem Azure Portal). Niedozwolone w stanie uruchomienia.
Wersja 1,0 z usługą SU >= 1 z usługą CDN|Standardowa/Premium bez sieci CDN|Dozwolone w stanie **zatrzymanym** . Niedozwolone w stanie **uruchomienia** .
Wersja 1,0 z usługą SU >= 1 z usługą CDN|Standardowa z siecią CDN/bez|Dozwolone w stanie **zatrzymanym** . Niedozwolone w stanie **uruchomienia** . Usługa CDN w wersji 1,0 zostanie usunięta i zostanie utworzona nowa i uruchomiona.
Wersja 1,0 z usługą SU >= 1 z usługą CDN|Premium z usługą CDN/bez|Dozwolone w stanie **zatrzymanym** . Niedozwolone w stanie **uruchomienia** . Klasyczna sieć CDN zostanie usunięta, a nowa została utworzona i uruchomiona.

## <a name="next-steps"></a>Następne kroki
Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
