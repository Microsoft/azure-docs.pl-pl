---
title: Informacje o zabezpieczeniach platformy Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — jak kontrolować dostęp do IoT Hub aplikacji urządzeń i aplikacji zaplecza. Zawiera informacje o tokenach zabezpieczających i obsłudze certyfikatów X. 509.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: dbe277c7451b02887ec5657b1a183fcd001d134e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148292"
---
# <a name="control-access-to-iot-hub"></a>Kontrola dostępu do centrum IoT Hub

W tym artykule opisano opcje zabezpieczania Centrum IoT. IoT Hub używa *uprawnień* do udzielania dostępu do każdego punktu końcowego Centrum IoT Hub. Uprawnienia ograniczają dostęp do centrum IoT Hub na podstawie funkcjonalności.

W tym artykule wprowadzono:

* Różne uprawnienia, które można przyznać urządzeniu lub aplikacji zaplecza, aby uzyskać dostęp do centrum IoT.
* Proces uwierzytelniania i tokeny, których używa do weryfikacji uprawnień.
* Określanie zakresu poświadczeń w celu ograniczenia dostępu do określonych zasobów.
* Obsługa IoT Hub dla certyfikatów X. 509.
* Niestandardowe mechanizmy uwierzytelniania urządzeń korzystające z istniejących rejestrów tożsamości urządzeń lub schematów uwierzytelniania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Musisz mieć odpowiednie uprawnienia, aby uzyskać dostęp do dowolnych punktów końcowych IoT Hub. Na przykład urządzenie musi zawierać token zawierający poświadczenia zabezpieczeń oraz każdy komunikat wysyłany do IoT Hub.

## <a name="access-control-and-permissions"></a>Kontrola dostępu i uprawnienia

