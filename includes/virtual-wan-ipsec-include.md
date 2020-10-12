---
title: plik dołączany
description: plik dołączany
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 02ced43f8c3fc7c83359b78362e8ad0feeab3070
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "72168411"
---
>[!NOTE]
>Podczas pracy z zasadami domyślnymi platforma Azure może działać jako inicjator i obiekt odpowiadający podczas instalacji tunelu IPsec. Brak obsługi platformy Azure jako obiektu odpowiadającego.
>

### <a name="initiator"></a>Inicjator

W poniższych sekcjach wymieniono obsługiwane kombinacje zasad, gdy platforma Azure jest inicjatorem tunelu.

**Faza 1**

* AES_256, SHA1 DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1 DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2

**Faza 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE

### <a name="responder"></a>Odbiorczy

W poniższych sekcjach wymieniono obsługiwane kombinacje zasad, gdy platforma Azure jest obiektem odpowiadającym dla tunelu.

**Faza 1**

* AES_256, SHA1 DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1 DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2

**Faza 2**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* AES_256, SHA_1, PFS_1
* AES_256, SHA_1, PFS_2
* AES_256, SHA_1, PFS_14
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* AES_256, SHA_256, PFS_1
* AES_256, SHA_256, PFS_2
* AES_256, SHA_256, PFS_14
* AES_256, SHA_1, PFS_24
* AES_256, SHA_256, PFS_24
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14