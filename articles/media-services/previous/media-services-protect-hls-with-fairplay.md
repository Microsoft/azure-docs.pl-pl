---
title: Ochrona zawartości HLS za pomocą oprogramowania Microsoft PlayReady lub Apple FairPlay-Azure | Microsoft Docs
description: Ten temat zawiera omówienie i pokazuje, jak używać Azure Media Services do dynamicznego szyfrowania zawartości HTTP Live Streaming (HLS) za pomocą Apple FairPlay. Pokazano w nim także, jak używać usługi dostarczania licencji Media Services do dostarczania licencji FairPlay klientom.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 870b567d5917c405a3eef70a27b1a19c021d7a80
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055148"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Ochrona zawartości HLS za pomocą oprogramowania Apple FairPlay lub Microsoft PlayReady

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).   > żadne nowe funkcje lub funkcje nie są dodawane do Media Services V2. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)
>

Azure Media Services umożliwia dynamiczne szyfrowanie zawartości HTTP Live Streaming (HLS) przy użyciu następujących formatów:  

* **Przezroczysty klucz koperty AES-128**

    Cały fragment jest szyfrowany przy użyciu trybu **CBC AES-128** . Odszyfrowywanie strumienia jest obsługiwane przez program iOS i system OS X Player natywnie. Aby uzyskać więcej informacji, zobacz [Korzystanie z szyfrowania dynamicznego AES-128 i usługi dostarczania kluczy](media-services-playready-license-template-overview.md).
* **Apple FairPlay**

    Poszczególne próbki wideo i audio są szyfrowane przy użyciu trybu **CBC AES-128** . **FairPlay Streaming** (FPS) jest zintegrowany z systemami operacyjnymi urządzeń z natywną obsługą systemu iOS i Apple TV. Przeglądarka Safari w systemie OS X umożliwia korzystanie z funkcji FPS przy użyciu interfejsu EME (Encrypted Media Extensions).
* **Microsoft PlayReady**

Na poniższej ilustracji przedstawiono przepływ pracy **szyfrowania dynamicznego HLS + FairPlay lub PlayReady** .

