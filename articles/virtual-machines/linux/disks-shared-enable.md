---
title: Włącz dyski udostępnione dla usługi Azure Managed disks
description: Skonfiguruj dysk zarządzany na platformie Azure z udostępnionymi dyskami (wersja zapoznawcza), aby umożliwić udostępnianie go na wielu maszynach wirtualnych
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8fdbe71ab1b4104fed9ecc7d6cd2cb637da860f6
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84337357"
---
# <a name="enable-shared-disk"></a>Włączanie dysku udostępnionego

W tym artykule opisano sposób włączania funkcji dysków udostępnionych (wersja zapoznawcza) dla usługi Azure Managed Disks. Azure Shared disks (wersja zapoznawcza) to nowa funkcja dysków zarządzanych przez platformę Azure, która umożliwia równoczesne dołączanie dysku zarządzanego do wielu maszyn wirtualnych. Dołączenie dysku zarządzanego do wielu maszyn wirtualnych pozwala wdrożyć nowe lub migrować istniejące aplikacje klastrowane na platformę Azure. 

Jeśli szukasz informacji koncepcyjnych dotyczących dysków zarządzanych, które mają włączone dyski udostępnione, zapoznaj się z [usługą Azure Shared disks](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]