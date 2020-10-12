---
title: plik dołączany
description: plik dołączany
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68249909"
---
Identyfikator zasobu usługi Azure AD wskazuje odbiorców, dla których wystawiony token może służyć do zapewnienia dostępu do zasobu platformy Azure. W przypadku usługi Azure Storage identyfikator zasobu może być specyficzny dla jednego konta magazynu lub może dotyczyć dowolnego konta magazynu. W poniższej tabeli opisano wartości, które można podać dla identyfikatora zasobu:

|Identyfikator zasobu  |Opis  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Punkt końcowy usługi dla danego konta magazynu. Użyj tej wartości, aby uzyskać Token autoryzacji żądań tylko do określonego konta i usługi Azure Storage. Zastąp wartość w nawiasach jako nazwę konta magazynu.      |
|`https://storage.azure.com/`     | Użyj, aby uzyskać Token autoryzacji żądań do dowolnego konta usługi Azure Storage.        |
