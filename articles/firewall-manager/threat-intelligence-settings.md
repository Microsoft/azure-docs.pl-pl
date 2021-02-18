---
title: Konfiguracja analizy zagrożeń zapory platformy Azure
description: Dowiedz się, jak skonfigurować filtrowanie na podstawie analizy zagrożeń dla zasad zapory platformy Azure w celu wygenerowania alertów i odmowy ruchu z i do znanych złośliwych adresów IP i domen.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651729"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Konfiguracja analizy zagrożeń zapory platformy Azure

Filtrowanie na podstawie analizy zagrożeń można skonfigurować dla zasad zapory platformy Azure w celu zgłaszania alertów i odmowy ruchu z i do znanych złośliwych adresów IP i domen. Adresy IP i domeny pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) zapewnia program Microsoft Threat Intelligence i jest używany przez wiele usług, w tym Azure Security Center.<br>

W przypadku skonfigurowania filtrowania opartego na analizie zagrożeń skojarzone reguły są przetwarzane przed dowolnymi regułami NAT, regułami sieciowymi lub regułami aplikacji.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Zasady analizy zagrożeń":::

## <a name="threat-intelligence-mode"></a>Tryb analizy zagrożeń

Analizę zagrożeń można skonfigurować w jednym z trzech trybów, które są opisane w poniższej tabeli. Domyślnie filtrowanie oparte na analizie zagrożeń jest włączone w trybie alertu.

|Tryb |Opis  |
|---------|---------|
|`Off`     | Funkcja analizy zagrożeń nie jest włączona dla zapory. |
|`Alert only`     | Będziesz otrzymywać alerty o wysokim poziomie pewności dla ruchu przechodzącego przez zaporę do lub ze znanych złośliwych adresów IP i domen. |
|`Alert and deny`     | Ruch jest blokowany i otrzymasz alerty o wysokim poziomie pewności, gdy zostanie wykryta próba przechodzenia przez zaporę do lub ze znanych złośliwych adresów IP i domen. |

> [!NOTE]
> Tryb analizy zagrożeń jest Dziedziczony z zasad nadrzędnych do zasad podrzędnych. Zasady podrzędne muszą być skonfigurowane przy użyciu tego samego lub rygorystycznego trybu niż zasady nadrzędne.

## <a name="allowlist-addresses"></a>Adresy dozwolonych

Analiza zagrożeń może wyzwolić fałszywie dodatnie i blokować ruch, który faktycznie jest prawidłowy. Można skonfigurować listę dozwolonych adresów IP, aby analiza zagrożeń nie odfiltrować żadnego z określonych adresów, zakresów lub podsieci.  

![Adresy dozwolonych](media/threat-intelligence-settings/allow-list.png)

Możesz zaktualizować dozwolonych z wieloma wpisami jednocześnie, przekazując plik CSV. Plik CSV może zawierać tylko adresy IP i zakresy. Plik nie może zawierać nagłówków.

> [!NOTE]
> Adresy dozwolonych analizy zagrożeń są dziedziczone z zasad nadrzędnych do zasad podrzędnych. Każdy adres IP lub zakres dodany do zasad nadrzędnych również zostaną zastosowane do wszystkich zasad podrzędnych.

## <a name="logs"></a>Dzienniki

Poniższy fragment dziennika przedstawia wyzwalaną regułę dla ruchu wychodzącego do złośliwej lokacji:

```json
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
