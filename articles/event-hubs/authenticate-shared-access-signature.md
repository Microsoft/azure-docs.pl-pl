---
title: Uwierzytelnianie dostępu do usługi Azure Event Hubs przy użyciu sygnatur dostępu współdzielonego
description: W tym artykule opisano sposób uwierzytelniania dostępu do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ff141fc1bb681e2356a4471dfdc808d622fd76b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98986499"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Uwierzytelnianie dostępu do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego (SAS)
Sygnatura dostępu współdzielonego (SAS) zapewnia szczegółową kontrolę nad typem dostępu przyznanym klientom, którzy mają sygnaturę dostępu współdzielonego. Poniżej przedstawiono niektóre kontrolki, które można ustawić w sygnaturze dostępu współdzielonego: 

- Interwał, w jakim sygnatura dostępu współdzielonego jest prawidłowa, włącznie z czasem rozpoczęcia i czasem wygaśnięcia.
- Uprawnienia przyznane przez sygnaturę dostępu współdzielonego. Na przykład sygnatura dostępu współdzielonego dla Event Hubs przestrzeni nazw może udzielić uprawnienia do nasłuchiwania, ale nie do uprawnienia do wysyłania.
- Tylko klienci, którzy zaprezentowania prawidłowych poświadczeń, mogą wysyłać dane do centrum zdarzeń.
- Klient nie może personifikować innego klienta.
- Nieautoryzowany klient może blokować wysyłanie danych do centrum zdarzeń.

