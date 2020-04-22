---
title: Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu witryny Azure portal
titleSuffix: Azure
description: Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu witryny Azure portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678513"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu witryny Azure portal

W tym artykule opisano sposób konwertowania istniejącej starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu witryny Azure portal.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i przewodnik [dla programu Exchange równorzędnej](walkthrough-exchange-all.md) przed rozpoczęciem konfiguracji.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange

Starsze połączenia komunikacji równorzędnej można konwertować przy użyciu zasobu **komunikacji równorzędnej.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Uruchamianie zasobu i konfigurowanie podstawowych ustawień
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Weryfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące komunikacji równorzędnej w Internecie](faqs.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