[Uprawnienia](#iot-hub-permissions) można udzielić w następujący sposób:

* **Zasady dostępu współdzielonego na poziomie Centrum IoT**. Zasady dostępu współdzielonego mogą przydzielić dowolną kombinację [uprawnień](#iot-hub-permissions). Zasady można definiować w [Azure Portal](https://portal.azure.com)programowo przy użyciu [interfejsów api REST zasobów IoT Hub](/rest/api/iothub/iothubresource)lub przy użyciu polecenia [AZ IoT Hub Policy](/cli/azure/iot/hub/policy?view=azure-cli-latest) . Nowo utworzone Centrum IoT Hub ma następujące zasady domyślne:
  
  | Zasady dostępu współdzielonego | Uprawnienia |
  | -------------------- | ----------- |
  | iothubowner | Wszystkie uprawnienia |
  | usługa | Uprawnienia **Serviceconnect** |
  | urządzenie | Uprawnienia **DeviceConnect** |
  | registryRead | Uprawnienia **RegistryRead** |
  | registryReadWrite | Uprawnienia **RegistryRead** i **RegistryWrite** |

* **Poświadczenia zabezpieczeń dla poszczególnych urządzeń**. Każdy IoT Hub zawiera [Rejestr tożsamości](iot-hub-devguide-identity-registry.md) dla każdego urządzenia w tym rejestrze tożsamości, można skonfigurować poświadczenia zabezpieczeń, które przyznają uprawnienia **DeviceConnect** do odpowiednich punktów końcowych urządzeń.

Na przykład w typowym rozwiązaniu IoT:

* Składnik zarządzania urządzeniami używa zasad *registryReadWriteymi* .
* Składnik procesora zdarzeń używa zasad *usługi* .
* Składnik logiki biznesowej urządzenia czasu wykonywania korzysta z zasad *usługi* .
* Poszczególne urządzenia łączą się przy użyciu poświadczeń przechowywanych w rejestrze tożsamości Centrum IoT.

> [!NOTE]
> Aby uzyskać szczegółowe informacje, zobacz [uprawnienia](#iot-hub-permissions) .

## <a name="authentication"></a>Authentication

Usługa Azure IoT Hub udziela dostępu do punktów końcowych, weryfikując token względem zasad dostępu współużytkowanego i poświadczeń zabezpieczeń rejestru tożsamości.

Poświadczenia zabezpieczeń, takie jak klucze symetryczne, nigdy nie są wysyłane przez sieć.

> [!NOTE]
> Dostawca zasobów usługi Azure IoT Hub jest zabezpieczony za pomocą subskrypcji platformy Azure, tak jak wszyscy dostawcy w [Azure Resource Manager](../azure-resource-manager/management/overview.md).

Aby uzyskać więcej informacji na temat tworzenia i używania tokenów zabezpieczających, zobacz [IoT Hub tokeny zabezpieczające](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Specyficzne dla protokołu

Każdy obsługiwany protokół, taki jak MQTT, AMQP i HTTPS, transportuje tokeny na różne sposoby.

W przypadku korzystania z MQTT pakiet CONNECT ma identyfikator deviceId jako ClientId, `{iothubhostname}/{deviceId}` w polu username i token sygnatury dostępu współdzielonego w polu hasło. `{iothubhostname}` powinna to być pełny rekord CName Centrum IoT Hub (na przykład contoso.azure-devices.net).

W przypadku korzystania z usługi [AMQP](https://www.amqp.org/)IoT Hub obsługuje [SASL Plain](https://tools.ietf.org/html/rfc4616) i [AMQP zabezpieczenia oparte na oświadczeniach](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

W przypadku korzystania z zabezpieczeń opartych na oświadczeniach AMQP standard określa sposób przesyłania tych tokenów.

W przypadku SASL PLAIN **Nazwa użytkownika** może:

* `{policyName}@sas.root.{iothubName}` w przypadku korzystania z tokenów na poziomie Centrum IoT.
* `{deviceId}@sas.{iothubname}` w przypadku używania tokenów z zakresem urządzeń.

W obu przypadkach pole Password zawiera token, zgodnie z opisem w [IoT Hub tokeny zabezpieczające](iot-hub-devguide-security.md#security-tokens).

Protokół HTTPS implementuje uwierzytelnianie, dołączając prawidłowy token w nagłówku żądania **autoryzacji** .

#### <a name="example"></a>Przykład

Nazwa użytkownika (identyfikator urządzenia jest rozróżniana wielkość liter): `iothubname.azure-devices.net/DeviceId`

Hasło (można wygenerować token sygnatury dostępu współdzielonego z poleceniem polecenia [AZ IoT Hub Generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-generate-sas-token)lub [Azure iot Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md) automatycznie generują tokeny podczas nawiązywania połączenia z usługą. W niektórych przypadkach zestawy SDK usługi Azure IoT nie obsługują wszystkich protokołów ani wszystkich metod uwierzytelniania.

### <a name="special-considerations-for-sasl-plain"></a>Specjalne zagadnienia dotyczące SASL PLAIN

W przypadku korzystania z SASL PLAIN z AMQP, klient łączący się z usługą IoT Hub może używać jednego tokenu dla każdego połączenia TCP. Po wygaśnięciu tokenu połączenie TCP rozłącza się z usługą i wyzwala ponowne połączenie. Takie zachowanie nie powoduje problemów z aplikacją zaplecza, co jest szkodliwe dla aplikacji urządzenia z następujących powodów:

* Bramy zazwyczaj nawiązują połączenie w imieniu wielu urządzeń. W przypadku korzystania z SASL PLAIN należy utworzyć oddzielne połączenie TCP dla każdego urządzenia łączącego się z usługą IoT Hub. W tym scenariuszu znacznie zwiększa się zużycie zasobów związanych z urządzeniami i zasobami sieciowymi oraz wydłuża opóźnienia poszczególnych połączeń urządzeń.

* W przypadku urządzeń z ograniczoną ilością zasobów można ponownie nawiązać połączenie po upłynięciu każdego wygaśnięcia tokenu.

## <a name="scope-iot-hub-level-credentials"></a>Zakres poświadczeń na poziomie Centrum IoT Hub

Aby ograniczyć zasady zabezpieczeń na poziomie Centrum IoT, można utworzyć tokeny z identyfikatorem URI zasobu z ograniczeniami. Na przykład punkt końcowy do wysyłania komunikatów z urządzenia do chmury z urządzenia to **/Devices/{deviceId}/messages/Events**. Można również użyć zasad dostępu współdzielonego na poziomie Centrum IoT z uprawnieniami **DeviceConnect** do podpisywania tokenu, którego resourceURI to **/Devices/{deviceId}**. To podejście tworzy token, który jest używany tylko do wysyłania wiadomości w imieniu urządzenia **deviceId**.

Ten mechanizm jest podobny do [zasad wydawcy Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)i umożliwia implementowanie niestandardowych metod uwierzytelniania.

## <a name="security-tokens"></a>Tokeny zabezpieczające

IoT Hub używa tokenów zabezpieczających do uwierzytelniania urządzeń i usług, aby uniknąć wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczone do okresu ważności i zakresu. [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md) automatycznie generują tokeny bez konieczności konfigurowania specjalnej konfiguracji. Niektóre scenariusze wymagają generowania i używania tokenów zabezpieczających bezpośrednio. Takie scenariusze obejmują:

* Bezpośrednie użycie powierzchni MQTT, AMQP lub HTTPS.

* Implementacja wzorca usługi tokenów, zgodnie z opisem w temacie [niestandardowe uwierzytelnianie urządzenia](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub umożliwia również urządzeniom uwierzytelnianie za pomocą IoT Hub przy użyciu [certyfikatów X. 509](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Struktura tokenu zabezpieczającego

Tokeny zabezpieczające są używane do udzielania ograniczonego czasowo dostępu do urządzeń i usług do określonych funkcji w IoT Hub. Aby uzyskać autoryzację w celu nawiązania połączenia z usługą IoT Hub, urządzenia i usługi muszą wysyłać tokeny zabezpieczające podpisane z dostępem współdzielonym lub kluczem symetrycznym. Klucze te są przechowywane z tożsamością urządzenia w rejestrze tożsamości.

Token podpisany za pomocą klucza dostępu współdzielonego umożliwia dostęp do wszystkich funkcji skojarzonych z uprawnieniami zasad dostępu współdzielonego. Token podpisany przy użyciu klucza symetrycznego tożsamości urządzenia przyznaje uprawnienia **DeviceConnect** dla skojarzonej tożsamości urządzenia.

Token zabezpieczający ma następujący format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Poniżej przedstawiono oczekiwane wartości:

| Wartość | Opis |
| --- | --- |
| podpisane |Ciąg sygnatury HMAC-SHA256 formularza: `{URL-encoded-resourceURI} + "\n" + expiry` . **Ważne**: klucz jest zdekodowany z formatu base64 i używany jako klucz do wykonywania obliczeń HMAC-SHA256. |
| ResourceURI |Prefiks identyfikatora URI (segment) punktów końcowych, do których można uzyskać dostęp za pomocą tego tokenu, rozpoczynając od nazwy hosta Centrum IoT (bez protokołu). Na przykład `myHub.azure-devices.net/devices/device1` |
| wygaśnięcia |Ciągi UTF8 przez liczbę sekund od czasu epoki 00:00:00 UTC 1 stycznia 1970. |
| {Zakodowany w adresie URL — resourceURI} |Małe adresy URL — kodowanie identyfikatora URI zasobu niższej wielkości liter |
| PolicyName |Nazwa zasad dostępu współdzielonego, do których odwołuje się ten token. Nieobecny, jeśli token odwołuje się do poświadczeń urządzenia-rejestru. |

**Uwaga dotycząca prefiksu**: Prefiks URI jest obliczany przez segment i nie przez znak. Na przykład `/a/b` jest prefiks dla, `/a/b/c` ale nie dla `/a/bc` .

Poniższy fragment kodu Node.js przedstawia funkcję o nazwie **generateSasToken** , która oblicza token na podstawie danych wejściowych `resourceUri, signingKey, policyName, expiresInMins` . W następnych sekcjach szczegółowo opisano, jak zainicjować różne dane wejściowe dla różnych przypadków użycia tokenu.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

W ramach porównania, odpowiedni kod języka Python służący do generowania tokenu zabezpieczającego:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

Funkcja języka C# do generowania tokenu zabezpieczającego:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}
```

W przypadku języka Java:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```


> [!NOTE]
> Ponieważ okres ważności tokenu jest weryfikowany na maszynach IoT Hub, odchylenie zegara maszyny generującej token musi być minimalne.

### <a name="use-sas-tokens-in-a-device-app"></a>Używanie tokenów SAS w aplikacji urządzenia

Istnieją dwa sposoby uzyskania uprawnień **DeviceConnect** IoT Hub za pomocą tokenów zabezpieczających: Użyj [klucza urządzenia symetrycznego z rejestru tożsamości](#use-a-symmetric-key-in-the-identity-registry)lub Użyj [klucza dostępu współdzielonego](#use-a-shared-access-policy).

Należy pamiętać, że wszystkie funkcje dostępne na urządzeniach są udostępniane przez projektowanie w punktach końcowych z prefiksem `/devices/{deviceId}` .

> [!IMPORTANT]
> Jedynym sposobem, w jaki IoT Hub uwierzytelniać określone urządzenie, jest użycie klucza symetrycznego tożsamości urządzenia. W przypadkach, gdy zasady dostępu współdzielonego są używane w celu uzyskania dostępu do funkcji urządzenia, rozwiązanie musi rozważyć składnik wystawiający token zabezpieczający jako zaufany podskładnik.

Punkty końcowe dostępne na urządzeniu są (niezależnie od protokołu):

| Punkt końcowy | Funkcjonalność |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Wysyłanie komunikatów z urządzenia do chmury. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Odbieraj komunikaty z chmury do urządzenia. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Używanie klucza symetrycznego w rejestrze tożsamości

W przypadku wygenerowania tokenu przy użyciu klucza symetrycznego tożsamości urządzenia `skn` zostanie pominięty element PolicyName () tokenu.

Na przykład token utworzony w celu uzyskania dostępu do wszystkich funkcji urządzenia powinien mieć następujące parametry:

* Identyfikator URI zasobu: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* klucz podpisywania: dowolny klucz symetryczny dla `{device id}` tożsamości,
* Brak nazwy zasad,
* dowolny czas wygaśnięcia.

Przykład przy użyciu poprzedniej funkcji Node.js:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Wynik, który daje dostęp do wszystkich funkcji dla device1, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Istnieje możliwość wygenerowania tokenu sygnatury dostępu współdzielonego za pomocą polecenia rozszerzenia interfejsu użytkownika [AZ IoT Hub Generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-generate-sas-token)lub [narzędzi Azure iot Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Korzystanie z zasad dostępu współdzielonego

Podczas tworzenia tokenu z zasad dostępu współdzielonego należy ustawić `skn` pole na nazwę zasady. Te zasady muszą udzielić uprawnienia **DeviceConnect** .

Dwa główne scenariusze używania zasad dostępu współdzielonego w celu uzyskania dostępu do funkcji urządzenia są następujące:

* [bramy protokołu w chmurze](iot-hub-devguide-endpoints.md),
* [usługi tokenów](iot-hub-devguide-security.md#custom-device-and-module-authentication) używane do implementowania niestandardowych schematów uwierzytelniania.

Ponieważ zasady dostępu współdzielonego mogą potencjalnie udzielić dostępu do łączenia się z dowolnego urządzenia, ważne jest, aby użyć poprawnego identyfikatora URI zasobu podczas tworzenia tokenów zabezpieczających. To ustawienie jest szczególnie ważne w przypadku usług tokenów, które muszą określać zakres tokenu na określonym urządzeniu przy użyciu identyfikatora URI zasobu. Ten punkt jest mniej istotny dla bram protokołu, ponieważ już mediating ruch dla wszystkich urządzeń.

Przykładowo usługa tokenu korzystająca z wstępnie utworzonych zasad dostępu współdzielonego o nazwie **urządzenie** utworzy token z następującymi parametrami:

* Identyfikator URI zasobu: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* klucz podpisywania: jeden z kluczy `device` zasad,
* Nazwa zasad: `device` ,
* dowolny czas wygaśnięcia.

Przykład przy użyciu poprzedniej funkcji Node.js:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, który daje dostęp do wszystkich funkcji dla device1, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Brama protokołu może używać tego samego tokenu dla wszystkich urządzeń, po prostu ustawiając identyfikator URI zasobu na `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Korzystanie z tokenów zabezpieczających ze składników usługi

Składniki usługi mogą generować tylko tokeny zabezpieczające przy użyciu zasad dostępu współdzielonego, które udzielają odpowiednich uprawnień, jak wyjaśniono wcześniej.

Poniżej przedstawiono funkcje usługi uwidocznione w punktach końcowych:

| Punkt końcowy | Funkcjonalność |
| --- | --- |
| `{iot hub host name}/devices` |Tworzenie, aktualizowanie, pobieranie i usuwanie tożsamości urządzeń. |
| `{iot hub host name}/messages/events` |Odbieraj komunikaty z urządzenia do chmury. |
| `{iot hub host name}/servicebound/feedback` |Otrzymuj informacje zwrotne dotyczące komunikatów z chmury do urządzenia. |
| `{iot hub host name}/devicebound` |Wysyłanie komunikatów z chmury do urządzenia. |

Przykładowo usługa generująca przy użyciu wstępnie utworzonych zasad dostępu współdzielonego o nazwie **registryRead** utworzy token z następującymi parametrami:

* Identyfikator URI zasobu: `{IoT hub name}.azure-devices.net/devices` ,
* klucz podpisywania: jeden z kluczy `registryRead` zasad,
* Nazwa zasad: `registryRead` ,
* dowolny czas wygaśnięcia.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

W efekcie można udzielić dostępu do odczytu wszystkich tożsamości urządzeń:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Obsługiwane certyfikaty X. 509

Można użyć dowolnego certyfikatu X. 509 w celu uwierzytelnienia urządzenia za pomocą IoT Hub przez przekazanie odcisku palca certyfikatu lub urzędu certyfikacji do usługi Azure IoT Hub. Uwierzytelnianie przy użyciu odcisków palców certyfikatów weryfikuje, czy przedstawiony odcisk palca jest zgodny ze skonfigurowanym odciskiem palca. Uwierzytelnianie przy użyciu urzędu certyfikacji sprawdza łańcuch certyfikatów. W obu przypadkach uzgadnianie protokołu TLS wymaga, aby urządzenie miało prawidłowy certyfikat i klucz prywatny. Aby uzyskać szczegółowe informacje, zobacz specyfikację protokołu TLS, na przykład: [RFC 5246-protokół Transport Layer Security (TLS) w wersji 1,2](https://tools.ietf.org/html/rfc5246/).

Obsługiwane są następujące certyfikaty:

* **Istniejący certyfikat X. 509**. Z urządzeniem może już być skojarzony certyfikat X. 509. Urządzenie może używać tego certyfikatu do uwierzytelniania za pomocą IoT Hub. Działa z odciskiem palca lub uwierzytelnianiem urzędu certyfikacji. 

* **Certyfikat X. 509 podpisany przez urząd certyfikacji**. Aby zidentyfikować urządzenie i uwierzytelnić je za pomocą IoT Hub, można użyć certyfikatu X. 509 wygenerowanego i podpisanego przez urząd certyfikacji (CA). Działa z odciskiem palca lub uwierzytelnianiem urzędu certyfikacji.

* **Certyfikat X-509 z podpisem własnym i z**podpisem własnym. Producent urządzenia lub wdrożenie wewnętrzne może generować te certyfikaty i przechowywać odpowiedni klucz prywatny (i certyfikat) na urządzeniu. Do tego celu można użyć narzędzi, takich jak [OpenSSL](https://www.openssl.org/) i [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) Utility. Działa tylko z uwierzytelnianiem przy użyciu odcisku palca. 

Na urządzeniu może być używany certyfikat X. 509 lub token zabezpieczający, ale nie oba te elementy.

Następujące funkcje nie są obsługiwane w przypadku urządzeń korzystających z uwierzytelniania urzędu certyfikacji X. 509:

* HTTPS, MQTT za pośrednictwem obiektów WebSockets i AMQP za pośrednictwem protokołów WebSockets.
* Operacje przekazywania plików (wszystkie protokoły).

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu urzędu certyfikacji, zobacz [uwierzytelnianie urządzeń za pomocą certyfikatów X. 509 urzędu certyfikacji](iot-hub-x509ca-overview.md). Aby uzyskać informacje na temat przekazywania i weryfikowania urzędu certyfikacji za pomocą usługi IoT Hub, zobacz [Konfigurowanie zabezpieczeń X. 509 w usłudze Azure IoT Hub](iot-hub-security-x509-get-started.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Rejestrowanie certyfikatu X. 509 dla urządzenia

[Zestaw SDK usługi Azure IoT dla języka C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (wersja 1.0.8 +) obsługuje rejestrowanie urządzenia korzystającego z certyfikatu X. 509 w celu uwierzytelnienia. Inne interfejsy API, takie jak Import/Export urządzeń, obsługują również certyfikaty X. 509.

Aby skonfigurować certyfikaty X. 509 dla urządzeń, można również użyć polecenia rozszerzenia interfejsu wiersza poleceń [AZ IoT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) .

### <a name="c-support"></a>Obsługa języka C \#

Klasa **registrymanager** zapewnia programowy sposób zarejestrowania urządzenia. W szczególności metody **AddDeviceAsync** i **UpdateDeviceAsync** umożliwiają rejestrowanie i aktualizowanie urządzenia w rejestrze tożsamości IoT Hub. Te dwie metody przyjmują wystąpienie **urządzenia** jako dane wejściowe. Klasa **urządzenia** zawiera właściwość **uwierzytelniania** , która pozwala określić podstawowy i pomocniczy odcisk palca certyfikatu X. 509. Odcisk palca reprezentuje Skrót SHA256 certyfikatu X. 509 (przechowywany przy użyciu binarnego kodowania DER). Istnieje możliwość określenia podstawowego odcisku palca lub pomocniczego odcisku palca lub obu tych elementów. Główne i pomocnicze odciski palca są obsługiwane w celu obsługi scenariuszy przerzucania certyfikatów.

Oto przykładowy fragment kodu języka C służący \# do rejestrowania urządzenia przy użyciu odcisku palca certyfikatu X. 509:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Używanie certyfikatu X. 509 podczas operacji w czasie wykonywania

[Zestaw SDK urządzeń Azure IoT dla platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (wersja 1.0.11 +) obsługuje używanie certyfikatów X. 509.

### <a name="c-support"></a>Obsługa języka C \#

Klasa **DeviceAuthenticationWithX509Certificate** obsługuje tworzenie wystąpień **DeviceClient** przy użyciu certyfikatu X. 509. Certyfikat X. 509 musi znajdować się w formacie PFX (nazywanym również formatem PKCS #12), który zawiera klucz prywatny.

Oto przykładowy fragment kodu:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Niestandardowe uwierzytelnianie urządzenia i modułu

Za pomocą [rejestru tożsamości](iot-hub-devguide-identity-registry.md) IoT Hub można skonfigurować poświadczenia zabezpieczeń poszczególnych urządzeń/modułów oraz kontrolę dostępu przy użyciu [tokenów](iot-hub-devguide-security.md#security-tokens). Jeśli rozwiązanie IoT ma już niestandardowy rejestr tożsamości i/lub schemat uwierzytelniania, rozważ utworzenie *usługi tokenu* umożliwiającej integrację tej infrastruktury z IoT Hub. W ten sposób można używać innych funkcji IoT w rozwiązaniu.

Usługa tokenów to niestandardowa usługa w chmurze. Używa *zasad dostępu współdzielonego* IoT Hub z uprawnieniami **DeviceConnect** lub **ModuleConnect** do tworzenia tokenów z zakresem *urządzeń* lub *modułów* . Te tokeny umożliwiają urządzeniu i modułowi łączenie się z Centrum IoT Hub.

![Kroki wzorca usługi tokenów](./media/iot-hub-devguide-security/tokenservice.png)

Oto główne kroki wzorca usługi tokenu:

1. Utwórz IoT Hub zasady dostępu współdzielonego z uprawnieniami **DeviceConnect** lub **ModuleConnect** dla Centrum IoT Hub. Te zasady można utworzyć w [Azure Portal](https://portal.azure.com) lub programowo. Usługa tokenów używa tych zasad do podpisywania tokenów, które tworzy.

2. Gdy urządzenie/moduł musi uzyskać dostęp do centrum IoT, żąda podpisanego tokenu z usługi tokenu. Urządzenie może być uwierzytelniane za pomocą niestandardowego schematu rejestracji tożsamości/uwierzytelniania w celu określenia tożsamości urządzenia/modułu używanego przez usługę tokenów do tworzenia tokenu.

3. Usługa tokenu zwraca token. Token jest tworzony przy użyciu `/devices/{deviceId}` lub `/devices/{deviceId}/module/{moduleId}` AS `resourceURI` , z opcją `deviceId` jako uwierzytelnianym urządzeniem lub `moduleId` jako uwierzytelnianym modułem. Usługa tokenów używa zasad dostępu współdzielonego do konstruowania tokenu.

4. Urządzenie/moduł używa tokenu bezpośrednio w usłudze IoT Hub.

> [!NOTE]
> Do utworzenia tokenu w usłudze tokenu można użyć klasy .NET [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) lub klasy [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) języka Java.

Usługa tokenów może ustawić wygaśnięcie tokenu zgodnie z potrzebami. Po wygaśnięciu tokenu Centrum IoT łączy połączenie z urządzeniem/modułem. Następnie urządzenie/moduł musi zażądać nowego tokenu z usługi tokenu. Krótki czas wygaśnięcia zwiększa obciążenie zarówno urządzenia/modułu, jak i usługi tokenu.

Aby urządzenie/moduł łączył się z centrum, należy dodać go do rejestru tożsamości IoT Hub, nawet jeśli używa tokenu, a nie klucza do nawiązania połączenia. W związku z tym można nadal używać kontroli dostępu dla poszczególnych urządzeń/poszczególnych modułów, włączając lub wyłączając tożsamości urządzeń/modułów w [rejestrze tożsamości](iot-hub-devguide-identity-registry.md). Takie podejście ogranicza ryzyko używania tokenów z długimi czasami wygaśnięcia.

### <a name="comparison-with-a-custom-gateway"></a>Porównanie z bramą niestandardową

Wzorzec usługi Token Service jest zalecanym sposobem implementowania niestandardowego schematu rejestru tożsamości i uwierzytelniania za pomocą IoT Hub. Ten wzorzec jest zalecany, ponieważ IoT Hub nadal obsłużyć większość ruchu rozwiązania. Jeśli jednak niestandardowy schemat uwierzytelniania to intertwined z protokołem, może być konieczne, aby *brama niestandardowa* mogła przetworzyć cały ruch. Przykładem takiego scenariusza jest użycie [Transport Layer Security (TLS) i kluczy wstępnych (PSKs)](https://tools.ietf.org/html/rfc4279). Aby uzyskać więcej informacji, zobacz artykuł dotyczący [bramy protokołu](iot-hub-protocol-gateway.md) .

## <a name="reference-topics"></a>Tematy dotyczące odwołań:

Poniższe tematy referencyjne zawierają więcej informacji na temat kontrolowania dostępu do centrum IoT Hub.

## <a name="iot-hub-permissions"></a>Uprawnienia IoT Hub

Poniższa tabela zawiera listę uprawnień, których można użyć do kontrolowania dostępu do centrum IoT Hub.

| Uprawnienie | Uwagi |
| --- | --- |
| **RegistryRead** |Przyznaje dostęp do odczytu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [Rejestr tożsamości](iot-hub-devguide-identity-registry.md). <br/>To uprawnienie jest używane przez usługi zaplecza w chmurze. |
| **RegistryReadWrite** |Przyznaje dostęp do odczytu i zapisu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [Rejestr tożsamości](iot-hub-devguide-identity-registry.md). <br/>To uprawnienie jest używane przez usługi zaplecza w chmurze. |
| **Serviceconnect** |Przyznaje dostęp do punktów końcowych komunikacji i monitorowania dla usługi w chmurze. <br/>Przyznaje uprawnienia do odbierania komunikatów z urządzenia do chmury, wysyłania komunikatów z chmury do urządzeń i pobierania odpowiednich potwierdzeń dostarczania. <br/>Przyznaje uprawnienia do pobierania potwierdzeń dostarczania dla przekazywania plików. <br/>Przyznaje uprawnienia dostępu bliźniaczych reprezentacji do aktualizowania tagów i żądanych właściwości, pobierania raportowanych właściwości i uruchamiania zapytań. <br/>To uprawnienie jest używane przez usługi zaplecza w chmurze. |
| **DeviceConnect** |Przyznaje dostęp do punktów końcowych dostępnych dla urządzenia. <br/>Przyznaje uprawnienia do wysyłania komunikatów z urządzenia do chmury i odbierania komunikatów z chmury do urządzenia. <br/>Przyznaje uprawnienia do przekazywania plików z urządzenia. <br/>Przyznaje uprawnienia do odbierania powiadomień o właściwościach z przędzki urządzenia i aktualizacji właściwości zgłaszanych przez urządzenie. <br/>Przyznaje uprawnienia do wykonywania operacji przekazywania plików. <br/>To uprawnienie jest używane przez urządzenia. |

## <a name="additional-reference-material"></a>Dodatkowe materiały referencyjne

Inne tematy referencyjne w przewodniku dewelopera IoT Hub obejmują:

* [IoT Hub punkty końcowe](iot-hub-devguide-endpoints.md) opisują różne punkty końcowe, które są uwidaczniane przez każde Centrum IoT Hub na potrzeby operacji w czasie wykonywania i zarządzania.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisują zachowania przydziałów i ograniczania przepustowości, które mają zastosowanie do usługi IoT Hub.

* [Zestawy SDK urządzeń i usług Azure IoT](iot-hub-devguide-sdks.md) wymieniają różne zestawy SDK języka, których można używać podczas tworzenia aplikacji dla urządzeń i usług, które współpracują z usługą IoT Hub.

* [Język zapytań IoT Hub](iot-hub-devguide-query-language.md) zawiera opis języka zapytań, za pomocą którego można pobrać informacje z IoT Hub dotyczące bliźniaczych reprezentacji i zadań urządzenia.

* [IoT Hub obsługa MQTT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi IoT Hub protokołu MQTT.

* [RFC 5246 — protokół Transport Layer Security (TLS) w wersji 1,2](https://tools.ietf.org/html/rfc5246/) zawiera więcej informacji na temat uwierzytelniania TLS.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak kontrolować dostęp IoT Hub, warto zainteresować Cię następujące IoT Hub tematy dotyczące przewodnika dla deweloperów:

* [Synchronizowanie stanu i konfiguracji przy użyciu urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md)
* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)
* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Jeśli chcesz wypróbować niektóre koncepcje opisane w tym artykule, zobacz następujące samouczki IoT Hub:

* [Rozpoczynanie pracy z usługą Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Jak wysyłać komunikaty z chmury do urządzenia za pomocą IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Jak przetwarzać komunikaty IoT Hub z urządzenia do chmury](tutorial-routing.md)