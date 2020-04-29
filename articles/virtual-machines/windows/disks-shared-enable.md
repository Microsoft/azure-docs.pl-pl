---
title: Włącz dyski udostępnione dla usługi Azure Managed disks
description: Skonfiguruj dysk zarządzany na platformie Azure z udostępnionymi dyskami (wersja zapoznawcza), aby umożliwić udostępnianie go na wielu maszynach wirtualnych
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0dbb1844d4c670abfdc5562580b0ee8b4549b6bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085487"
---
# <a name="enable-shared-disk"></a>Włącz dysk udostępniony

W tym artykule opisano sposób włączania funkcji dysków udostępnionych (wersja zapoznawcza) dla usługi Azure Managed Disks. Azure Shared disks (wersja zapoznawcza) to nowa funkcja dysków zarządzanych przez platformę Azure, która umożliwia równoczesne dołączanie dysku zarządzanego do wielu maszyn wirtualnych. Dołączenie dysku zarządzanego do wielu maszyn wirtualnych pozwala wdrożyć nowe lub migrować istniejące aplikacje klastrowane na platformę Azure. 

Jeśli szukasz informacji koncepcyjnych dotyczących dysków zarządzanych, które mają włączone dyski udostępnione, zapoznaj się z [usługą Azure Shared disks](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]