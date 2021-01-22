---
title: Microsoft Azure Media Services typowe scenariusze | Microsoft Docs
description: Ten artykuł zawiera omówienie scenariuszy Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/3/2020
ms.author: inhenkel
ms.openlocfilehash: d195ad6715c47b9b4c14dc2e65ba1d07ebf79ce8
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696265"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Microsoft Azure Media Services typowe scenariusze

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/media-services-overview.md). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Usługa Microsoft Azure Media Services (AMS) umożliwia bezpieczne przekazywanie, przechowywanie, kodowanie i tworzenie pakietów zawartości wideo lub audio na potrzeby transmisji strumieniowej na żądanie i na żywo do różnych klientów (np. odbiorników TV, komputerów i urządzeń przenośnych).

W tym artykule przedstawiono typowe scenariusze dostarczania zawartości na żywo lub na żądanie.

## <a name="overview"></a>Omówienie

### <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure. Jeśli nie masz konta, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com).
* Konto usługi Azure Media Services. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](media-services-portal-create-account.md).
* Punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**.

    Po utworzeniu konta AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego w stanie **zatrzymanym** . Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania, punkt końcowy przesyłania strumieniowego musi mieć stan **Uruchomiony**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Najczęściej używane obiekty podczas projektowania w modelu AMS OData

Na poniższym obrazie przedstawiono niektóre z najczęściej używanych obiektów podczas tworzenia w modelu Media Services OData.

Kliknij obraz, aby go wyświetlić w pełnym rozmiarze.  

