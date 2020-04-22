---
title: Uwierzytelnij dostęp do centrów zdarzeń platformy Azure za pomocą podpisów dostępu współdzielonego
description: W tym artykule pokazano, jak uwierzytelnić dostęp do zasobów Centrum zdarzeń przy użyciu podpisów dostępu współdzielonego.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: cde5992355d274410bb43b1e3e60fbba1afe4e44
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676351"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Uwierzytelnij dostęp do zasobów Centrum zdarzeń przy użyciu sygnatur dostępu współdzielonego (SAS)
Podpis dostępu współdzielonego (SAS) zapewnia szczegółową kontrolę nad typem dostępu udzielonego klientom, którzy mają podpis dostępu współdzielonego. Oto niektóre z formantów, które można ustawić w sygnatury dostępu Współdzielonego: 

- Interwał, w którym sygnatury dostępu Współdzielonego jest prawidłowy, w tym czas rozpoczęcia i wygaśnięcia.
- Uprawnienia przyznane przez sygnaturę dostępu Współdzielonego. Na przykład sygnatury dostępu Współdzielonego dla obszaru nazw usługi Event Hubs może udzielić uprawnienia nasłuchiwanie, ale nie uprawnienie do wysyłania.
- Tylko klienci, którzy prezentują prawidłowe poświadczenia, mogą wysyłać dane do Centrum zdarzeń.
- Klient nie może personifikować innego klienta.
- Nieautoryzowanego klienta można zablokować wysyłanie danych do centrum zdarzeń.

