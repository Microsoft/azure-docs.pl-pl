---
title: Co to jest Zapora aplikacji sieci Web platformy Azure na platformie Azure front-drzwi?
description: Dowiedz się, jak Zapora aplikacji sieci Web platformy Azure w usłudze Azure front-drzwi chroni aplikacje sieci Web przed złośliwymi atakami.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: victorh
ms.openlocfilehash: 66ca039a449e355e47563b689f5f44cced164fea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724153"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Zapora aplikacji sieci Web platformy Azure na platformie Azure

Zapora aplikacji sieci Web platformy Azure (WAF) na platformie Azure front-drzwi zapewnia scentralizowaną ochronę aplikacji sieci Web. WAF obrony usług sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach. Zapewnia ona wysoką dostępność usługi dla użytkowników i pomaga spełnić wymagania dotyczące zgodności.

WAF na wierzchu drzwi to globalne i scentralizowane rozwiązanie. Jest ona wdrażana w lokalizacjach brzegowych sieci platformy Azure na całym świecie. WAF włączone aplikacje sieci Web sprawdzają każde żądanie przychodzące dostarczone przez tylne drzwi na granicy sieci. 

WAF zapobiega złośliwym atakom blisko źródeł ataków przed wprowadzeniem do sieci wirtualnej. Na dużą skalę możesz uzyskać ochronę globalną, bez obniżania wydajności. Zasady WAF łatwo łączą się z dowolnym profilem drzwi do przodu w ramach subskrypcji. Nowe reguły można wdrożyć w ciągu kilku minut, dzięki czemu można szybko reagować na zmiany wzorców zagrożeń.

![Zapora aplikacji internetowej platformy Azure](../media/overview/wafoverview.png)

