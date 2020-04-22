---
title: Zawartość chroniona za pomocą programu Azure Media Player
description: Usługa Azure Media Player obsługuje obecnie zaszyfrowaną zawartość z kopertą bitową AES-128 i wspólną zaszyfrowaną zawartość.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 64414d3ec31e8763b7c576af93374bf514141fd4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726494"
---
# <a name="protected-content"></a>Zawartość chroniona #

Usługa Azure Media Player obsługuje obecnie zaszyfrowaną zawartość z kopertą bitową AES-128 i wspólną zaszyfrowaną zawartość (za pośrednictwem playready i widevine) lub zaszyfrowaną zawartość za pośrednictwem usługi FairPlay. Aby poprawnie odtworzyć zawartość chroniona, należy poinformować `protectionInfo`program Azure Media Player o programie . Te informacje istnieją na źródło i mogą `<source>` być dodawane bezpośrednio do tagu za pośrednictwem pliku `data-setup`.  Można również dodać `protectionInfo` bezpośrednio jako parametr, jeśli dynamicznie ustawia źródło.

`protectionInfo`akceptuje obiekt JSON i obejmuje:

- `type`: `AES` `PlayReady` lub `Widevine` lub`FairPlay`
- `certificateUrl`: powinien to być bezpośredni link do hostowanego certyfikatu FairPlay

- `authenticationToken`: jest to pole opcji, aby dodać niekodowany token uwierzytelniania

> [!IMPORTANT]
> Obiekt **certificateUrl** jest potrzebny tylko dla funkcji DrM fairplay.***
>[!NOTE]
> Domyślny techOrder został zmieniony, aby `html5FairPlayHLS` pomieścić nową technologię, w szczególności do odtwarzania zawartości FairPlay natywnie w przeglądarkach, które go obsługują (Safari na OSX 8 +). Jeśli masz zawartość FairPlay do odtwarzania **i** zmieniłeś domyślny techOrder na niestandardowy w aplikacji, musisz dodać tę nową technologię do obiektu techOrder. Zalecamy dołączenie go przed programem SilverlightSS, aby zawartość nie była odtwarzana za pośrednictwem programu PlayReady.

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

lub, z wieloma DRM

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
> Nie wszystkie przeglądarki/platformy są w stanie odtwarzać zawartość chronioną. Zobacz sekcję [Technologia odtwarzania, aby](azure-media-player-playback-technology.md) uzyskać więcej informacji na temat obsługiwanych informacji.
> [!IMPORTANT]
> Token przekazany do odtwarzacza jest przeznaczony dla zabezpieczonej zawartości i używany tylko dla uwierzytelnionych użytkowników. Zakłada się, że aplikacja używa SSL lub innej formy środka bezpieczeństwa. Ponadto użytkownik końcowy jest assummed do zaufania, aby nie nadużywać tokenu; jeśli tak nie jest, proszę zaangażować swoich ekspertów w dziedzinie bezpieczeństwa.

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)