W tym artykule opisano uwierzytelnianie dostępu do zasobów usługi Event Hubs przy użyciu sygnatury dostępu Współdzielonego. Aby dowiedzieć się więcej o **autoryzowaniu** dostępu do zasobów usługi Event Hubs przy użyciu sygnatury dostępu Współdzielonego, zobacz [ten artykuł](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Firma Microsoft zaleca używanie poświadczeń usługi Azure AD, jeśli to możliwe, jako najlepsze rozwiązanie w zakresie zabezpieczeń, zamiast używania podpisów dostępu udostępnionego, które mogą być łatwiej zagrożone. Chociaż można nadal używać podpisów dostępu współdzielonego (SAS) do udzielania szczegółowego dostępu do zasobów usługi Event Hubs, usługa Azure AD oferuje podobne możliwości bez konieczności zarządzania tokenami sygnatury dostępu Współdzielonego lub martwić się o odwołanie zagrożonego sygnatury dostępu Współdzielonego.
> 
> Aby uzyskać więcej informacji na temat integracji usługi Azure AD w usłudze Azure Event Hubs, zobacz [Autoryzowanie dostępu do centrów zdarzeń przy użyciu usługi Azure AD.](authorize-access-azure-active-directory.md) 


## <a name="configuring-for-sas-authentication"></a>Konfigurowanie uwierzytelniania sygnatury dostępu Współdzielonego
Regułę autoryzacji dostępu udostępnionego EventHubs można skonfigurować w obszarze nazw Centrum zdarzeń lub w encji (wystąpienie centrum zdarzeń lub temat platformy Kafka w centrum zdarzeń). Konfigurowanie reguły autoryzacji dostępu współdzielonego w grupie odbiorców nie jest obecnie obsługiwane, ale można użyć reguł skonfigurowanych w obszarze nazw lub encji w celu zabezpieczenia dostępu do grupy odbiorców. 

Na poniższej ilustracji przedstawiono, jak reguły autoryzacji mają zastosowanie do przykładowych jednostek. 

![Konfigurowanie reguły autoryzacji](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

W tym przykładzie przykładowy obszar nazw centrów zdarzeń (ExampleNamespace) ma dwie jednostki: eh1 i topic1. Reguły autoryzacji są definiowane zarówno na poziomie jednostki, jak i na poziomie obszaru nazw.  

Reguły autoryzacji manageRuleNS, sendRuleNS i listenRuleNS mają zastosowanie zarówno do wystąpienia centrum zdarzeń eh1, jak i tematu t1. Reguły autoryzacji listenRule-eh i sendRule-eh mają zastosowanie tylko do wystąpienia centrum zdarzeń eh1 i reguły autoryzacji sendRuleT ma zastosowanie tylko do tematu temat1. 

Podczas korzystania z reguły autoryzacji sendRuleNS aplikacje klienckie mogą wysyłać do eh1 i topic1. Gdy używana jest reguła autoryzacji sendRuleT, wymusza tylko szczegółowy dostęp do topic1 i dlatego aplikacje klienckie korzystające z tej reguły dostępu nie mogą teraz wysyłać do eh1, ale tylko do topic1.

## <a name="generate-a-shared-access-signature-token"></a>Generowanie tokenu podpisu dostępu współdzielonego 
Każdy klient, który ma dostęp do nazwy nazwy reguły autoryzacji i jeden z jego kluczy podpisywania może wygenerować token sygnatury dostępu Współdzielonego. Token jest generowany przez wykonanie ciągu w następującym formacie:

- `se`– Natychmiastowe wygaśnięcie tokenu. Liczba całkowita odzwierciedlająca sekundy od 00:00:00 UTC w dniu 1 stycznia 1970 r. (epoka UNIX) po wygaśnięciu tokenu
- `skn`– Nazwa reguły autoryzacji, czyli nazwa klucza sygnatury dostępu Współdzielonego.
- `sr`– Identyfikator URI zasobu, do który uzyskuje się dostęp.
- `sig`– Podpis.

Ciąg podpisu jest skrótem SHA-256 obliczonym za pośrednictwem identyfikatora URI zasobu (zakres opisany w poprzedniej sekcji) i reprezentacją ciągu natychmiastowego wygaśnięcia tokenu, oddzielonym crlf.

Obliczenia skrótu wygląda podobnie do następującego pseudo kodu i zwraca wartość mieszania 256-bit/32-bajtowy. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token zawiera wartości nie skrótowe, dzięki czemu odbiorca może ponownie obliczyć wartość skrótu z tymi samymi parametrami, sprawdzając, czy wystawca jest w posiadaniu prawidłowego klucza podpisywania.

Identyfikator URI zasobu jest pełnym identyfikatorem URI zasobu usługi Service Bus, do którego jest żądany dostęp. Na przykład http://<namespace>.servicebus.windows.net/<entityPath> lub `sb://<namespace>.servicebus.windows.net/<entityPath>;` oznacza . `http://contoso.servicebus.windows.net/eventhubs/eh1`

Identyfikator URI musi być zakodowany w procentach.

Reguła autoryzacji dostępu współdzielonego używana do podpisywania musi być skonfigurowana w jednostce określonej przez ten identyfikator URI lub przez jeden z jej hierarchicznych łzawych. Na przykład `http://contoso.servicebus.windows.net/eventhubs/eh1` `http://contoso.servicebus.windows.net` lub w poprzednim przykładzie.

Token sygnatury dostępu Współdzielonego jest prawidłowy dla wszystkich zasobów <resourceURI> poprzedzonych ciągiem podpisu.

> [!NOTE]
> Generowanie tokenu dostępu dla centrów zdarzeń przy użyciu zasad dostępu współdzielonego. Aby uzyskać więcej informacji, zobacz [Zasady autoryzacji dostępu współdzielonego](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Generowanie podpisu (tokenu) z zasad 
W poniższej sekcji przedstawiono generowanie tokenu sygnatury dostępu współdzielonego przy użyciu zasad podpisu dostępu współdzielonego,

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

#### <a name="java"></a>Java

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

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Uwierzytelnianie wydawców centrów zdarzeń za pomocą usługi SAS 
Wydawca zdarzeń definiuje wirtualny punkt końcowy dla centrum zdarzeń. Wydawca może służyć tylko do wysyłania wiadomości do centrum zdarzeń i nie odbierać wiadomości.

Zazwyczaj centrum zdarzeń zatrudnia jednego wydawcę na klienta. Wszystkie wiadomości, które są wysyłane do dowolnego wydawców centrum zdarzeń są ujmowane w kolejce w tym centrum zdarzeń. Wydawcy umożliwiają precyzyjną kontrolę dostępu.

Każdemu klientowi centrum zdarzeń jest przypisywany unikatowy token, który jest przekazywał do klienta. Tokeny są produkowane w taki sposób, że każdy unikatowy token udziela dostępu do innego unikatowego wydawcy. Klient, który posiada token można wysłać tylko do jednego wydawcy, a nie inny wydawca. Jeśli wielu klientów współużytkuje ten sam token, każdy z nich współużytkuje wydawcę.

Wszystkie tokeny są przypisywane z kluczami sygnatury dostępu Współdzielonego. Zazwyczaj wszystkie tokeny są podpisane za pomocą tego samego klucza. Klienci nie są świadomi klucza, co uniemożliwia klientom produkcję tokenów. Klienci działają na tych samych tokenach, dopóki nie wygasną.

Na przykład, aby zdefiniować reguły autoryzacji ograniczone do wysyłania/publikowania tylko do centrum zdarzeń, należy zdefiniować regułę autoryzacji wysyłania. Można to zrobić na poziomie obszaru nazw lub nadać bardziej szczegółowy zakres określonej jednostce (wystąpienie centrów zdarzeń lub temat). Klient lub aplikacja, która jest objęta zakresem z takim szczegółowym dostępem jest wywoływana, Event Hubs wydawcy. Aby to zrobić, wykonaj następujące kroki:

1. Utwórz klucz Sygnatury dostępu Współdzielonego dla encji, którą chcesz opublikować, aby przypisać zakres **wysyłania.** Aby uzyskać więcej informacji, zobacz [Zasady autoryzacji dostępu współdzielonego](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Generowanie tokenu sygnatury dostępu Współdzielonego z czasem wygaśnięcia dla określonego wydawcy przy użyciu klucza wygenerowanego w kroku 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Podaj token do klienta wydawcy, który można wysłać tylko do jednostki i wydawcy, który token udziela dostępu do.

    Po wygaśnięciu tokenu klient traci dostęp do wysyłania/publikowania do jednostki. 


> [!NOTE]
> Chociaż nie jest to zalecane, jest możliwe wyposażenie urządzeń z tokenów, które udzielają dostępu do centrum zdarzeń lub obszaru nazw. Każde urządzenie, które przechowuje ten token może wysyłać wiadomości bezpośrednio do tego centrum zdarzeń. Ponadto urządzenia nie można na czarnej liście od wysyłania do tego centrum zdarzeń.
> 
> Zawsze zaleca się nadawaj konkretne i szczegółowe zakresy.

> [!IMPORTANT]
> Po utworzeniu tokenów każdy klient jest aprowizowany za pomocą własnego unikatowego tokenu.
>
> Gdy klient wysyła dane do centrum zdarzeń, oznacza jego żądanie tokenem. Aby zapobiec podsłuchiwanie i kradzież tokenu przez osobę atakującą, komunikacja między klientem a centrum zdarzeń musi odbywać się za jej zawertowanym kanałem.
> 
> Jeśli token zostanie skradziony przez osobę atakującą, osoba atakująca może podszyć się pod klienta, którego token został skradziony. Na czarnej liście wydawcy powoduje, że klient nie będzie używany, dopóki nie otrzyma nowego tokenu, który używa innego wydawcy.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Uwierzytelnianie konsumentów centrów zdarzeń za pomocą usługi SAS 
Aby uwierzytelnić aplikacje zaplecza, które zużywają dane generowane przez producentów usługi Event Hubs, uwierzytelnianie tokenu usługi Event Hubs wymaga, aby jego klienci mieli prawa do **zarządzania** lub uprawnienia **nasłuchiwanie** przypisane do obszaru nazw Centrum zdarzeń lub wystąpienia lub tematu centrum zdarzeń. Dane są używane z Centrów zdarzeń przy użyciu grup odbiorców. Podczas gdy zasady sygnatury dostępu Współdzielonego zapewnia szczegółowy zakres, zakres ten jest zdefiniowany tylko na poziomie jednostki, a nie na poziomie konsumenta. Oznacza to, że uprawnienia zdefiniowane na poziomie obszaru nazw lub wystąpienia centrum zdarzeń lub poziomu tematu zostaną zastosowane do grup konsumentów tej jednostki.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Autoryzowanie przy użyciu sygnatury dostępu Współdzielonego](authenticate-shared-access-signature.md)
- [Autoryzacja przy użyciu podstawowej kontroli dostępu roli (RBAC)](authenticate-shared-access-signature.md)
- [Dowiedz się więcej o Centrach zdarzeń](event-hubs-about.md)

Zobacz następujące artykuły pokrewne:

- [Uwierzytelnij żądania do usługi Azure Event Hubs z aplikacji przy użyciu usługi Azure Active Directory](authenticate-application.md)
- [Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Event Hubs](authenticate-managed-identity.md)
- [Autoryzowanie dostępu do zasobów usługi Event Hubs przy użyciu usługi Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autoryzowanie dostępu do zasobów Centrum zdarzeń przy użyciu podpisów dostępu współdzielonego](authorize-access-shared-access-signature.md)
