---
title: Używanie Azure Media Services do dostarczania licencji DRM lub kluczy AES | Microsoft Docs
description: W tym artykule opisano, jak za pomocą usługi Azure Media Services dostarczać licencje PlayReady i/lub Widevine oraz klucze AES, ale nie tylko (kodowania, szyfrowania i przesyłania strumieniowego) przy użyciu serwerów lokalnych.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 8546c2c1-430b-4254-a88d-4436a83f9192
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: f2d965ec17fb605362c1e8cd8ef781a6bd2029d0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067388"
---
# <a name="use-media-services-to-deliver-drm-licenses-or-aes-keys"></a>Używanie Media Services do dostarczania licencji DRM lub kluczy AES

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services umożliwia pozyskiwanie, kodowanie, Dodawanie ochrony zawartości i przesyłanie strumieniowe zawartości. Aby uzyskać więcej informacji, zobacz [Korzystanie z dynamicznego szyfrowania Common Encryption w programie PlayReady i/lub Widevine](media-services-protect-with-playready-widevine.md). Niektórzy klienci chcą używać Media Services tylko w celu dostarczania licencji i/lub kluczy oraz kodowania, szyfrowania i przesyłania strumieniowego przy użyciu serwerów lokalnych. W tym artykule opisano, jak można użyć Media Services w celu dostarczenia licencji PlayReady i/lub Widevine, ale pozostałej pracy z serwerami lokalnymi. 

Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="overview"></a>Omówienie
Media Services zapewnia usługę do dostarczania licencji Widevine i zarządzania prawami cyfrowymi (DRM) oraz kluczy AES-128. Media Services udostępnia również interfejsy API, które umożliwiają skonfigurowanie praw i ograniczeń, które mają zostać wymuszone dla środowiska uruchomieniowego DRM, gdy użytkownik odtwarza zawartość chronioną przez DRM. Gdy użytkownik zażąda zawartości chronionej, aplikacja odtwarzacza żąda licencji z usługi licencjonowania Media Services. Jeśli licencja jest autoryzowana, usługa licencjonowania Media Services wystawia licencję dla odtwarzacza. Licencje PlayReady i Widevine zawierają klucz odszyfrowujący, który może być używany przez odtwarzacz klienta w celu odszyfrowania i strumieniowego przesyłania zawartości.

Media Services obsługuje wiele sposobów autoryzowania użytkowników, którzy podejmują licencje lub kluczowe żądania. Należy skonfigurować zasady autoryzacji klucza zawartości. Zasady mogą mieć jedno lub więcej ograniczeń. Opcje są ograniczeniami Open lub tokenem. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Media Services obsługuje tokeny w formacie prostego tokenu sieci Web (SWT) i formacie tokenu sieci Web JSON (JWT).

Na poniższym diagramie przedstawiono główne kroki, które należy wykonać, aby korzystać z Media Services w celu dostarczenia licencji PlayReady i/lub Widevine, ale pozostały w przypadku serwerów lokalnych:

