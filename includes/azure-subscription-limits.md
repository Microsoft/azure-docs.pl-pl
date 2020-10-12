---
title: plik dołączany
description: plik dołączany
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: db822722b12921ab98b3e5cae67e28f4ca7ede04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87298891"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Procesorów wirtualnych vCPU na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md)<sup>1</sup> |20 |10 000 |
| [Współadministratorzy](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na subskrypcję |200 |200 |
| [Konta magazynu](../articles/storage/common/storage-create-storage-account.md) na subskrypcję<sup>2</sup> |100 |100 |
| [Usługi w chmurze](../articles/cloud-services/cloud-services-choose-me.md) na subskrypcję |20 |200 |
| [Sieci lokalne](/previous-versions/azure/reference/jj157100(v=azure.100)) na subskrypcję |10 |500 |
| Serwery DNS na subskrypcję |9 |100 |
| Zastrzeżone adresy IP na subskrypcję |20 |100 |
| [Grupy koligacji](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) na subskrypcję |256 |256 |
| Długość nazwy subskrypcji (znaki) | 64 | 64 |

<sup>1</sup> Liczba bardzo małych wystąpień jest traktowana jako jedna vCPU do limitu vCPU, mimo że jest używana część rdzeń procesora CPU.

<sup>2</sup> Limit konta magazynu obejmuje konta magazynu w warstwie Standardowa i Premium. 

