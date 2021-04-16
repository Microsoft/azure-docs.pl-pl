---
title: Programowe tworzenie subskrypcji platformy Azure
description: Ten artykuł ułatwia zapoznanie się z opcjami dostępnymi w celu programowego tworzenia subskrypcji platformy Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce08ebf473b11eecae327c7de050c791f5bc1b1a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379013"
---
# <a name="create-azure-subscriptions-programmatically"></a>Programowe tworzenie subskrypcji platformy Azure

Ten artykuł ułatwia zapoznanie się z opcjami dostępnymi w celu programowego tworzenia subskrypcji platformy Azure.

Korzystając z różnych interfejsów API REST, można utworzyć subskrypcję dla następujących typów umów platformy Azure:

- Enterprise Agreement (EA)
- Umowa klienta firmy Microsoft (MCA)
- Umowa partnerska firmy Microsoft

Za pomocą interfejsów API REST nie można programowo tworzyć dodatkowych subskrypcji dla innych typów umów.

Wymagania i szczegółowe informacje dotyczące tworzenia subskrypcji różnią się w zależności od różnych umów i wersji interfejsu API. Zapoznaj się z następującymi artykułami dotyczącymi danej sytuacji:

Najnowsze interfejsy API:

- [Tworzenie subskrypcji EA](programmatically-create-subscription-enterprise-agreement.md)
- [Tworzenie subskrypcji MCA](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Tworzenie subskrypcji MPA](programmatically-create-subscription-microsoft-partner-agreement.md)

W tych artykułach popisano również, jak tworzyć subskrypcje za pomocą Azure Resource Manager szablonu usługi ARM. Szablon usługi ARM pomaga zautomatyzować proces tworzenia subskrypcji.

Jeśli nadal używasz interfejsów API w wersji [zapoznawczej,](programmatically-create-subscription-preview.md)możesz nadal tworzyć za ich pomocą subskrypcje. 

## <a name="next-steps"></a>Następne kroki

* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji za pomocą grup zarządzania, zobacz [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../governance/management-groups/overview.md).
