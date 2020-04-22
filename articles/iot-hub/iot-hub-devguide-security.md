---
title: Poznaj zabezpieczenia usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — jak kontrolować dostęp do Usługi IoT Hub dla aplikacji na urządzenia i aplikacji zaplecza. Zawiera informacje o tokenach zabezpieczających i obsłudze certyfikatów X.509.
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
ms.openlocfilehash: b7f9ac7e6e7049a3b744151bc9cb05115fbac935
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729228"
---
# <a name="control-access-to-iot-hub"></a>Kontrola dostępu do centrum IoT Hub

W tym artykule opisano opcje zabezpieczania centrum IoT Hub. Centrum IoT hub używa *uprawnień* do udzielania dostępu do każdego punktu końcowego centrum IoT. Uprawnienia ograniczają dostęp do centrum IoT na podstawie funkcji.

W tym artykule przedstawiono:

* Różne uprawnienia, które można udzielić urządzeniu lub aplikacji zaplecza, aby uzyskać dostęp do centrum IoT Hub.
* Proces uwierzytelniania i tokeny używane do weryfikacji uprawnień.
* Jak zakres poświadczeń, aby ograniczyć dostęp do określonych zasobów.
* Obsługa ioT Hub dla certyfikatów X.509.
* Niestandardowe mechanizmy uwierzytelniania urządzeń, które używają istniejących rejestrów tożsamości urządzenia lub schematów uwierzytelniania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Musisz mieć odpowiednie uprawnienia, aby uzyskać dostęp do dowolnego punktu końcowego Usługi IoT Hub. Na przykład urządzenie musi zawierać token zawierający poświadczenia zabezpieczeń wraz z każdą wiadomością, którą wysyła do Usługi IoT Hub.

## <a name="access-control-and-permissions"></a>Kontrola dostępu i uprawnienia

