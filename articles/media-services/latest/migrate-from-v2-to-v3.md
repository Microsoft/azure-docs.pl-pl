---
title: Migrowanie z Azure Media Services V2 do wersji v3 | Microsoft Docs
description: W tym artykule opisano zmiany wprowadzone w Azure Media Services v3 i przedstawiono różnice między dwiema wersjami. Artykuł zawiera również wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, broadcast, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b4e79a2aab5ca72ff8263bfc5734757bbff41005
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297743"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Wskazówki dotyczące migracji dotyczące przenoszenia z Media Services V2 do wersji v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Otrzymuj powiadomienia o tym, kiedy należy ponownie odwiedzić Tę stronę pod kątem aktualizacji przez skopiowanie i wklejenie tego adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us` do czytnika źródła danych RSS.

Ten artykuł zawiera wskazówki dotyczące migracji z Media Services V2 do wersji v3.

Jeśli masz już zainstalowaną usługę wideo na [starszej wersji interfejsów api Media Services V2](../previous/media-services-overview.md), przed migracją do interfejsów API v3 należy zapoznać się z poniższymi wskazówkami i kwestiami. Istnieje wiele zalet i nowych funkcji w interfejsie API v3, które zwiększają możliwości środowiska deweloperskiego i funkcje Media Services. Jednak zgodnie z opisem w sekcji [znane problemy](#known-issues) w tym artykule istnieją także pewne ograniczenia związane z zmianami między wersjami interfejsu API. Ta strona będzie utrzymywana, gdy zespół Media Services wprowadza dalsze ulepszenia interfejsów API v3 i rozwiązuje luki między wersjami. 

## <a name="prerequisites"></a>Wymagania wstępne

* Przegląd [Media Services V2 a v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Zalety Media Services v3
  
### <a name="api-is-more-approachable"></a>Interfejs API jest bardziej wydajny

*  Wersja 3 opiera się na ujednoliconej powierzchni interfejsu API, która udostępnia zarówno funkcje zarządzania, jak i operacji oparte na usłudze Azure Resource Manager. Przy użyciu szablonów Azure Resource Manager można tworzyć i wdrażać transformacje, punkty końcowe przesyłania strumieniowego, zdarzenia na żywo i wiele innych.
* Dokument [specyfikacji openapi (dawniej: Swagger)](https://aka.ms/ams-v3-rest-sdk) .
    Uwidacznia schemat dla wszystkich składników usługi, w tym kodowania opartego na plikach.
* Zestawy SDK dostępne dla [platform .NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](/javascript/api/overview/azure/mediaservices/management), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [go](https://aka.ms/ams-v3-go-ref)i Ruby.
* Integracja [interfejsu wiersza polecenia platformy Azure](https://aka.ms/ams-v3-cli-ref) w celu obsługi prostych skryptów.

### <a name="new-features"></a>Nowe funkcje

* W przypadku przetwarzania zadań opartych na plikach można użyć adresu URL HTTP (S) jako danych wejściowych.<br/>Nie musisz mieć już przechowywanej zawartości na platformie Azure ani nie musisz tworzyć zasobów.
* Wprowadza koncepcję [transformacji](transforms-jobs-concept.md) w przypadku przetwarzania zadań opartych na plikach. Przekształcenie może służyć do kompilowania konfiguracji do wielokrotnego użytku, tworzenia Azure Resource Manager szablonów i izolowania ustawień przetwarzania między wieloma klientami lub dzierżawcami.
* Element zawartości może zawierać wiele [lokalizatorów przesyłania strumieniowego](streaming-locators-concept.md) z różnymi [pakietami dynamicznymi](dynamic-packaging-overview.md) i dynamicznymi ustawieniami szyfrowania.
* Funkcja [Content Protection](content-key-policy-concept.md) obsługuje funkcje wielokluczowe.
* Można przesyłać strumieniowo zdarzenia na żywo, które są maksymalnie 24 godziny, przy użyciu Media Services do transkodowania pojedynczego źródła szybkości transmisji bitów do strumienia wyjściowego, który ma wiele szybkości transmisji bitów.
* Obsługa przesyłania strumieniowego na żywo w przypadku nowych małych opóźnień na żywo. Aby uzyskać więcej informacji, zobacz [opóźnienie](live-event-latency.md).
* Usługa Live Event Preview obsługuje [dynamiczne pakowanie](dynamic-packaging-overview.md) i szyfrowanie dynamiczne. Umożliwia to ochronę zawartości w wersji zapoznawczej, a także używanie ŁĄCZNIKa i HLS.
* Użycie danych wyjściowych na żywo jest łatwiejsze niż w przypadku jednostki programu w interfejsach API v2. 
* Ulepszona obsługa protokołu RTMP (Zwiększona stabilność i więcej obsługa kodera-źródła).
* Bezpieczne pozyskiwanie RTMP.<br/>Gdy tworzysz wydarzenie na żywo, otrzymujesz 4 adresy URL pozyskiwania. 4 adresy URL pozyskiwania są prawie identyczne, mają ten sam token przesyłania strumieniowego (AppId), tylko część numeru portu. Dwa z adresów URL to podstawowy i zapasowy dla RTMP.   
* Kontrola dostępu oparta na rolach (RBAC) jest dostępna dla jednostek. 

## <a name="known-issues"></a>Znane problemy

*  Obecnie można użyć [Azure Portal](https://portal.azure.com/) , aby:

    * Zarządzaj [zdarzeniami na żywo](live-events-outputs-concept.md)Media Services v3, 
    * Wyświetl [zasoby](assets-concept.md)v3 (nie Zarządzaj), 
    * [Uzyskaj informacje na temat uzyskiwania dostępu do interfejsów API](./access-api-howto.md). 

    W przypadku wszystkich innych zadań zarządzania (na przykład [transformacji i zadań](transforms-jobs-concept.md) oraz [ochrony zawartości](content-protection-overview.md)) należy użyć [interfejsu API REST](/rest/api/media/), [interfejsu wiersza polecenia](https://aka.ms/ams-v3-cli-ref)lub jednego z obsługiwanych [zestawów SDK](media-services-apis-overview.md#sdks).
* Musisz zainicjować obsługę jednostek zarezerwowanych multimediów (MRUs) na koncie, aby kontrolować współbieżność i wydajność zadań, w szczególności na potrzeby analizy wideo lub audio. Aby uzyskać więcej informacji, zobacz [Scaling Media Processing](../previous/media-services-scale-media-processing-overview.md) (Skalowanie przetwarzania multimediów). MRUs można zarządzać za pomocą [interfejsu wiersza polecenia 2,0 dla Media Services v3](media-reserved-units-cli-how-to.md)przy użyciu [Azure Portal](../previous/media-services-portal-scale-media-processing.md)lub przy użyciu [interfejsów API v2](../previous/media-services-dotnet-encoding-units.md). Należy udostępnić MRUs, niezależnie od tego, czy są Media Services używane interfejsy API w wersji 2 i v3.
* Media Services jednostek utworzonych za pomocą interfejsu API V3 nie można zarządzać za pomocą interfejsu API v2.  
* Nie wszystkie jednostki w interfejsie API v2 są automatycznie wyświetlane w interfejsie API v3.  Poniżej przedstawiono przykłady jednostek w dwóch niezgodnych wersjach:  
    * Zadania i zadania utworzone w wersji 2 nie są wyświetlane w programie v3, ponieważ nie są skojarzone z przekształceniem. Zaleca się przełączenie na transformacje v3 i zadania. Podczas przełączenia będzie konieczne monitorowanie zadań numerów porządkowych określających w wersji 2.
    * Kanały i programy utworzone przy użyciu wersji 2 (które są mapowane na zdarzenia na żywo i wyjście na żywo w programie v3) nie mogą być nadal zarządzane przy użyciu wersji 3. Zalecenie polega na przełączeniu się do wydarzeń na żywo i danych wyjściowych na żywo w wygodnym stopniu kanału.<br/>Obecnie nie można migrować ciągle uruchomionych kanałów.  

> [!NOTE]
> Ta strona będzie utrzymywana, gdy zespół Media Services wprowadza dalsze ulepszenia interfejsów API v3 i rozwiązuje luki między wersjami.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

[Samouczek: kodowanie pliku zdalnego na podstawie adresu URL i strumieniowego wideo — .NET](stream-files-dotnet-quickstart.md)
