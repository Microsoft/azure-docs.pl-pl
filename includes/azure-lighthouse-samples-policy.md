---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 09/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a9c9b5081232988cb4ab80bfcbb9f2d92d1d025f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336580"
---
Te przykłady pokazują, jak używać usługi Azure Policy z subskrypcjami, które zostały dołączone do usługi Azure Lighthouse.

| **Szablon** | **Opis** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Przypisuje zasady, które dodają tag do subskrypcji delegowanej lub go z niej usuwają (za pomocą efektu modify). Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Przypisuje zasady, które będą przeprowadzać inspekcję przypisań delegacji. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Przypisuje zasady, aby potwierdzić, że subskrypcje w grupie zarządzania zostały delegowane do dzierżawy zarządzającej, a jeśli tak się nie stało, tworzy przypisanie.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Przypisuje zasady, które umożliwiają diagnostykę zasobów usługi Azure Key Vault w subskrypcjach delegowanych (przy użyciu efektu deployIfNotExists). Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Przypisuje kilka zasad w celu włączenia diagnostyki w subskrypcji delegowanej oraz łączy wszystkie maszyny wirtualne z systemem Windows i Linux z obszarem roboczym usługi Log Analytics utworzonym przez te zasady. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Stosuje inicjatywę (wiele powiązanych definicji zasad) do subskrypcji delegowanej. |

