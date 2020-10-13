---
title: plik dołączany
description: plik dołączany
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84465127"
---
Przejdź do konta magazynu, dla którego chcesz ograniczyć publiczny punkt końcowy do określonych sieci wirtualnych. W spisie treści dla konta magazynu wybierz pozycję **zapory i sieci wirtualne**. 

W górnej części strony wybierz przycisk radiowy **wybrane sieci** . Spowoduje to ukrycie szeregu ustawień służących do kontrolowania ograniczenia publicznego punktu końcowego. Kliknij pozycję **+ Dodaj istniejącą sieć wirtualną** , aby wybrać konkretną sieć wirtualną, która powinna mieć zezwolenie na dostęp do konta magazynu za pośrednictwem publicznego punktu końcowego. Będzie to wymagało wybrania sieci wirtualnej i podsieci dla tej sieci wirtualnej. 

Zaznacz pole wyboru **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta usługi** , aby umożliwić zaufanym usługom firmy Microsoft, takim jak Azure File Sync, uzyskanie dostępu do konta magazynu.

[![Zrzut ekranu przedstawiający blok zapory i sieci wirtualne z określoną siecią wirtualną, która może uzyskiwać dostęp do konta magazynu za pośrednictwem publicznego punktu końcowego](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)