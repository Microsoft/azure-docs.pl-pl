---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204429"
---
- Obecnie nie można przenosić migawek przyrostowych między subskrypcjami.
- Obecnie można generować identyfikatory URI SAS maksymalnie pięć migawek określonej rodziny migawek w danym momencie.
- Nie można utworzyć migawki przyrostowej dla określonego dysku poza subskrypcją tego dysku.
- Do siedmiu migawek przyrostowych na dysk można utworzyć co pięć minut.
- Można utworzyć łączną liczbę migawek przyrostowych 200 dla jednego dysku.
- Nie można uzyskać zmian między migawkami podjętymi przed zmianą i po zmianie rozmiaru dysku nadrzędnego na granicy 4 TB. Konieczne będzie ponowne pobranie pełnej kopii migawki utworzonej po zmianie rozmiaru. Następnie można uzyskać zmiany między migawkami utworzonymi po zmianie rozmiaru na granicach 4 TB. 
