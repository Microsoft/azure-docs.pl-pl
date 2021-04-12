---
title: Korzystanie z funkcji wskaźnikowej platformy Azure za pomocą zapory aplikacji sieci Web platformy Azure
description: W tym artykule pokazano, jak używać usługi Azure wskaźnikowej przy użyciu zapory aplikacji sieci Web platformy Azure (WAF)
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 3d905dd1e6acab8f9f6d3885c882dd9c32133cb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596432"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Korzystanie z funkcji wskaźnikowej platformy Azure za pomocą zapory aplikacji sieci Web platformy Azure

Zapora aplikacji sieci Web platformy Azure (WAF) w połączeniu z platformą Azure — informacje o zabezpieczeniach — zarządzanie zdarzeniami dla zasobów WAF. Usługa Azure WAF zapewnia analizę zabezpieczeń przy użyciu Log Analytics, co pozwala na łatwe rozbicie i wyświetlenie danych. Korzystając z funkcji wskaźnikowej platformy Azure, możesz uzyskać dostęp do wstępnie skompilowanych skoroszytów i zmodyfikować je tak, aby najlepiej odpowiadały potrzebom organizacji. Skoroszyt może przedstawiać analizę WAF w usłudze Azure Content Delivery Network (CDN), WAF na platformie Azure z przodu i WAF na Application Gateway wielu subskrypcjach i obszarach roboczych.

## <a name="waf-log-analytics-categories"></a>WAF — kategorie usługi log Analytics

WAF log Analytics są podzielone na następujące kategorie:  

- Wszystkie wykonane akcje WAF 
- 40 najpopularniejszych adresów URI żądań 
- Najważniejsze wyzwalacze zdarzeń 50,  
- Komunikaty w czasie 
- Pełne szczegóły komunikatu 
- Zdarzenia ataku według komunikatów  
- Zdarzenia ataku w czasie 
- Filtr identyfikatorów śledzenia 
- Komunikaty o IDENTYFIKATORze śledzenia 
- 10 najważniejszych adresów IP ataków 
- Komunikaty ataków adresów IP 

## <a name="waf-workbook-examples"></a>Przykłady skoroszytów WAF

Następujące przykłady skoroszytu WAF pokazują przykładowe dane:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="Filtr akcji WAF":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="Najważniejsze zdarzenia 50":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Zdarzenia ataku":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="10 najważniejszych adresów IP ataków":::

## <a name="launch-a-waf-workbook"></a>Uruchamianie skoroszytu WAF

Skoroszyt WAF działa dla wszystkich zewnętrznych drzwi platformy Azure, Application Gateway i sieciowi sieci CDN. Przed połączeniem danych z tych zasobów należy włączyć usługę log Analytics dla zasobu. 

Aby włączyć usługę log Analytics dla każdego zasobu, przejdź do poszczególnych zasobów usług Azure front-drzwi, Application Gateway lub CDN:

1. Wybierz pozycję **Ustawienia diagnostyczne**.
2. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne**. 
3. Na stronie Ustawienia diagnostyczne:
   1. Wpisz nazwę. 
   1. Wybierz pozycję **Wyślij do log Analytics**. 
   1. Wybierz docelowy obszar roboczy dziennika. 
   1. Wybierz typy dzienników, które chcesz analizować:
      1. Application Gateway: "ApplicationGatewayAccessLog" i "ApplicationGatewayFirewallLog"
      1. Drzwi frontonu platformy Azure: "FrontDoorAccessLog" i "FrontDoorFirewallLog"
      1. CDN: "AzureCdnAccessLog"
   1. Wybierz pozycję **Zapisz**.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Ustawienie diagnostyczne":::

4. Na stronie głównej platformy Azure wpisz **wskaźnik na platformie Azure** na pasku wyszukiwania, a następnie wybierz zasób **wskaźnik platformy Azure** . 
2. Wybierz już aktywny obszar roboczy lub Utwórz nowy obszar roboczy. 
3. W panelu po lewej stronie w obszarze **Konfiguracja** wybierz pozycję **Łączniki danych**.
4. Wyszukaj **zaporę aplikacji sieci Web firmy Microsoft** i wybierz pozycję **Zapora aplikacji sieci Web firmy Microsoft (WAF)**. Wybierz pozycję **Otwórz stronę łącznika** w prawym dolnym rogu.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Łączniki danych":::

8. Postępuj zgodnie z instrukcjami w obszarze **Konfiguracja** dla każdego zasobu WAF, który ma mieć dane analityczne dziennika, jeśli wcześniej nie zostało to zrobione.
6. Po zakończeniu konfigurowania poszczególnych zasobów WAF wybierz kartę **następne kroki** . Wybierz jeden z zalecanych skoroszytów. Ten skoroszyt będzie używać wszystkich danych analitycznych dziennika, które zostały wcześniej włączone. Dla zasobów usługi WAF powinien istnieć skoroszyt roboczy WAF.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF skoroszyty":::


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej na temat platformy Azure — Wskaźnikowanie](../sentinel/overview.md)
- [Dowiedz się więcej o Azure Monitor skoroszytach](../azure-monitor/visualize/workbooks-overview.md)
