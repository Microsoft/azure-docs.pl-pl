---
title: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu Azure Portal
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu Azure Portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680945"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu Azure Portal

W tym artykule opisano sposób tworzenia komunikacji równorzędnej programu Microsoft Exchange przy użyciu Azure Portal. W tym artykule przedstawiono również sposób sprawdzania stanu zasobu, aktualizowania go lub usuwania i anulowania aprowizacji.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [programu PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj się z tematem [wymagania wstępne](prerequisites.md) i [Przewodnik po komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md) .
* Jeśli masz już komunikację równorzędną programu Exchange z firmą Microsoft, która nie jest konwertowana na zasoby platformy Azure, zobacz [konwertowanie starszej komunikacji równorzędnej programu Exchange do zasobu platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Tworzenie i Inicjowanie obsługi komunikacji równorzędnej programu Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej programu Exchange

Nowe żądanie komunikacji równorzędnej można utworzyć przy użyciu zasobu **komunikacji równorzędnej** .

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Uruchom zasób i skonfiguruj ustawienia podstawowe
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Weryfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Anulowanie aprowizacji komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md).
