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
ms.openlocfilehash: be014a257a68a266a5946bf5822b094c5a968f6e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535774"
---
- Tylko jedna sieć wirtualna może być połączona z obiektem dostępu do dysku.
- Sieć wirtualna musi znajdować się w tej samej subskrypcji, co obiekt dostępu do dysku, aby je połączyć.
- Do 10 dysków lub migawek można importować lub eksportować jednocześnie przy użyciu tego samego obiektu dostępu do dysku.
- Nie można zażądać ręcznego zatwierdzania w celu połączenia sieci wirtualnej z obiektem dostępu do dysku.
- Funkcja różnicowa nie jest obsługiwana w przypadku migawek przyrostowych, które są skojarzone z obiektem dostępu do dysku.