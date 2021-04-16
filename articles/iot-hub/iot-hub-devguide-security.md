---
title: Opis Azure IoT Hub zabezpieczeń | Microsoft Docs
description: Przewodnik dla deweloperów — jak kontrolować dostęp do IoT Hub dla aplikacji urządzeń i aplikacji na zadomowić. Zawiera informacje o tokenach zabezpieczających i pomocy technicznej dla certyfikatów X.509.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/15/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: e72af412f61f2084fb78907c15a92a22b9e3bc99
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567183"
---
# <a name="control-access-to-iot-hub"></a>Kontrola dostępu do centrum IoT Hub

W tym artykule opisano opcje zabezpieczania centrum IoT Hub. IoT Hub używa *uprawnień do* udzielania dostępu do każdego punktu końcowego centrum IoT. Uprawnienia ograniczają dostęp do centrum IoT na podstawie funkcjonalności.

W tym artykule oprowadzono:

* Różne uprawnienia, które można przyznać urządzeniu lub aplikacji back-end w celu uzyskania dostępu do centrum IoT.
* Proces uwierzytelniania i tokeny używane do weryfikowania uprawnień.
* Jak ograniczyć dostęp do określonych zasobów za pomocą poświadczeń.
* IoT Hub obsługę certyfikatów X.509.
* Niestandardowe mechanizmy uwierzytelniania urządzeń, które używają istniejących rejestrów tożsamości urządzeń lub schematów uwierzytelniania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Musisz mieć odpowiednie uprawnienia, aby uzyskać dostęp do dowolnego z IoT Hub końcowych. Na przykład urządzenie musi zawierać token zawierający poświadczenia zabezpieczeń wraz z każdym komunikatem, który wysyła do IoT Hub.

## <a name="access-control-and-permissions"></a>Kontrola dostępu i uprawnienia

