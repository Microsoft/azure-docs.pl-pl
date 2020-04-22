---
title: Tworzenie lub modyfikowanie komunikacji równorzędnej bezpośredniej przy użyciu witryny Azure portal
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej bezpośredniej przy użyciu witryny Azure portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681049"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej bezpośredniej przy użyciu witryny Azure portal

W tym artykule opisano sposób tworzenia komunikacji równorzędnej microsoft direct przy użyciu witryny Azure portal. W tym artykule pokazano również, jak sprawdzić stan zasobu, zaktualizować go lub usunąć i anulować jego aprowizję.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu programu Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i bezpośredni przewodnik [komunikacji równorzędnej](walkthrough-direct-all.md) przed rozpoczęciem konfiguracji.
* Jeśli masz już bezpośrednie połączenia komunikacji równorzędnej z firmą Microsoft, które nie są konwertowane na zasoby platformy Azure, zobacz [Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Tworzenie i inicjowanie obsługi administracyjnej komunikacji równorzędnej

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej bezpośredniej

Nowe żądanie komunikacji równorzędnej można utworzyć przy użyciu zasobu **komunikacji równorzędnej.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Uruchamianie zasobu i konfigurowanie podstawowych ustawień
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie komunikacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej bezpośredniej
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Anulowanie obsługi administracyjnej funkcji bezpośrednie równorzędne
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące komunikacji równorzędnej w Internecie](faqs.md).
