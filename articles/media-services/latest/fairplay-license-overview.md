---
title: Media Services obsługa licencji Apple FairPlay
description: Ten temat zawiera omówienie wymagań i konfiguracji licencji firmy Apple FairPlay.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 187c1e60d97e0bebb3b6216b0055ddffe6e6cb4c
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454385"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Wymagania licencyjne i konfiguracja technologii FairPlay firmy Apple

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services umożliwia zaszyfrowanie zawartości HLS za pomocą **Apple FairPlay** (CBC AES-128). Media Services udostępnia również usługę do dostarczania licencji FairPlay. Gdy gracz podejmie próbę odtworzenia zawartości chronionej przez FairPlay, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, wystawia licencję, która jest wysyłana do klienta i służy do odszyfrowania i odtworzenia określonej zawartości.

Media Services udostępnia również interfejsy API, których można użyć do skonfigurowania licencji FairPlay. W tym temacie omówiono wymagania dotyczące licencji FairPlay i pokazano, jak można skonfigurować licencję **FairPlay** przy użyciu interfejsów API Media Services. 

## <a name="requirements"></a>Wymagania

Poniższe informacje są wymagane w przypadku korzystania z Media Services w celu zaszyfrowania zawartości HLS przy użyciu usługi **Apple FairPlay** i użycia Media Services w celu dostarczenia licencji FairPlay:

* Zarejestruj się w [programie Apple Development](https://developer.apple.com/).
* Firma Apple wymaga od właściciela zawartości, aby uzyskać [pakiet wdrożeniowy](https://developer.apple.com/contact/fps/). Stan, w którym został już zaimplementowany moduł zabezpieczeń (KSM) z Media Services i że żądasz końcowego pakietu FPS. W końcowym pakiecie FPS znajdują się instrukcje generowania certyfikatów i uzyskiwania klucza tajnego aplikacji. Używasz poproszenia o skonfigurowanie FairPlay.
* Następujące elementy muszą zostać ustawione na stronie dostarczania klucza Media Services/licencji:

    * **Certyfikat aplikacji (AC)**: jest to plik PFX, który zawiera klucz prywatny. Ten plik jest tworzony i szyfrowany przy użyciu hasła. Plik PFX powinien być w formacie base64.

        W poniższych krokach opisano sposób generowania pliku certyfikatu PFX dla FairPlay:

        1. Zainstaluj OpenSSL z https://slproweb.com/products/Win32OpenSSL.html .

            Przejdź do folderu, w którym znajduje się certyfikat FairPlay i inne pliki dostarczone przez firmę Apple.
        2. W wierszu polecenia uruchom następujące polecenie. Spowoduje to przekonwertowanie pliku CER na plik PEM.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 — informowanie algorytmu der-in FairPlay. cer-out FairPlay-out. pem
        3. W wierszu polecenia uruchom następujące polecenie. Spowoduje to przekonwertowanie pliku PEM na plik PFX z kluczem prywatnym. Hasło do pliku PFX jest następnie zadawane przez OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" PKCS12 — wyeksportuj FairPlay-out. pfx-INKEY PrivateKey. pem-in FairPlay-out. pem — passer file:privatekey-pem-pass.txt
            
    * **Hasło certyfikatu aplikacji**: hasło do tworzenia pliku PFX.
    * **Poproszenie**: ten klucz jest odbierany podczas generowania certyfikacji przy użyciu portalu dla deweloperów firmy Apple. Każdy zespół programistyczny otrzymuje unikatowe żądanie. Zapisz kopię pytania i przechowuj ją w bezpiecznym miejscu. Musisz skonfigurować program FairPlayAsk z Media Services.
    
* Po stronie klienta FPS muszą być ustawione następujące elementy:

  * **Certyfikat aplikacji (AC)**: plik CER/. der zawierający klucz publiczny, który jest używany przez system operacyjny do szyfrowania niektórych ładunków. Media Services musi wiedzieć o tym, ponieważ jest wymagany przez odtwarzacz. Usługa Key Delivery odszyfrowuje ją przy użyciu odpowiedniego klucza prywatnego.

* Aby odtworzyć zaszyfrowany strumień FairPlay, najpierw zapoznaj się z rzeczywistym zapytaniami, a następnie Wygeneruj prawdziwy certyfikat. Ten proces tworzy wszystkie trzy części:

  * plik. der
  * plik PFX
  * hasło do pliku PFX
  
> [!NOTE]
> Azure Media Services nie sprawdza daty wygaśnięcia certyfikatu podczas pakowania lub dostarczania klucza. Będzie ona nadal działała po wygaśnięciu certyfikatu.

## <a name="fairplay-and-player-apps"></a>Aplikacje FairPlay i odtwarzacze

Gdy zawartość jest zaszyfrowana za pomocą **Apple FairPlay**, poszczególne próbki wideo i audio są szyfrowane przy użyciu trybu **CBC AES-128** . **FairPlay Streaming** (FPS) jest zintegrowany z systemami operacyjnymi urządzeń z natywną obsługą systemu iOS i Apple TV. Przeglądarka Safari w systemie OS X umożliwia korzystanie z funkcji FPS przy użyciu interfejsu EME (Encrypted Media Extensions).

Azure Media Player obsługuje również odtwarzanie FairPlay. Aby uzyskać więcej informacji, zobacz [dokumentację Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html).

Własne aplikacje odtwarzacza można opracowywać przy użyciu zestawu iOS SDK. Aby można było odtwarzać zawartość FairPlay, należy zaimplementować protokół wymiany licencji. Ten protokół nie jest określony przez firmę Apple. Jest to każda aplikacja, w której można wysyłać żądania dostarczania kluczy. Usługa dostarczania kluczy Media Services FairPlay oczekuje, że SPC ma być komunikatem post w formacie www-form-URL, w następującej postaci:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>Przykład konfiguracji FairPlay .NET

Do konfigurowania licencji FairPlay można użyć interfejsu API Media Services. Gdy gracz podejmie próbę odtworzenia zawartości chronionej przez FairPlay, żądanie jest wysyłane do usługi dostarczania licencji w celu uzyskania licencji. Jeśli usługa licencji zatwierdzi żądanie, usługa wystawia licencję. Jest on wysyłany do klienta i jest używany do odszyfrowywania i odtwarzania określonej zawartości.

> [!NOTE]
> Na ogół można skonfigurować opcje zasad FairPlay tylko raz, ponieważ tylko jeden zestaw certyfikacji i pytania.

W poniższym przykładzie zastosowano [Media Services .NET SDK](/dotnet/api/microsoft.azure.management.media.models) w celu skonfigurowania licencji.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tematem [Ochrona przy użyciu technologii DRM](protect-with-drm.md)
