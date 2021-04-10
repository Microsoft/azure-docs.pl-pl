---
title: Szyfrowanie zawartości przy użyciu szyfrowania magazynu za pomocą interfejsu API REST usługi AMS
description: Dowiedz się, jak szyfrować zawartość za pomocą szyfrowania magazynu za pomocą interfejsów API REST usługi AMS.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 885390d9246937247107128114e9242aa5e2dc01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015475"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Szyfrowanie zawartości przy użyciu szyfrowania magazynu

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).   > żadne nowe funkcje lub funkcje nie są dodawane do Media Services V2. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)
>

Ten artykuł zawiera omówienie szyfrowania magazynu usługi AMS i pokazuje, jak przekazać zaszyfrowaną zawartość magazynu:

* Utwórz klucz zawartości.
* Utwórz element zawartości. Ustaw wartość AssetCreationOption na StorageEncryption podczas tworzenia elementu zawartości.
  
     Zaszyfrowane zasoby są skojarzone z kluczami zawartości.
* Połącz klucz zawartości z zasobem.  
* Ustaw parametry związane z szyfrowaniem w jednostkach AssetFile.

## <a name="considerations"></a>Zagadnienia do rozważenia 

Jeśli chcesz dostarczyć zasób zaszyfrowanego magazynu, musisz skonfigurować zasady dostarczania zasobów. Zanim będzie można przesłać strumieniowo zasób, serwer przesyłania strumieniowego usunie szyfrowanie magazynu i strumieniuje zawartość przy użyciu określonych zasad dostarczania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostarczania elementów zawartości](media-services-rest-configure-asset-delivery-policy.md).

