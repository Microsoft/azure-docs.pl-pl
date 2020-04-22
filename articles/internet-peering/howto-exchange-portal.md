---
title: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu Azure
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680945"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu Azure

W tym artykule opisano sposób tworzenia komunikacji równorzędnej programu Microsoft Exchange przy użyciu witryny Azure portal. W tym artykule pokazano również, jak sprawdzić stan zasobu, zaktualizować go lub usunąć i anulować jego aprowizję.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i przewodnik [dla programu Exchange równorzędnej](walkthrough-exchange-all.md) przed rozpoczęciem konfiguracji.
* Jeśli masz już programy komunikacji równorzędnej programu Exchange z firmą Microsoft, które nie są konwertowane na zasoby platformy Azure, zobacz [Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Tworzenie i inicjowanie obsługi administracyjnej komunikacji równorzędnej programu Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej programu Exchange

Nowe żądanie komunikacji równorzędnej można utworzyć przy użyciu zasobu **komunikacji równorzędnej.**

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Uruchamianie zasobu i konfigurowanie podstawowych ustawień
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Weryfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Anulowanie obsługi administracyjnej komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej direct przy użyciu portalu](howto-direct-portal.md)
* [Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące komunikacji równorzędnej w Internecie](faqs.md).
