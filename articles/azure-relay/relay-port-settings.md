---
title: Ustawienia portów Azure Relay | Microsoft Docs
description: Ten artykuł zawiera tabelę opisującą wymaganą konfigurację wartości portów dla Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 97640debe81041ff7e2b082c6a9ac606d6088664
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85314268"
---
# <a name="azure-relay-port-settings"></a>Ustawienia portu Azure Relay

W poniższej tabeli opisano wymaganą konfigurację dla wartości portów dla Azure Relay.

## <a name="hybrid-connections"></a>Połączenia hybrydowe

Połączenia hybrydowe używa obiektów WebSockets na porcie 443 przy użyciu protokołu TLS jako podstawowego mechanizmu transportowego, który używa tylko **protokołu HTTPS** . 

## <a name="wcf-relays"></a>Przekaźniki WCF
  
|Wiązanie|Zabezpieczenia transportu|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Tak|HTTPS| 
|" |Nie|HTTP|  
|[BasicHttpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (usługa)|Dowolny|9351/HTTP|  
|[NetEventRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Tak|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[NetEventRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (usługa)|Dowolny|9351/HTTP|  
|[NetTcpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/usługa)|Dowolny|5671/9352/HTTP (9352/9353, jeśli używasz hybrydowej)|  
|[NetOnewayRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Tak|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[NetOnewayRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (usługa)|Dowolny|9351/HTTP|  
|[WebHttpRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Tak|HTTPS|  
|" |Nie|HTTP|  
|[WebHttpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (usługa)|Dowolny|9351/HTTP|  
|[WS2007HttpRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Tak|HTTPS|  
|" |Nie|HTTP|  
|[WS2007HttpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (usługa)|Dowolny|9351/HTTP|

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Azure Relay, odwiedź następujące linki:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)