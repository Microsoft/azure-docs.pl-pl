---
title: Korzystanie z szyfrowania dynamicznego AES-128 i usługi dostarczania kluczy | Microsoft Docs
description: W tym temacie przedstawiono sposób dynamicznego szyfrowania przy użyciu algorytmu AES-128 i używania usługi Key Delivery.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: a0e2f785bb3c097b7d2918198457a3f7baf6a39c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96023452"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Używanie dynamicznego szyfrowania AES-128 i usługi dostarczania kluczy

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

Za pomocą Media Services można dostarczyć HTTP Live Streaming (HLS) i Smooth Streaming zaszyfrowanych za pomocą algorytmu AES przy użyciu kluczy szyfrowania 128-bitowych. Media Services zapewnia również usługę dostarczania kluczy, która dostarcza klucze szyfrowania do autoryzowanych użytkowników. Jeśli chcesz, aby Media Services szyfrowanie zasobu, skojarz klucz szyfrowania z zasobem, a także Skonfiguruj zasady autoryzacji dla klucza. Gdy gracz prosi o przesłanie strumienia, Media Services używa określonego klucza do dynamicznego szyfrowania zawartości przy użyciu szyfrowania AES. Aby odszyfrować strumień, odtwarzacz żąda klucza z usługi dostarczania kluczy. Aby określić, czy użytkownik ma uprawnienia do uzyskiwania klucza, usługa szacuje zasady autoryzacji określone dla klucza.

Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń: ograniczenia otwarte lub ograniczenia tokenu. Zasadom ograniczenia tokenu musi towarzyszyć token wystawiony przez usługę tokenu zabezpieczającego (STS). Usługa Media Services obsługuje następujące formaty tokenów: [prosty token sieci Web](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) i [token sieci Web JSON](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) (JWT). Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad autoryzacji klucza zawartości](media-services-protect-with-aes128.md#configure_key_auth_policy).

Aby móc skorzystać z szyfrowania dynamicznego, należy posiadać element zawartości zawierający zestaw plików MP4 o różnych szybkościach transmisji bitów lub pliki źródłowe Smooth Streaming o różnych szybkościach transmisji bitów. Należy również skonfigurować zasady dostarczania dla zasobu (opisane w dalszej części tego artykułu). Następnie na podstawie formatu określonego w adresie URL przesyłanego strumienia serwer przesyłania strumieniowego na żądanie zapewnia, że strumień jest dostarczany za pomocą wybranego protokołu. W związku z tym należy przechowywać i płacisz tylko za pliki w jednym formacie magazynu. Usługa Media Services tworzy i udostępnia właściwą odpowiedź na podstawie żądań klienta.

Ten artykuł jest przydatny dla deweloperów pracujących nad aplikacjami dostarczającymi chronione multimedia. W tym artykule opisano sposób konfigurowania usługi dostarczania kluczy przy użyciu zasad autoryzacji, tak aby tylko autoryzowani klienci mogli odbierać klucze szyfrowania. Pokazano również, jak używać szyfrowania dynamicznego.

Aby uzyskać informacje na temat sposobu szyfrowania zawartości przy użyciu Advanced Encryption Standard (AES) na potrzeby dostarczania do przeglądarki Safari w witrynie macOS, zobacz [ten wpis w blogu](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
Aby uzyskać informacje na temat sposobu ochrony zawartości multimedialnej przy użyciu szyfrowania AES, zobacz [ten klip wideo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Dynamiczny szyfrowanie AES-128 i przepływ pracy usługi dostarczania kluczy

Podczas szyfrowania zasobów za pomocą algorytmu AES należy wykonać następujące czynności ogólne przy użyciu usługi dostarczania kluczy Media Services, a także przy użyciu szyfrowania dynamicznego:

1. [Utwórz element zawartości i przekaż pliki do elementu zawartości](media-services-protect-with-aes128.md#create_asset).

2. [Zakoduj element zawartości zawierający plik na zestaw plików MP4 z adaptacyjną szybkością transmisji bitów](media-services-protect-with-aes128.md#encode_asset).

3. [Utwórz klucz zawartości i skojarz go z zakodowanym elementem](media-services-protect-with-aes128.md#create_contentkey)zawartości. W usłudze Media Services klucz zawartości zawiera klucz szyfrowania elementu zawartości.

4. [Skonfiguruj zasady autoryzacji klucza zawartości](media-services-protect-with-aes128.md#configure_key_auth_policy). Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient musi spełnić te zasady, aby klucz zawartości został do niego dostarczony.

5. [Skonfiguruj zasady dostarczania dla elementu zawartości](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfiguracja zasad dostarczania obejmuje adres URL pozyskiwania klucza i wektor inicjujący (IV). (Algorytm AES-128 wymaga tego samego elementu IV do szyfrowania i odszyfrowywania). Konfiguracja obejmuje również protokół dostarczania (na przykład MPEG-KRESKa, HLS, Smooth Streaming lub wszystkie) oraz typ szyfrowania dynamicznego (na przykład koperta lub bez szyfrowania dynamicznego).

    Dla każdego protokołu dotyczącego danego elementu zawartości można stosować inne zasady. Na przykład dla protokołu Smooth/DASH można zastosować szyfrowanie PlayReady, zaś dla protokołu HLS — szyfrowanie AES Envelope. Wszystkie protokoły, które nie są zdefiniowane w zasadach dostarczania, są blokowane przed przesyłaniem strumieniowym. (Przykładem jest dodanie jednej zasady, która określa tylko HLS jako protokół). Wyjątek polega na tym, że w ogóle nie zdefiniowano żadnych zasad dostarczania elementów zawartości. Wówczas są dozwolone wszystkie protokoły.

6. [Utwórz lokalizator OnDemand](media-services-protect-with-aes128.md#create_locator) w celu pobrania adresu URL przesyłania strumieniowego.

W tym artykule pokazano również, [jak aplikacja kliencka może zażądać klucza z usługi dostarczania kluczy](media-services-protect-with-aes128.md#client_request).

Kompletny [przykład platformy .NET](media-services-protect-with-aes128.md#example) można znaleźć na końcu artykułu.

Poniższa ilustracja pokazuje opisany wcześniej przepływ pracy. Jest tu używany token do uwierzytelniania.

![Ochrona za pomocą algorytmu AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Pozostała część tego artykułu zawiera wyjaśnienia, przykłady kodu i linki do tematów, w których pokazano, jak wykonać zadania opisane wcześniej.

## <a name="current-limitations"></a>Bieżące ograniczenia
W przypadku dodania lub zaktualizowania zasad dostarczania elementów zawartości należy usunąć skojarzony lokalizator (jeśli istnieje) i utworzyć nowy.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Tworzenie zasobu i przekazywanie plików do elementu zawartości
W celu kodowania i przesyłania strumieniowego filmów oraz zarządzania nimi należy najpierw przekazać zawartość do usługi Media Services. Po przekazaniu zawartości jest ona bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego. 

Aby uzyskać więcej informacji, zobacz [Przekazywanie plików na konto usługi Media Services](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>Przekodowywanie elementu zawartości zawierającego plik na zestaw MP4 o adaptacyjnej szybkości bitowej
W przypadku szyfrowania dynamicznego należy utworzyć element zawartości zawierający zestaw plików MP4 o różnych szybkościach transmisji bitów lub pliki źródłowe Smooth Streaming o różnych szybkościach transmisji bitów. Następnie na podstawie określonego formatu w żądaniu manifestu lub fragmentu serwer przesyłania strumieniowego na żądanie zapewnia otrzymanie strumienia w wybranym protokole. Następnie wystarczy przechowywać i płacisz tylko za pliki w jednym formacie magazynu. Usługa Media Services tworzy i udostępnia właściwą odpowiedź na podstawie żądań klienta. Aby uzyskać więcej informacji, zobacz [Omówienie dynamicznego tworzenia pakietów](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>Po utworzeniu konta usług Media Services do Twojego konta dodawany jest domyślny punkt końcowy przesyłania strumieniowego w stanie „Zatrzymany”. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan „Uruchomiony”. 
>
>Ponadto, aby korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, element zawartości musi zawierać zestaw z adaptacyjną szybkością transmisji bitów pliki MP4 lub z adaptacyjną szybkością transmisji bitów Smooth Streaming.

Aby uzyskać instrukcje na temat kodowania, zobacz [Kodowanie elementów zawartości przy użyciu standardowego kodera multimediów](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Utwórz klucz zawartości i skojarz go z zakodowanym zasobem
W usłudze Media Services klucz zawartości zawiera klucz, za pomocą którego chcesz zaszyfrować element zawartości.

Aby uzyskać więcej informacji, zobacz [Tworzenie klucza zawartości](media-services-dotnet-create-contentkey.md).

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Konfigurowanie zasad autoryzacji klucza zawartości
Usługa Media Services obsługuje wiele sposobów uwierzytelniania użytkowników, którzy tworzą żądania klucza. Musisz skonfigurować zasady autoryzacji klucza zawartości. Klient (odtwarzacz) musi spełnić zasady, aby można było dostarczyć klucz do klienta programu. Zasady autoryzacji klucza zawartości mogą mieć jedno lub więcej ograniczeń autoryzacji, otwarte, ograniczenie tokenu lub ograniczenie adresów IP.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad autoryzacji klucza zawartości](media-services-dotnet-configure-content-key-auth-policy.md).

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Konfigurowanie zasad dostarczania elementu zawartości
Skonfiguruj zasady dostarczania dla swojego elementu zawartości. Niektóre elementy objęte konfiguracją zasad dostarczania elementów zawartości:

* Adres URL pozyskiwania klucza. 
* Wektor inicjalizacji (IV) do użycia na potrzeby szyfrowania koperty. Algorytm AES-128 wymaga tego samego elementu IV do szyfrowania i odszyfrowywania. 
* Protokół dostarczania elementów zawartości (na przykład MPEG DASH, HLS, Smooth Streaming lub wszystkie z nich).
* Typ szyfrowania dynamicznego (na przykład koperta AES) lub bez szyfrowania dynamicznego. 

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
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Możesz przetestować strumień za pomocą usługi [Azure Media Services Player](https://aka.ms/azuremediaplayer).

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Jak klient może zażądać klucza od usługi dostarczania kluczy?
W poprzednim kroku został skonstruowany adres URL wskazujący na plik manifestu. Klient musi wyodrębnić niezbędne informacje z plików manifestu przesyłania strumieniowego, aby wysłać żądanie do usługi dostarczania kluczy.

### <a name="manifest-files"></a>Pliki manifestu
Klient musi wyodrębnić adres URL (zawierający także wartość identyfikatora klucza zawartości [dzieciak]) z pliku manifestu. Klient próbuje pobrać klucz szyfrowania z usługi dostarczania kluczy. Klient musi również wyodrębnić wartość IV i użyć jej do odszyfrowania strumienia. Poniższy fragment kodu przedstawia `<Protection>` element manifestu Smooth Streaming:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

W przypadku HLS Manifest główny jest podzielony na pliki segmentów. 

Na przykład, głównym manifestem jest: http: \/ /test001.Origin.MediaServices.Windows.NET/8bfe7d6f-34e3-4d1a-B289-3e48a8762490/BigBuckBunny.ISM/manifest (format = M3U8-AAPL). Zawiera listę nazw plików segmentów.

```text
. . . 
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
…
```

Jeśli otworzysz jeden z plików segmentu w edytorze tekstu (na przykład http: \/ /test001.Origin.MediaServices.Windows.NET/8bfe7d6f-34e3-4d1a-B289-3e48a8762490/BigBuckBunny.ISM/QualityLevels (514369)/manifest (wideo, format = M3U8-AAPL), zawiera on #EXT-X-Key, co oznacza, że plik jest szyfrowany.

```text
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:9
#EXT-X-KEY:METHOD=AES-128,
URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
        kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
        IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:8.425708,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXT-X-ENDLIST
```

>[!NOTE] 
>Jeśli planujesz odtwarzanie HLS szyfrowanych algorytmem AES w przeglądarce Safari, zobacz [ten blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Zażądaj klucza z usługi dostarczania kluczy

Poniższy kod przedstawia sposób wysyłania żądania do usługi dostarczania kluczy Media Services przy użyciu identyfikatora URI dostarczania klucza (wyodrębnionego z manifestu) i tokenu. (W tym artykule nie wyjaśniono, jak uzyskać SWTs z usługi STS).

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Ochrona zawartości za pomocą algorytmu AES-128 przy użyciu platformy .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Tworzenie i konfigurowanie projektu programu Visual Studio

1. Skonfiguruj środowisko deweloperskie i wypełnij plik app.config za pomocą informacji o połączeniu, zgodnie z opisem w temacie [Media Services Development z platformą .NET](media-services-dotnet-how-to-use.md).

2. Dodaj następujące elementy do appSettings, zgodnie z definicją w pliku app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Przykład

Zastąp kod w pliku Program.cs kodem przedstawionym w tej sekcji.
 
>[!NOTE]
>Istnieje limit 1 000 000 zasad dla różnych zasad Media Services (na przykład w przypadku zasad lokalizatora lub ContentKeyAuthorizationPolicy). Użyj tego samego identyfikatora zasad, jeśli zawsze używasz tych samych dni/uprawnień dostępu. Przykładem są zasady dla lokalizatorów przeznaczone do długotrwałego stosowania (nieprzekazywane zasady). Aby uzyskać więcej informacji, zobacz sekcję "ograniczanie dostępu do zasad" w temacie [Zarządzanie zasobami i powiązanymi jednostkami przy użyciu zestawu SDK platformy .net Media Services](media-services-dotnet-manage-entities.md#limit-access-policies).

Upewnij się, że zaktualizowano zmienne, tak aby wskazywały foldery, w których znajdują się pliki danych wejściowych.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