[W wersji zapoznawczej platformy Azure z przodu wprowadzono dwie nowe jednostki SKU](https://docs.microsoft.com/azure/frontdoor/standard-premium/overview): Standard tylnego drzwi i jednostkę SKU w warstwie Premium. WAF jest natywnie zintegrowana z jednostką SKU Premium dla drzwi z pełnymi możliwościami. Dla standardowej jednostki SKU z przodu, obsługiwane są tylko [reguły niestandardowe](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#custom-authored-rules) .

## <a name="waf-policy-and-rules"></a>Zasady i reguły WAF

Można skonfigurować [zasady WAF](waf-front-door-create-portal.md) i skojarzyć te zasady z co najmniej jednym frontonem czołowym dla ochrony. Zasady WAF obejmują dwa typy reguł zabezpieczeń:

- Reguły niestandardowe, które zostały utworzone przez klienta.

- zarządzane zestawy reguł, które są zbiorem wstępnie skonfigurowanych zestawów reguł zarządzanego przez platformę Azure.

Gdy obie są obecne, reguły niestandardowe są przetwarzane przed przetworzeniem reguł w zarządzanym zestawie reguł. Reguła zawiera warunek dopasowania, priorytet i akcję. Obsługiwane typy akcji to: Zezwalaj, Blokuj, Rejestruj i Przekieruj. Można utworzyć w pełni dostosowane zasady spełniające określone wymagania dotyczące ochrony aplikacji przez połączenie reguł zarządzanych i niestandardowych.

Reguły w ramach zasad są przetwarzane w kolejności priorytetów. Priorytet jest unikatową liczbą całkowitą, która definiuje kolejność reguł do przetworzenia. Mniejsza wartość całkowita oznacza wyższy priorytet, a reguły są oceniane przed regułami o wyższej wartości całkowitej. Po dopasowaniu reguły odpowiednia akcja zdefiniowana w regule zostanie zastosowana do żądania. Gdy takie dopasowanie zostanie przetworzone, reguły o niższych priorytetach nie są przetwarzane więcej.

Aplikacja sieci Web dostarczana przez tylne drzwi może mieć tylko jedną zasadę WAFą skojarzoną z nim jednocześnie. Istnieje jednak możliwość skonfigurowania drzwi z przodu bez żadnych skojarzonych z nimi zasad WAF. Jeśli zasady WAF są obecne, są replikowane do wszystkich naszych lokalizacji brzegowych w celu zapewnienia spójnych zasad zabezpieczeń na całym świecie.

## <a name="waf-modes"></a>Tryby WAF

Zasady WAF można skonfigurować tak, aby były uruchamiane w następujących dwóch trybach:

- **Tryb wykrywania:** Po uruchomieniu w trybie wykrywania WAF nie przyjmuje żadnych innych akcji innych niż monitory i rejestruje żądanie i dopasowaną regułę WAF do dzienników WAF. Można włączyć diagnostykę rejestrowania dla drzwi z przodu. W przypadku korzystania z portalu przejdź do sekcji **Diagnostyka** .

- **Tryb zapobiegania:** W trybie zapobiegania WAF wykonuje określoną akcję, jeśli żądanie jest zgodne z regułą. W przypadku znalezienia dopasowania nie są oceniane dalsze reguły o niższym priorytecie. Wszystkie dopasowane żądania są również rejestrowane w dziennikach WAF.

## <a name="waf-actions"></a>Akcje WAF

WAF klienci mogą wybrać uruchamianie z jednej z akcji, gdy żądanie spełnia warunki reguły:

- **Zezwalaj:**  Żądanie przechodzi przez WAF i jest przekazywane do zaplecza. Żadne dalsze reguły o niższym priorytecie nie mogą blokować tego żądania.
- **Blokuj:** Żądanie jest blokowane i WAF wysyła odpowiedź do klienta bez przesyłania dalej żądania do zaplecza.
- **Dziennik:**  Żądanie jest rejestrowane w dziennikach WAF, a WAF kontynuuje szacowanie reguł o niższym priorytecie.
- **Przekierowanie:** WAF przekierowuje żądanie do określonego identyfikatora URI. Określony identyfikator URI jest ustawieniem poziomu zasad. Po skonfigurowaniu wszystkie żądania zgodne z akcją **przekierowania** zostaną wysłane do tego identyfikatora URI.

## <a name="waf-rules"></a>Reguły WAF

Zasady WAFymi mogą składać się z dwóch typów reguł zabezpieczeń — niestandardowych reguł, utworzonych przez klienta i zarządzane zestawy reguł, zarządzane wstępnie przez platformę Azure.

### <a name="custom-authored-rules"></a>Niestandardowe reguły utworzone

Niestandardowe reguły WAF można skonfigurować w następujący sposób:

- **Lista dozwolonych adresów IP i lista zablokowanych:** Możesz kontrolować dostęp do aplikacji sieci Web na podstawie listy adresów IP klientów lub zakresów adresów IP. Obsługiwane są zarówno typy adresów IPv4, jak i IPv6. Tę listę można skonfigurować do blokowania lub zezwalania na te żądania, w przypadku których źródłowy adres IP jest zgodny z adresem IP na liście.

- **Kontrola dostępu oparta na geograficznym:** Możesz kontrolować dostęp do aplikacji sieci Web w oparciu o kod kraju skojarzony z adresem IP klienta.

- **Kontrola dostępu oparta na parametrach http:** W parametrach żądania HTTP/HTTPS można oprzeć reguły dotyczące dopasowania ciągu.  Na przykład ciągi zapytań, POST args, identyfikator URI żądania, nagłówek żądania i treść żądania.

- **Zażądaj kontroli dostępu opartej na metodzie:** Reguły oparte na metodzie żądania HTTP. Na przykład GET, PUT lub szef.

- **Ograniczenie rozmiaru:** Można oprzeć reguły dotyczące długości określonych części żądania, takich jak ciąg zapytania, identyfikator URI lub treść żądania.

- **Reguły ograniczania szybkości:** Reguła kontroli częstotliwości polega na ograniczeniu nietypowego natężenia ruchu z dowolnego adresu IP klienta. W czasie trwania jednej minuty można skonfigurować próg liczby żądań sieci Web dozwolonych przez adres IP klienta. Ta reguła jest odrębna od reguły typu "Zezwól/Blokuj" na podstawie listy adresów IP, która zezwala na wszystkie żądania z adresu IP klienta lub blokuje je. Limity szybkości można łączyć z dodatkowymi warunkami dopasowania, takimi jak parametry protokołu HTTP (S), aby uzyskać szczegółowy formant kontroli szybkości.

### <a name="azure-managed-rule-sets"></a>Zestawy reguł zarządzane przez platformę Azure

Zestawy reguł zarządzane przez platformę Azure zapewniają łatwy sposób wdrażania ochrony przed wspólnym zbiorem zagrożeń bezpieczeństwa. Ponieważ takie zestaw reguł jest zarządzany przez platformę Azure, reguły są aktualizowane w razie potrzeby w celu ochrony przed nowymi sygnaturami ataków. Zestaw reguł domyślnych zarządzany przez platformę Azure zawiera reguły dotyczące następujących kategorii zagrożeń:

- Użycie skryptów między witrynami
- Ataki Java
- Włączenie lokalnego pliku
- Ataki w języku PHP
- Zdalne wykonywanie poleceń
- Dołączanie pliku zdalnego
- Utrwalanie sesji
- Ochrona przed atakami polegającymi na iniekcji SQL
- Atakujący protokołu

Numer wersji zestawu reguł domyślnych jest zwiększany, gdy do zestawu reguł są dodawane nowe podpisy ataków.
Domyślny zestaw reguł jest domyślnie włączony w trybie wykrywania w zasadach WAF. Można wyłączyć lub włączyć poszczególne reguły w ramach domyślnego zestawu reguł, aby spełniały wymagania aplikacji. Można również ustawić określone akcje (Zezwalaj/Blokuj/REDIRECT/LOG) dla każdej reguły.

Czasami może być konieczne pominięcie niektórych atrybutów żądania z oceny WAF. Typowym przykładem są tokeny wstawione Active Directory, które są używane do uwierzytelniania. Można skonfigurować listę wykluczeń dla reguły zarządzanej, grupy reguł lub całego zestawu reguł.  

Domyślną akcją jest blokowanie. Ponadto reguły niestandardowe można skonfigurować w tych samych zasadach WAF, jeśli chcesz pominąć wszystkie wstępnie skonfigurowane reguły w domyślnym zestawie reguł.

Reguły niestandardowe są zawsze stosowane przed oceną reguł w domyślnym zestawie reguł. Jeśli żądanie jest zgodne z regułą niestandardową, zostanie zastosowana odpowiednia akcja reguły. Żądanie jest blokowane lub przenoszone do zaplecza. Nie są przetwarzane żadne inne reguły niestandardowe lub reguły w domyślnym zestawie reguł. Można również usunąć domyślny zestaw reguł z zasad WAFymi.

### <a name="bot-protection-rule-set-preview"></a>Zestaw reguł ochrony bot (wersja zapoznawcza)

Można włączyć zarządzaną regułę ochrony bot, aby podejmować niestandardowe akcje na żądaniach ze znanych kategorii bot. 

Obsługiwane są trzy kategorie bot: złe, dobre i nieznane. Podpisy bot są zarządzane i dynamicznie aktualizowane przez platformę WAF.

Złe botów obejmują botów ze złośliwych adresów IP i botów, które miały sfałszowane tożsamości. Złośliwe adresy IP są źródłem ze źródła analizy zagrożeń firmy Microsoft i aktualizowane co godzinę. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) zapewnia program Microsoft Threat Intelligence i jest używany przez wiele usług, w tym Azure Security Center.

