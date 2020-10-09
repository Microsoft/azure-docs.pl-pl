---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4a6d9ef04a33c84d68dff1429fb39a193a249280
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204674"
---
Te przykłady ilustrują różne zadania, które można wykonywać w scenariuszach zarządzania wieloma dzierżawami.

| **Szablon** | **Opis** |
|---------|---------|
| [create-keyvault-secret](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | Tworzy magazyn kluczy w dzierżawie klienta i tworzy zasady dostępu.
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Wdraża konta magazynu w dwóch różnych grupach zasobów.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Tworzy usługi zarządzania platformą Azure, łączy je i wdraża dodatkowe rozwiązania. Aby uzyskać kompleksowe wdrożenie, użyj szablonu **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Włącza i konfiguruje usługę Azure Security Center w docelowej subskrypcji platformy Azure. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Wdraża i włącza rozwiązanie Azure Sentinel w istniejącym obszarze roboczym usługi Log Analytics w subskrypcji delegowanej. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Te szablony umożliwiają wdrożenie rozszerzeń maszyn wirtualnych usługi Log Analytics na maszynach wirtualnych z systemem Windows i Linux, łącząc je ze wskazanym obszarem roboczym usługi Log Analytics |
