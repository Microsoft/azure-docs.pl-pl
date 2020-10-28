---
title: Używanie Azure CDN z mechanizmem CORS | Microsoft Docs
description: Dowiedz się, jak korzystać z usługi Azure Content Delivery Network (CDN) z funkcją udostępniania zasobów między źródłami (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f7edf790e526329dd285d03a31137a26220e52ee
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778935"
---
# <a name="using-azure-cdn-with-cors"></a>Używanie Azure CDN z mechanizmem CORS
## <a name="what-is-cors"></a>Co to jest mechanizm CORS?
Mechanizm CORS (udostępnianie zasobów między źródłami) to funkcja protokołu HTTP, która umożliwia aplikacji internetowej działającej w ramach jednej domeny dostęp do zasobów w innej domenie. Aby zmniejszyć prawdopodobieństwo ataków na skrypty między lokacjami, wszystkie nowoczesne przeglądarki sieci Web implementują ograniczenie zabezpieczeń znane jako [zasady tego samego źródła](https://www.w3.org/Security/wiki/Same_Origin_Policy).  Zapobiega to wywoływaniu interfejsów API w innej domenie przez stronę sieci Web.  Mechanizm CORS umożliwia bezpieczny sposób wywoływania interfejsów API w innym źródle.

## <a name="how-it-works"></a>Jak to działa
Istnieją dwa typy żądań CORS, *proste żądania* i *skomplikowane żądania.*

### <a name="for-simple-requests"></a>W przypadku prostych żądań:

1. Przeglądarka wysyła żądanie CORS przy użyciu dodatkowego nagłówka żądania HTTP **źródła** . Wartość tego nagłówka jest źródłem, który obsługuje stronę nadrzędną, która jest definiowana jako kombinacja *protokołu,* *domeny* i *portu.*  Gdy strona z usługi https \: //www.contoso.com próbuje uzyskać dostęp do danych użytkownika w pochodzeniu fabrikam.com, do fabrikam.com zostanie wysłany następujący nagłówek żądania:

   `Origin: https://www.contoso.com`

2. Serwer może odpowiedzieć z dowolnego z następujących elementów:

   * Nagłówek **Access-Control-Allow-Origin** w odpowiedzi wskazujący, która lokacja pochodzenia jest dozwolona. Na przykład:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kod błędu HTTP, taki jak 403, jeśli serwer nie zezwala na żądanie między źródłami po sprawdzeniu nagłówka źródła

   * Nagłówek **Access-Control-Allow-Origin** z symbolem wieloznacznym, który zezwala na wszystkie źródła:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>W przypadku złożonych żądań:

Żądanie złożone to żądanie CORS, w którym przeglądarka jest wymagana do wysłania *żądania wstępnego* (czyli wstępnej sondy) przed wysłaniem rzeczywistego żądania CORS. Żądanie inspekcji wstępnej prosi o zezwolenie na serwer, jeśli będzie można wykonać oryginalne żądanie CORS i jest `OPTIONS` żądaniem tego samego adresu URL.

> [!TIP]
> Aby uzyskać więcej informacji na temat przepływów CORS i wspólnych pułapek, zobacz [Przewodnik dotyczący funkcji CORS dla interfejsów API REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scenariusze dotyczące symboli wieloznacznych lub pojedynczej lokalizacji pochodzenia
Mechanizm CORS na Azure CDN będzie działał automatycznie bez dodatkowej konfiguracji, gdy nagłówek **Access-Control-Allow-Origin** ma wartość symbol wieloznaczny (*) lub pojedyncze źródło.  Usługa CDN będzie buforować pierwszą odpowiedź, a kolejne żądania będą używały tego samego nagłówka.

Jeśli żądania zostały już wprowadzone do usługi CDN przed ustawieniem CORS w pochodzeniu, konieczne będzie przeczyszczenie zawartości z zawartości punktu końcowego w celu ponownego załadowania zawartości przy użyciu nagłówka **Access-Control-Allow-Origin** .

## <a name="multiple-origin-scenarios"></a>Scenariusze wielu źródeł
Jeśli trzeba zezwolić na dostęp do konkretnej listy pochodzenia dla mechanizmu CORS, znacznie bardziej skomplikowane są pewne elementy. Ten problem występuje, gdy Usługa CDN buforuje nagłówek **Access-Control-Allow-Origin** dla pierwszego źródła CORS.  Gdy inne źródło CORS wykonuje kolejne żądanie, Usługa CDN będzie obsługiwała nagłówek cache **-Control-Allow-Origin** , który nie jest zgodny.  Istnieje kilka sposobów, aby rozwiązać ten problem.

### <a name="azure-cdn-standard-profiles"></a>Profile standardowe Azure CDN
W przypadku usługi Azure CDN Standard od firmy Microsoft można utworzyć regułę w [aparacie standardowych reguł](cdn-standard-rules-engine-reference.md) , aby sprawdzić nagłówek **źródła** żądania. Jeśli jest to prawidłowe źródło, reguła ustawi nagłówek **Access-Control-Allow-Origin** z pożądaną wartością. W takim przypadku nagłówek **Access-Control-Allow-Origin** z serwera pochodzenia pliku jest ignorowany, a aparat reguł sieci CDN całkowicie zarządza dozwolonymi źródłami CORS.

![Przykład reguł z aparatem reguł standardowych](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> Możesz dodać dodatkowe akcje do reguły, aby zmodyfikować dodatkowe nagłówki odpowiedzi, takie jak **Access-Control-Allow-Methods** .
> 

W **Azure CDN standardowym z Akamai** , jedynym mechanizmem do zezwalania na wiele źródeł bez użycia symboli wieloznacznych jest użycie [buforowania ciągu zapytania](cdn-query-string.md). Włącz ustawienie ciągu zapytania dla punktu końcowego usługi CDN, a następnie użyj unikatowego ciągu zapytania dla żądań z każdej dozwolonej domeny. W efekcie Usługa CDN będzie buforować osobny obiekt dla każdego unikatowego ciągu zapytania. Takie podejście nie jest jednak idealne, ponieważ spowoduje to powstanie wielu kopii tego samego pliku w pamięci podręcznej w sieci CDN.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium od Verizon
Korzystając z aparatu reguł Verizon Premium, musisz [utworzyć regułę](./cdn-verizon-premium-rules-engine.md) , aby sprawdzić nagłówek **źródła** żądania.  Jeśli jest to prawidłowe źródło, reguła ustawi nagłówek **Access-Control-Allow-Origin** ze źródłem podanym w żądaniu.  Jeśli źródło określone w nagłówku **źródła** jest niedozwolone, reguła powinna pominąć nagłówek **Access-Control-Allow-Origin** , co spowoduje odrzucenie żądania przez przeglądarkę. 

Istnieją dwa sposoby wykonania tej czynności za pomocą aparatu reguł Premium. W obu przypadkach nagłówek **Access-Control-Allow-Origin** z serwera pochodzenia pliku jest ignorowany, a aparat reguł sieci CDN całkowicie zarządza dozwolonymi źródłami CORS.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Jedno wyrażenie regularne ze wszystkimi prawidłowymi źródłami
W tym przypadku utworzysz wyrażenie regularne zawierające wszystkie źródła, które mają być dozwolone: 

```http
https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
```

> [!TIP]
> **Azure CDN Premium from Verizon** używa [wyrażeń regularnych zgodnych](https://pcre.org/) ze standardem Perl jako aparat dla wyrażeń regularnych.  Aby zweryfikować wyrażenie regularne, można użyć narzędzia, takiego jak [wyrażenia regularne 101](https://regex101.com/) .  Należy zauważyć, że znak "/" jest prawidłowy w wyrażeniach regularnych i nie musi być zmieniony, ale ucieczki, że znak jest uznawany za najlepsze rozwiązanie i jest oczekiwany przez niektóre walidacje wyrażenia regularnego.
> 
> 

Jeśli wyrażenie regularne jest zgodne, reguła zastąpi nagłówek **Access-Control-Allow-Origin** (jeśli istnieje) z lokalizacji źródłowej, która wysłała żądanie.  Można również dodać dodatkowe nagłówki CORS, takie jak **Access-Control-Allow-Methods** .

![Przykład reguł z wyrażeniem regularnym](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Reguła nagłówka żądania dla każdego źródła.
Zamiast wyrażeń regularnych można utworzyć oddzielną regułę dla każdego pochodzenia, który ma być dozwolony przy użyciu [warunku dopasowania](/previous-versions/azure/mt757336(v=azure.100)#match-conditions) **symboli wieloznacznych nagłówka żądania** . Podobnie jak w przypadku metody wyrażenia regularnego, aparat reguł ustawia nagłówki CORS. 

![Przykład reguł bez wyrażenia regularnego](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> W powyższym przykładzie użycie symbolu wieloznacznego * Instruuje aparat reguł, aby pasował do protokołu HTTP i HTTPS.
> 
>