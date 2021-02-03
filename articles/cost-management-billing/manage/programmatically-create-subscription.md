---
title: Programowe tworzenie subskrypcji platformy Azure
description: Ten artykuł ułatwia zapoznanie się z opcjami dostępnymi w celu programowego tworzenia subskrypcji platformy Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 546ed24b5f9e7892f40c9d425b668f60ad705f8f
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493892"
---
# <a name="create-azure-subscriptions-programmatically"></a>Programowe tworzenie subskrypcji platformy Azure

Ten artykuł ułatwia zapoznanie się z opcjami dostępnymi w celu programowego tworzenia subskrypcji platformy Azure.

Korzystając z różnych interfejsów API REST, można utworzyć subskrypcję dla następujących typów umów platformy Azure:

- Enterprise Agreement (EA)
- Umowa klienta firmy Microsoft (MCA)
- Umowa partnerska firmy Microsoft

Nie można programowo tworzyć dodatkowych subskrypcji dla innych typów umów z interfejsami API REST.

Wymagania i szczegółowe informacje dotyczące tworzenia subskrypcji różnią się w zależności od różnych umów i wersji interfejsu API. Zapoznaj się z następującymi artykułami dotyczącymi danej sytuacji:

Najnowsze interfejsy API:

- [Tworzenie subskrypcji EA](programmatically-create-subscription-enterprise-agreement.md)
- [Tworzenie subskrypcji MCA](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Tworzenie subskrypcji MPA](programmatically-create-subscription-microsoft-partner-agreement.md)

Jeśli nadal używasz [interfejsów API w wersji zapoznawczej](programmatically-create-subscription-preview.md), możesz nadal tworzyć subskrypcje za ich pomocą. 

Możesz też [tworzyć subskrypcje przy użyciu szablonu usługi ARM](create-subscription-template.md). Szablon usługi ARM pomaga zautomatyzować proces tworzenia subskrypcji za pomocą interfejsów API REST. 

## <a name="next-steps"></a>Następne kroki

* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji za pomocą grup zarządzania, zobacz [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../governance/management-groups/overview.md).