[![Diagram przedstawiający niektóre z najczęściej używanych obiektów podczas tworzenia względem modelu danych Azure Media Services obiektów.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

Cały model możesz obejrzeć [tutaj](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Ochrona zawartości w magazynie i dostarczanie multimediów strumieniowych w formie niezaszyfrowanej

![Wideo na żądanie — przepływ pracy](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Przekaż plik multimedialny wysokiej jakości do elementu zawartości.

    Zastosowanie opcji szyfrowania magazynu dla zasobu w celu ochrony zawartości podczas przekazywania oraz zaleca się, aby w przypadku przechowywania danych w magazynie było zalecane.

1. Wykonaj kodowanie do zestawu plików MP4 o adaptacyjnej szybkości transmisji bitów.

    Zaleca się zastosowanie opcji szyfrowania magazynu dla wyjściowego elementu zawartości, aby chronić zawartość w stanie spoczynku.

1. Skonfiguruj zasady dostarczania elementu zawartości (stosowane podczas dynamicznego tworzenia pakietów).

    Jeśli element zawartości jest szyfrowany w magazynie, **musisz** skonfigurować zasady dostarczania elementu zawartości.
1. Opublikuj element zawartości, tworząc lokalizator OnDemand.
1. Prześlij strumieniowo opublikowaną zawartość.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Ochrona zawartości w magazynie i dostarczanie dynamicznie szyfrowanych multimediów strumieniowych

![Ochrona za pomocą PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Przekaż plik multimedialny wysokiej jakości do elementu zawartości. Zastosuj opcję szyfrowania magazynu w odniesieniu do elementu zawartości.
1. Wykonaj kodowanie do zestawu plików MP4 o adaptacyjnej szybkości transmisji bitów. Zastosuj opcję szyfrowania magazynu w odniesieniu do elementu zawartości wyjściowej.
1. Utwórz klucz szyfrowania zawartości dla elementu zawartości, który ma zostać dynamicznie zaszyfrowany podczas odtwarzania.
1. Skonfiguruj zasady autoryzacji klucza zawartości.
1. Skonfiguruj zasady dostarczania zasobu (stosowane podczas pakowania dynamicznego i szyfrowania dynamicznego).
1. Opublikuj element zawartości, tworząc lokalizator OnDemand.
1. Prześlij strumieniowo opublikowaną zawartość.

## <a name="deliver-progressive-download"></a>Dostarczanie pobierania progresywnego

1. Przekaż plik multimedialny wysokiej jakości do elementu zawartości.
1. Wykonaj kodowanie do pojedynczego pliku MP4.
1. Opublikuj element zawartości, tworząc lokalizator OnDemand lub SAS. W przypadku użycia lokalizatora SAS zawartość zostanie pobrana z magazynu Azure Blob Storage. Nie musisz mieć punktów końcowych przesyłania strumieniowego w stanie uruchomienia.
1. Pobierz progresywnie zawartość.

## <a name="delivering-live-streaming-events"></a>Dostarczanie zdarzeń transmisji strumieniowej na żywo

1. Pozyskaj zawartość na żywo przy użyciu różnych protokołów transmisji strumieniowej na żywo (np. RTMP lub Smooth Streaming).
1. (Opcjonalnie) Zakoduj strumień w formie strumienia o adaptacyjnej szybkości transmisji bitów.
1. Wyświetl podgląd transmisji strumieniowej na żywo.
1. Dostarcz zawartość za pomocą:
    1. Typowe protokoły przesyłania strumieniowego (np. MPEG PAUZy, gładkie, HLS) bezpośrednio do klientów,
    1. Do Content Delivery Network (CDN) w celu dalszej dystrybucji lub
    1. Rejestruj i przechowuj pozyskaną zawartość do przesyłania strumieniowego później (wideo na żądanie).

W trakcie transmisji strumieniowej na żywo można wybrać jedną z następujących tras:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych (przekazujących)

Na poniższym diagramie przedstawiono główne elementy platformy AMS, które są zaangażowane w przepływ pracy **przekazywania**.

![Diagram przedstawiający główne części platformy M S, które są związane z przepływem pracy "pass-through".](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami odbierającymi strumień na żywo o różnych szybkościach transmisji bitów z koderów lokalnych](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Praca z kanałami obsługującymi kodowanie na żywo za pomocą usługi Azure Media Services

Na poniższym diagramie przedstawiono główne części platformy AMS, które są uwzględnione w przepływie pracy przesyłania strumieniowego na żywo, gdzie kanał jest włączony do kodowania na żywo przy użyciu Media Services.

![Przepływ pracy na żywo](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Korzystanie z zawartości

Usługa Azure Media Services udostępnia narzędzia potrzebne do tworzenia zaawansowanych, dynamicznych aplikacji klienckich odtwarzacza dla większości platform, takich jak: urządzenia z systemem iOS, urządzenia z systemem Android, urządzenia z systemem Windows, telefony z systemem Windows Phone, konsole Xbox i dekodery.

## <a name="enabling-azure-cdn"></a>Włączanie usługi Azure CDN

Usługa Media Services obsługuje integrację z usługą Azure CDN. Aby uzyskać informacje o sposobie włączania usługi Azure CDN, zobacz temat [Zarządzanie punktami końcowymi przesyłania strumieniowego na koncie usługi Media Services](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Skalowanie konta usługi Media Services

Klienci usługi AMS mogą skalować punkty końcowe przesyłania strumieniowego, przetwarzanie multimediów i przechowywanie na swoich kontach usługi AMS.

* Klienci usługi Media Services mogą wybrać **Standardowy** punkt końcowy przesyłania strumieniowego lub punkt końcowy przesyłania strumieniowego **Premium**. **Standardowy** punkt końcowy przesyłania strumieniowego jest odpowiedni w przypadku większości obciążeń przesyłania strumieniowego. Oferuje on te same funkcje, co punkty końcowe przesyłania strumieniowego **Premium**, oraz automatycznie skaluje przepustowość wychodzącą.

    Punkty końcowe przesyłania strumieniowego **Premium** są odpowiednie w przypadku zaawansowanych obciążeń, ponieważ zapewniają dedykowaną i skalowalną pojemność przepustowości. Klienci, którzy mają punkt końcowy przesyłania strumieniowego **Premium**, domyślnie uzyskują jedną jednostkę przesyłania strumieniowego (SU, streaming unit). Punkt końcowy przesyłania strumieniowego można skalować poprzez dodawanie jednostek SU. Każdy jednostka SU zwiększa pojemność przepustowości aplikacji. Aby uzyskać więcej informacji na temat skalowania punktów końcowych przesyłania strumieniowego **Premium**, zobacz temat [Skalowanie punktów końcowych przesyłania strumieniowego](media-services-portal-scale-streaming-endpoints.md).

* Konto usługi Media Services jest skojarzone z typem jednostki zarezerwowanej określającym szybkość, z jaką są przetwarzane zadania przetwarzania multimediów. Można wybrać następujące typy jednostek zarezerwowanych: **S1**, **S2** lub **S3**. Na przykład to samo zadanie kodowania jest wykonywane szybciej przy użyciu typu jednostki zarezerwowanej **S2** niż w przypadku użycia typu **S1**.

    Oprócz określania typu jednostki zarezerwowanej możesz określić, aby udostępnić konto za pomocą **jednostek zarezerwowanych** (jednostek ru). Liczba zainicjowanych jednostek zarezerwowanych określa liczbę zadań multimedialnych, które mogą być przetwarzane jednocześnie w ramach danego konta.

    > [!NOTE]
    > Jednostki zarezerwowane przekształcają wszystkie operacje przetwarzania multimediów do postaci równoległej, uwzględniając zadania Indeksowania za pomocą usługi Azure Media Indexer. Jednak w przeciwieństwie do kodowania zadania indeksowania nie są przetwarzane szybciej przy użyciu szybszych jednostek zarezerwowanych.

    Aby uzyskać więcej informacji, zobacz [Skalowanie przetwarzania multimediów](media-services-portal-scale-media-processing.md).

* Możliwe jest także skalowanie konta usługi Media Services przez dodanie do niego kont magazynu. Pojemność każdego konta magazynu jest ograniczona do 500 TB. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie kontami magazynu](./media-services-managing-multiple-storage-accounts.md).

## <a name="next-steps"></a>Następne kroki

[Migrowanie do usługi Media Services w wersji 3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]