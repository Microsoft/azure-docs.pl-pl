---
title: Azure PowerShell — Subskrybuj subskrypcję platformy Azure
description: Ten artykuł zawiera przykładowy skrypt Azure PowerShell, który pokazuje, jak subskrybować zdarzenia Event Grid dla subskrypcji platformy Azure.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 4a6539966ff2e907c4f6f942dd0c238ee35b6836
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76721459"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Subskrybowanie zdarzeń dla subskrypcji platformy Azure przy użyciu programu PowerShell

Ten skrypt tworzy subskrypcję usługi Event Grid na potrzeby zdarzeń związanych z subskrypcją platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Przykładowy skrypt — stabilny

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>Przykładowy skrypt — moduł w wersji zapoznawczej

Ten przykładowy skrypt w wersji zapoznawczej wymaga modułu Event Grid. Aby go zainstalować, uruchom polecenie `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia w celu utworzenia subskrypcji zdarzeń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Tworzy subskrypcję usługi Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat programu PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
