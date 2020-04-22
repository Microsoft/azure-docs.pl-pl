---
title: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 23c905f148da614c7785b61b76abed191206cd90
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678687"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell

W tym artykule opisano sposób tworzenia komunikacji równorzędnej programu Microsoft Exchange przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania Menedżera zasobów. W tym artykule pokazano również, jak sprawdzić stan zasobu, zaktualizować go lub usunąć i anulować jego aprowizowanie.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [witryny](howto-exchange-portal.md)Azure portal .

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i przewodnik [dla programu Exchange równorzędnej](walkthrough-exchange-all.md) przed rozpoczęciem konfiguracji.
* Jeśli masz już programy komunikacji równorzędnej programu Exchange z firmą Microsoft, które nie są konwertowane na zasoby platformy Azure, zobacz [Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell.](howto-legacy-exchange-powershell.md)

### <a name="work-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Tworzenie i inicjowanie obsługi administracyjnej komunikacji równorzędnej programu Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Pobierz listę obsługiwanych lokalizacji komunikacji równorzędnej dla komunikacji równorzędnej programu Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej programu Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Uzyskaj komunikację równorzędnie programu Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Anulowanie obsługi administracyjnej komunikacji równorzędnej programu Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej direct przy użyciu programu PowerShell](howto-direct-powershell.md)
* [Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Dodatkowe zasoby
Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące komunikacji równorzędnej w Internecie](faqs.md).
