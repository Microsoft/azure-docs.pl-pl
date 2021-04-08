---
title: Zasady kluczy zawartości w Media Services — Azure
description: Ten artykuł zawiera wyjaśnienie zasad kluczy zawartości i sposobu ich użycia przez Azure Media Services.
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
ms.custom: seodec18
ms.openlocfilehash: 1333bbcc7e2ea9219d28fcc1e63f21a833c2bc6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709567"
---
# <a name="content-key-policies"></a>Zasady kluczy zawartości

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Za pomocą Media Services można dostarczyć zawartość dynamiczną i na żądanie zaszyfrowaną dynamicznie przy użyciu Advanced Encryption Standard (AES-128) lub dowolnego z trzech głównych systemów zarządzania prawami cyfrowymi (DRM): Microsoft PlayReady, Google Widevine i Apple FairPlay. Media Services udostępnia również usługi do dostarczania kluczy AES oraz licencji DRM (PlayReady, Widevine i FairPlay) do autoryzowanych klientów. 

Aby określić opcje szyfrowania w strumieniu, należy utworzyć [zasady przesyłania strumieniowego](streaming-policy-concept.md) i skojarzyć je z [lokalizatorem przesyłania strumieniowego](streaming-locators-concept.md). Tworzenie [zasad klucza zawartości](/rest/api/media/contentkeypolicies) w celu skonfigurowania sposobu, w jaki klucz zawartości (zapewniający bezpieczny dostęp do [zasobów](assets-concept.md)) jest dostarczany do klientów końcowych. Należy ustawić wymagania (ograniczenia) dotyczące zasad kluczy zawartości, które muszą zostać spełnione, aby klucze z określoną konfiguracją były dostarczane do klientów. Zasady kluczy zawartości nie są wymagane do wyczyszczenia przesyłania strumieniowego ani pobierania. 

Zazwyczaj należy skojarzyć Zasady kluczy zawartości z [lokalizatorem przesyłania strumieniowego](streaming-locators-concept.md). Alternatywnie możesz określić zasady dotyczące klucza zawartości w ramach [zasad przesyłania strumieniowego](streaming-policy-concept.md) (podczas tworzenia niestandardowych zasad przesyłania strumieniowego dla scenariuszy zaawansowanych). 

## <a name="best-practices-and-considerations"></a>Najlepsze rozwiązania i zagadnienia

> [!IMPORTANT]
> Zapoznaj się z poniższymi zaleceniami.

* Należy zapoznać się z ograniczonym zestawem zasad dla konta usługi multimediów i użyć ich ponownie dla lokalizatorów przesyłania strumieniowego, gdy są potrzebne te same opcje. Aby uzyskać więcej informacji, zobacz limity [przydziału i limity](limits-quotas-constraints.md).
* Zasady kluczy zawartości są aktualizowalne. W przypadku pamięci podręcznej dostarczania kluczy do aktualizacji i pobrania zaktualizowanych zasad może upłynąć do 15 minut. 

   Aktualizacja zasad powoduje zastąpienie istniejącej pamięci podręcznej usługi CDN, co może spowodować problemy z odtwarzaniem klientów korzystających z zawartości w pamięci podręcznej.  
* Zalecamy, aby nie tworzyć nowych zasad kluczy zawartości dla każdego zasobu. Główną zaletą udostępniania tych samych zasad kluczy zawartości między zasobami, które wymagają tych samych opcji zasad, są:
   
   * Zarządzanie niewielką liczbą zasad jest łatwiejsze.
   * Jeśli musisz wprowadzić aktualizacje zasad klucza zawartości, zmiany zaczną obowiązywać od wszystkich nowych żądań licencji niemal od razu.
* Jeśli musisz utworzyć nowe zasady, musisz utworzyć nowy lokalizator przesyłania strumieniowego dla elementu zawartości.
* Zalecane jest, aby Media Services automatycznie generować klucz zawartości. 

   Zazwyczaj należy użyć długiego klucza i sprawdzić istnienie zasad klucza zawartości za pomocą [Get](/rest/api/media/contentkeypolicies/get). Aby uzyskać klucz, należy wywołać oddzielną metodę akcji w celu uzyskania kluczy tajnych lub poświadczeń, zobacz Poniższy przykład.

## <a name="example"></a>Przykład

Aby przejść do klucza, użyj `GetPolicyPropertiesWithSecretsAsync` , jak pokazano w przykładzie [Pobieranie klucza podpisywania z istniejącego zasad](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) .

## <a name="filtering-ordering-paging"></a>Filtrowanie, porządkowanie, stronicowanie

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek Media Services](entities-overview.md).

## <a name="additional-notes"></a>Uwagi dodatkowe

* Właściwości zasad kluczy zawartości `Datetime` typu są zawsze w formacie UTC.
* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki

* [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy](protect-with-aes128.md)
* [Używanie usługi dostarczania licencji i szyfrowania dynamicznego w technologii DRM](protect-with-drm.md)
* [Podstawowe szyfrowanie klucza szyfrowania kluczem i kod przykładowy przesyłania strumieniowego](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)
