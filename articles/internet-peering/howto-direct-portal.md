---
title: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal
titleSuffix: Azure
description: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681049"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu Azure Portal

W tym artykule opisano sposób tworzenia bezpośredniej komunikacji równorzędnej firmy Microsoft przy użyciu Azure Portal. W tym artykule przedstawiono również sposób sprawdzania stanu zasobu, aktualizowania go lub usuwania i anulowania aprowizacji.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu programu Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](prerequisites.md) i [bezpośredni Przewodnik po komunikacji równorzędnej](walkthrough-direct-all.md) .
* Jeśli masz już bezpośrednie połączenia komunikacji równorzędnej z firmą Microsoft, które nie są konwertowane na zasoby platformy Azure, zobacz [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Tworzenie i obsługa bezpośredniej komunikacji równorzędnej

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Tworzenie bezpośredniej komunikacji równorzędnej

Nowe żądanie komunikacji równorzędnej można utworzyć przy użyciu zasobu **komunikacji równorzędnej** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Uruchom zasób i skonfiguruj ustawienia podstawowe
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modyfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Anulowanie aprowizacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md).
