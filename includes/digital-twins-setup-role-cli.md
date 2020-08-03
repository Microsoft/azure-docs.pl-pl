---
author: baanders
description: plik dołączany dla wymagania roli w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407478"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Warunki wstępne: wymagania dotyczące uprawnień

Aby móc wykonać wszystkie kroki opisane w tym artykule, musisz być sklasyfikowany jako właściciel w ramach subskrypcji platformy Azure. 

Poziom uprawnień można sprawdzić, uruchamiając następujące polecenie w Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Jeśli jesteś właścicielem, `roleDefinitionName` wartość w danych wyjściowych jest *właścicielem*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Okno Cloud Shell pokazujące dane wyjściowe polecenia AZ role przypisania list":::

Jeśli okaże się, że wartość to *współautor* lub coś innego niż *właściciel*, można wykonać jedną z następujących czynności:
* Skontaktuj się z właścicielem subskrypcji i poproś właściciela o wykonanie kroków opisanych w tym artykule w Twoim imieniu
* Skontaktuj się z właścicielem subskrypcji lub osobą z rolą administratora dostępu użytkowników w ramach subskrypcji i poproś o podniesienie uprawnień do właściciela subskrypcji, aby móc kontynuować pracę. Niezależnie od tego, czy jest to odpowiednie, zależy od organizacji i Twojej roli.