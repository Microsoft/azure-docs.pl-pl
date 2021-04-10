---
title: Używanie dynamicznego szyfrowania Common Encryption w usługach PlayReady i Widevine | Microsoft Docs
description: Usługa Azure Media Services umożliwia dostarczanie strumieni MPEG-DASH, Smooth Streaming i HTTP Live Streaming (HLS) chronionych przy użyciu usługi Microsoft PlayReady DRM. Pozwala też dostarczać strumienie DASH szyfrowane przy użyciu usługi Widevine DRM. W tym temacie przedstawiono sposób dynamicznego szyfrowania przy użyciu usług PlayReady i Widevine DRM.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 9a0e8a513023c093157a3f14a64de9f87de543ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009457"
---
# <a name="use-playready-andor-widevine-dynamic-common-encryption"></a>Używanie dynamicznego szyfrowania Common Encryption w usługach PlayReady i Widevine

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).   > żadne nowe funkcje lub funkcje nie są dodawane do Media Services V2. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)
>   

## <a name="overview"></a>Omówienie

 Usługa Media Services umożliwia dostarczanie strumieni MPEG-DASH, Smooth Streaming i HTTP Live Streaming (HLS) chronionych przy użyciu [usługi zarządzania prawami cyfrowymi (DRM) PlayReady](https://www.microsoft.com/playready/overview/). Można też dostarczać zaszyfrowane strumienie DASH korzystające z licencji Widevine DRM. Obie usługi, PlayReady i Widevine, szyfrują dane zgodnie ze specyfikacją Common Encryption (ISO/IEC CENC 23001-7). Aby skorzystać z usługi Widevine, można skonfigurować obiekt AssetDeliveryConfiguration przy użyciu zestawu [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (począwszy od wersji 3.5.1) lub interfejsu API REST.

Usługa Media Services udostępnia usługę dostarczania licencji PlayReady i Widevine DRM. Usługa Media Services dostarcza również interfejsy API umożliwiające skonfigurowanie uprawnień i ograniczeń, które powinny być wymuszane w uruchomionym środowisku PlayReady lub Widevine DRM, gdy użytkownik odtwarza chronioną zawartość. Gdy użytkownik zażąda zawartości chronionej przy użyciu funkcji DRM, aplikacja odtwarzacza zażąda licencji od usługi licencjonowania Media Services. Jeśli aplikacja odtwarzacza zostanie autoryzowana, usługa licencjonowania Media Services wystawi licencję odtwarzaczowi. Licencja PlayReady lub Widevine zawiera klucz odszyfrowujący, który może zostać użyty przez odtwarzacz klienta do odszyfrowania i strumieniowego przesyłania zawartości.

Można też skorzystać z usług następujących partnerów usługi Media Services, aby łatwiej dostarczać licencje Widevine: 

* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Aby uzyskać więcej informacji, zapoznaj się z integracją z usługami [Axinom](media-services-axinom-integration.md) i [castLabs](media-services-castlabs-integration.md).

Usługa Media Services obsługuje wiele sposobów autoryzacji użytkowników, którzy tworzą żądania klucza. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń: ograniczenia otwarte lub ograniczenia tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje następujące formaty tokenów: [prosty token sieci Web](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) i [token sieci Web JSON](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) (JWT). 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad autoryzacji klucza zawartości](media-services-protect-with-aes128.md#configure_key_auth_policy).

Aby można było skorzystać z szyfrowania dynamicznego, potrzebny jest element zawartości zawierający zestaw plików MP4 o różnych szybkościach transmisji bitów lub pliki źródłowe Smooth Streaming o różnych szybkościach transmisji bitów. Należy również skonfigurować zasady dostarczania tego elementu zawartości (opisane w dalszej części niniejszego tematu). Następnie na podstawie formatu określonego w adresie URL przesyłanego strumienia serwer przesyłania strumieniowego na żądanie zapewnia, że strumień jest dostarczany za pomocą wybranego protokołu. W efekcie przechowujesz i opłacasz pliki tylko w jednym formacie magazynu. Usługa Media Services tworzy i udostępnia właściwą odpowiedź HTTP na podstawie każdego żądania klienta.

Ten artykuł jest przydatny dla deweloperów pracujących nad aplikacjami, które dostarczają zawartość chronioną przy użyciu wielu protokołów DRM, takich jak PlayReady i Widevine. W tym artykule opisano sposób konfigurowania usługi dostarczania licencji PlayReady przy użyciu zasad autoryzacji, tak aby tylko autoryzowani klienci mogli odbierać licencje PlayReady lub Widevine. Przedstawiono także sposób korzystania z szyfrowania przy użyciu usług PlayReady lub Widevine DRM dla strumienia DASH.

>[!NOTE]
>Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan „Uruchomiony”. 

## <a name="download-the-sample"></a>Pobieranie przykładu
Opisany w tym artykule przykład możesz pobrać ze [strony przykładów dotyczących platformy Azure w serwisie GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

## <a name="configure-dynamic-common-encryption-and-drm-license-delivery-services"></a>Konfigurowanie dynamicznego szyfrowania Common Encryption i usług dostarczania licencji DRM

Wykonaj następujące czynności ogólne w przypadku ochrony elementów zawartości przy użyciu usługi PlayReady przez zastosowanie usługi dostarczania licencji Media Services oraz szyfrowania dynamicznego:

1. Utwórz element zawartości i przekaż do niego pliki.

2. Przekoduj element zawartości zawierający plik na zestaw MP4 o adaptacyjnej szybkości bitowej.

3. Utwórz klucz zawartości i skojarz go z zakodowanym elementem zawartości. W usłudze Media Services klucz zawartości zawiera klucz szyfrowania elementu zawartości.

4. Skonfiguruj zasady autoryzacji klucza zawartości. Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient musi spełnić te zasady, aby klucz zawartości został do niego dostarczony.

    Podczas tworzenia zasad autoryzacji klucza zawartości należy określić metodę dostarczania (PlayReady lub Widevine) i ograniczenia (otwarte lub tokenu). Należy też określić informacje specyficzne dla typu klucza dostarczania, które definiują sposób dostarczania klucza do klienta (szablon licencji [PlayReady](media-services-playready-license-template-overview.md) lub [Widevine](media-services-widevine-license-template-overview.md)).

5. Skonfiguruj zasadę dostarczania elementu zawartości. Konfiguracja zasad dostarczania obejmuje protokół dostarczania (na przykład MPEG-DASH, HLS, Smooth Streaming lub wszystkie). Konfiguracja obejmuje również typ szyfrowania dynamicznego (na przykład Common Encryption) oraz adres URL pozyskiwania licencji PlayReady lub Widevine.

    Dla każdego protokołu dotyczącego danego elementu zawartości można stosować inne zasady. Na przykład dla protokołu Smooth/DASH można zastosować szyfrowanie PlayReady, zaś dla protokołu HLS — szyfrowanie AES Envelope. Protokoły, które nie są zdefiniowane w zasadach dostarczania (na przykład w przypadku dodania jednych zasad, które określają tylko protokół HLS), nie mogą być używane w przesyłaniu strumieniowym. Wyjątkiem jest przypadek, w którym nie zdefiniowano żadnych zasad dostarczania elementów zawartości. Wówczas są dozwolone wszystkie protokoły.

6. Utwórz lokalizator OnDemand w celu pobrania adresu URL przesyłania strumieniowego.

Na końcu tego artykułu znajduje się pełny przykład dla platformy .NET.

Poniższa ilustracja pokazuje opisany wcześniej przepływ pracy. Jest tu używany token do uwierzytelniania.

![Ochrona za pomocą PlayReady](media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Pozostała część tego artykułu zawiera szczegółowe wyjaśnienia, przykłady kodu i linki do tematów, w których pokazano, jak wykonać poprzednio opisane zadania.

## <a name="current-limitations"></a>Bieżące ograniczenia
W przypadku dodania lub zaktualizowania zasad dostarczania elementów zawartości należy usunąć skojarzony lokalizator (jeśli istnieje) i utworzyć nowy.

Używanie wielu kluczy zawartości nie jest obecnie obsługiwane w przypadku szyfrowania przy użyciu usługi Widevine w usłudze Media Services. 

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>Utworzenie elementu zawartości i przekazanie do niego plików
W celu kodowania i przesyłania strumieniowego filmów oraz zarządzania nimi należy najpierw przekazać zawartość do usługi Media Services. Po przekazaniu zawartości jest ona bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego.

Aby uzyskać więcej informacji, zobacz [Przekazywanie plików na konto usługi Media Services](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a>Przekodowywanie elementu zawartości zawierającego plik na zestaw MP4 o adaptacyjnej szybkości bitowej
W przypadku szyfrowania dynamicznego należy utworzyć element zawartości zawierający zestaw plików MP4 o różnych szybkościach transmisji bitów lub pliki źródłowe Smooth Streaming o różnych szybkościach transmisji bitów. Następnie na podstawie formatu określonego w manifeście i żądaniu fragmentu serwer przesyłania strumieniowego na żądanie zapewnia, że strumień jest dostarczany za pomocą wybranego protokołu. W efekcie przechowujesz i opłacasz pliki tylko w jednym formacie magazynu. Usługa Media Services tworzy i udostępnia właściwą odpowiedź na podstawie żądań klienta. Aby uzyskać więcej informacji, zobacz [Omówienie dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

Aby uzyskać instrukcje na temat kodowania, zobacz [Kodowanie elementów zawartości przy użyciu standardowego kodera multimediów](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Utwórz klucz zawartości i skojarz go z zakodowanym zasobem
W usłudze Media Services klucz zawartości zawiera klucz, za pomocą którego chcesz zaszyfrować element zawartości.

Aby uzyskać więcej informacji, zobacz [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md).

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Konfigurowanie zasad autoryzacji klucza zawartości
Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient (odtwarzacz) musi spełnić te zasady, aby klucz został do niego dostarczony. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń: ograniczenia otwarte lub ograniczenia tokenu.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad autoryzacji klucza zawartości](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Konfigurowanie zasad dostarczania elementu zawartości
Skonfiguruj zasady dostarczania dla swojego elementu zawartości. Niektóre elementy objęte konfiguracją zasad dostarczania elementów zawartości:

* Adres URL pozyskiwania licencji DRM.
* Protokół dostarczania elementów zawartości (na przykład MPEG DASH, HLS, Smooth Streaming lub wszystkie z nich).
* Typ szyfrowania dynamicznego (w tym przypadku jest to Common Encryption).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementu zawartości](media-services-dotnet-configure-asset-delivery-policy.md).

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Tworzenie lokalizatora OnDemand przesyłania strumieniowego w celu pobrania adresu URL przesyłania strumieniowego
W przypadku metod Smooth Streaming, DASH lub HLS należy podać użytkownikowi adres URL przesyłania strumieniowego.

> [!NOTE]
> W przypadku dodania lub zaktualizowania zasad dostarczania elementów zawartości należy usunąć skojarzony lokalizator (jeśli istnieje) i utworzyć nowy.
>
>

Aby uzyskać instrukcje dotyczące sposobu publikowania elementów zawartości i tworzenia adresu URL przesyłania strumieniowego, zobacz artykuł [Build a streaming URL](media-services-deliver-streaming-content.md) (Tworzenie adresu URL przesyłania strumieniowego).

## <a name="get-a-test-token"></a>Pobieranie tokenu testowego
Pobierz token testowy, uwzględniając ograniczenia tokenu wybrane w zasadach autoryzacji klucza.

```csharp
// Deserializes a string containing an XML representation of a TokenRestrictionTemplate
// back into a TokenRestrictionTemplate class instance.
TokenRestrictionTemplate tokenTemplate =
TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

// Generate a test token based on the data in the given TokenRestrictionTemplate.
//The GenerateTestToken method returns the token without the word "Bearer" in front,
//so you have to add it in front of the token string.
string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Możesz przetestować strumień za pomocą usługi [Azure Media Services Player](https://aka.ms/azuremediaplayer).

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko deweloperskie i wypełnij plik app.config za pomocą informacji o połączeniu, zgodnie z opisem w temacie [Media Services Development z platformą .NET](media-services-dotnet-how-to-use.md).

2. Dodaj następujące elementy do węzła **appSettings** zdefiniowanego w pliku app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Przykład

W poniższym przykładzie pokazano funkcje wprowadzone w zestawie Media Services SDK dla platformy .NET w wersji 3.5.2. (W szczególności obejmuje on możliwość definiowania szablonu licencji Widevine i żądania licencji Widevine z usługi Media Services).

Zastąp kod w pliku Program.cs kodem przedstawionym w tej sekcji.

>[!NOTE]
>Limit różnych zasad usługi Media Services wynosi 1 milion (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Jeśli zawsze używasz tych samych dni i uprawnień dostępu, używaj tego samego identyfikatora zasad. Przykładem są zasady dla lokalizatorów przeznaczone do długotrwałego stosowania (nieprzekazywane zasady). 

Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami i powiązanymi jednostkami za pomocą zestawu Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Upewnij się, że zaktualizowano zmienne, tak aby wskazywały foldery, w których znajdują się pliki danych wejściowych.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DynamicEncryptionWithDRM
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an XML representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note that you need to pass the key ID GUID because 
                // TokenClaim.ContentKeyIdentifierClaim was specified during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            // You can use the https://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
            // Note that DASH works on Internet Explorer 11 (via PlayReady), Microsoft Edge (via PlayReady), and Chrome (via Widevine).

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                        inputAsset.Name,
                        encodingPreset));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }


        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
        {

            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryption);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with open restrictions
            // and create an authorization policy.         

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
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

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
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

            // Associate the content key authorization policy with the content key.
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
            // The following code configures the PlayReady license template by using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
            //It contains a field for a custom data string between the license server and the application
            //(which might be useful for custom app logic) as well as a list of one or more license templates.
            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template you can use to create PlayReady licenses
            // to be returned to end users.
            //It contains the data on the content key in the license and any rights or restrictions to be
            //enforced by the PlayReady DRM runtime when you use the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
            //Configure whether the license is persistent (saved in persistent storage on the client)
            //or nonpersistent (held in memory only while the player uses the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150. If false (the default), the MinimumSecurityLevel property of the license is set to 2,000.
            licenseTemplate.AllowTestDevices = true;

            // You also can configure the PlayRight in the PlayReady license by using the PlayReadyPlayRight class.
            // It grants the user the ability to play back the content subject to the zero or more restrictions
            // configured in the license and on the PlayRight itself (for playback-specific policy).
            // Much of the policy on the PlayRight has to do with output restrictions,
            // which control the types of outputs that the content can be played over and
            // any restrictions that must be put in place when you use a given output.
            // For example, if DigitalVideoOnlyContentRestriction is enabled,
            // the DRM runtime allows the video to be displayed only over digital outputs
            //(analog video outputs aren't allowed to pass the content).

            //IMPORTANT: These types of restrictions can be very powerful but also can affect the consumer experience.
            // If output protections are too restrictive, 
            // content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

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
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            // Get the PlayReady license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

            // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
            // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
            // WidevineBaseLicenseAcquisitionUrl (used in the following) also tells dynamic encryption
            // to append /? KID =< keyId > to the end of the URL when you create the manifest.
            // As a result, the Widevine license acquisition URL has the KID appended twice,
            // so you need to remove the KID in the URL when you call GetKeyDeliveryUrl.

            Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
            UriBuilder uriBuilder = new UriBuilder(widevineUrl);
            uriBuilder.Query = String.Empty;
            widevineUrl = uriBuilder.Uri;

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

            };

            // In this case, we specify only the DASH streaming protocol in the delivery policy.
            // All other protocols are blocked from streaming.
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


            // Add AssetDelivery Policy to the asset.
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }

        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the 
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day read-only access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
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

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też

* [Używanie szyfrowania CENC z technologią multi-DRM i kontrolą dostępu](media-services-cenc-with-multidrm-access-control.md)
* [Konfigurowanie tworzenia pakietów Widevine przy użyciu usługi Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)
* [Rozpoczynanie korzystania z zestawu SDK klienta Java dla usług Azure Media Services](./media-services-java-how-to-use.md)
* Aby pobrać najnowszy zestaw SDK języka PHP dla usługi Media Services, poszukaj wersji 0.5.7 pakietu Microsoft/WindowsAzure w [repozytorium Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7). 
