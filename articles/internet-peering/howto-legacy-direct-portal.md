---
title: Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu witryny Azure portal
titleSuffix: Azure
description: Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu witryny Azure portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678857"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu witryny Azure portal

W tym artykule opisano sposób konwertowania istniejącej starszej funkcji direct komunikacji równorzędnej na zasób platformy Azure przy użyciu witryny Azure portal.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i bezpośredni przewodnik [komunikacji równorzędnej](walkthrough-direct-all.md) przed rozpoczęciem konfiguracji.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej

Starsze połączenia komunikacji równorzędnej można konwertować przy użyciu zasobu **komunikacji równorzędnej.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Uruchamianie zasobu i konfigurowanie podstawowych ustawień
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie komunikacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące komunikacji równorzędnej w Internecie](faqs.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej direct przy użyciu portalu](howto-direct-portal.md)
