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
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992922"
---
- Obecnie nie można przenosić migawek przyrostowych między subskrypcjami.
- Obecnie można generować identyfikatory URI SAS maksymalnie pięć migawek określonej rodziny migawek w danym momencie.
- Nie można utworzyć migawki przyrostowej dla określonego dysku poza subskrypcją tego dysku.
- Do siedmiu migawek przyrostowych na dysk można utworzyć co pięć minut.
- Można utworzyć łączną liczbę migawek przyrostowych 200 dla jednego dysku.
- Nie można uzyskać zmian między migawkami wykonanymi przed i po zmianie rozmiaru dysku nadrzędnego na granicę 4 TB. Na przykład migawka przyrostowa migawki — gdy rozmiar dysku wynosi 2 TB. Teraz zwiększono rozmiar dysku do 6 TB, a następnie trwało kolejną migawkę migawek przyrostowych-b. Nie można pobrać zmian między migawką a i migawką b. Należy ponownie pobrać pełną kopię migawki-b utworzoną po zmianie rozmiaru. Następnie można uzyskać zmiany między migawką-b i migawkami utworzonymi po migawce-b. 
