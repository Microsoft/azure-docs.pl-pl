---
title: plik dołączany
description: plik dołączany
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77133606"
---
Otwórz konsolę programu PowerShell z podniesionymi uprawnieniami.

Jeśli używasz Azure PowerShell lokalnie, Połącz się z kontem platformy Azure. Polecenie cmdlet *Connect-AzAccount* poprosi o poświadczenia. Po uwierzytelnieniu program pobiera Twoje ustawienia konta, aby były dostępne do Azure PowerShell. Jeśli używasz Azure Cloud Shell, nie musisz uruchamiać polecenia *Connect-AzAccount*. Azure Cloud Shell automatycznie nawiązuje połączenie z kontem platformy Azure.

```azurepowershell
Connect-AzAccount
```

Jeśli masz więcej niż jedną subskrypcję, uzyskaj listę subskrypcji platformy Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Wskaż subskrypcję, której chcesz użyć.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```