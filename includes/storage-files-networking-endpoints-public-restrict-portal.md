---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98798673"
---
Przejdź do konta magazynu, dla którego chcesz ograniczyć publiczny punkt końcowy do określonych sieci wirtualnych. W spisie treści dla konta magazynu wybierz pozycję **Sieć**. 

W górnej części strony wybierz przycisk radiowy **wybrane sieci** . Spowoduje to ukrycie szeregu ustawień służących do kontrolowania ograniczenia publicznego punktu końcowego. Kliknij pozycję **+ Dodaj istniejącą sieć wirtualną** , aby wybrać konkretną sieć wirtualną, która powinna mieć zezwolenie na dostęp do konta magazynu za pośrednictwem publicznego punktu końcowego. Będzie to wymagało wybrania sieci wirtualnej i podsieci dla tej sieci wirtualnej. 

Zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta usługi** , aby umożliwić zaufanym usługom firmy Microsoft, takim jak Azure File Sync, uzyskanie dostępu do konta magazynu.

[![Zrzut ekranu przedstawiający blok sieć z określoną siecią wirtualną, która może uzyskać dostęp do konta magazynu za pośrednictwem publicznego punktu końcowego](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)