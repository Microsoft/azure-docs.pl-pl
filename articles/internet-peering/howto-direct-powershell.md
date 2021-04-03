---
title: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
titleSuffix: Azure
description: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be4bab10d369fcefe626bbe890acfff7fc7004b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079068"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu programu PowerShell

W tym artykule opisano sposób tworzenia bezpośredniej komunikacji równorzędnej firmy Microsoft przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Azure Resource Manager. W tym artykule przedstawiono również sposób sprawdzania stanu zasobu, aktualizowania go lub usuwania i anulowania aprowizacji.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu witryny Azure [Portal](howto-direct-portal.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz
* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](prerequisites.md) i [bezpośredni Przewodnik po komunikacji równorzędnej](walkthrough-direct-all.md) .
* Jeśli masz już bezpośrednie połączenia komunikacji równorzędnej z firmą Microsoft, które nie są konwertowane na zasoby platformy Azure, zobacz [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Pracuj z Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Tworzenie i obsługa bezpośredniej komunikacji równorzędnej

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się do konta platformy Azure i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Pobierz listę obsługiwanych lokalizacji komunikacji równorzędnej dla bezpośredniej komunikacji równorzędnej
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Tworzenie bezpośredniej komunikacji równorzędnej
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modyfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Anulowanie aprowizacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu programu PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Dodatkowe zasoby
Aby uzyskać szczegółowe opisy wszystkich parametrów, należy uruchomić następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby uzyskać więcej informacji, zobacz temat [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md).
