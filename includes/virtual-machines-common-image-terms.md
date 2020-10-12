---
title: plik dołączany
description: plik dołączany
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71174977"
---
## <a name="terminology"></a>Terminologia

Obraz portalu Marketplace na platformie Azure ma następujące atrybuty:

* **Wydawca**: organizacja, która utworzyła obraz. Przykłady: Canonical, MicrosoftWindowsServer
* **Oferta**: Nazwa grupy powiązanych obrazów utworzonych przez wydawcę. Przykłady: UbuntuServer, WindowsServer
* **Jednostka SKU**: wystąpienie oferty, takie jak główna wersja dystrybucji. Przykłady: 18,04-LTS, 2019-Datacenter
* **Wersja**: numer wersji jednostki SKU obrazu. 

Aby zidentyfikować obraz z witryny Marketplace podczas programistycznego wdrażania maszyny wirtualnej, należy podać te wartości osobno jako parametry. Niektóre narzędzia akceptują *nazwę URN*obrazu, która łączy te wartości, rozdzielając je średnikiem (:) znak: *Wydawca*:*Oferta*:*SKU*:*wersja*. W nazwie URN można zastąpić numer wersji "najnowszy", co spowoduje wybranie najnowszej wersji obrazu. 

Jeśli Wydawca obrazu udostępni dodatkowe warunki licencji i zakupu, należy zaakceptować te warunki i włączyć wdrażanie programistyczne. Należy również podać parametry *planu zakupu* przy programistycznym wdrażaniu maszyny wirtualnej. Zobacz [wdrażanie obrazu z warunkami witryny Marketplace](#deploy-an-image-with-marketplace-terms).
