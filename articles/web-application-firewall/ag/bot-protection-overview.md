---
title: WAF na temat usługi Azure Application Gateway bot Protection — Omówienie
titleSuffix: Azure Web Application Firewall
description: Ten artykuł zawiera omówienie zapory aplikacji sieci Web (WAF) na Application Gateway bot Protection
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83714903"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Omówienie funkcji Zapora aplikacji sieci Web platformy Azure w usłudze Azure Application Gateway bot Protection

Około 20% całego ruchu internetowego pochodzi z nieprawidłowych botów. Mogą to być takie jak braki, skanowanie i wyszukiwanie luk w zabezpieczeniach aplikacji sieci Web. Gdy te botów są zatrzymane w zaporze aplikacji sieci Web (WAF), nie mogą się z nią zaatakować. Nie mogą oni również używać zasobów i usług, takich jak frontony i inna podstawowa infrastruktura.

Możesz włączyć zarządzany zestaw reguł ochrony przed botami dla zapory aplikacji internetowej, aby blokować lub rejestrować żądania ze znanych złośliwych adresów IP. Adresy IP pochodzą z kanału informacyjnego analizy zagrożeń firmy Microsoft. System Intelligent Security Graph obsługuje analizę zagrożeń firmy Microsoft i jest używany przez wiele usług, w tym Azure Security Center.

> [!IMPORTANT]
> Zestaw reguł ochrony bot jest obecnie w publicznej wersji zapoznawczej i jest dostarczany z umową dotyczącą poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure   .

## <a name="use-with-owasp-rulesets"></a>Używanie z zestawami reguł OWASP

Możesz użyć zestawu reguł ochrony przed botami wraz z dowolnym zestawem reguł OWASP (2.2.9, 3.0 i 3.1). W danym momencie może być używany tylko jeden zestaw reguł OWASP. Zestaw reguł ochrony przed botami zawiera dodatkową regułę, która pojawia się we własnym zestawie reguł. Jest ona zatytułowana **Microsoft_BotManagerRuleSet_0 dziesiąta** i można ją włączyć lub wyłączyć, podobnie jak w przypadku innych reguł OWASP.

![Zestaw reguł bot](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Aktualizacja zestawu reguł

Lista zestawów reguł ograniczenia ryzyka działania botów ze znanymi nieprawidłowymi adresami IP jest aktualizowana wiele razy dziennie w kanale informacyjnym analizy zagrożeń firmy Microsoft, aby zachować synchronizację z botami. Aplikacje internetowe są stale chronione, nawet w przypadku zmiany wektorów ataków botów.

## <a name="log-example"></a>Przykład dziennika

Oto przykład wpisu dziennika dla ochrony bot:

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ochrony przed botami dla zapory aplikacji internetowej w usłudze Azure Application Gateway (wersja zapoznawcza)](bot-protection.md)
