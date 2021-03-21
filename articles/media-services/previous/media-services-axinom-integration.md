---
title: Używanie Axinom do dostarczania licencji Widevine do Azure Media Services | Microsoft Docs
description: W tym artykule opisano, jak można użyć Azure Media Services (AMS) do dostarczania strumienia, który jest dynamicznie szyfrowany za pomocą usługi PlayReady i Widevine protokołów DRM. Licencja PlayReady pochodzi z Media Services serwera licencji PlayReady i licencji Widevine jest dostarczana przez serwer licencji Axinom.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 532e982b02bb35074370db45fec8cc42a898c83e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015526"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Korzystanie z Axinom w celu dostarczania licencji Widevine do usługi Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Omówienie
Azure Media Services (AMS) dodał ochronę dynamiczną Google Widevine (szczegółowe informacje znajdują się w [blogu Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) ). Ponadto Azure Media Player (AMP) również dodaliśmy obsługę Widevine (szczegółowe informacje znajdują się w [dokumencie amp](https://amp.azure.net/libs/amp/latest/docs/) ). Jest to poważny wpływ na zawartość KRESek strumieniowych chronioną przez CENC z wielojęzyczną funkcją DRM (PlayReady i Widevine) w nowoczesnych przeglądarkach, które są wyposażone w MSE i EME.

Począwszy od zestawu SDK Media Services .NET w wersji 3.5.2, Media Services umożliwia skonfigurowanie szablonu licencji Widevine i uzyskanie licencji Widevine. Licencje Widevine są dostępne również u następujących partnerów usługi AMS: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) i [castLabs](https://castlabs.com/company/partners/azure/).

W tym artykule opisano sposób integrowania i testowania serwera licencji Widevine zarządzanego przez program Axinom. Obejmuje to:  

* Konfigurowanie Common Encryption dynamicznych za pomocą technologii wielowątkowości (PlayReady i Widevine) z odpowiednimi adresami URL pozyskiwania licencji;
* Generowanie tokenu JWT w celu spełnienia wymagań dotyczących serwera licencji;
* Opracowywanie aplikacji Azure Media Player, która obsługuje pozyskiwanie licencji z uwierzytelnianiem tokenów JWT;

Kompletny system i przepływ klucza zawartości, identyfikatora klucza, inicjatora kluczy, tokenu JTW i jego oświadczeń można najlepiej opisać na poniższym diagramie:

![KRESKa i CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Aby skonfigurować ochronę dynamiczną i zasady dostarczania kluczy, zobacz blog Mingfei: [How to configure Widevine pakowanie with Azure Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Dynamiczną ochronę CENC można skonfigurować przy użyciu technologii wielowątkowości w przypadku przesyłania strumieniowego z użyciem obu następujących elementów:

1. Ochrona technologii PlayReady dla przeglądarki Microsoft Edge i IE11, która może mieć ograniczenie autoryzacji tokenu. Do zasad z ograniczeniami tokenu musi towarzyszyć token wystawiony przez usługę bezpiecznego tokenu (STS), taką jak Azure Active Directory;
2. Widevine ochronę dla programu Chrome może wymagać uwierzytelniania tokenu przy użyciu tokenu wystawionego przez inną usługę STS. 

Zobacz sekcję [generowanie tokenów JWT](media-services-axinom-integration.md#jwt-token-generation) , dla której Azure Active Directory nie może być używany jako serwer licencji usługi STS dla Axinom Widevine.

### <a name="considerations"></a>Zagadnienia do rozważenia
1. Aby wygenerować klucz zawartości służący do konfigurowania usługi dostarczania kluczy, należy użyć Axinom określonego inicjatora kluczy (8888000000000000000000000000000000000000) oraz wygenerowanego lub wybranego identyfikatora klucza. Serwer licencji Axinom wystawia wszystkie licencje zawierające klucze zawartości na podstawie tego samego inicjatora kluczy, który jest prawidłowy dla testowania i produkcji.
2. Adres URL pozyskiwania licencji Widevine do testowania: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense) . Dozwolone są zarówno HTTP, jak i HTTS.

## <a name="azure-media-player-preparation"></a>Przygotowanie Azure Media Player
AMP v 1.4.0 obsługuje odtwarzanie zawartości AMS, która jest dynamicznie pakowana przy użyciu oprogramowania PlayReady i Widevine DRM.
Jeśli serwer licencji Widevine nie wymaga uwierzytelniania tokenów, nie ma żadnych dodatkowych czynności, które należy wykonać, aby przetestować zawartość KRESKOWAną chronioną przez Widevine. Na przykład zespół AMP udostępnia prosty [przykład](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), w którym można zobaczyć, że działa on w przeglądarkach Microsoft Edge i IE11 za pomocą technologii PlayReady i Chrome z Widevine.
Serwer licencji Widevine dostarczany przez Axinom wymaga uwierzytelniania tokenu JWT. Token JWT należy przesłać z żądaniem licencji za pośrednictwem nagłówka HTTP "X-AxDRM-Message". W tym celu należy dodać następujący kod JavaScript na stronie sieci Web Hosting AMP przed ustawieniem źródła:

```html
<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>
```

Pozostała część kodu AMP jest standardowym interfejsem API AMP, jak w dokumencie AMP [tutaj](https://amp.azure.net/libs/amp/latest/docs/).

Powyższy kod JavaScript do ustawiania niestandardowego nagłówka autoryzacji jest nadal krótkoterminowym podejściem przed wydaniem oficjalnego podejścia długoterminowego w AMP.

## <a name="jwt-token-generation"></a>Generowanie tokenu JWT
Serwer licencji Axinom Widevine do testowania wymaga uwierzytelniania tokenu JWT. Ponadto jedno z oświadczeń w tokenie JWT jest złożonym typem obiektu, a nie typem danych pierwotnych.

Niestety usługa Azure AD może wystawiać tylko tokeny JWT z typami pierwotnymi. Podobnie interfejsy API .NET Framework (System. IdentityModel. Tokens. SecurityTokenHandler i JwtPayload) umożliwiają tylko wprowadzanie złożonych typów obiektów jako oświadczeń. Jednak oświadczenia są nadal serializowane jako ciąg. W związku z tym nie można użyć żadnego z dwóch tych elementów do wygenerowania tokenu JWT dla żądania licencji Widevine.

[Pakiet NuGet JWT](https://www.nuget.org/packages/JWT) Sheehan jest zgodny z potrzebami, więc zamierzamy użyć tego pakietu NuGet.

Poniżej znajduje się kod generujący token JWT z wymaganymi oświadczeniami wymaganymi przez Axinom Widevine License Server for test:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.IdentityModel.Tokens;
using System.IdentityModel.Protocols.WSTrust;
using System.Security.Claims;

namespace OpenIdConnectWeb.Utils
{
    public class JwtUtils
    {
        //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
        public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
        {
            byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

            var payload = new Dictionary<string, object>()
            {
                { "version", 1 },
                { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
            };

            string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

            return token;
        }

        //convert hex string to byte[]
        public static byte[] ConvertHexStringToByteArray(string hexString)
        {
            if (hexString.Length % 2 != 0)
            {
                throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
            }

            byte[] HexAsBytes = new byte[hexString.Length / 2];
            for (int index = 0; index < HexAsBytes.Length; index++)
            {
                string byteValue = hexString.Substring(index * 2, 2);
                HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
            }

            return HexAsBytes;
        }

    }  

}  
```

Serwer licencji Axinom Widevine

```xml
<add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />
```

### <a name="considerations"></a>Zagadnienia do rozważenia
1. Mimo że usługa dostarczania licencji usługi AMS PlayReady wymaga "Bearer =" poprzedzającego token uwierzytelniania, serwer licencji Axinom Widevine nie używa tego elementu.
2. Klucz komunikacji Axinom jest używany jako klucz podpisywania. Klucz jest ciągiem szesnastkowym, ale musi być traktowany jako seria bajtów, a nie ciąg znaków podczas kodowania. Jest to osiągane przez metodę ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Pobieranie identyfikatora klucza
Być może zauważono, że w kodzie do generowania tokenu JWT wymagany jest identyfikator klucza. Ponieważ token JWT musi być gotowy przed załadowaniem odtwarzacza AMP, należy pobrać identyfikator klucza w celu wygenerowania tokenu JWT.

Oczywiście istnieje wiele sposobów na uzyskanie identyfikatora klucza. Na przykład jeden może przechowywać identyfikator klucza razem z metadanymi zawartości w bazie danych. Można też pobrać identyfikator klucza z ŁĄCZNIKa MPD (Media Presentation Description). Poniższy kod jest przeznaczony dla tego ostatniego.

```csharp
//get key_id from DASH MPD
public static string GetKeyID(string dashUrl)
{
    if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
    {
        dashUrl += "(format=mpd-time-csf)";
    }

    XPathDocument objXPathDocument = new XPathDocument(dashUrl);
    XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
    XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
    objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
    objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
    objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
    objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
    objXmlNamespaceManager.PushScope();

    XPathNodeIterator objXPathNodeIterator;
    objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

    string key_id = string.Empty;
    if (objXPathNodeIterator.MoveNext())
    {
        key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
    }

    return key_id;
}
```

## <a name="summary"></a>Podsumowanie

Dzięki najnowszym dodaniu obsługi Widevine Azure Media Services w Content Protection i Azure Media Player można zaimplementować przesyłanie strumieniowe z użyciem ŁĄCZNIKa i wielonatywnej technologii DRM (PlayReady + Widevine) przy użyciu usługi licencjonowania PlayReady na serwerze licencji AMS i Widevine z Axinom dla następujących nowoczesnych przeglądarek:

* Chrome
* Microsoft Edge w systemie Windows 10
* Program IE 11 w systemach Windows 8.1 i Windows 10
* Programy Firefox (Desktop) i Safari na komputerach Mac (nie iOS) są również obsługiwane przez program Silverlight i ten sam adres URL z Azure Media Player

W przypadku korzystania z serwera licencji Axinom Widevine wymagane są następujące parametry. Oprócz identyfikatora klucza pozostałe parametry są udostępniane przez Axinom na podstawie konfiguracji serwera Widevine.

| Parametr | Sposób użycia |
| --- | --- |
| Identyfikator klucza komunikacji |Musi być uwzględniony jako wartość "com_key_id" w tokenie JWT (zobacz [tę](media-services-axinom-integration.md#jwt-token-generation) sekcję). |
| Klucz komunikacyjny |Musi być używany jako klucz podpisywania tokenu JWT (patrz [Ta](media-services-axinom-integration.md#jwt-token-generation) sekcja). |
| Inicjator kluczy |Musi zostać użyty do wygenerowania klucza zawartości z dowolnym IDENTYFIKATORem klucza zawartości (zobacz  [tę](media-services-axinom-integration.md#content-protection) sekcję). |
| Adres URL pozyskiwania licencji Widevine |Musi być używany podczas konfigurowania zasad dostarczania elementów zawartości dla przesyłania strumieniowego z użyciem ŁĄCZNIKów (zobacz  [tę](media-services-axinom-integration.md#content-protection) sekcję). |
| Identyfikator klucza zawartości |Musi być uwzględniony jako część wartości żądania komunikatu o uprawnienia token JWT (patrz [Ta](media-services-axinom-integration.md#jwt-token-generation) sekcja). |

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Podziękowania
Chcemy potwierdzić następujące osoby, które przyczyniają się do tworzenia tego dokumentu: Kristjan jõgi of Axinom, Mingfei Yan i Amit Rajput.

