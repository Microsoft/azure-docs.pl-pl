---
title: plik dołączania
description: plik dołączania
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377376"
---
- Tylko jedna sieć wirtualna może być połączona z obiektem dostępu do dysku.
- Sieć wirtualna musi znajdować się w tej samej subskrypcji, co obiekt dostępu do dysku, aby je połączyć.
- Do 10 dysków lub migawek można importować lub eksportować jednocześnie przy użyciu tego samego obiektu dostępu do dysku.
- Nie można zażądać ręcznego zatwierdzania w celu połączenia sieci wirtualnej z obiektem dostępu do dysku.
- Migawek przyrostowych nie można eksportować, jeśli są one skojarzone z obiektem dostępu do dysku.
- Nie można użyć AzCopy, aby pobrać dysk VHD dysku/migawki zabezpieczony za pośrednictwem prywatnych linków do konta magazynu. Można jednak używać AzCopy do pobierania dysków VHD do maszyn wirtualnych.
