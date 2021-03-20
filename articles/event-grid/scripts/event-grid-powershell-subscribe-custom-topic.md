---
title: Przykładowy skrypt programu Azure PowerShell — subskrybowanie do tematu niestandardowego | Microsoft Docs
description: Ten artykuł zawiera przykładowy skrypt Azure PowerShell, który pokazuje, jak subskrybować zdarzenia Event Grid dla tematu niestandardowego.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: f37a90b84e5e55298efc0b3cd53812db95e4d86d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460732"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>Subskrybowanie do zdarzeń dla tematu niestandardowego przy użyciu programu PowerShell

Ten skrypt tworzy subskrypcję usługi Event Grid na potrzeby zdarzeń związanych z tematem niestandardowym.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Przykładowy skrypt wersji zapoznawczej wymaga modułu usługi Event Grid. Aby go zainstalować, uruchom polecenie `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Przykładowy skrypt — stabilny

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]

## <a name="sample-script---preview-module"></a>Przykładowy skrypt — moduł w wersji zapoznawczej

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia w celu utworzenia subskrypcji zdarzeń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Tworzy subskrypcję usługi Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat programu PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/get-started-azureps).