W tym artykule omówiono uwierzytelnianie dostępu do zasobów Event Hubs przy użyciu SAS. Aby dowiedzieć się więcej na temat **autoryzowania** dostępu do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego, zobacz [ten artykuł](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Firma Microsoft zaleca, aby użyć poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepszych rozwiązań w zakresie zabezpieczeń, a nie przy użyciu sygnatur dostępu współdzielonego, które mogą być bardziej łatwo naruszone. Można nadal używać sygnatur dostępu współdzielonego (SAS), aby udzielać szczegółowego dostępu do zasobów Event Hubs, usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokenami SAS ani do odwoływania się do odwołującego się do naruszenia złamanej sygnatury dostępu współdzielonego.
> 
> Aby uzyskać więcej informacji na temat integracji usługi Azure AD w usłudze Azure Event Hubs, zobacz [Autoryzuj dostęp do Event Hubs za pomocą usługi Azure AD](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Konfigurowanie do uwierzytelniania za sygnaturą dostępu współdzielonego
Można skonfigurować regułę autoryzacji dostępu współdzielonego EventHubs w przestrzeni nazw Event Hubs lub jednostkę (wystąpienie centrum zdarzeń lub temat Kafka w centrum zdarzeń). Konfigurowanie reguły autoryzacji dostępu współdzielonego w grupie odbiorców nie jest obecnie obsługiwane, ale można użyć reguł skonfigurowanych dla przestrzeni nazw lub jednostki w celu zabezpieczenia dostępu do grupy odbiorców. 

Na poniższej ilustracji przedstawiono sposób stosowania reguł autoryzacji na przykładowych jednostkach. 

![Konfigurowanie reguły autoryzacji](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

W tym przykładzie Przykładowa przestrzeń nazw Event Hubs (ExampleNamespace) ma dwie jednostki: EH1 i topic1. Reguły autoryzacji są definiowane zarówno na poziomie jednostki, jak i na poziomie przestrzeni nazw.  

Reguły autoryzacji manageRuleNS, sendRuleNS i listenRuleNS dotyczą zarówno wystąpienia usługi Event Hub EH1, jak i tematu T1. Reguły autoryzacji listenRule i EH są stosowane tylko w przypadku wystąpienia centrum zdarzeń reguła autoryzacji EH1 i sendRuleT dotyczy tylko tematu topic1. 

W przypadku korzystania z reguły autoryzacji sendRuleNS aplikacje klienckie mogą wysyłać do EH1 i topic1. Gdy jest używana reguła autoryzacji sendRuleT, wymusza ona szczegółowy dostęp do topic1, a więc aplikacje klienckie korzystające z tej reguły dla dostępu nie mogą wysyłać do EH1, ale tylko do topic1.

## <a name="generate-a-shared-access-signature-token"></a>Generowanie tokenu sygnatury dostępu współdzielonego 
Każdy klient, który ma dostęp do nazwy reguły autoryzacji i jednego z jego kluczy podpisywania, może generować token SAS. Token jest generowany przez przedpływanie ciągu w następującym formacie:

- `se`  — Czas wygaśnięcia tokenu. Liczba całkowita odzwierciedlająca sekundy od czasu 00:00:00 czasu UTC na 1 stycznia 1970 (Epoka systemu UNIX) po wygaśnięciu tokenu
- `skn` — Nazwa reguły autoryzacji, która jest nazwą klucza sygnatury dostępu współdzielonego.
- `sr` — Identyfikator URI zasobu, do którego uzyskuje się dostęp.
- `sig` Podpisane.

Ciąg sygnatury to skrót SHA-256 obliczany na podstawie identyfikatora URI zasobu (zakres, zgodnie z opisem w poprzedniej sekcji) oraz ciąg reprezentujący czas wygaśnięcia tokenu, oddzielony znakiem CRLF.

Obliczenia skrótu wyglądają podobnie jak w poniższym pseudo kodzie i zwracają 256-bitową/32-bajtową wartość skrótu. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token zawiera wartości niebędące skrótami, dzięki czemu odbiorca może ponownie obliczyć skrót z tymi samymi parametrami, sprawdzając, czy Wystawca ma prawidłowy klucz podpisywania.

Identyfikator URI zasobu to pełny identyfikator URI zasobu Service Bus, do którego odnosi się dostęp. Na przykład http:// <namespace> . ServiceBus.Windows.NET/ <entityPath> lub `sb://<namespace>.servicebus.windows.net/<entityPath>;` to, `http://contoso.servicebus.windows.net/eventhubs/eh1` .

Identyfikator URI musi być zakodowany przy użyciu wartości procentowej.

Reguła autoryzacji dostępu współdzielonego używana do podpisywania musi być skonfigurowana w jednostce określonej przez ten identyfikator URI lub według jednego z hierarchicznych obiektów nadrzędnych. Na przykład `http://contoso.servicebus.windows.net/eventhubs/eh1` lub `http://contoso.servicebus.windows.net` w poprzednim przykładzie.

Token sygnatury dostępu współdzielonego jest prawidłowy dla wszystkich zasobów poprzedzonych prefiksem <resourceURI> używanym w ciągu sygnatury.

> [!NOTE]
> Token dostępu jest generowany dla Event Hubs przy użyciu zasad dostępu współdzielonego. Aby uzyskać więcej informacji, zobacz [zasady autoryzacji dostępu współdzielonego](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Generowanie sygnatury (tokenu) z zasad 
W poniższej sekcji przedstawiono generowanie tokenu SAS przy użyciu zasad sygnatury dostępu współdzielonego.

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>OPROGRAMOWANIA

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Uwierzytelnianie Event Hubs wydawców przy użyciu sygnatury dostępu współdzielonego 
Wydawca zdarzeń definiuje wirtualny punkt końcowy centrum zdarzeń. Wydawca może być używany tylko do wysyłania komunikatów do centrum zdarzeń i nie odbierania komunikatów.

Zwykle centrum zdarzeń wykorzystuje jednego wydawcę na każdego klienta. Wszystkie komunikaty wysyłane do dowolnego wydawcy centrum zdarzeń znajdują się w kolejce w tym centrum zdarzeń. Wydawcy włączają precyzyjną kontrolę dostępu.

Każdy klient Event Hubs ma przypisany unikatowy token, który jest przekazywany do klienta programu. Tokeny są tworzone w taki sposób, że każdy unikatowy token przyznaje dostęp do innego unikatowego wydawcy. Klient, który przechowuje token, może wysyłać tylko jednego wydawcę, a nie innego wydawcy. Jeśli wielu klientów współużytkują ten sam token, każdy z nich udostępnia wydawcę.

Wszystkie tokeny są przypisywane przy użyciu kluczy SAS. Zwykle wszystkie tokeny są podpisane przy użyciu tego samego klucza. Klienci nie mają informacji o kluczu, co uniemożliwia klientom użycie tokenów produkcyjnych. Klienci działają na tych samych tokenach do momentu ich wygaśnięcia.

Na przykład w celu zdefiniowania reguł autoryzacji objętych zakresem do wysyłania/publikowania do Event Hubs należy zdefiniować regułę autoryzacji wysyłania. Można to zrobić na poziomie przestrzeni nazw lub nadać bardziej szczegółowy zakres określonej jednostce (wystąpienie Centra zdarzeń lub temat). Jest wywoływany klient lub aplikacja objęta zakresem takich szczegółowych praw dostępu, Event Hubs wydawcy. W tym celu wykonaj następujące czynności:

1. Utwórz klucz sygnatury dostępu współdzielonego na jednostce, którą chcesz opublikować, aby przypisać do niej zakres **wysyłania** . Aby uzyskać więcej informacji, zobacz [zasady autoryzacji dostępu współdzielonego](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Wygeneruj token sygnatury dostępu współdzielonego z upływem czasu wygaśnięcia określonego wydawcy przy użyciu klucza wygenerowanego w krok 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Podaj token dla klienta wydawcy, który może być wysyłany tylko do jednostki i wydawcy, do którego token przyznaje dostęp.

    Po wygaśnięciu tokenu Klient utraci dostęp do wysyłania/publikowania w jednostce. 


> [!NOTE]
> Chociaż nie jest to zalecane, istnieje możliwość wyposażenia urządzeń z tokenami, które udzielają dostępu do centrum zdarzeń lub przestrzeni nazw. Każde urządzenie, które przechowuje ten token, może wysyłać wiadomości bezpośrednio do tego centrum zdarzeń. Ponadto urządzenie nie może zostać blocklisted wysłane do tego centrum zdarzeń.
> 
> Zawsze zaleca się przyznanie określonych i szczegółowych zakresów.

> [!IMPORTANT]
> Po utworzeniu tokenów każdy klient zostanie zainicjowany z własnym unikatowym tokenem.
>
> Gdy klient wysyła dane do centrum zdarzeń, oznacza jego żądanie z tokenem. Aby zapobiec podsłuchiwaniu i kradzieży tokenu przez atakującego, komunikacja między klientem a centrum zdarzeń musi odbywać się za pośrednictwem zaszyfrowanego kanału.
> 
> W przypadku kradzieży tokenu przez atakującego, osoba atakująca może personifikować klienta, którego token został skradziony. Blocklisting wydawcy, renderuje tego klienta, dopóki nie odbierze nowego tokenu, który używa innego wydawcy.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Uwierzytelnianie Event Hubs użytkowników przy użyciu sygnatury dostępu współdzielonego 
Aby uwierzytelniać aplikacje zaplecza, które wykorzystują dane wygenerowane przez producentów Event Hubs, uwierzytelnianie tokenów Event Hubs wymaga, aby klienci mieli uprawnienia do **zarządzania** prawami lub **nasłuchiwanie** przypisane do jego przestrzeni nazw Event Hubs lub wystąpienia centrum zdarzeń lub tematu. Dane są używane z Event Hubs przy użyciu grup odbiorców. Chociaż zasady SAS dają szczegółowy zakres, ten zakres jest definiowany tylko na poziomie jednostki, a nie na poziomie odbiorcy. Oznacza to, że uprawnienia zdefiniowane na poziomie przestrzeni nazw lub wystąpieniu centrum zdarzeń lub poziomie tematu zostaną zastosowane do grup odbiorców tej jednostki.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Autoryzuj przy użyciu sygnatury dostępu współdzielonego](authenticate-shared-access-signature.md)
- [Autoryzuj przy użyciu kontroli dostępu opartej na rolach (Azure RBAC)](authenticate-shared-access-signature.md)
- [Dowiedz się więcej o Event Hubs](event-hubs-about.md)

Zapoznaj się z następującymi artykułami:

- [Uwierzytelniaj żądania do Event Hubs platformy Azure z aplikacji przy użyciu Azure Active Directory](authenticate-application.md)
- [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-managed-identity.md)
- [Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autoryzuj dostęp do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md)
