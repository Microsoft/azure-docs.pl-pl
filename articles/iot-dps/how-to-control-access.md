---
title: Punkty końcowe zabezpieczeń w usłudze IoT Device Provisioning | Microsoft Docs
description: Koncepcje — jak kontrolować dostęp do usługi IoT Device Provisioning Service (DPS) dla aplikacji zaplecza. Zawiera informacje na temat tokenów zabezpieczających.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 024dbf6518748a4048873de4eb54a53f9d9a6362
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954328"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Kontrola dostępu do usługi Azure IoT Hub Device Provisioning Service

W tym artykule opisano opcje zabezpieczania usługi IoT Device Provisioning. Usługa aprowizacji używa *uprawnień* do udzielania dostępu do każdego punktu końcowego. Uprawnienia ograniczają dostęp do wystąpienia usługi na podstawie funkcjonalności.

W tym artykule opisano:

* Różne uprawnienia, które można przyznać aplikacji zaplecza, aby uzyskać dostęp do usługi aprowizacji.
* Proces uwierzytelniania i tokeny, których używa do weryfikacji uprawnień.

### <a name="when-to-use"></a>Kiedy stosować

Musisz mieć odpowiednie uprawnienia, aby uzyskać dostęp do dowolnych punktów końcowych usługi aprowizacji. Na przykład aplikacja zaplecza musi zawierać token zawierający poświadczenia zabezpieczeń oraz każdy komunikat wysyłany do usługi.

## <a name="access-control-and-permissions"></a>Kontrola dostępu i uprawnienia

