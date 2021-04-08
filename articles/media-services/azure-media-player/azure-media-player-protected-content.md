---
title: Azure Media Player zawartość chroniona
description: Azure Media Player obecnie obsługuje zaszyfrowaną zawartość obwiedni AES-128 i często zaszyfrowaną zawartość.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 83f144c06c23f3ab5507e3561be4a12350e20a42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91329690"
---
# <a name="protected-content"></a>Chroniona zawartość #

Azure Media Player obecnie obsługuje zaszyfrowaną zawartość obwiedni AES-128 i często zaszyfrowaną zawartość (za pośrednictwem oprogramowania PlayReady i Widevine) lub zaszyfrowaną zawartość za pośrednictwem FairPlay. W celu poprawnego odtwarzania chronionej zawartości należy powiedzieć, Azure Media Player `protectionInfo` . Te informacje istnieją dla źródła i można je dodać bezpośrednio do `<source>` tagu za pośrednictwem `data-setup` .  Możesz również dodać `protectionInfo` bezpośrednio jako parametr, jeśli źródło zostanie ustawione dynamicznie.

`protectionInfo` akceptuje obiekt JSON i zawiera:

- `type`: `AES` lub `PlayReady` lub `Widevine``FairPlay`
- `certificateUrl`: powinien to być bezpośredni link do hostowanego certyfikatu FairPlay

- `authenticationToken`: to pole opcji umożliwia dodanie niezakodowanego tokenu uwierzytelniania

> [!IMPORTANT]
> Obiekt **certificateUrl** jest wymagany tylko w przypadku funkcji DRM FairPlay. * * *
>[!NOTE]
> Domyślny techOrder został zmieniony tak, aby pomieścić nowe techniczne, `html5FairPlayHLS` przeznaczone do samoobsługowego odtwarzania zawartości FairPlay w przeglądarkach, które go obsługują (Safari on OSX 8 +). Jeśli masz FairPlay zawartość do odtwarzania **i** zmienisz domyślną techOrder na niestandardową w aplikacji, musisz dodać nową pozycję techniczną do obiektu techOrder. Zalecamy uwzględnienie jej przed technologią Silverlight, aby zawartość nie była odtwarzana za pośrednictwem programu PlayReady.

## <a name="code-sample"></a>Przykład kodu ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

lub z wieloma usługami DRM

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> Nie wszystkie przeglądarki/platformy mogą odtwarzać zawartość chronioną. Zapoznaj się z sekcją [technologia odtwarzania](azure-media-player-playback-technology.md) , aby uzyskać więcej informacji na temat tego, co jest obsługiwane.
> [!IMPORTANT]
> Token przekazywany do odtwarzacza jest przeznaczony dla zabezpieczonej zawartości i używany tylko dla uwierzytelnionych użytkowników. Przyjęto założenie, że aplikacja korzysta z protokołu SSL lub innej formy miary zabezpieczeń. Ponadto użytkownik końcowy jest assummed jako zaufany, aby nie nadużyć tokenu; Jeśli tak się nie dzieje, zapoznaj się z ekspertami ds. zabezpieczeń.

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)