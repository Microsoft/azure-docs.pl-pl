---
title: Tworzenie lub modyfikowanie komunikacji równorzędnej direct przy użyciu programu PowerShell
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej direct przy użyciu programu PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680775"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej direct przy użyciu programu PowerShell

W tym artykule opisano sposób tworzenia komunikacji równorzędnej microsoft direct przy użyciu poleceń cmdlet programu PowerShell i modelu wdrażania usługi Azure Resource Manager. W tym artykule pokazano również, jak sprawdzić stan zasobu, zaktualizować go lub usunąć i anulować jego aprowizowanie.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu [witryny](howto-direct-portal.md)Azure portal .

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i bezpośredni przewodnik [komunikacji równorzędnej](walkthrough-direct-all.md) przed rozpoczęciem konfiguracji.
* Jeśli masz już bezpośrednie połączenia komunikacji równorzędnej z firmą Microsoft, które nie są konwertowane na zasoby platformy Azure, zobacz [Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Praca z programem Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Tworzenie i inicjowanie obsługi administracyjnej komunikacji równorzędnej

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Zaloguj się na swoje konto platformy Azure i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Pobierz listę obsługiwanych lokalizacji komunikacji równorzędnej dla komunikacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej bezpośredniej
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie komunikacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej bezpośredniej
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Anulowanie obsługi administracyjnej funkcji bezpośrednie równorzędne
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu programu PowerShell](howto-exchange-powershell.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu programu PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Dodatkowe zasoby
Szczegółowe opisy wszystkich parametrów można uzyskać, uruchamiając następujące polecenie:

```powershell
Get-Help Get-AzPeering -detailed
```

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące komunikacji równorzędnej w Internecie](faqs.md).
