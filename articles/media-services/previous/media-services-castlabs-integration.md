---
title: Używanie castLabs do dostarczania licencji Widevine do Azure Media Services | Microsoft Docs
description: W tym artykule opisano, jak można użyć Azure Media Services (AMS) do dostarczania strumienia, który jest dynamicznie szyfrowany za pomocą usługi PlayReady i Widevine protokołów DRM.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan
ms.openlocfilehash: 576ac636f166e2daebbb9919d6666fea913a17be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017056"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Korzystanie z castLabs w celu dostarczania licencji Widevine do usługi Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Omówienie

W tym artykule opisano, jak można użyć Azure Media Services (AMS) do dostarczania strumienia, który jest dynamicznie szyfrowany za pomocą usługi PlayReady i Widevine protokołów DRM. Licencja PlayReady pochodzi z Media Services serwera licencji PlayReady i licencji Widevine jest dostarczana przez serwer licencji **castLabs** .

Aby odtworzyć zawartość strumieniową chronioną przez CENC (PlayReady i/lub Widevine), można użyć  [Azure Media Player](https://aka.ms/azuremediaplayer). Szczegóły można znaleźć w [dokumencie amp](https://amp.azure.net/libs/amp/latest/docs/) .

Na poniższym diagramie przedstawiono architekturę integracji na poziomie Azure Media Services i castLabs.

![integration](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Typowa konfiguracja systemu

* Zawartość multimedialna jest przechowywana w AMS.
* Identyfikatory kluczy zawartości są przechowywane zarówno w castLabs, jak i AMS.
* zarówno castLabs, jak i AMS mają wbudowaną funkcję uwierzytelniania tokenów. W poniższych sekcjach omówiono tokeny uwierzytelniania. 
* Gdy klient zażąda strumieniowego przegrania filmu wideo, zawartość jest dynamicznie szyfrowana przy użyciu **Common Encryption** (Cenc) i dynamicznie PAKOWANA przez AMS do Smooth Streaming i pauzy. Dostarczamy również usługi PlayReady M2TS podstawowe szyfrowanie strumienia dla protokołu przesyłania strumieniowego HLS.
* Licencja PlayReady jest pobierana z serwera licencji usługi AMS i licencja Widevine jest pobierana z serwera licencji castLabs. 
* Media Player automatycznie decyduje, którą licencję pobrać na podstawie możliwości platformy klienta. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Generowanie tokenu uwierzytelniania na potrzeby uzyskiwania licencji

Zarówno castLabs, jak i AMS obsługują format tokenu JWT (token sieci Web JSON) używany do autoryzacji licencji. 

### <a name="jwt-token-in-ams"></a>Token JWT w AMS

W poniższej tabeli opisano token JWT w AMS. 

| Wystawca | Ciąg wystawcy z wybranej usługi bezpiecznego tokenu (STS) |
| --- | --- |
| Grupy odbiorców |Ciąg odbiorców z użycia usługi STS |
| Oświadczenia |Zestaw oświadczeń |
| Nie wcześniej niż |Rozpocznij ważność tokenu |
| Wygasa |Koniec ważności tokenu |
| SigningCredentials |Klucz, który jest współużytkowany przez serwer licencji PlayReady, castLabs serwera licencji i usługi STS, może być kluczem symetrycznym lub asymetrycznym. |

### <a name="jwt-token-in-castlabs"></a>Token JWT w castLabs

W poniższej tabeli opisano token JWT w castLabs. 

| Nazwa | Opis |
| --- | --- |
| Znalezienia optdata |Ciąg JSON zawierający informacje o użytkowniku. |
| CRT |Ciąg JSON zawierający informacje o elemencie zawartości, jego informacji o licencji i prawach odtwarzania. |
| IAT |Bieżąca data i godzina w epoki. |
| jti |Unikatowy identyfikator tego tokenu (każdy token może być używany tylko raz w systemie castLabs). |

## <a name="sample-solution-setup"></a>Przykładowa konfiguracja rozwiązania

[Przykładowe rozwiązanie](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) składa się z dwóch projektów:

* Aplikacja konsolowa, która może służyć do ustawiania ograniczeń DRM dla już pozyskiwanego zasobu, dla oprogramowania PlayReady i Widevine.
* Aplikacja sieci Web, która wyczerpuje tokeny, co może być widoczne jako bardzo UPROSZCZONa wersja usługi STS.

Aby użyć aplikacji konsolowej:

1. Zmień app.config, aby skonfigurować poświadczenia usługi AMS, poświadczenia castLabs, konfigurację usługi STS i klucz współużytkowany.
2. Przekaż element zawartości do AMS.
3. Pobierz identyfikator UUID z przekazanego elementu zawartości i Zmień wiersz 32 w pliku program. cs:
   
      var objIAsset = _context. Assets. WHERE (x => x.Id = = "NB: CID: UUID: dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault ();
4. Użyj AssetId do nadawania nazw zasobom w systemie castLabs (wiersz 44 w pliku program. cs).
   
   Należy ustawić AssetId dla **castLabs**; musi być unikatowym ciągiem alfanumerycznym.
5. Uruchomisz program.

Aby użyć aplikacji sieci Web (STS):

1. Zmień web.config, aby skonfigurować identyfikator handlowca castlabs, konfigurację usługi STS i klucz współużytkowany.
2. Wdróż w usłudze Azure Websites.
3. Przejdź do witryny sieci Web.

## <a name="playing-back-a-video"></a>Odtwarzanie wideo

Aby odtworzyć wideo szyfrowane za pomocą typowego szyfrowania (PlayReady i/lub Widevine), można użyć [Azure Media Player](https://aka.ms/azuremediaplayer). Podczas uruchamiania aplikacji konsolowej identyfikator klucza zawartości i adres URL manifestu są wyświetlane jako echo.

1. Otwórz nową kartę i uruchom usługę STS: http://[yourStsName]. azurewebsites. NET/API/token/AssetID/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Przejdź do [Azure Media Player](https://aka.ms/azuremediaplayer).
3. Wklej w adresie URL przesyłania strumieniowego.
4. Kliknij pole wyboru **Zaawansowane opcje** .
5. Na liście rozwijanej **Ochrona** wybierz pozycję PlayReady i/lub Widevine.
6. Wklej token uzyskany z usługi STS w polu tekstowym tokenu. 
   
   Serwer licencji castLab nie potrzebuje prefiksu "Bearer =" przed tokenem. Usuń to przed przesłaniem tokenu.
7. Aktualizowanie odtwarzacza.
8. Film wideo powinien być odtwarzany.

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

