---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420293"
---
- Tylko jedna sieć wirtualna może być połączona z obiektem dostępu do dysku.
- Sieć wirtualna musi znajdować się w tej samej subskrypcji, co obiekt dostępu do dysku, aby je połączyć.
- Do 10 dysków lub migawek można importować lub eksportować jednocześnie przy użyciu tego samego obiektu dostępu do dysku.
- Nie można zażądać ręcznego zatwierdzania w celu połączenia sieci wirtualnej z obiektem dostępu do dysku.
- Funkcja różnicowa nie jest obsługiwana w przypadku migawek przyrostowych, które są skojarzone z obiektem dostępu do dysku.
- Nie można użyć AzCopy, aby pobrać dysk VHD dysku/migawki zabezpieczony za pośrednictwem prywatnych linków do konta magazynu. Można jednak używać AzCopy do pobierania dysków VHD do maszyn wirtualnych.