[Uprawnienia](#iot-hub-permissions) można przyznać w następujący sposób:

* **Zasady dostępu współdzielonego na poziomie centrum IoT**. Zasady dostępu współdzielonego mogą przyznawać dowolną [kombinację uprawnień](#iot-hub-permissions). Zasady można definiować w [witrynie Azure portal](https://portal.azure.com), programowo przy użyciu [interfejsów API REST zasobów usługi IoT Hub](/rest/api/iothub/iothubresource)lub przy użyciu interfejsu wiersza polecenia zasad [az iot hub.](/cli/azure/iot/hub/policy?view=azure-cli-latest) Nowo utworzone centrum IoT hub ma następujące zasady domyślne:
  
  | Zasady dostępu współdzielonego | Uprawnienia |
  | -------------------- | ----------- |
  | iothubowner | Wszystkie uprawnienia |
  | usługa | **Uprawnienia ServiceConnect** |
  | urządzenie | Uprawnienia **DeviceConnect** |
  | rejestrCzytaj | **Uprawnienia registryread** |
  | registryReadWrite | **Uprawnienia RegistryRead** i **RegistryWrite** |

* **Poświadczenia zabezpieczeń na urządzenie**. Każde Centrum IoT zawiera [rejestr tożsamości](iot-hub-devguide-identity-registry.md) Dla każdego urządzenia w tym rejestrze tożsamości można skonfigurować poświadczenia zabezpieczeń, które przyznają uprawnienia **DeviceConnect** o zakresie do odpowiednich punktów końcowych urządzenia.

Na przykład w typowym rozwiązaniu IoT:

* Składnik zarządzania urządzeniami używa *zasad registryReadWrite.*
* Składnik procesora zdarzeń używa zasad *usługi.*
* Składnik logiki biznesowej urządzenia w czasie wykonywania używa zasad *usługi.*
* Poszczególne urządzenia łączą się przy użyciu poświadczeń przechowywanych w rejestrze tożsamości centrum IoT.

> [!NOTE]
> Szczegółowe informacje można znaleźć w [uprawnieniach.](#iot-hub-permissions)

## <a name="authentication"></a>Authentication

Usługa Azure IoT Hub udziela dostępu do punktów końcowych, weryfikując token względem zasad dostępu współużytkowanego i poświadczeń zabezpieczeń rejestru tożsamości.

Poświadczenia zabezpieczeń, takie jak klucze symetryczne, nigdy nie są wysyłane za cały okres.

> [!NOTE]
> Dostawca zasobów usługi Azure IoT Hub jest zabezpieczony za pośrednictwem subskrypcji platformy Azure, podobnie jak wszyscy dostawcy w [usłudze Azure Resource Manager.](../azure-resource-manager/management/overview.md)

Aby uzyskać więcej informacji na temat konstruowania i używania tokenów zabezpieczających, zobacz [Tokeny zabezpieczające usługi IoT Hub](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Szczegóły protokołu

Każdy obsługiwany protokół, taki jak MQTT, AMQP i HTTPS, transportuje tokeny na różne sposoby.

Podczas korzystania z MQTT pakiet CONNECT ma identyfikator `{iothubhostname}/{deviceId}` deviceId jako identyfikator klienta w polu Nazwa użytkownika i token Sygnatury dostępu Współdzielonego w polu Hasło. `{iothubhostname}`powinna być pełna nazwa C hub IoT hub (na przykład contoso.azure-devices.net).

W przypadku korzystania z [usługi AMQP,](https://www.amqp.org/)Centrum IoT obsługuje [usługi SASL PLAIN](https://tools.ietf.org/html/rfc4616) i zabezpieczenia oparte na [roszczeniach AMQP.](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)

Jeśli używasz zabezpieczeń opartych na oświadczeniach AMQP, standard określa sposób przesyłania tych tokenów.

W przypadku SASL PLAIN **nazwa użytkownika** może być:

* `{policyName}@sas.root.{iothubName}`jeśli używa tokenów na poziomie centrum IoT.
* `{deviceId}@sas.{iothubname}`jeśli używa tokenów o zakresie urządzenia.

W obu przypadkach pole hasła zawiera token, zgodnie z opisem w [tokenach zabezpieczających Usługi IoT Hub.](iot-hub-devguide-security.md#security-tokens)

Protokół HTTPS implementuje uwierzytelnianie przez dołączenie prawidłowego tokenu w nagłówku żądania **autoryzacji.**

#### <a name="example"></a>Przykład

Nazwa użytkownika (DeviceId jest rozróżniana wielkość liter):`iothubname.azure-devices.net/DeviceId`

Hasło (token sygnatury dostępu Współdzielonego można wygenerować za pomocą polecenia rozszerzenia interfejsu wiersza polecenia [az iot hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)lub [narzędzia Azure IoT Tools for Visual Studio Code):](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Zestaw [SDK usługi Azure IoT](iot-hub-devguide-sdks.md) automatycznie generuje tokeny podczas łączenia się z usługą. W niektórych przypadkach zestawów SDK usługi Azure IoT nie obsługują wszystkich protokołów ani wszystkich metod uwierzytelniania.

### <a name="special-considerations-for-sasl-plain"></a>Uwagi szczególne dotyczące SASL PLAIN

Podczas korzystania z SASL PLAIN z protokołu AMQP klient łączący się z centrum IoT hub może używać pojedynczego tokenu dla każdego połączenia TCP. Po wygaśnięciu tokenu połączenie TCP rozłącza się z usługą i wyzwala ponowne połączenie. To zachowanie, choć nie jest problematyczne dla aplikacji zaplecza, jest szkodliwe dla aplikacji urządzenia z następujących powodów:

* Bramy zwykle łączą się w imieniu wielu urządzeń. Korzystając z usługi SASL PLAIN, muszą utworzyć odrębne połączenie TCP dla każdego urządzenia łączącego się z koncentratorem IoT. Ten scenariusz znacznie zwiększa zużycie energii i zasobów sieciowych i zwiększa opóźnienie każdego połączenia urządzenia.

* Urządzenia ograniczone zasobami są niekorzystnie dotknięte zwiększonym użyciem zasobów do ponownego połączenia po każdym wygaśnięciu tokenu.

## <a name="scope-iot-hub-level-credentials"></a>Poświadczenia na poziomie usługi IoT zakresu

Zasady zabezpieczeń na poziomie centrum IoT można rozpojąć, tworząc tokeny z identyfikatorem URI zasobu z ograniczeniami. Na przykład punktem końcowym do wysyłania komunikatów z urządzenia do chmury z urządzenia jest **/devices/{deviceId}/messages/events**. Można również użyć zasady dostępu współużytkującego na poziomie centrum IoT z uprawnieniami **DeviceConnect** do podpisywania tokenu, którego identyfikator RESOURCEURI to **/devices/{deviceId}**. Takie podejście tworzy token, który jest tylko do wysyłania wiadomości w imieniu **deviceId**urządzenia.

Ten mechanizm jest podobny do [zasad wydawcy usługi Event Hubs](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)i umożliwia implementowanie metod uwierzytelniania niestandardowego.

## <a name="security-tokens"></a>Tokeny zabezpieczające

Usługa IoT Hub używa tokenów zabezpieczających do uwierzytelniania urządzeń i usług, aby uniknąć wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczone w czasie ważności i zakresu. [Zestaw SDK usługi Azure IoT](iot-hub-devguide-sdks.md) automatycznie generuje tokeny bez konieczności konieczności specjalnej konfiguracji. Niektóre scenariusze wymagają bezpośredniego generowania i używania tokenów zabezpieczających. Takie scenariusze obejmują:

* Bezpośrednie wykorzystanie powierzchni MQTT, AMQP lub HTTPS.

* Implementacja wzorca usługi tokenu, jak wyjaśniono w [uwierzytelnianiu urządzeń niestandardowych](iot-hub-devguide-security.md#custom-device-and-module-authentication).

Centrum IoT Hub umożliwia również urządzeniom uwierzytelnienie za pomocą usługi IoT Hub przy użyciu [certyfikatów X.509.](iot-hub-devguide-security.md#supported-x509-certificates)

### <a name="security-token-structure"></a>Struktura tokenów zabezpieczających

Tokeny zabezpieczające są używane do udzielania ograniczonego czasowo dostępu do urządzeń i usług określonym funkcjom usługi IoT Hub. Aby uzyskać autoryzację do łączenia się z usługą IoT Hub, urządzenia i usługi muszą wysyłać tokeny zabezpieczające podpisane za pomocą klucza udostępnionego lub symetrycznego. Te klucze są przechowywane z tożsamością urządzenia w rejestrze tożsamości.

Token podpisany przy za pomocą klucza dostępu udostępnionego udziela dostępu do wszystkich funkcji skojarzonych z uprawnieniami zasad dostępu współdzielonego. Token podpisany przy za pomocą klucza symetrycznego tożsamości urządzenia tylko udziela **deviceconnect** uprawnienia dla tożsamości skojarzonego urządzenia.

Token zabezpieczający ma następujący format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Oto oczekiwane wartości:

| Wartość | Opis |
| --- | --- |
| {podpis} |Ciąg podpisu HMAC-SHA256 formularza: `{URL-encoded-resourceURI} + "\n" + expiry`. **Ważne:** Klucz jest dekodowany z base64 i używany jako klucz do wykonywania obliczeń HMAC-SHA256. |
| {resourceURI} |Prefiks identyfikatora URI (według segmentu) punktów końcowych, do których można uzyskać dostęp za pomocą tego tokenu, począwszy od nazwy hosta centrum IoT hub (bez protokołu). Na przykład: `myHub.azure-devices.net/devices/device1` |
| {wygaśnięcie} |Ciągi UTF8 przez kilka sekund od 00:00:00 UTC w dniu 1 stycznia 1970. |
| {IdentyfikatorURI zakodowany w adresie URL} |Małe wielkości kodowania adresów URL mniejszego identyfikatora URI zasobu wielkości liter |
| {nazwa_polityki} |Nazwa zasady dostępu współdzielonego, do której odnosi się ten token. Nieobecny, jeśli token odwołuje się do poświadczeń rejestru urządzeń. |

**Uwaga dotycząca prefiksu:** Prefiks identyfikatora URI jest obliczany według segmentu, a nie według znaku. Na `/a/b` przykład jest prefiksem dla `/a/b/c` , ale nie dla `/a/bc`.

Poniższy fragment kodu Node.js pokazuje funkcję o nazwie **generateSasToken,** która `resourceUri, signingKey, policyName, expiresInMins`oblicza token z danych wejściowych . W następnych sekcjach szczegółowo opisano, jak zainicjować różne dane wejściowe dla różnych przypadków użycia tokenu.

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

Dla porównania, równoważny kod języka Python do generowania tokenu zabezpieczającego jest:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print sign_key
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

Poniżej przedstawiono instrukcje instalacji wymagań wstępnych.

[!INCLUDE [Iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]


Funkcja w języku C# do generowania tokenu zabezpieczającego jest:

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


> [!NOTE]
> Ponieważ ważność czasu tokenu jest sprawdzana na komputerach Usługi IoT Hub, dryf na zegarze komputera, który generuje token musi być minimalny.

### <a name="use-sas-tokens-in-a-device-app"></a>Używanie tokenów sygnatury dostępu Współdzielonego w aplikacji na urządzenia

Istnieją dwa sposoby uzyskiwania uprawnień **DeviceConnect** za pomocą usługi IoT Hub za pomocą tokenów zabezpieczających: użyj [symetrycznego klucza urządzenia z rejestru tożsamości](#use-a-symmetric-key-in-the-identity-registry)lub użyj [udostępnionego klucza dostępu.](#use-a-shared-access-policy)

Należy pamiętać, że wszystkie funkcje dostępne z urządzeń są `/devices/{deviceId}`widoczne zgodnie z projektem w punktach końcowych z prefiksem .

> [!IMPORTANT]
> Jedynym sposobem, w jaki Usługa IoT Hub uwierzytelnia określone urządzenie, jest użycie klucza symetrycznego tożsamości urządzenia. W przypadkach, gdy zasady dostępu współdzielonego jest używany do uzyskiwania dostępu do funkcji urządzenia, rozwiązanie należy wziąć pod uwagę składnik emitujący token zabezpieczający jako zaufany podskładnik.

Punkty końcowe skierowane do urządzenia są (niezależnie od protokołu):

| Endpoint | Funkcjonalność |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Wysyłanie wiadomości z urządzenia do chmury. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Odbieranie komunikatów z chmury do urządzenia. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Używanie klucza symetrycznego w rejestrze tożsamości

Podczas korzystania z klucza symetrycznego tożsamości urządzenia do generowania tokenu, policyName (`skn`) element tokenu jest pomijany.

Na przykład token utworzony w celu uzyskania dostępu do wszystkich funkcji urządzenia powinien mieć następujące parametry:

* identyfikator URI `{IoT hub name}.azure-devices.net/devices/{device id}`zasobu: ,
* klucz podpisywania: dowolny klucz `{device id}` symetryczny dla tożsamości,
* brak nazwy zasad,
* w dowolnym momencie ważności.

Przykładem użycia poprzedniej funkcji Node.js będzie:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Rezultatem, który daje dostęp do wszystkich funkcji urządzenia1, byłoby:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Możliwe jest wygenerowanie tokenu Sygnatury dostępu Współdzielonego za pomocą polecenia rozszerzenia interfejsu wiersza polecenia [az iot hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)lub [narzędzia Azure IoT Tools for Visual Studio Code.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="use-a-shared-access-policy"></a>Korzystanie z zasad dostępu współdzielonego

Podczas tworzenia tokenu z zasad dostępu `skn` współdzielonego należy ustawić to pole na nazwę zasad. Ta zasada musi udzielić uprawnienia **DeviceConnect.**

Dwa główne scenariusze korzystania z zasad dostępu współdzielonego w celu uzyskania dostępu do funkcji urządzenia to:

* [bramy protokołów w chmurze](iot-hub-devguide-endpoints.md),
* [usługi tokenu](iot-hub-devguide-security.md#custom-device-and-module-authentication) używane do implementowania schematów uwierzytelniania niestandardowego.

Ponieważ zasady dostępu współdzielonego mogą potencjalnie udzielić dostępu do łączenia się jako dowolne urządzenie, podczas tworzenia tokenów zabezpieczających należy użyć poprawnego identyfikatora URI zasobu. To ustawienie jest szczególnie ważne dla usług tokenu, które mają zakres tokenu do określonego urządzenia przy użyciu identyfikatora URI zasobu. Ten punkt jest mniej istotne dla bram protokołu, ponieważ są one już pośrednicowania ruchu dla wszystkich urządzeń.

Na przykład usługa tokenu przy użyciu wstępnie utworzonej zasady dostępu współdzielonego o nazwie **device** utworzy token z następującymi parametrami:

* identyfikator URI `{IoT hub name}.azure-devices.net/devices/{device id}`zasobu: ,
* klucz do podpisywania: `device` jeden z kluczy polityki,
* nazwa zasad: `device`,
* w dowolnym momencie ważności.

Przykładem użycia poprzedniej funkcji Node.js będzie:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Rezultatem, który daje dostęp do wszystkich funkcji urządzenia1, byłoby:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Brama protokołu może używać tego samego tokenu dla `myhub.azure-devices.net/devices`wszystkich urządzeń po prostu ustawienie identyfikatora URI zasobu na .

### <a name="use-security-tokens-from-service-components"></a>Używanie tokenów zabezpieczających ze składników usługi

Składniki usługi można wygenerować tylko tokeny zabezpieczające przy użyciu zasad dostępu współdzielonego, przyznając odpowiednie uprawnienia, jak wyjaśniono wcześniej.

Oto funkcje usługi udostępniane w punktach końcowych:

| Endpoint | Funkcjonalność |
| --- | --- |
| `{iot hub host name}/devices` |Tworzenie, aktualizowanie, pobieranie i usuwanie tożsamości urządzeń. |
| `{iot hub host name}/messages/events` |Odbieranie wiadomości z urządzenia do chmury. |
| `{iot hub host name}/servicebound/feedback` |Otrzymuj opinie dotyczące wiadomości z chmury do urządzenia. |
| `{iot hub host name}/devicebound` |Wysyłanie komunikatów z chmury do urządzenia. |

Na przykład usługa generująca przy użyciu wstępnie utworzonej zasady dostępu współdzielonego o nazwie **registryRead** utworzy token z następującymi parametrami:

* identyfikator URI `{IoT hub name}.azure-devices.net/devices`zasobu: ,
* klucz do podpisywania: `registryRead` jeden z kluczy polityki,
* nazwa zasad: `registryRead`,
* w dowolnym momencie ważności.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Wynik, który przyznałby dostęp do wszystkich tożsamości urządzeń, będzie:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Obsługiwane certyfikaty X.509

Dowolnego certyfikatu X.509 można użyć do uwierzytelnienia urządzenia za pomocą usługi IoT Hub, przekazując do usługi Azure IoT Hub odcisk palca certyfikatu lub urząd certyfikacji (CA). Uwierzytelnianie przy użyciu odcisków palców certyfikatu sprawdza, czy przedstawiony odcisk palca pasuje do skonfigurowany odcisk palca. Uwierzytelnianie przy użyciu urzędu certyfikacji sprawdza poprawność łańcucha certyfikatów. Tak czy inaczej uzgadnianie TLS wymaga urządzenia, aby mieć prawidłowy certyfikat i klucz prywatny. Szczegółowe informacje można znaleźć w specyfikacji protokołu TLS, na przykład: [RFC 5246 — Protokół TLS (Transport Layer Security) w wersji 1.2](https://tools.ietf.org/html/rfc5246/).

Obsługiwane certyfikaty obejmują:

* **Istniejący certyfikat X.509**. Urządzenie może mieć już skojarzony certyfikat X.509. Urządzenie może używać tego certyfikatu do uwierzytelniania za pomocą usługi IoT Hub. Działa z odciskiem palca lub uwierzytelnianiem urzędu certyfikacji. 

* **Certyfikat X.509 podpisany przez urząd certyfikacji**. Aby zidentyfikować urządzenie i uwierzytelnić je za pomocą usługi IoT Hub, można użyć certyfikatu X.509 wygenerowanego i podpisanego przez urząd certyfikacji (CA). Działa z odciskiem palca lub uwierzytelnianiem urzędu certyfikacji.

* **Samodzielnie wygenerowany i podpisany samodzielnie certyfikat X-509**. Producent urządzenia lub wdrażający urządzenia może wygenerować te certyfikaty i przechowywać odpowiedni klucz prywatny (i certyfikat) na urządzeniu. W tym celu można użyć narzędzi, takich jak [OpenSSL](https://www.openssl.org/) i [Windows SelfSignedCertificate.](/powershell/module/pkiclient/new-selfsignedcertificate) Działa tylko z uwierzytelnianiem odcisków palców. 

Urządzenie może używać certyfikatu X.509 lub tokenu zabezpieczającego do uwierzytelniania, ale nie obu.

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu urzędu certyfikacji, zobacz [Uwierzytelnianie urządzeń przy użyciu certyfikatów urzędu certyfikacji X.509](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Rejestrowanie certyfikatu X.509 dla urządzenia

Zestaw [SDK usługi Azure IoT dla języka C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (wersja 1.0.8+) obsługuje rejestrowanie urządzenia, które używa certyfikatu X.509 do uwierzytelniania. Inne interfejsy API, takie jak importowanie/eksport urządzeń, również obsługują certyfikaty X.509.

Można również użyć polecenia rozszerzenia interfejsu wiersza polecenia [az iot hub device-identity,](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) aby skonfigurować certyfikaty X.509 dla urządzeń.

### <a name="c-support"></a>C\# Wsparcie

**RegistryManager** Klasa zapewnia programowy sposób rejestracji urządzenia. W szczególności **AddDeviceAsync** i **UpdateDeviceAsync** metody umożliwiają rejestrowanie i aktualizowanie urządzenia w rejestrze tożsamości Usługi IoT Hub. Te dwie metody wziąć **Device wystąpienie** jako dane wejściowe. **Device** Klasa zawiera **Authentication** właściwość, która umożliwia określenie podstawowych i pomocniczych odcisków palców certyfikatu X.509. Odcisk palca reprezentuje skrót SHA256 certyfikatu X.509 (przechowywane przy użyciu binarnego kodowania DER). Można określić podstawowy odcisk palca lub pomocniczy odcisk palca lub oba te elementy. Podstawowe i pomocnicze odciski palców są obsługiwane do obsługi scenariuszy przerzucenia certyfikatu.

Oto przykładowy\# fragment kodu C, aby zarejestrować urządzenie przy użyciu odcisku palca certyfikatu X.509:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Używanie certyfikatu X.509 podczas operacji wyładowywowych

Zestaw [SDK urządzenia Usługi Azure IoT dla platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (wersja 1.0.11+) obsługuje korzystanie z certyfikatów X.509.

### <a name="c-support"></a>C\# Wsparcie

Klasa **DeviceAuthenticationWithX509Certificate** obsługuje tworzenie **deviceclient** wystąpień przy użyciu certyfikatu X.509. Certyfikat X.509 musi być w formacie PFX (zwanym także PKCS #12), który zawiera klucz prywatny.

Oto przykładowy fragment kodu:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Uwierzytelnianie niestandardowe urządzeń i modułów

Za pomocą [rejestru tożsamości](iot-hub-devguide-identity-registry.md) usługi IoT Hub można skonfigurować poświadczenia zabezpieczeń na urządzenie/moduł i kontrolę dostępu przy użyciu [tokenów](iot-hub-devguide-security.md#security-tokens). Jeśli rozwiązanie IoT ma już niestandardowy rejestr tożsamości i/lub schemat uwierzytelniania, należy rozważyć utworzenie *usługi tokenu* w celu zintegrowania tej infrastruktury z Centrum IoT. W ten sposób można użyć innych funkcji IoT w rozwiązaniu.

Usługa tokenu to niestandardowa usługa w chmurze. Używa *zasad dostępu współdzielonego* usługi IoT Hub z uprawnieniami **DeviceConnect** lub **ModuleConnect** do tworzenia tokenów *o zakresie urządzenia* lub *modułu.* Tokeny te umożliwiają urządzeniu i modułowi łączenie się z centrum IoT Hub.

![Kroki wzorca usługi tokenu](./media/iot-hub-devguide-security/tokenservice.png)

Oto główne kroki wzorca usługi tokenu:

1. Utwórz zasady dostępu współdzielonego usługi IoT Hub z uprawnieniami **DeviceConnect** lub **ModuleConnect** dla centrum IoT Hub. Tę zasadę można utworzyć w [witrynie Azure portal](https://portal.azure.com) lub programowo. Usługa tokenu używa tej zasady do podpisywania tokenów, które tworzy.

2. Gdy urządzenie/moduł musi uzyskać dostęp do centrum IoT hub, żąda podpisanego tokenu z usługi tokenu. Urządzenie może uwierzytelniać się przy użyciu niestandardowego schematu uwierzytelniania tożsamości w celu określenia tożsamości urządzenia/modułu używanego przez usługę tokenu do utworzenia tokenu.

3. Usługa tokenu zwraca token. Token jest tworzony `/devices/{deviceId}` przy `/devices/{deviceId}/module/{moduleId}` `resourceURI`użyciu `deviceId` lub jako , z `moduleId` jako urządzenie jest uwierzytelniony lub jako moduł jest uwierzytelniony. Usługa tokenu używa zasad dostępu współużytkowego do skonstruowania tokenu.

4. Urządzenie/moduł używa tokenu bezpośrednio z centrum IoT hub.

> [!NOTE]
> Do utworzenia tokenu w usłudze tokenu można użyć klasy [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) lub klasy Java [IotHubServiceSasaken.](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken)

Usługa tokenu można ustawić wygaśnięcia tokenu zgodnie z potrzebami. Po wygaśnięciu tokenu centrum IoT hub odcina połączenie urządzenia/modułu. Następnie device/module musi zażądać nowego tokenu z usługi tokenu. Krótki czas wygaśnięcia zwiększa obciążenie zarówno urządzenia/modułu, jak i usługi tokenu.

Aby urządzenie/moduł łączył się z koncentratorem, należy nadal dodać go do rejestru tożsamości usługi IoT Hub — nawet jeśli używa tokenu, a nie klucza do nawiązania połączenia. W związku z tym można nadal używać kontroli dostępu na urządzenie/na moduł, włączając lub wyłączając tożsamości urządzenia/modułu w [rejestrze tożsamości](iot-hub-devguide-identity-registry.md). Takie podejście zmniejsza ryzyko używania tokenów z długimi okresami wygaśnięcia.

### <a name="comparison-with-a-custom-gateway"></a>Porównanie z bramą niestandardową

Wzorzec usługi tokenu jest zalecanym sposobem zaimplementowania niestandardowego schematu rejestru tożsamości/uwierzytelniania za pomocą usługi IoT Hub. Ten wzorzec jest zalecane, ponieważ Usługa IoT Hub nadal obsługiwać większość ruchu rozwiązania. Jeśli jednak schemat uwierzytelniania niestandardowego jest tak powiązany z protokołem, może być wymagana *brama niestandardowa* do przetworzenia całego ruchu. Przykładem takiego scenariusza jest użycie [zabezpieczeń warstwy transportowej (TLS) i kluczy wstępnie udostępnionych (PSK).](https://tools.ietf.org/html/rfc4279) Aby uzyskać więcej informacji, zobacz artykuł [bramy protokołu.](iot-hub-protocol-gateway.md)

## <a name="reference-topics"></a>Tematy referencyjne:

Poniższe tematy referencyjne zawierają więcej informacji na temat kontrolowania dostępu do centrum IoT Hub.

## <a name="iot-hub-permissions"></a>Uprawnienia usługi IoT Hub

W poniższej tabeli wymieniono uprawnienia, których można używać do kontrolowania dostępu do centrum IoT Hub.

| Uprawnienie | Uwagi |
| --- | --- |
| **RejestrCzytaj** |Udziela dostępu do odczytu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [Rejestr tożsamości](iot-hub-devguide-identity-registry.md). <br/>To uprawnienie jest używane przez usługi w chmurze zaplecza. |
| **RegistryReadWrite** |Udziela dostępu do odczytu i zapisu do rejestru tożsamości. Aby uzyskać więcej informacji, zobacz [Rejestr tożsamości](iot-hub-devguide-identity-registry.md). <br/>To uprawnienie jest używane przez usługi w chmurze zaplecza. |
| **ServiceConnect (połączenie serwisowe)** |Zapewnia dostęp do komunikacji i monitorowania punktów końcowych opartych na usługach w chmurze. <br/>Udziela uprawnień do odbierania wiadomości z urządzenia do chmury, wysyłania wiadomości z chmury do urządzenia i pobierania odpowiednich potwierdzeń dostarczania. <br/>Udziela uprawnień do pobierania potwierdzeń dostarczania dla przekazywania plików. <br/>Udziela uprawnień dostępu do bliźniąt, aby zaktualizować tagi i żądane właściwości, pobrać zgłoszone właściwości i uruchomić kwerendy. <br/>To uprawnienie jest używane przez usługi w chmurze zaplecza. |
| **Połączenie urządzenia** |Udziela dostępu do punktów końcowych skierowanych do urządzenia. <br/>Udziela uprawnień do wysyłania wiadomości z urządzenia do chmury i odbierania komunikatów z chmury do urządzenia. <br/>Udziela uprawnień do przekazywania plików z urządzenia. <br/>Udziela uprawnień do odbierania bliźniaczej reprezentacji urządzenia żądanych powiadomień o właściwościach i aktualizowania bliźniaczych właściwości zgłoszonych urządzeń. <br/>Udziela uprawnień do przekazywania plików. <br/>To uprawnienie jest używane przez urządzenia. |

## <a name="additional-reference-material"></a>Dodatkowy materiał referencyjny

Inne tematy referencyjne w przewodniku dla deweloperów usługi IoT Hub obejmują:

* [Punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md) opisano różne punkty końcowe, które każdy centrum IoT udostępnia dla operacji wykonywania i zarządzania.

* [Ograniczanie i przydziały](iot-hub-devguide-quotas-throttling.md) opisuje przydziały i zachowania ograniczania przepustowości, które mają zastosowanie do usługi Centrum IoT.

* [Zestaw SDK urządzeń i usług Usługi Azure IoT](iot-hub-devguide-sdks.md) zawiera listę zestawów SDK w różnych językach, których można używać podczas tworzenia aplikacji na urządzenia i usługi, które współdziałają z centrum IoT Hub.

* [Język zapytań centrum IoT](iot-hub-devguide-query-language.md) zawiera opis języka zapytań, którego można użyć do pobierania informacji z Usługi IoT Hub o bliźniaczych urządzeń i zadaniach.

* [Obsługa protokołu MQTT w uężoniać w uiszczaprzestwu IoT Hub](iot-hub-mqtt-support.md) zawiera więcej informacji na temat obsługi protokołu MQTT w centrum IoT Hub.

* [RFC 5246 — protokół TLS (Transport Layer Security) w wersji 1.2](https://tools.ietf.org/html/rfc5246/) zawiera więcej informacji na temat uwierzytelniania TLS.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz instrukcje kontrolowania dostępu Usługi IoT Hub, możesz zainteresować się następującymi tematami przewodnika dla deweloperów usługi IoT Hub:

* [Synchronizowanie stanu i konfiguracji za pomocą bliźniąt urządzeń](iot-hub-devguide-device-twins.md)
* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md)
* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md)

Jeśli chcesz wypróbować niektóre pojęcia opisane w tym artykule, zobacz następujące samouczki Centrum IoT:

* [Wprowadzenie do usługi Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Jak wysyłać wiadomości z chmury do urządzenia za pomocą Usługi IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Jak przetwarzać komunikaty z urządzenia do chmury usługi IoT Hub](tutorial-routing.md)