![Diagram dynamicznego przepływu pracy szyfrowania](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

W tym artykule pokazano, jak za pomocą Media Services dynamicznie szyfrować zawartość HLS przy użyciu usługi Apple FairPlay. Pokazano w nim także, jak używać usługi dostarczania licencji Media Services do dostarczania licencji FairPlay klientom.

> [!NOTE]
> Jeśli chcesz również zaszyfrować zawartość HLS za pomocą oprogramowania PlayReady, musisz utworzyć wspólny klucz zawartości i skojarzyć go z elementem zawartości. Należy również skonfigurować zasady autoryzacji klucza zawartości, zgodnie z opisem w temacie Korzystanie z [uwierzytelniania powszechnie używanego](media-services-protect-with-playready-widevine.md)w programie PlayReady.
>
>

## <a name="requirements-and-considerations"></a>Wymagania i zagadnienia

W przypadku Media Services korzystania z usługi HLS zaszyfrowanej za pomocą FairPlay i dostarczania licencji FairPlay wymagane są następujące elementy:

  * Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Konto usługi Media Services. Aby go utworzyć, zobacz [Tworzenie konta Azure Media Services przy użyciu Azure Portal](media-services-portal-create-account.md).
  * Zarejestruj się w [programie Apple Development](https://developer.apple.com/).
  * Firma Apple wymaga od właściciela zawartości, aby uzyskać [pakiet wdrożeniowy](https://developer.apple.com/contact/fps/). Stan, w którym został już zaimplementowany moduł zabezpieczeń (KSM) z Media Services i że żądasz końcowego pakietu FPS. W końcowym pakiecie FPS znajdują się instrukcje generowania certyfikatów i uzyskiwania klucza tajnego aplikacji. Używasz poproszenia o skonfigurowanie FairPlay.
  * Azure Media Services .NET SDK w wersji **3.6.0** lub nowszej.

Następujące elementy muszą zostać ustawione w Media Services stronie dostarczania klucza:

  * **Certyfikat aplikacji (AC)**: jest to plik PFX, który zawiera klucz prywatny. Ten plik jest tworzony i szyfrowany przy użyciu hasła.

       Konfigurując zasady dostarczania kluczy, należy podać to hasło i plik PFX w formacie base64.

      W poniższych krokach opisano sposób generowania pliku certyfikatu PFX dla FairPlay:

    1. Zainstaluj OpenSSL z https://slproweb.com/products/Win32OpenSSL.html .

        Przejdź do folderu, w którym znajduje się certyfikat FairPlay i inne pliki dostarczone przez firmę Apple.
    2. W wierszu polecenia uruchom następujące polecenie. Spowoduje to przekonwertowanie pliku CER na plik PEM.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 — informowanie algorytmu der-in FairPlay. cer-out FairPlay-out. pem
    3. W wierszu polecenia uruchom następujące polecenie. Spowoduje to przekonwertowanie pliku PEM na plik PFX z kluczem prywatnym. Hasło do pliku PFX jest następnie zadawane przez OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12 — wyeksportuj FairPlay-out. pfx-INKEY PrivateKey. pem-in FairPlay-out. pem — passer file:privatekey-pem-pass.txt
  * **Hasło certyfikatu aplikacji**: hasło do tworzenia pliku PFX.
  * **Identyfikator hasła certyfikatu aplikacji**: należy przekazać hasło, podobnie jak w przypadku przekazywania innych kluczy Media Services. Użyj wartości wyliczenia **ContentKeyType. FairPlayPfxPassword** , aby uzyskać identyfikator Media Services. Jest to potrzebne do użycia w opcji zasady dostarczania kluczy.
  * **IV**: jest to wartość losowa o 16 bajtów. Musi być zgodna z IV w ramach zasad dostarczania elementów zawartości. Wygenerujesz IV i umieścisz ją w obydwu miejscach: zasady dostarczania zasobów i opcja zasady dostarczania kluczy.
  * **Poproszenie**: ten klucz jest odbierany podczas generowania certyfikacji przy użyciu portalu dla deweloperów firmy Apple. Każdy zespół programistyczny otrzymuje unikatowe żądanie. Zapisz kopię pytania i przechowuj ją w bezpiecznym miejscu. Aby Media Services później, należy skonfigurować zapytanie jako FairPlayAsk.
  * **Identyfikator pytania**: ten identyfikator jest uzyskiwany podczas przekazywania do Media Services. Należy przekazać zapytanie przy użyciu wartości wyliczenia **ContentKeyType. FairPlayAsk** . W związku z tym zwracany jest identyfikator Media Services i jest to, co powinno być używane podczas ustawiania opcji zasady dostarczania kluczy.

Po stronie klienta FPS muszą być ustawione następujące elementy:

  * **Certyfikat aplikacji (AC)**: plik CER/. der zawierający klucz publiczny, który jest używany przez system operacyjny do szyfrowania niektórych ładunków. Media Services musi wiedzieć o tym, ponieważ jest wymagany przez odtwarzacz. Usługa Key Delivery odszyfrowuje ją przy użyciu odpowiedniego klucza prywatnego.

Aby odtworzyć zaszyfrowany strumień FairPlay, najpierw zapoznaj się z rzeczywistym zapytaniami, a następnie Wygeneruj prawdziwy certyfikat. Ten proces tworzy wszystkie trzy części:

  * plik. der
  * plik PFX
  * hasło do pliku PFX

Następujący klienci obsługują HLS z szyfrowaniem **AES-128 CBC** : Safari w systemie OS X, Apple TV i iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurowanie szyfrowania dynamicznego FairPlay i usług dostarczania licencji
Poniżej przedstawiono ogólne kroki ochrony zasobów w programie FairPlay przy użyciu usługi dostarczania licencji Media Services, a także przy użyciu szyfrowania dynamicznego.

1. Utwórz element zawartości i przekaż do niego pliki.
2. Przekoduj element zawartości zawierający plik na zestaw MP4 o adaptacyjnej szybkości bitowej.
3. Utwórz klucz zawartości i skojarz go z zakodowanym elementem zawartości.  
4. Skonfiguruj zasady autoryzacji klucza zawartości. Określ następujące ustawienia:

   * Metoda dostarczania (w tym przypadku FairPlay).
   * Konfiguracja opcji zasad FairPlay. Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania FairPlay, zobacz metodę **ConfigureFairPlayPolicyOptions ()** w poniższym przykładzie.

     > [!NOTE]
     > Na ogół można skonfigurować opcje zasad FairPlay tylko raz, ponieważ tylko jeden zestaw certyfikacji i pytania.
     >
     >
   * Ograniczenia (otwarte lub tokenu).
   * Informacje specyficzne dla typu dostarczania klucza, które definiują sposób dostarczania klucza do klienta.
5. Skonfiguruj zasady dostarczania elementów zawartości. Konfiguracja zasad dostarczania obejmuje:

   * Protokół dostarczania (HLS).
   * Typ szyfrowania dynamicznego (typowe szyfrowanie CBC).
   * Adres URL pozyskiwania licencji.

     > [!NOTE]
     > Jeśli chcesz dostarczyć strumień zaszyfrowany za pomocą FairPlay i innego systemu Digital Rights Management (DRM), musisz skonfigurować oddzielne zasady dostarczania:
     >
     > * Jeden IAssetDeliveryPolicy do konfigurowania dynamicznego adaptacyjnego przesyłania strumieniowego za pośrednictwem protokołu HTTP (PAUZy) z Common Encryption (CENC) (PlayReady + Widevine) i bezproblemowe korzystanie z oprogramowania PlayReady
     > * Inna IAssetDeliveryPolicy do skonfigurowania FairPlay dla HLS
     >
     >
6. Utwórz lokalizator OnDemand w celu pobrania adresu URL przesyłania strumieniowego.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Używanie dostarczania kluczy FairPlay przez aplikacje odtwarzacza
Aplikacje odtwarzacza można opracowywać przy użyciu zestawu iOS SDK. Aby można było odtwarzać zawartość FairPlay, należy zaimplementować protokół wymiany licencji. Ten protokół nie jest określony przez firmę Apple. Jest to każda aplikacja, w której można wysyłać żądania dostarczania kluczy. Usługa dostarczania kluczy Media Services FairPlay oczekuje, że SPC ma być komunikatem post w formacie www-form-URL, w następującej postaci:

`spc=<Base64 encoded SPC>`

> [!NOTE]
> Azure Media Player obsługuje odtwarzanie FairPlay. Więcej informacji można znaleźć w [dokumentacji Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) .
>
>

## <a name="streaming-urls"></a>Adresy URL przesyłania strumieniowego
Jeśli zasób został zaszyfrowany za pomocą więcej niż jednego obiektu DRM, należy użyć znacznika szyfrowania w adresie URL przesyłania strumieniowego: (format = 3u8-AAPL ', szyfrowanie = ' xxx ').

Obowiązują następujące zastrzeżenia:

* Można określić tylko zero lub jeden typ szyfrowania.
* Nie trzeba określać typu szyfrowania w adresie URL, jeśli do zasobu zastosowano tylko jedno szyfrowanie.
* W przypadku typu szyfrowania wielkość liter nie jest rozróżniana.
* Można określić następujące typy szyfrowania:  
  * **Cenc**: typowe szyfrowanie (PlayReady lub Widevine)
  * **cbcs-AAPL**: FairPlay
  * **CBC**: szyfrowanie na kopercie AES

## <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko projektowe i wypełnij plik app.config przy użyciu informacji dotyczących połączenia, zgodnie z opisem w sekcji [Projektowanie usługi Media Services na platformie .NET](media-services-dotnet-how-to-use.md). 
2. Dodaj następujące elementy do węzła **appSettings** zdefiniowanego w pliku app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Przykład

Poniższy przykład demonstruje możliwość używania Media Services do dostarczania zawartości zaszyfrowanej za pomocą FairPlay. Ta funkcja została wprowadzona w zestawie Azure Media Services SDK dla platformy .NET w wersji 3.6.0. 

Zastąp kod w pliku Program.cs kodem przedstawionym w tej sekcji.

>[!NOTE]
>Limit różnych zasad usługi AMS wynosi 1 000 000 (na przykład zasad lokalizatorów lub ContentKeyAuthorizationPolicy). Należy używać tego samego identyfikatora zasad, jeśli zawsze są używane uprawnienia dotyczące tych samych dni lub tego samego dostępu, na przykład dla lokalizatorów przeznaczonych do długotrwałego stosowania (nieprzekazywanych zasad). Aby uzyskać więcej informacji, zobacz [ten](media-services-dotnet-manage-entities.md#limit-access-policies) artykuł.

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
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
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

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

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

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

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

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
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

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
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

## <a name="next-steps-media-services-learning-paths"></a>Następne kroki: ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
