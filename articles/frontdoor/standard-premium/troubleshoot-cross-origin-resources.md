---
title: Korzystanie z platformy Azure w warstwie Standardowa/Premium z udostępnianiem zasobów między źródłami
description: Dowiedz się, jak korzystać z platformy Azure Front drzwiczk (AFD) z funkcją udostępniania zasobów między źródłami (CORS).
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099369"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>Korzystanie z platformy Azure w warstwie Standardowa/Premium z udostępnianiem zasobów między źródłami (CORS)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

## <a name="what-is-cors"></a>Co to jest mechanizm CORS?

Mechanizm CORS (udostępnianie zasobów między źródłami) to funkcja protokołu HTTP, która umożliwia aplikacji internetowej działającej w ramach jednej domeny dostęp do zasobów w innej domenie. Aby zmniejszyć prawdopodobieństwo ataków na skrypty między lokacjami, wszystkie nowoczesne przeglądarki sieci Web implementują ograniczenie zabezpieczeń znane jako [zasady tego samego źródła](https://www.w3.org/Security/wiki/Same_Origin_Policy). Zapobiega to wywoływaniu interfejsów API w innej domenie przez stronę sieci Web.  Mechanizm CORS umożliwia bezpieczny sposób wywoływania interfejsów API w innym źródle.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Jak to działa

Istnieją dwa typy żądań CORS, *proste żądania* i *skomplikowane żądania.*

### <a name="for-simple-requests"></a>W przypadku prostych żądań:

1. Przeglądarka wysyła żądanie CORS przy użyciu innego nagłówka żądania HTTP **źródła** . Wartość tego nagłówka jest źródłem, który obsługuje stronę nadrzędną, która jest definiowana jako kombinacja *protokołu,* *domeny* i *portu.*  Gdy strona z usługi https \: //www.contoso.com próbuje uzyskać dostęp do danych użytkownika w pochodzeniu fabrikam.com, do fabrikam.com zostanie wysłany następujący nagłówek żądania:

   `Origin: https://www.contoso.com`

2. Serwer może odpowiedzieć z dowolnego z następujących elementów:

   * Nagłówek **Access-Control-Allow-Origin** w odpowiedzi wskazujący, która lokacja pochodzenia jest dozwolona. Na przykład:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Kod błędu HTTP, taki jak 403, jeśli serwer nie zezwala na żądanie między źródłami po sprawdzeniu nagłówka źródła

   * Nagłówek **Access-Control-Allow-Origin** z symbolem wieloznacznym, który zezwala na wszystkie źródła:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>W przypadku złożonych żądań:

Żądanie złożone to żądanie CORS, w którym przeglądarka jest wymagana do wysłania *żądania wstępnego* (czyli wstępnej sondy) przed wysłaniem rzeczywistego żądania CORS. Żądanie inspekcji wstępnej prosi o zezwolenie na serwer w przypadku kontynuowania oryginalnego żądania CORS i jest `OPTIONS` żądaniem tego samego adresu URL.

> [!TIP]
> Aby uzyskać więcej informacji na temat przepływów CORS i wspólnych pułapek, zobacz [Przewodnik dotyczący funkcji CORS dla interfejsów API REST](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scenariusze dotyczące symboli wieloznacznych lub pojedynczej lokalizacji pochodzenia
Mechanizm CORS na platformie Azure front-drzwi będzie działał automatycznie bez dodatkowej konfiguracji, gdy nagłówek **Access-Control-Allow-Origin** ma wartość symbol wieloznaczny (*) lub pojedyncze źródło.  Drzwi frontonu platformy Azure będą buforować pierwszą odpowiedź, a żądania będą używać tego samego nagłówka.

Jeśli żądania zostały już wprowadzone do drzwi frontonu platformy Azure przed ustawieniem CORS w pochodzeniu, musisz przeczyścić zawartość w zawartości punktu końcowego, aby ponownie załadować zawartość z nagłówkiem **Access-Control-Allow-Origin** .

## <a name="multiple-origin-scenarios"></a>Scenariusze wielu źródeł
Jeśli trzeba zezwolić na dostęp do konkretnej listy pochodzenia dla mechanizmu CORS, znacznie bardziej skomplikowane są pewne elementy. Ten problem występuje, gdy Usługa CDN buforuje nagłówek **Access-Control-Allow-Origin** dla pierwszego źródła CORS.  Gdy inne źródło CORS wykonuje kolejne żądanie, Usługa CDN będzie obsługiwała nagłówek buforowanego **dostępu-Control-Allow-Origin** , który nie jest zgodny. Istnieje kilka sposobów rozwiązania tego problemu.

### <a name="azure-front-door-rule-set"></a>Zestaw reguł dla drzwi frontonu platformy Azure

Na platformie Azure Front Drzwiczks można utworzyć regułę w [zestawie reguł](concept-rule-set.md) drzwi platformy Azure, aby sprawdzić nagłówek **źródła** żądania. Jeśli jest to prawidłowe źródło, reguła ustawi nagłówek **Access-Control-Allow-Origin** z poprawną wartością. W takim przypadku nagłówek **Access-Control-Allow-Origin** z serwera pochodzenia pliku jest ignorowany, a aparat reguł AFD w pełni zarządza dozwolonymi źródłami CORS.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="Zrzut ekranu reguł na przykład z zestawem reguł.":::

> [!TIP]
> Możesz dodać dodatkowe akcje do reguły, aby zmodyfikować dodatkowe nagłówki odpowiedzi, takie jak **Access-Control-Allow-Methods**.
> 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć usługę Front Door](create-front-door-portal.md).
