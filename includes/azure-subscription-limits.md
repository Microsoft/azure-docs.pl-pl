---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563075"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Procesorów wirtualnych vCPU na [subskrypcję](https://azure.microsoft.com/pricing/)<sup>1</sup> |20 |10 000 |
| [Współadministratorzy](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na subskrypcję |200 |200 |
| [Konta magazynu](../articles/storage/common/storage-account-create.md) na subskrypcję<sup>2</sup> |100 |100 |
| [Usługi w chmurze](../articles/cloud-services/cloud-services-choose-me.md) na subskrypcję |20 |200 |
| [Sieci lokalne](/previous-versions/azure/reference/jj157100(v=azure.100)) na subskrypcję |10 |500 |
| Serwery DNS na subskrypcję |9 |100 |
| Zastrzeżone adresy IP na subskrypcję |20 |100 |
| [Grupy koligacji](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) na subskrypcję |256 |256 |
| Długość nazwy subskrypcji (znaki) | 64 | 64 |

<sup>1</sup> Liczba bardzo małych wystąpień jest traktowana jako jedna vCPU do limitu vCPU, mimo że jest używana część rdzeń procesora CPU.

<sup>2</sup> Limit konta magazynu obejmuje konta magazynu w warstwie Standardowa i Premium.