Uprawnienia można [przyznać](#iot-hub-permissions) w następujący sposób:

* **Zasady dostępu współdzielone na poziomie centrum IoT.** Zasady dostępu współdzielonych mogą przyznać dowolną kombinację [uprawnień.](#iot-hub-permissions) Zasady można definiować w Azure Portal [,](https://portal.azure.com)programowo przy użyciu interfejsów API [REST](/rest/api/iothub/iothubresource)zasobów IoT Hub lub za pomocą interfejsu wiersza polecenia [az iot hub policy.](/cli/azure/iot/hub/policy) Nowo utworzone centrum IoT ma następujące zasady domyślne:
  
  | Zasady dostępu współdzielonych | Uprawnienia |
  | -------------------- | ----------- |
  | iothubowner | Wszystkie uprawnienia |
  | usługa | **Uprawnienia usługi ServiceConnect** |
  | urządzenie | **Uprawnienia deviceConnect** |
  | registryRead | **Uprawnienia RegistryRead** |
  | registryReadWrite | **Uprawnienia RegistryRead** **i RegistryWrite** |

* **Poświadczenia zabezpieczeń dla 1 urządzenia.** Każdy IoT Hub zawiera rejestr [](iot-hub-devguide-identity-registry.md) tożsamości Dla każdego urządzenia w tym rejestrze tożsamości można skonfigurować poświadczenia zabezpieczeń, które przyznają uprawnienia **DeviceConnect** w zakresie odpowiadającym im punktom końcowym urządzenia.

Na przykład w typowym rozwiązaniu IoT:

* Składnik zarządzania urządzeniami używa zasad *registryReadWrite.*
* Składnik procesora zdarzeń używa *zasad* usługi.
* Składnik logiki biznesowej urządzenia w czasie działania korzysta z *zasad* usługi.
* Poszczególne urządzenia łączą się przy użyciu poświadczeń przechowywanych w rejestrze tożsamości centrum IoT Hub.

> [!NOTE]
> Zobacz [uprawnienia,](#iot-hub-permissions) aby uzyskać szczegółowe informacje.

## <a name="authentication"></a>Authentication

Usługa Azure IoT Hub udziela dostępu do punktów końcowych, weryfikując token względem zasad dostępu współużytkowanego i poświadczeń zabezpieczeń rejestru tożsamości.

Poświadczenia zabezpieczeń, takie jak klucze symetryczne, nigdy nie są wysyłane za pośrednictwem sieci.

> [!NOTE]
> Dostawca Azure IoT Hub zasobów jest zabezpieczony za pośrednictwem subskrypcji platformy Azure, podobnie jak wszyscy dostawcy w Azure Resource Manager [.](../azure-resource-manager/management/overview.md)

Aby uzyskać więcej informacji na temat tworzenia i używania tokenów zabezpieczających, [zobacz IoT Hub tokeny zabezpieczające](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Specyfika protokołu

Każdy obsługiwany protokół, taki jak MQTT, AMQP i HTTPS, transportuje tokeny na różne sposoby.

W przypadku korzystania z protokołu MQTT pakiet CONNECT ma w polu Nazwa użytkownika pole deviceId jako deviceId, a token SAS w `{iothubhostname}/{deviceId}` polu Hasło. `{iothubhostname}` powinna być pełną nazwą CName centrum IoT (na przykład contoso.azure-devices.net).

W przypadku [korzystania z usługi AMQP](https://www.amqp.org/)program IoT Hub obsługuje [SASL PLAIN](https://tools.ietf.org/html/rfc4616) i zabezpieczenia oparte na oświadczeniach [AMQP.](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)

Jeśli używasz zabezpieczeń opartych na oświadczeniach AMQP, standard określa sposób przesyłania tych tokenów.

W SASL PLAIN nazwa **użytkownika może** być:

* `{policyName}@sas.root.{iothubName}` w przypadku używania tokenów na poziomie centrum IoT.
* `{deviceId}@sas.{iothubname}` w przypadku używania tokenów o zakresie urządzenia.

W obu przypadkach pole hasło zawiera token zgodnie z opisem w te IoT Hub [tokenów zabezpieczających](iot-hub-devguide-security.md#security-tokens).

Protokół HTTPS implementuje uwierzytelnianie, uwzględniając prawidłowy token w **nagłówku** żądania autoryzacji.

#### <a name="example"></a>Przykład

Nazwa użytkownika (w deviceid jest zróżnicowa wielkość liter): `iothubname.azure-devices.net/DeviceId`

Hasło (token SAS można wygenerować za pomocą polecenia rozszerzenia interfejsu wiersza polecenia [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)lub Azure IoT Tools [dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Zestawy [SDK usługi Azure IoT automatycznie](iot-hub-devguide-sdks.md) generują tokeny podczas nawiązywania połączenia z usługą. W niektórych przypadkach zestawy SDK usługi Azure IoT nie obsługują wszystkich protokołów ani wszystkich metod uwierzytelniania.

### <a name="special-considerations-for-sasl-plain"></a>Specjalne zagadnienia dotyczące SASL PLAIN

W przypadku SASL PLAIN protokołu AMQP klient łączący się z centrum IoT może używać jednego tokenu dla każdego połączenia TCP. Po wygaśnięciu tokenu połączenie TCP rozłącza się z usługą i wyzwala ponowne połączenie. To zachowanie, chociaż nie jest problematyczne dla aplikacji back-end, jest szkodliwe dla aplikacji urządzenia z następujących powodów:

* Bramy zazwyczaj łączą się w imieniu wielu urządzeń. W przypadku SASL PLAIN muszą utworzyć odrębne połączenie TCP dla każdego urządzenia łączącego się z centrum IoT. Ten scenariusz znacznie zwiększa zużycie energii i zasobów sieciowych oraz zwiększa opóźnienie każdego połączenia urządzenia.

* Zwiększone użycie zasobów do ponownego nawiązywania połączenia po każdym wygaśnięciu tokenu ma negatywny wpływ na urządzenia z ograniczeniami zasobów.

## <a name="scope-iot-hub-level-credentials"></a>Zakres poświadczeń na poziomie centrum IoT

Zakres zasad zabezpieczeń na poziomie centrum IoT można określić, tworząc tokeny z ograniczonymi uri zasobów. Na przykład punkt końcowy do wysyłania komunikatów z urządzenia do chmury z urządzenia to **/devices/{deviceId}/messages/events.** Możesz również użyć zasad dostępu współdzielonych na poziomie centrum IoT z uprawnieniami **DeviceConnect,** aby podpisać token, którego identyfikator resourceURI to **/devices/{deviceId}**. Takie podejście powoduje utworzenie tokenu, który może być używany tylko do wysyłania komunikatów w imieniu urządzenia **deviceId.**

Ten mechanizm jest podobny do [zasad Event Hubs wydawcy](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)i umożliwia implementowanie niestandardowych metod uwierzytelniania.

## <a name="security-tokens"></a>Tokeny zabezpieczające

IoT Hub używa tokenów zabezpieczających do uwierzytelniania urządzeń i usług, aby uniknąć wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające mają ograniczoną ważność i zakres czasu. [Zestawy SDK usługi Azure IoT automatycznie](iot-hub-devguide-sdks.md) generują tokeny bez konieczności specjalnej konfiguracji. Niektóre scenariusze wymagają bezpośredniego generowania i używania tokenów zabezpieczających. Takie scenariusze obejmują:

* Bezpośrednie użycie powierzchni MQTT, AMQP lub HTTPS.

* Implementacja wzorca usługi tokenu, zgodnie z objaśnieniami w [tesłudze Niestandardowe uwierzytelnianie urządzeń.](iot-hub-devguide-security.md#custom-device-and-module-authentication)

IoT Hub umożliwia również uwierzytelnianie urządzeń za pomocą usługi IoT Hub przy użyciu [certyfikatów X.509.](iot-hub-devguide-security.md#supported-x509-certificates)

### <a name="security-token-structure"></a>Struktura tokenu zabezpieczającego

Tokeny zabezpieczające są służące do udzielania ograniczonych w czasie dostępu do urządzeń i usług do określonych funkcji w IoT Hub. Aby uzyskać autoryzację do nawiązywania połączenia z IoT Hub, urządzenia i usługi muszą wysyłać tokeny zabezpieczające podpisane przy użyciu dostępu współdzielonych lub klucza symetrycznego. Te klucze są przechowywane z tożsamością urządzenia w rejestrze tożsamości.

Token podpisany za pomocą klucza dostępu współdzielonych udziela dostępu do wszystkich funkcji skojarzonych z uprawnieniami zasad dostępu współdzielonych. Token podpisany przy użyciu klucza symetrycznego tożsamości urządzenia przyznaje tylko uprawnienie **DeviceConnect** dla skojarzonej tożsamości urządzenia.

Token zabezpieczający ma następujący format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Oto oczekiwane wartości:

| Wartość | Opis |
| --- | --- |
| {signature} |Ciąg podpisu HMAC-SHA256 formularza: `{URL-encoded-resourceURI} + "\n" + expiry` . **Ważne:** klucz jest zdekodowany z base64 i używany jako klucz do wykonywania obliczeń HMAC-SHA256. |
| {resourceURI} |Prefiks identyfikatora URI (według segmentu) punktów końcowych, do których można uzyskać dostęp za pomocą tego tokenu, rozpoczynając od nazwy hosta centrum IoT (bez protokołu). Na przykład `myHub.azure-devices.net/devices/device1` |
| {expiry} |Ciągi UTF8 dla liczby sekund od epoki 00:00:00 UTC 1 stycznia 1970 r. |
| {Identyfikator URL zakodowany resourceURI} |Kodowanie adresu URL przy małej literze w polu URI zasobu |
| {policyName} |Nazwa zasad dostępu współdzielonych, do których odwołuje się ten token. Brak, jeśli token odwołuje się do poświadczeń rejestru urządzeń. |

**Uwaga w przypadku prefiksu:** prefiks identyfikatora URI jest obliczany według segmentu, a nie przez znak. Na przykład `/a/b` jest prefiksem dla , `/a/b/c` ale nie dla `/a/bc` .

Poniższy fragment Node.js przedstawia funkcję o nazwie **generateSasToken,** która oblicza token na podstawie danych wejściowych `resourceUri, signingKey, policyName, expiresInMins` . W następnych sekcjach szczegółowo opisano sposób inicjowania różnych danych wejściowych dla różnych przypadków użycia tokenu.

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

Dla porównania równoważny kod języka Python do generowania tokenu zabezpieczającego to:

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

Funkcja generowania tokenu zabezpieczającego w języku C# jest:

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
> Ponieważ czas ważności tokenu jest weryfikowany na IoT Hub, dryf na zegarze maszyny, która generuje token musi być minimalny.

### <a name="use-sas-tokens-in-a-device-app"></a>Używanie tokenów SAS w aplikacji urządzenia

Istnieją dwa sposoby uzyskiwania uprawnień **DeviceConnect** za pomocą usługi IoT Hub tokenami zabezpieczającymi: użycie [symetrycznego](#use-a-symmetric-key-in-the-identity-registry)klucza urządzenia z rejestru tożsamości lub użycie klucza dostępu [współdzielonych](#use-a-shared-access-policy).

Należy pamiętać, że wszystkie funkcje dostępne z urządzeń są domyślnie udostępniane w punktach końcowych z prefiksem `/devices/{deviceId}` .

> [!IMPORTANT]
> Jedynym sposobem uwierzytelnienia IoT Hub urządzenia jest użycie klucza symetrycznego tożsamości urządzenia. W przypadkach, gdy zasady dostępu współdzielonych są używane do uzyskiwania dostępu do funkcji urządzenia, rozwiązanie musi uznać składnik wystawiający token zabezpieczający za zaufany podskładnik.

Punkty końcowe dostępne dla urządzeń to (niezależnie od protokołu):

| Punkt końcowy | Funkcjonalność |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Wysyłanie komunikatów z urządzenia do chmury. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Odbieranie komunikatów z chmury do urządzenia. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Używanie klucza symetrycznego w rejestrze tożsamości

Podczas generowania tokenu przy użyciu klucza symetrycznego tożsamości urządzenia element policyName `skn` () tokenu zostanie pominięty.

Na przykład token utworzony w celu uzyskania dostępu do wszystkich funkcji urządzenia powinien mieć następujące parametry:

* zasób URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* klucz podpisywania: dowolny klucz symetryczny `{device id}` dla tożsamości,
* brak nazwy zasad,
* w dowolnym czasie wygaśnięcia.

Przykład użycia poprzedniej funkcji Node.js to:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Wynik, który przyznaje dostęp do wszystkich funkcji dla urządzenia device1, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Token SAS można wygenerować za pomocą polecenia rozszerzenia interfejsu wiersza polecenia [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)lub Azure IoT Tools [polecenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Korzystanie z zasad dostępu współdzielonych

Podczas tworzenia tokenu z zasad dostępu współdzielonych należy ustawić `skn` pole na nazwę zasad. Te zasady muszą przyznać **uprawnienie DeviceConnect.**

Dwa główne scenariusze użycia zasad dostępu współdzielonych w celu uzyskania dostępu do funkcji urządzenia to:

* [bramy protokołów w chmurze,](iot-hub-devguide-endpoints.md)
* [usługi tokenów](iot-hub-devguide-security.md#custom-device-and-module-authentication) używane do implementowania niestandardowych schematów uwierzytelniania.

Ponieważ zasady dostępu współdzielonych mogą potencjalnie udzielać dostępu w celu nawiązania połączenia jako dowolne urządzenie, ważne jest, aby podczas tworzenia tokenów zabezpieczających używać poprawnego URI zasobu. To ustawienie jest szczególnie ważne w przypadku usług tokenów, które muszą określać zakres tokenu dla określonego urządzenia przy użyciu wartości URI zasobu. Ten punkt jest mniej istotny w przypadku bram protokołów, ponieważ już mediatują ruch dla wszystkich urządzeń.

Na przykład usługa tokenu korzystająca ze wstępnie utworzonych zasad dostępu współdzielonych o nazwie **device** utworzy token o następujących parametrach:

* zasób URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* klucz podpisywania: jeden z kluczy `device` zasad,
* nazwa zasad: `device` ,
* dowolny czas wygaśnięcia.

Przykład użycia poprzedniej Node.js to:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, który przyznaje dostęp do wszystkich funkcji dla urządzenia device1, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Brama protokołu może używać tego samego tokenu dla wszystkich urządzeń, po prostu ustawiając dla tego zasobu URI `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Używanie tokenów zabezpieczających ze składników usługi

Składniki usługi mogą generować tokeny zabezpieczające tylko przy użyciu zasad dostępu współdzielonych, które przyznają odpowiednie uprawnienia, jak wyjaśniono wcześniej.

Poniżej podano funkcje usługi dostępne w punktach końcowych:

| Punkt końcowy | Funkcjonalność |
| --- | --- |
| `{iot hub host name}/devices` |Tworzenie, aktualizowanie, pobieranie i usuwanie tożsamości urządzeń. |
| `{iot hub host name}/messages/events` |Odbieranie komunikatów z urządzenia do chmury. |
| `{iot hub host name}/servicebound/feedback` |Otrzymywanie opinii na temat komunikatów z chmury do urządzenia. |
| `{iot hub host name}/devicebound` |Wysyłanie komunikatów z chmury do urządzeń. |

Na przykład usługa generująca przy użyciu wstępnie utworzonych zasad dostępu współdzielonych o nazwie **registryRead** utworzy token z następującymi parametrami:

* zasób URI: `{IoT hub name}.azure-devices.net/devices` ,
* klucz podpisywania: jeden z kluczy `registryRead` zasad,
* nazwa zasad: `registryRead` ,
* w dowolnym czasie wygaśnięcia.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, który udzieli dostępu do odczytu wszystkich tożsamości urządzeń, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Obsługiwane certyfikaty X.509

Możesz użyć dowolnego certyfikatu X.509, aby uwierzytelnić urządzenie za pomocą usługi IoT Hub, przesyłając odcisk palca certyfikatu lub urząd certyfikacji do usługi Azure IoT Hub. Uwierzytelnianie przy użyciu odcisków palca certyfikatu sprawdza, czy przedstawiony odcisk palca jest taki, jak skonfigurowany odcisk palca. Uwierzytelnianie przy użyciu urzędu certyfikacji weryfikuje łańcuch certyfikatów. Tak czy inaczej, uściślicie TLS wymaga, aby urządzenie było mieć prawidłowy certyfikat i klucz prywatny. Zapoznaj się ze specyfikacją protokołu TLS, aby uzyskać szczegółowe informacje, na przykład: [RFC 5246 — The Transport Layer Security (TLS) Protocol Version 1.2 (Protokół TLS w wersji 1.2).](https://tools.ietf.org/html/rfc5246/)

Obsługiwane certyfikaty obejmują:

* **Istniejący certyfikat X.509.** Z urządzeniem może już być skojarzony certyfikat X.509. Urządzenie może używać tego certyfikatu do uwierzytelniania za pomocą IoT Hub. Działa z uwierzytelnianiem odcisku palca lub urzędu certyfikacji. 

* **Certyfikat X.509 podpisany** przez urząd certyfikacji. Aby zidentyfikować urządzenie i uwierzytelnić je za pomocą IoT Hub, można użyć certyfikatu X.509 wygenerowanego i podpisanego przez urząd certyfikacji. Działa z uwierzytelnianiem odcisku palca lub urzędu certyfikacji.

* **Wygenerowany samodzielnie certyfikat X-509 z podpisem własnym.** Producent urządzenia lub in-house deployer może wygenerować te certyfikaty i przechowywać odpowiedni klucz prywatny (i certyfikat) na urządzeniu. W tym celu można użyć narzędzi, takich jak [OpenSSL](https://www.openssl.org/) i [narzędzie SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) systemu Windows. Działa tylko z uwierzytelnianiem odcisku palca.

Urządzenie może używać certyfikatu X.509 lub tokenu zabezpieczającego do uwierzytelniania, ale nie obu tych metod. W przypadku uwierzytelniania certyfikatu X.509 upewnij się, że masz strategię obsługi przechowania certyfikatów po wygaśnięciu istniejącego certyfikatu.

Następujące funkcje dla urządzeń, które korzystają z uwierzytelniania urzędu certyfikacji X.509, nie są jeszcze ogólnie dostępne, a tryb podglądu [musi być włączony:](iot-hub-preview-mode.md)

* HTTPS, MQTT za pośrednictwem protokołów WebSockets i AMQP za pośrednictwem protokołów WebSockets.
* Przekazywanie plików (wszystkie protokoły).

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu urzędu certyfikacji, zobacz [Device Authentication using X.509 CA Certificates](iot-hub-x509ca-overview.md)(Uwierzytelnianie urządzeń przy użyciu certyfikatów X.509 urzędu certyfikacji). Aby uzyskać informacje na temat przekazywania i weryfikowania urzędu certyfikacji w centrum IoT, zobacz Konfigurowanie zabezpieczeń [X.509](iot-hub-security-x509-get-started.md)w centrum Azure IoT.

### <a name="register-an-x509-certificate-for-a-device"></a>Rejestrowanie certyfikatu X.509 dla urządzenia

Zestaw [SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) dla języka C# (wersja 1.0.8 lub nowsza) obsługuje rejestrowanie urządzenia, które używa certyfikatu X.509 do uwierzytelniania. Inne interfejsy API, takie jak import/eksport urządzeń, również obsługują certyfikaty X.509.

Możesz również użyć polecenia rozszerzenia interfejsu wiersza polecenia [az iot hub device-identity,](/cli/azure/ext/azure-iot/iot/hub/device-identity) aby skonfigurować certyfikaty X.509 dla urządzeń.

### <a name="c-support"></a>Obsługa języka C \#

Klasa **RegistryManager** zapewnia programowy sposób rejestrowania urządzenia. W szczególności metody **AddDeviceAsync** i **UpdateDeviceAsync** umożliwiają zarejestrowanie i zaktualizowanie urządzenia w IoT Hub tożsamości. Te dwie metody przyjmą **wystąpienie** urządzenia jako dane wejściowe. Klasa **Device** zawiera właściwość **Authentication,** która umożliwia określenie podstawowych i pomocniczych odcisków palca certyfikatu X.509. Odcisk palca reprezentuje skrót SHA256 certyfikatu X.509 (przechowywany przy użyciu kodowania binarnego DER). Możesz określić podstawowy odcisk palca, pomocniczy odcisk palca lub oba te typy. Podstawowe i pomocnicze odciski palca są obsługiwane w celu obsługi scenariuszy przechowania certyfikatów.

Oto przykładowy fragment kodu języka C do rejestrowania urządzenia przy użyciu odcisku palca certyfikatu \# X.509:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Używanie certyfikatu X.509 podczas operacji w czasie działania

Zestaw [SDK urządzenia usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) dla platformy .NET (wersja 1.0.11 lub nowsza) obsługuje korzystanie z certyfikatów X.509.

### <a name="c-support"></a>Obsługa języka C \#

Klasa **DeviceAuthenticationWithX509Certificate** obsługuje tworzenie **wystąpień DeviceClient** przy użyciu certyfikatu X.509. Certyfikat X.509 musi być w formacie PFX (nazywanym również PKCS #12), który zawiera klucz prywatny.

Oto przykładowy fragment kodu:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Niestandardowe uwierzytelnianie urządzeń i modułów

Rejestru tożsamości usługi IoT Hub [można](iot-hub-devguide-identity-registry.md) użyć do skonfigurowania poświadczeń zabezpieczeń dla urządzenia/modułu i kontroli dostępu przy użyciu [tokenów](iot-hub-devguide-security.md#security-tokens). Jeśli rozwiązanie IoT ma już niestandardowy rejestr tożsamości i/lub schemat uwierzytelniania, rozważ utworzenie usługi *tokenu* w celu zintegrowania tej infrastruktury z IoT Hub. W ten sposób możesz użyć innych funkcji IoT w swoim rozwiązaniu.

Usługa tokenu jest niestandardową usługą w chmurze. Używa ona zasad IoT Hub *dostępu* współdzielonych z uprawnieniami  **DeviceConnect** lub **ModuleConnect** do tworzenia *tokenów* o zakresie urządzenia lub modułu. Te tokeny umożliwiają urządzeniu i modułowi nawiązywanie połączenia z centrum IoT.

![Kroki wzorca usługi tokenu](./media/iot-hub-devguide-security/tokenservice.png)

Poniżej podano główne kroki wzorca usługi tokenu:

1. Utwórz zasady IoT Hub dostępu współdzielone przy użyciu uprawnień **DeviceConnect** lub **ModuleConnect** dla centrum IoT. Te zasady można utworzyć w Azure Portal [lub](https://portal.azure.com) programowo. Usługa tokenu używa tych zasad do podpisywania tokenów, które tworzy.

2. Gdy urządzenie/moduł musi uzyskać dostęp do centrum IoT, żąda podpisanego tokenu z usługi tokenu. Urządzenie może uwierzytelniać się za pomocą niestandardowego rejestru tożsamości/schematu uwierzytelniania, aby określić tożsamość urządzenia/modułu używaną przez usługę tokenu do utworzenia tokenu.

3. Usługa tokenu zwraca token. Token jest tworzony przy użyciu lub jako , przy użyciu funkcji jako uwierzytelnionego urządzenia lub jako `/devices/{deviceId}` `/devices/{deviceId}/module/{moduleId}` `resourceURI` `deviceId` `moduleId` uwierzytelniania modułu. Usługa tokenu używa zasad dostępu współdzielonych do konstruowania tokenu.

4. Urządzenie/moduł używa tokenu bezpośrednio w centrum IoT.

> [!NOTE]
> Możesz użyć klasy .NET [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) lub klasy [Java IotHubServiceSasToken,](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) aby utworzyć token w usłudze tokenu.

Usługa tokenu może ustawić wygaśnięcie tokenu zgodnie z potrzebami. Po wygaśnięciu tokenu centrum IoT przejmuje połączenie urządzenia/modułu. Następnie urządzenie/moduł musi zażądać nowego tokenu z usługi tokenu. Krótki czas wygaśnięcia zwiększa obciążenie zarówno urządzenia/modułu, jak i usługi tokenu.

Aby urządzenie/moduł łączył się z centrum, nadal musisz dodać go do rejestru tożsamości usługi IoT Hub — nawet jeśli do nawiązywania połączenia jest on przy użyciu tokenu, a nie klucza. W związku z tym można nadal używać kontroli dostępu dla 1 urządzenia/modułu, włączając lub wyłączając tożsamości urządzeń/modułów w [rejestrze tożsamości.](iot-hub-devguide-identity-registry.md) Takie podejście zmniejsza ryzyko związane z używaniem tokenów z długim czasem wygaśnięcia.

### <a name="comparison-with-a-custom-gateway"></a>Porównanie z bramą niestandardową

Wzorzec usługi tokenu to zalecany sposób implementacji niestandardowego rejestru tożsamości/schematu uwierzytelniania przy użyciu IoT Hub. Ten wzorzec jest zalecany, IoT Hub nadal obsługuje większość ruchu rozwiązania. Jeśli jednak schemat uwierzytelniania niestandardowego jest tak połączony z protokołem, może być konieczne, aby brama niestandardowa przetwarzała cały ruch.  Przykładem takiego scenariusza jest użycie kluczy [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc4279)i kluczy wstępnych (PSK). Aby uzyskać więcej informacji, zobacz [artykuł brama](iot-hub-protocol-gateway.md) protokołu.

## <a name="reference-topics"></a>Tematy referencyjne:

Poniższe tematy referencyjne zawierają więcej informacji na temat kontrolowania dostępu do centrum IoT.

## <a name="iot-hub-permissions"></a>IoT Hub uprawnienia

W poniższej tabeli wymieniono uprawnienia, których można użyć do kontrolowania dostępu do centrum IoT.

| Uprawnienie | Uwagi |
| --- | --- |
| **RegistryRead** |Przyznaje dostęp do odczytu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz Identity registry ( [Rejestr tożsamości).](iot-hub-devguide-identity-registry.md) <br/>To uprawnienie jest używane przez usługi w chmurze na zadomowieniach. |
| **RegistryReadWrite** |Przyznaje dostęp do odczytu i zapisu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz Identity registry ( [Rejestr tożsamości).](iot-hub-devguide-identity-registry.md) <br/>To uprawnienie jest używane przez usługi w chmurze na zadomowieniach. |
| **ServiceConnect** |Przyznaje dostęp do punktów końcowych komunikacji i monitorowania dla usług w chmurze. <br/>Przyznaje uprawnienia do odbierania komunikatów z urządzenia do chmury, wysyłania komunikatów z chmury do urządzeń i pobierania odpowiednich potwierdzeń dostarczenia. <br/>Przyznaje uprawnienia do pobierania potwierdzeń dostarczenia dla przekazywania plików. <br/>Przyznaje uprawnienia dostępu do bliźniaczych reprezentacji w celu aktualizowania tagów i żądanych właściwości, pobierania zgłaszanych właściwości i uruchamiania zapytań. <br/>To uprawnienie jest używane przez usługi w chmurze na zabłysku. |
| **DeviceConnect** |Przyznaje dostęp do punktów końcowych dla urządzeń. <br/>Przyznaje uprawnienia do wysyłania komunikatów z urządzenia do chmury i odbierania komunikatów z chmury do urządzeń. <br/>Przyznaje uprawnienia do wykonywania przekazywania plików z urządzenia. <br/>Przyznaje uprawnienia do odbierania powiadomień o żądanej właściwości bliźniaczej reprezentacji urządzenia i aktualizowania zgłoszonych właściwości bliźniaczej reprezentacji urządzenia. <br/>Przyznaje uprawnienia do wykonywania przekazywania plików. <br/>To uprawnienie jest używane przez urządzenia. |

## <a name="additional-reference-material"></a>Dodatkowy materiał referencyjny

Inne tematy referencyjne w przewodniku IoT Hub dla deweloperów obejmują:

* [IoT Hub końcowe opisują](iot-hub-devguide-endpoints.md) różne punkty końcowe, które każde centrum IoT udostępnia dla operacji w czasie działania i zarządzania.

* [W temacie Throttling and quotas](iot-hub-devguide-quotas-throttling.md) (Ograniczanie przepustowości i przydziały) opisano przydziały i zachowania ograniczania przepustowości, które mają IoT Hub usługi.

* [Zestawy SDK urządzeń](iot-hub-devguide-sdks.md) i usług Azure IoT to lista różnych zestawów SDK języka, których można używać podczas tworzenia aplikacji dla urządzeń i usług, które współdziałają z IoT Hub.

* [IoT Hub języka zapytań opisano](iot-hub-devguide-query-language.md) język zapytań, który umożliwia pobieranie informacji IoT Hub o bliźniaczych reprezentacji urządzenia i zadaniach.

* [IoT Hub MQTT](iot-hub-mqtt-support.md) zawiera więcej informacji na temat IoT Hub obsługi protokołu MQTT.

* Więcej informacji na temat uwierzytelniania TLS można znaleźć w dokumencie [RFC 5246 — The Transport Layer Security (TLS) Protocol Version 1.2](https://tools.ietf.org/html/rfc5246/) (Protokół TLS w wersji 1.2).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak kontrolować dostęp do IoT Hub, mogą Cię zainteresować następujące tematy IoT Hub dewelopera:

* [Synchronizowanie stanu i konfiguracji za pomocą bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md)
* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)
* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Jeśli chcesz wypróbować niektóre pojęcia opisane w tym artykule, zobacz następujące samouczki IoT Hub samouczków:

* [Rozpoczynanie pracy z usługą Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Jak wysyłać komunikaty z chmury do urządzenia za pomocą IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Jak przetwarzać IoT Hub z urządzenia do chmury](tutorial-routing.md)
