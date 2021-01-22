---
title: Przekazywanie plików na konto usługi Azure Media Services z rozwiązania Azure StorSimple | Microsoft Docs
description: Ten artykuł zawiera krótkie omówienie usługi Azure StorSimple Data Manager. Artykuł zawiera również linki do samouczków, które przedstawiają sposób wyodrębniania danych z rozwiązania StorSimple i przekazywania ich jako elementów zawartości na konto usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d1d43f11c1a90456b24f02a5ec43982d5fdc3de7
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694524"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Przekazywanie plików na konto usługi Azure Media Services z rozwiązania Azure StorSimple 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)
>
> 
> Usługa Azure StorSimple Data Manager jest obecnie dostępna w prywatnej wersji zapoznawczej. 
> 

## <a name="overview"></a>Omówienie

Za pomocą usługi Media Services można przekazać pliki cyfrowe do elementu zawartości. Element zawartości może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów (oraz metadane dotyczące tych plików). Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze w celu dalszej przetwarzania i przesyłania strumieniowego.

Usługa [Azure StorSimple](../../storsimple/index.yml) używa magazynu w chmurze jako rozszerzenia rozwiązania lokalnego i automatycznie tworzy warstwy danych w magazynie lokalnym oraz magazynie w chmurze. Urządzenie StorSimple usuwa duplikaty i kompresuje dane przed wysłaniem ich do chmury, dzięki czemu wysyłanie dużych plików do chmury staje się bardzo wydajne. Usługa [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) oferuje interfejsy API, które umożliwiają wyodrębnianie danych z rozwiązania StorSimple i przedstawianie ich w postaci elementów zawartości usługi AMS.

## <a name="get-started"></a>Rozpoczęcie pracy

1. [Utwórz konto usługi Media Services](media-services-portal-create-account.md) , do którego chcesz przenieść elementy zawartości.
2. Utwórz nowe konto wersji zapoznawczej usługi Data Manager, zgodnie z opisem w artykule [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md).
3. Utwórz konto usługi StorSimple Data Manager.
4. Utwórz zadanie przekształcania danych, które po uruchomieniu wyodrębnia dane z urządzenia StorSimple i przekazuje je na konto usługi AMS jako elementy zawartości. 

    Gdy zadanie zacznie działać, zostanie utworzona kolejka magazynu. Jest ona wypełniana przy użyciu komunikatów dotyczących przekształconych obiektów blob, gdy będą gotowe. Nazwa tej kolejki jest taka sama jak nazwa definicji zadania. Za pomocą tej kolejki można określić, czy element zawartości jest gotowy i wywołać żądaną operację usługi Media Services do uruchomienia w jego obrębie. Tej kolejki można na przykład użyć do wyzwalania funkcji Azure Function, która zawiera niezbędny kod usługi Media Services.

## <a name="see-also"></a>Zobacz także

[Użyj zestawu SDK platformy .NET do wyzwalania zadań w Data Manager](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki

Teraz możesz zakodować przekazane elementy zawartości. Więcej informacji znajduje się na stronie [Kodowanie elementów zawartości](media-services-portal-encode.md).