![Ochrona za pomocą PlayReady](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko deweloperskie i wypełnij plik app.config za pomocą informacji o połączeniu, zgodnie z opisem w temacie [Media Services Development z platformą .NET](media-services-dotnet-how-to-use.md).

2. Dodaj następujące elementy do węzła **appSettings** zdefiniowanego w pliku app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```
 
## <a name="net-code-example"></a>Przykład kodu platformy .NET
Poniższy przykład kodu pokazuje, jak utworzyć wspólny klucz zawartości i uzyskać adresy URL pozyskiwania licencji PlayReady lub Widevine. Aby skonfigurować serwer lokalny, należy dysponować kluczem zawartości, IDENTYFIKATORem klucza i adresem URL pozyskiwania licencji. Po skonfigurowaniu serwera lokalnego można przesyłać strumieniowo z własnego serwera przesyłania strumieniowego. Ze względu na to, że zaszyfrowany strumień wskazuje na serwer licencji Media Services, odtwarzacz żąda licencji od Media Services. W przypadku wybrania opcji uwierzytelnianie tokenu serwer licencji Media Services sprawdza poprawność tokenu wysyłanego za pośrednictwem protokołu HTTPS. Jeśli token jest prawidłowy, serwer licencji dostarcza licencję z powrotem do odtwarzacza. Poniższy przykład kodu pokazuje, jak utworzyć wspólny klucz zawartości i uzyskać adresy URL pozyskiwania licencji PlayReady lub Widevine. Jeśli chcesz dostarczyć klucze AES-128, musisz utworzyć klucz zawartości koperty i uzyskać adres URL pozyskiwania klucza. Aby uzyskać więcej informacji, zobacz [Korzystanie z szyfrowania dynamicznego AES-128 i usługi dostarczania kluczy](media-services-playready-license-template-overview.md).

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DeliverDRMLicenses
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = true;
            string tokenTemplateString = null;


            IContentKey key = CreateCommonTypeContentKey();

            // Print out the key ID and Key in base64 string format
            Console.WriteLine("Created key {0} with key value {1} ",
                key.Id, System.Convert.ToBase64String(key.GetClearKeyValue()));

            Console.WriteLine("PlayReady License Key delivery URL: {0}",
                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));

            Console.WriteLine("Widevine License Key delivery URL: {0}",
                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine));

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}",
                key.AuthorizationPolicyId);
            Console.WriteLine();
            Console.ReadLine();
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with Open restrictions 
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>
            {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
            };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                _context.ContentKeyAuthorizationPolicyOptions.Create("",
                    ContentKeyDeliveryType.PlayReadyLicense,
                        restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
                _context.ContentKeyAuthorizationPolicyOptions.Create("",
                    ContentKeyDeliveryType.Widevine,
                    restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                        ContentKeyAuthorizationPolicies.
                        CreateAsync("Deliver Common Content Key with no restrictions").
                        Result;


            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>
            {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
            };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                    ContentKeyDeliveryType.PlayReadyLicense,
                        restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                    ContentKeyDeliveryType.Widevine,
                        restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                        ContentKeyAuthorizationPolicies.
                        CreateAsync("Deliver Common Content Key with token restrictions").
                        Result;

            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static private string ConfigurePlayReadyLicenseTemplate()
        {
            // The following code configures PlayReady License Template using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template 
            //for the response sent back to the end user. 
            //It contains a field for a custom data string between the license server 
            //and the application (may be useful for custom app logic) 
            //as well as a list of one or more license templates.

            PlayReadyLicenseResponseTemplate responseTemplate =
                new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template 
            // for creating PlayReady licenses
            // to be returned to the end users. 
            // It contains the data on the content key in the license 
            // and any rights or restrictions to be 
            // enforced by the PlayReady DRM runtime when using the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();

            // Configure whether the license is persistent 
            // (saved in persistent storage on the client) 
            // or non-persistent (only held in memory while the player is using the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150.  If false (the default), 
            // the MinimumSecurityLevel property of the license is set to 2000.
            licenseTemplate.AllowTestDevices = true;

            // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
            // It grants the user the ability to play back the content subject to the zero or more restrictions 
            // configured in the license and on the PlayRight itself (for playback specific policy). 
            // Much of the policy on the PlayRight has to do with output restrictions 
            // which control the types of outputs that the content can be played over and 
            // any restrictions that must be put in place when using a given output.
            // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
            //then the DRM runtime will only allow the video to be displayed over digital outputs 
            //(analog video outputs won’t be allowed to pass the content).

            // IMPORTANT: These types of restrictions can be very powerful 
            // but can also affect the consumer experience. 
            // If the output protections are configured too restrictive, 
            // the content might be unplayable on some clients. 
            // For more information, see the PlayReady Compliance Rules document.

            // For example:
            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

            responseTemplate.LicenseTemplates.Add(licenseTemplate);

            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
        }


        private static string ConfigureWidevineLicenseTemplate()
        {
            var template = new WidevineMessage
            {
                allowed_track_types = AllowedTrackTypes.SD_HD,
                content_key_specs = new[]
                {
                            new ContentKeySpecs
                            {
                                required_output_protection =
                                    new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                policy_overrides = new
                {
                    can_play = true,
                    can_persist = true,
                    can_renew = false
                }
            };

            string configuration = JsonConvert.SerializeObject(template);
            return configuration;
        }


        static public IContentKey CreateCommonTypeContentKey()
        {
            // Create envelope encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                                    keyId,
                                    contentKey,
                                    "ContentKey",
                                    ContentKeyType.CommonEncryption);

            return key;
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
                new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
* [Używanie dynamicznego szyfrowania Common Encryption w usługach PlayReady i Widevine](media-services-protect-with-playready-widevine.md)
* [Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy](media-services-playready-license-template-overview.md)
