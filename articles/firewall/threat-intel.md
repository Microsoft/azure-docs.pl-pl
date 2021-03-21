---
title: Filtrowanie na podstawie analizy zagrożeń zapory platformy Azure
description: Dla zapory można włączyć filtrowanie na podstawie analizy zagrożeń, aby zapora mogła odrzucać ruch z/do znanych złośliwych adresów IP i domen oraz wysyłać odpowiednie alerty.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/12/2020
ms.author: victorh
ms.openlocfilehash: 77e36d5ab98f1177b5a0b89ee10b3e6bd22560c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791463"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtrowanie na podstawie analizy zagrożeń zapory platformy Azure

Dla zapory można włączyć filtrowanie na podstawie analizy zagrożeń, aby zapora mogła odrzucać ruch z/do znanych złośliwych adresów IP i domen oraz wysyłać odpowiednie alerty. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) zapewnia program Microsoft Threat Intelligence i jest używany przez wiele usług, w tym Azure Security Center.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Analiza zagrożeń zapory" border="false":::

W przypadku włączenia filtrowania opartego na analizie zagrożeń skojarzone reguły są przetwarzane przed dowolnymi regułami NAT, regułami sieciowymi lub regułami aplikacji.

Można wybrać opcję rejestrowania alertu, gdy reguła jest wyzwalana, lub wybrać opcję alert i tryb Odmów.

Domyślnie filtrowanie oparte na analizie zagrożeń jest włączone w trybie alertu. Nie można wyłączyć tej funkcji ani zmienić trybu do momentu udostępnienia interfejsu portalu w Twoim regionie.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Interfejs portalu filtrowania oparty na analizie zagrożeń":::

## <a name="logs"></a>Dzienniki

Poniższy fragment dziennika przedstawia wyzwalaną regułę:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testowanie

- **Testowanie wychodzące** — alerty ruchu wychodzącego powinny być rzadkimi wystąpieniami, ponieważ oznacza to naruszenie bezpieczeństwa środowiska. Aby można było przetestować alerty wychodzące, została utworzona testowa nazwa FQDN, która wyzwala alert. Użyj **testmaliciousdomain.eastus.cloudapp.Azure.com** dla testów wychodzących.

- **Testowanie przychodzące** — możesz oczekiwać, że alerty dotyczące ruchu przychodzącego są wyświetlane, jeśli reguły DNAT są skonfigurowane na zaporze. Jest to prawdziwe, nawet jeśli tylko określone źródła są dozwolone w regule DNAT, a ruch jest odrzucany w inny sposób. Zapora platformy Azure nie ma alertu dotyczącego wszystkich znanych skanerów portów; tylko w przypadku skanerów, które są znane również w przypadku złośliwego działania.

## <a name="next-steps"></a>Następne kroki

- Zobacz [przykłady log Analytics zapory platformy Azure](./firewall-workbook.md)
- Dowiedz się [, jak wdrożyć i skonfigurować zaporę platformy Azure](tutorial-firewall-deploy-portal.md)
- Przejrzyj [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)