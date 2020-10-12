---
title: Dzienniki Azure Monitor dla zapory platformy Azure
description: Za pomocą dzienników Azure Monitor można analizować zaporę platformy Azure. Przykładowy plik jest skompilowany w projektancie widoków w Azure Monitor.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/11/2020
ms.author: victorh
ms.openlocfilehash: 5acbc1f3b8c5519c22105f05219ab2cef5c15892
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90023877"
---
# <a name="azure-monitor-logs-for-azure-firewall"></a>Dzienniki Azure Monitor dla zapory platformy Azure

Poniższe przykłady dzienników Azure Monitor mogą służyć do analizowania dzienników zapory platformy Azure. Przykładowy plik jest wbudowany w projektanta widoków w Azure Monitor, [Projektant widoków w Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-view-designer) artykule zawiera więcej informacji na temat koncepcji projektowej widoku.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-monitor-logs-view"></a>Widok dzienników Azure Monitor

Oto jak można skonfigurować przykładową wizualizację dzienników Azure Monitor. Przykładową wizualizację można pobrać z repozytorium [Azure-docs-JSON-Samples](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview) . Najprostszym sposobem jest kliknięcie prawym przyciskiem myszy hiperłącza na tej stronie i wybranie polecenia *Zapisz jako* i podanie nazwy, takiej jak **AzureFirewall. omsview**. 

Wykonaj następujące kroki, aby dodać widok do obszaru roboczego Log Analytics:

1. Otwórz obszar roboczy Log Analytics w Azure Portal.
2. Otwórz **projektanta widoków** poniżej **ogólnego**.
3. Kliknij pozycję **Importuj**.
4. Przeglądaj i wybierz pobrany wcześniej plik **AzureFirewall. omsview** .
5. Kliknij przycisk **Zapisz**.

Oto jak widok wyszukuje dane dziennika reguły aplikacji:

![Dane dziennika reguły aplikacji](./media/log-analytics-samples/azurefirewall-applicationrulelogstats.png)

I dla danych dziennika reguły sieci:

![Dane dziennika reguły sieci]( ./media/log-analytics-samples/azurefirewall-networkrulelogstats.png)

Zapora platformy Azure rejestruje dane poniżej AzureDiagnostics z kategorią jako **AzureFirewallApplicationRule** lub **AzureFirewallNetworkRule**. Dane zawierające szczegóły są przechowywane w polu msg_s. Za pomocą operatora [analizy](https://docs.microsoft.com/azure/kusto/query/parseoperator) możemy wyodrębnić różne interesujące właściwości z pola msg_s. Poniższe zapytania wyodrębniają informacje dla obu kategorii.

## <a name="application-rules-log-data-query"></a>Zapytanie o dane dziennika reguł aplikacji

Poniższe zapytanie analizuje dane dziennika reguły aplikacji. W różnych wierszach komentarzy istnieją pewne wskazówki dotyczące sposobu tworzenia zapytania:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//this first parse statement is valid for all entries as they all start with this format
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
//case 1: for records that end with: "was denied. Reason: SNI TLS extension was missing."
| parse TempDetails with "was " Action1 ". Reason: " Rule1
//case 2: for records that end with
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
//"to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
//case 2a: for records that end with:
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
//case 2b: for records that end with:
//for records that end with: "to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend 
SourcePort = tostring(SourcePortInt)
|extend
TargetPort = tostring(TargetPortInt)
| extend
//make sure we only have Allowed / Deny in the Action Field
Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend
    Action = case(Action2 == "",Action1,Action2),
    Rule = case(Rule2a == "",case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
    RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a),RuleCollection2b),
    FQDN = case(FQDN == "", "N/A", FQDN),
    TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

To samo zapytanie w bardziej zagęszczonym formacie:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
| parse TempDetails with "was " Action1 ". Reason: " Rule1
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend SourcePort = tostring(SourcePortInt)
| extend TargetPort = tostring(TargetPortInt)
| extend Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend Action = case(Action2 == "",Action1,Action2),Rule = case(Rule2a == "", case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a), RuleCollection2b),FQDN = case(FQDN == "", "N/A", FQDN),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

## <a name="network-rules-log-data-query"></a>Zapytanie o dane dziennika reguł sieciowych

Poniższe zapytanie analizuje dane dziennika reguł sieci. W różnych wierszach komentarzy istnieją pewne wskazówki dotyczące sposobu tworzenia zapytania:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//case 1: for records that look like this:
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
//case 1a: for regular network rules
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
| parse msg_s with * ". Action: " Action1a
//case 1b: for NAT rules
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with * " was " Action1b " to " NatDestination
//case 2: for ICMP records
//ICMP request from 10.0.2.4 to 10.0.3.4. Action: Allow
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend
SourcePort = tostring(SourcePortInt),
TargetPort = tostring(TargetPortInt)
| extend 
    Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),
    Protocol = case(Protocol == "", Protocol2, Protocol),
    SourceIP = case(SourceIP == "", SourceIP2, SourceIP),
    TargetIP = case(TargetIP == "", TargetIP2, TargetIP),
    //ICMP records don't have port information
    SourcePort = case(SourcePort == "", "N/A", SourcePort),
    TargetPort = case(TargetPort == "", "N/A", TargetPort),
    //Regular network rules don't have a DNAT destination
    NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

To samo zapytanie w bardziej zagęszczonym formacie:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action1a
| parse msg_s with * " was " Action1b " to " NatDestination
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

## <a name="threat-intelligence-log-data-query"></a>Zapytanie o dane dziennika analizy zagrożeń

Następujące zapytanie analizuje dane dziennika reguły analizy zagrożeń:

```Kusto
AzureDiagnostics
| where OperationName  == "AzureFirewallThreatIntelLog"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action "." Message
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| sort by TimeGenerated desc | project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action,Message
```

## <a name="sample-logs"></a>Przykładowe dzienniki

Poniższe przykłady dzienników przedstawiają dane zawarte w wpisie dziennika.

![wpis dziennika — jeden](media/log-analytics-samples/log1.png)

![wpis dziennika dwa ](media/log-analytics-samples/log2.png)

![wpis dziennika trzy](media/log-analytics-samples/log3.png)
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitorowania i diagnostyki zapory platformy Azure, zobacz [Samouczek: monitorowanie dzienników i metryk zapory platformy Azure](tutorial-diagnostics.md).
