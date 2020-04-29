---
title: Ustawienia portów Azure Relay | Microsoft Docs
description: Ten artykuł zawiera tabelę opisującą wymaganą konfigurację wartości portów dla Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80529108"
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
|[BasicHttpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (usługa)|Żadnego|9351/HTTP|  
|[NetEventRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Tak|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[NetEventRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (usługa)|Żadnego|9351/HTTP|  
|[NetTcpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/usługa)|Żadnego|5671/9352/HTTP (9352/9353, jeśli używasz hybrydowej)|  
|[NetOnewayRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Tak|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[NetOnewayRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (usługa)|Żadnego|9351/HTTP|  
|[WebHttpRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Tak|HTTPS|  
|" |Nie|HTTP|  
|[WebHttpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (usługa)|Żadnego|9351/HTTP|  
|[WS2007HttpRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Tak|HTTPS|  
|" |Nie|HTTP|  
|[WS2007HttpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (usługa)|Żadnego|9351/HTTP|

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Azure Relay, odwiedź następujące linki:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)