[Uprawnienia](#device-provisioning-service-permissions) można udzielić w następujący sposób:

* **Zasady autoryzacji dostępu współdzielonego**. Zasady dostępu współdzielonego mogą przydzielić dowolną kombinację [uprawnień](#device-provisioning-service-permissions). Zasady można definiować w [Azure Portal][lnk-management-portal]lub programowo przy użyciu [interfejsów API REST usługi Device Provisioning][lnk-resource-provider-apis]. Nowo utworzona usługa aprowizacji ma następujące zasady domyślne:

* **provisioningserviceowner**: zasady z wszystkimi uprawnieniami.

> [!NOTE]
> Aby uzyskać szczegółowe informacje, zobacz [uprawnienia](#device-provisioning-service-permissions) .

## <a name="authentication"></a>Authentication

Usługa Azure IoT Hub Device Provisioning Service udziela dostępu do punktów końcowych przez Weryfikowanie tokenu względem zasad dostępu współdzielonego. Poświadczenia zabezpieczeń, takie jak klucze symetryczne, nigdy nie są wysyłane przez sieć.

> [!NOTE]
> Dostawca zasobów usługi Device Provisioning jest zabezpieczony za pomocą subskrypcji platformy Azure, tak jak wszyscy dostawcy w [Azure Resource Manager][lnk-azure-resource-manager].

Aby uzyskać więcej informacji o sposobie konstruowania i używania tokenów zabezpieczających, zobacz następną sekcję.

Protokół HTTP jest jedynym obsługiwanym protokołem i implementuje uwierzytelnianie przez dołączenie prawidłowego tokenu w nagłówku żądania **autoryzacji** .

#### <a name="example"></a>Przykład
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [Zestawy SDK usługi Azure IoT Device Provisioning][lnk-sdks] automatycznie generują tokeny podczas nawiązywania połączenia z usługą.

## <a name="security-tokens"></a>Tokeny zabezpieczające

Usługa Device Provisioning używa tokenów zabezpieczających do uwierzytelniania usług, aby uniknąć wysyłania kluczy w sieci. Ponadto tokeny zabezpieczające są ograniczone do okresu ważności i zakresu. [Zestawy SDK usługi Azure IoT Device Provisioning][lnk-sdks] automatycznie generują tokeny bez konieczności konfigurowania specjalnej. Niektóre scenariusze wymagają generowania i używania tokenów zabezpieczających bezpośrednio. Takie scenariusze obejmują bezpośrednie korzystanie z powierzchni HTTP.

### <a name="security-token-structure"></a>Struktura tokenu zabezpieczającego

Tokeny zabezpieczające są używane do udzielania ograniczonego czasowo dostępu do określonych funkcji w usłudze IoT Device Provisioning. Aby uzyskać autoryzację w celu nawiązania połączenia z usługą aprowizacji, usługi muszą wysyłać tokeny zabezpieczające podpisane z dostępem współdzielonym lub kluczem symetrycznym.

Token podpisany za pomocą klucza dostępu współdzielonego umożliwia dostęp do wszystkich funkcji skojarzonych z uprawnieniami zasad dostępu współdzielonego. 

Token zabezpieczający ma następujący format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Poniżej przedstawiono oczekiwane wartości:

| Wartość | Opis |
| --- | --- |
| podpisane |Ciąg sygnatury HMAC-SHA256 formularza: `{URL-encoded-resourceURI} + "\n" + expiry` . **Ważne**: klucz jest zdekodowany z formatu base64 i używany jako klucz do wykonywania obliczeń HMAC-SHA256.|
| wygaśnięcia |Ciągi UTF8 przez liczbę sekund od czasu epoki 00:00:00 UTC 1 stycznia 1970. |
| {Zakodowany w adresie URL — resourceURI} | Dolny adres URL w przypadku małych liter — kodowanie identyfikatora URI zasobu małymi literami. Prefiks identyfikatora URI (segment) punktów końcowych, do których można uzyskać dostęp za pomocą tego tokenu, rozpoczynając od nazwy hosta usługi IoT Device Provisioning (brak protokołu). Na przykład `mydps.azure-devices-provisioning.net`. |
| PolicyName |Nazwa zasad dostępu współdzielonego, do których odwołuje się ten token. |

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
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

W ramach porównania, odpowiedni kod języka Python służący do generowania tokenu zabezpieczającego:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Ponieważ okres ważności tokenu jest weryfikowany na maszynach usługi IoT Device Provisioning Service, dryf zegara maszyny generującej token musi być minimalny.

### <a name="use-security-tokens-from-service-components"></a>Korzystanie z tokenów zabezpieczających ze składników usługi

Składniki usługi mogą generować tylko tokeny zabezpieczające przy użyciu zasad dostępu współdzielonego, które udzielają odpowiednich uprawnień, jak wyjaśniono wcześniej.

Poniżej przedstawiono funkcje usługi uwidocznione w punktach końcowych:

| Punkt końcowy | Funkcjonalność |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Zapewnia operacje rejestracji urządzeń w usłudze Device Provisioning. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Zawiera operacje zarządzania grupami rejestracji urządzeń. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Zawiera operacje pobierania i zarządzania stanem rejestracji urządzeń. |


Przykładowo usługa wygenerowana przy użyciu wstępnie utworzonych zasad dostępu współdzielonego o nazwie **enrollmentread** utworzy token z następującymi parametrami:

* Identyfikator URI zasobu: `{mydps}.azure-devices-provisioning.net` ,
* klucz podpisywania: jeden z kluczy `enrollmentread` zasad,
* Nazwa zasad: `enrollmentread` ,
* dowolny czas wygaśnięcia. backn

![Tworzenie zasad dostępu współdzielonego dla wystąpienia usługi Device Provisioning w portalu][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

W efekcie można udzielić dostępu do odczytu wszystkich rekordów rejestracji:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Tematy dotyczące odwołań:

Poniższe tematy referencyjne zawierają więcej informacji na temat kontrolowania dostępu do usługi IoT Device Provisioning.

### <a name="device-provisioning-service-permissions"></a>Uprawnienia usługi Device Provisioning

Poniższa tabela zawiera listę uprawnień, których można użyć do kontrolowania dostępu do usługi IoT Device Provisioning.

| Uprawnienie | Uwagi |
| --- | --- |
| **Konfiguracja ServiceConfig** |Przyznaje dostęp do zmiany konfiguracji usługi. <br/>To uprawnienie jest używane przez usługi w chmurze zaplecza. |
| **EnrollmentRead** |Przyznaje dostęp do odczytu do rejestracji urządzeń i grup rejestracji. <br/>To uprawnienie jest używane przez usługi w chmurze zaplecza. |
| **EnrollmentWrite** |Przyznaje dostęp do zapisu do rejestracji urządzeń i grup rejestracji. <br/>To uprawnienie jest używane przez usługi w chmurze zaplecza. |
| **RegistrationStatusRead** |Zezwala na dostęp do odczytu do stanu rejestracji urządzenia. <br/>To uprawnienie jest używane przez usługi w chmurze zaplecza. |
| **RegistrationStatusWrite**  |Przyznaje prawo do usuwania stanu rejestracji urządzeń. <br/>To uprawnienie jest używane przez usługi w chmurze zaplecza. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/