Podczas uzyskiwania dostępu do jednostek w Media Services należy ustawić określone pola nagłówka i wartości w żądaniach HTTP. Aby uzyskać więcej informacji, zobacz [konfigurowanie Media Services tworzenia interfejsu API REST](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Szyfrowanie po stronie magazynu

|Opcja szyfrowania|Opis|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services szyfrowanie magazynu|Szyfrowanie AES-256, klucz zarządzany przez Media Services|Obsługiwane<sup>(1)</sup>|Nieobsługiwane<sup>(2)</sup>|
|[szyfrowanie usługi Storage danych magazynowanych](../../storage/common/storage-service-encryption.md)|Szyfrowanie po stronie serwera oferowane przez usługę Azure Storage, klucz zarządzany przez platformę Azure lub przez klienta|Obsługiwane|Obsługiwane|
|[Szyfrowanie Client-Side magazynu](../../storage/common/storage-client-side-encryption.md)|Szyfrowanie po stronie klienta oferowane przez usługę Azure Storage, klucz zarządzany przez klienta w Key Vault|Nieobsługiwane|Nieobsługiwane|

<sup>1</sup> usługa Media Services obsługuje obsługę zawartości w trybie Wyczyść/bez żadnej formy szyfrowania, dlatego nie jest to zalecane.

<sup>2</sup> w Media Services v3, szyfrowanie magazynu (szyfrowanie AES-256) jest obsługiwane tylko w przypadku zgodności z poprzednimi wersjami, gdy zasoby zostały utworzone przy użyciu Media Services V2. Oznacza to, że wersja v3 współpracuje z istniejącymi zasobami zaszyfrowanymi magazynu, ale nie umożliwia tworzenia nowych.

## <a name="connect-to-media-services"></a>Łączenie się z usługą Media Services

Aby uzyskać informacje na temat nawiązywania połączenia z interfejsem API usługi AMS, zobacz [dostęp do interfejsu api Azure Media Services przy użyciu uwierzytelniania w usłudze Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Szyfrowanie magazynu — Omówienie
Szyfrowanie magazynu AMS stosuje szyfrowanie trybu **AES-Rob** do całego pliku.  Tryb AES-Rob jest szyfrem blokowym, który może szyfrować dowolne dane długości bez potrzeby uzupełniania. Działa przez zaszyfrowanie bloku licznika algorytmem AES, a następnie XOR — przeprowadzenie danych wyjściowych algorytmu AES z danymi do szyfrowania lub odszyfrowywania.  Używany blok licznika jest konstruowany przez skopiowanie wartości InitializationVector do bajtów od 0 do 7 wartości licznika i bajtów 8 do 15 wartości licznika wynosi zero. W bloku licznika 16-bajtowym bajty od 8 do 15 (czyli najmniej znaczące bajty) są używane jako proste 64-bitowe liczby całkowite bez znaku, które są zwiększane o jeden dla każdego kolejnego bloku przetwarzanych danych i są przechowywane w kolejności bajtów sieciowych. Jeśli ta liczba całkowita osiągnie wartość maksymalną (0xFFFFFFFFFFFFFFFF), a następnie zwiększa szybkość, resetuje licznik bloku do wartości zero (bajty od 8 do 15) bez wpływu na pozostałe 64 bitów licznika (czyli bajty od 0 do 7).   Aby zachować bezpieczeństwo szyfrowania w trybie AES-Rob, wartość InitializationVector dla danego identyfikatora klucza dla każdego klucza zawartości jest unikatowa dla każdego pliku i plików, które mają mniej niż 2 ^ 64 bloków.  Ta unikatowa wartość polega na zapewnieniu, że wartość licznika nigdy nie jest ponownie używana z danym kluczem. Aby uzyskać więcej informacji na temat trybu Rob, zobacz [Tę stronę typu wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (w artykule wiki jest stosowany termin "Identyfikator jednorazowy" zamiast "InitializationVector").

Użyj **szyfrowania magazynu** , aby lokalnie zaszyfrować oczyszczoną zawartość przy użyciu szyfrowania AES-256 bitowego, a następnie przekazać ją do usługi Azure Storage, gdzie jest przechowywana w stanie zaszyfrowanej. Zasoby chronione za pomocą szyfrowania magazynu są automatycznie odszyfrowane i umieszczane w zaszyfrowanym systemie plików przed kodowaniem i opcjonalnie ponownie szyfrowane przed przekazaniem ich jako nowego wyjściowego elementu zawartości. Podstawowym przypadkiem użycia szyfrowania magazynu jest Zabezpieczanie plików multimedialnych o wysokiej jakości z użyciem silnego szyfrowania na dysku.

Aby można było dostarczyć zasób zaszyfrowanego magazynu, należy skonfigurować zasady dostarczania zasobów, aby Media Services wie, jak chcesz dostarczyć zawartość. Aby można było przesłać strumieniowo zasób, serwer przesyłania strumieniowego usuwa szyfrowanie magazynu i strumieniuje zawartość przy użyciu określonych zasad dostarczania (na przykład AES, Common Encryption lub bez szyfrowania).

## <a name="create-contentkeys-used-for-encryption"></a>Utwórz ContentKeys używany do szyfrowania
Zaszyfrowane zasoby są skojarzone z kluczami szyfrowania magazynu. Utwórz klucz zawartości, który ma być używany do szyfrowania przed utworzeniem plików zasobów. W tej sekcji opisano sposób tworzenia klucza zawartości.

Poniżej znajdują się ogólne czynności związane z generowaniem kluczy zawartości skojarzonych z zasobami, które mają być szyfrowane. 

1. W przypadku szyfrowania magazynu należy losowo wygenerować klucz 32-bajtowy AES. 
   
    Klucz 32-bajtowy AES jest kluczem zawartości dla zasobu, co oznacza, że wszystkie pliki skojarzone z tym zasobem muszą używać tego samego klucza zawartości podczas odszyfrowywania. 
2. Wywołaj metody [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) i [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) w celu uzyskania poprawnego certyfikatu X. 509, który musi być używany do szyfrowania klucza zawartości.
3. Zaszyfruj klucz zawartości przy użyciu klucza publicznego certyfikatu X. 509. 
   
   Media Services .NET SDK używa RSA z OAEP podczas szyfrowania.  W [funkcji EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)można zobaczyć przykład platformy .NET.
4. Utwórz wartość sumy kontrolnej obliczoną przy użyciu identyfikatora klucza i klucza zawartości. Poniższy przykład .NET oblicza sumę kontrolną przy użyciu części identyfikatora klucza i klucza zawartości czystego.

    ```csharp
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
    {
        const int ChecksumLength = 8;
        const int KeyIdLength = 16;

        byte[] encryptedKeyId = null;

        // Checksum is computed by AES-ECB encrypting the KID
        // with the content key.
        using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
        {
            rijndael.Mode = CipherMode.ECB;
            rijndael.Key = contentKey;
            rijndael.Padding = PaddingMode.None;

            ICryptoTransform encryptor = rijndael.CreateEncryptor();
            encryptedKeyId = new byte[KeyIdLength];
            encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
        }

        byte[] retVal = new byte[ChecksumLength];
        Array.Copy(encryptedKeyId, retVal, ChecksumLength);

        return Convert.ToBase64String(retVal);
    }
    ```

5. Utwórz klucz zawartości z **EncryptedContentKey** (przekonwertowanym na ciąg szyfrowany algorytmem Base64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** i wartościami **sum kontrolnych** , które zostały odebrane w poprzednich krokach.

    W przypadku szyfrowania magazynu należy uwzględnić w treści żądania następujące właściwości.

    Właściwość treści żądania    | Opis
    ---|---
    Id | Identyfikator ContentKey jest generowany w następującym formacie: "NB: dzieciak: UUID: \<NEW GUID> ".
    ContentKeyType | Typ klucza zawartości jest liczbą całkowitą, która definiuje klucz. W przypadku formatu szyfrowania magazynu wartość jest równa 1.
    EncryptedContentKey | Tworzymy nową wartość klucza zawartości, która jest 256-bitową (32 bajtów). Klucz jest szyfrowany przy użyciu certyfikatu X. 509 szyfrowania magazynu pobranego z Microsoft Azure Media Services przez wykonanie żądania HTTP GET dla metod GetProtectionKeyId i GetProtectionKey. Na przykład, zobacz następujący kod platformy .NET: Metoda  **EncryptSymmetricKeyData** zdefiniowana [tutaj](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Jest to identyfikator klucza ochrony dla certyfikatu X. 509 szyfrowania magazynu, który został użyty do zaszyfrowania naszego klucza zawartości.
    ProtectionKeyType | Jest to typ szyfrowania klucza ochrony, który został użyty do szyfrowania klucza zawartości. Ta wartość jest StorageEncryption (1) dla naszego przykładu.
    Suma kontrolna |Obliczona suma kontrolna MD5 dla klucza zawartości. Jest on obliczany przez zaszyfrowanie identyfikatora zawartości kluczem zawartości. Przykładowy kod demonstruje, jak obliczyć sumę kontrolną.


### <a name="retrieve-the-protectionkeyid"></a>Pobierz ProtectionKeyId
Poniższy przykład pokazuje, jak pobrać ProtectionKeyId, odcisk palca certyfikatu dla certyfikatu, którego należy użyć podczas szyfrowania klucza zawartości. Wykonaj ten krok, aby upewnić się, że masz już odpowiedni certyfikat na komputerze.

Żądanie:

```console
GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
```

Odpowiedź:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 139
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Pobierz ProtectionKey dla ProtectionKeyId
Poniższy przykład pokazuje, jak pobrać certyfikat X. 509 przy użyciu ProtectionKeyId otrzymanego w poprzednim kroku.

Żądanie:

```console
GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
Host: media.windows.net
```

Odpowiedź:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 1227
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Thu, 05 Feb 2015 07:52:30 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

### <a name="create-the-content-key"></a>Utwórz klucz zawartości
Po pobraniu certyfikatu X. 509 i użyciu jego klucza publicznego do szyfrowania klucza zawartości Utwórz jednostkę **ContentKey** i odpowiednio ustaw jej wartości właściwości.

Jedną z wartości, które należy ustawić podczas tworzenia klucza zawartości jest typ. W przypadku korzystania z szyfrowania magazynu wartość powinna być równa "1". 

Poniższy przykład pokazuje, jak utworzyć **ContentKey** z zestawem **ContentKeyType** dla szyfrowania magazynu ("1") i **ProtectionKeyType** ustawioną na "0", aby wskazać, że identyfikator klucza ochrony jest odciskiem palca certyfikatu X. 509.  

Żądanie

```console
POST https://media.windows.net/api/ContentKeys HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
{
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
"ContentKeyType":"1", 
"ProtectionKeyType":"0",
"EncryptedContentKey":"your encrypted content key",
"Checksum":"calculated checksum"
}
```

Odpowiedź:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 777
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
Server: Microsoft-IIS/8.5
request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:37:46 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
"LastModified":"2015-02-04T02:37:46.9684379Z",
"ContentKeyType":1,
"EncryptedContentKey":"your encrypted content key",
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
"ProtectionKeyType":0,
"Checksum":"calculated checksum"}
```

## <a name="create-an-asset"></a>Tworzenie zasobu
Poniższy przykład pokazuje, jak utworzyć element zawartości.

**Żądanie HTTP**

```console
POST https://media.windows.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"Name":"BigBuckBunny" "Options":1}
```

**Odpowiedź HTTP**

W przypadku powodzenia następuje zwrócenie następującej odpowiedzi:

```console
HTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 452
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
request-id: e98be122-ae09-473a-8072-0ccd234a0657
x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT
{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny.mp4",
   "Options":1,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

## <a name="associate-the-contentkey-with-an-asset"></a>Kojarzenie ContentKey z elementem zawartości
Po utworzeniu ContentKey skojarz ją z elementem zawartości przy użyciu operacji $links, jak pokazano w następującym przykładzie:

Żądanie:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}
```

Odpowiedź:

```console
HTTP/1.1 204 No Content 
```

## <a name="create-an-assetfile"></a>Utwórz AssetFile
Jednostka [AssetFile](/rest/api/media/operations/assetfile) reprezentuje plik wideo lub audio, który jest przechowywany w kontenerze obiektów BLOB. Plik zasobów jest zawsze skojarzony z zasobem, a zasób może zawierać jeden lub wiele plików zasobów. Zadanie kodera Media Services nie powiedzie się, jeśli obiekt pliku zasobów nie jest skojarzony z plikiem cyfrowym w kontenerze obiektów BLOB.

Wystąpienie **AssetFile** i rzeczywisty plik multimedialny są dwa odrębne obiekty. Wystąpienie AssetFile zawiera metadane dotyczące pliku nośnika, natomiast plik multimedialny zawiera rzeczywistą zawartość multimedialną.

Po przekazaniu pliku multimediów cyfrowych do kontenera obiektów BLOB użyjesz żądania **scalania** http, aby zaktualizować AssetFile za pomocą informacji o pliku multimedialnym (nie jest to pokazane w tym artykule). 

**Żądanie HTTP**

```console
POST https://media.windows.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
Content-Length: 164

{  
   "IsEncrypted":"true",
   "EncryptionScheme" : "StorageEncryption", 
   "EncryptionVersion" : "1.0",       
   "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector" : "397304628502661816</d:InitializationVector",
   "Options":0,
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**Odpowiedź HTTP**

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 535
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
Server: Microsoft-IIS/8.5
request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 00:34:07 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "Name":"BigBuckBunny.mp4",
   "ContentFileSize":"0",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "EncryptionVersion": "1.0",
   "EncryptionScheme": "StorageEncryption",
   "IsEncrypted":true,
   "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector":"397304628502661816</d:InitializationVector",
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```