Dobre botów obejmują sprawdzone aparaty wyszukiwania. Nieznane kategorie obejmują dodatkowe grupy bot, które zostały zidentyfikowane jako botów. Na przykład Analizator rynku, narzędzia do pobierania kanałów informacyjnych i agenci zbierania danych. 

Nieznane botów są klasyfikowane przez opublikowanych agentów użytkownika bez dodatkowej weryfikacji. Można ustawić akcje niestandardowe w celu blokowania, zezwalania, rejestrowania lub przekierowywania dla różnych typów botów.

![Zestaw reguł ochrony bot](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Zestaw reguł ochrony bot jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli ochrona bot jest włączona, przychodzące żądania zgodne z regułami bot są rejestrowane w dzienniku FrontdoorWebApplicationFirewallLog. Użytkownik może uzyskać dostęp do dzienników WAF z konta magazynu, centrum zdarzeń lub usługi log Analytics.

## <a name="configuration"></a>Konfigurowanie

Można skonfigurować i wdrożyć wszystkie typy reguł WAF za pomocą Azure Portal, interfejsów API REST, szablonów Azure Resource Manager i Azure PowerShell.

## <a name="monitoring"></a>Monitorowanie

Monitorowanie pod kątem WAFów w drzwiach zewnętrznych jest zintegrowane z Azure Monitor do śledzenia alertów i łatwego monitorowania trendów ruchu.

## <a name="next-steps"></a>Następne kroki

- Informacje o [zaporze aplikacji sieci Web w usłudze Azure Application Gateway](../ag/ag-overview.md)
