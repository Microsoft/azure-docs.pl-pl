---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 10/12/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 20d80fe1a09d388552b6289f8a9b23c878672f94
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974293"
---
Te przykłady pokazują, jak używać usługi Azure Policy z subskrypcjami, które zostały dołączone do usługi Azure Lighthouse.

| **Szablon** | **Opis** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Przypisuje zasady, które dodają tag do subskrypcji delegowanej lub go z niej usuwają (za pomocą efektu modify). Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | Przypisuje zasady ograniczające delegowania usługi Azure Lighthouse do określonych dzierżaw zarządzających. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Przypisuje zasady, które będą przeprowadzać inspekcję przypisań delegacji. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Przypisuje zasady, aby potwierdzić, że subskrypcje w grupie zarządzania zostały delegowane do dzierżawy zarządzającej, a jeśli tak się nie stało, tworzy przypisanie.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Przypisuje zasady, które umożliwiają diagnostykę zasobów usługi Azure Key Vault w subskrypcjach delegowanych (przy użyciu efektu deployIfNotExists). Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Przypisuje kilka zasad w celu włączenia diagnostyki w subskrypcji delegowanej oraz łączy wszystkie maszyny wirtualne z systemem Windows i Linux z obszarem roboczym usługi Log Analytics utworzonym przez te zasady. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Stosuje inicjatywę (wiele powiązanych definicji zasad) do subskrypcji delegowanej. |

