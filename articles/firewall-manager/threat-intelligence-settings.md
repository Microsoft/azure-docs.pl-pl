---
title: Konfiguracja analizy zagrożeń zapory platformy Azure
description: Dowiedz się, jak skonfigurować filtrowanie na podstawie analizy zagrożeń dla zasad zapory platformy Azure w celu wygenerowania alertów i odmowy ruchu z i do znanych złośliwych adresów IP i domen.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530645"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Konfiguracja analizy zagrożeń zapory platformy Azure

Filtrowanie na podstawie analizy zagrożeń można skonfigurować dla zasad zapory platformy Azure w celu zgłaszania alertów i odmowy ruchu z i do znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) zapewnia program Microsoft Threat Intelligence i jest używany przez wiele usług, w tym Azure Security Center.<br>

W przypadku skonfigurowania filtrowania opartego na analizie zagrożeń skojarzone reguły są przetwarzane przed dowolnymi regułami NAT, regułami sieciowymi lub regułami aplikacji.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Zasady analizy zagrożeń":::

## <a name="threat-intelligence-mode"></a>Tryb analizy zagrożeń

Można wybrać opcję rejestrowania alertu tylko w przypadku wyzwolenia reguły lub można wybrać opcję alert i tryb odmowy.

Domyślnie filtrowanie oparte na analizie zagrożeń jest włączone w trybie alertu.

## <a name="allowed-list-addresses"></a>Adresy listy dozwolonych

Można skonfigurować listę dozwolonych adresów IP, aby analiza zagrożeń nie odfiltrować żadnego z określonych adresów, zakresów lub podsieci.



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

- Przejrzyj [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)