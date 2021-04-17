---
author: baanders
description: uwzględnij plik opisujący rozwiązanie tokenu do ograniczenia między dzierżawami z Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 5bc7e8af356ebe5968fe8a74783edc215788e5b4
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589360"
---
Można to zrobić za pomocą następującego polecenia interfejsu wiersza polecenia, gdzie to identyfikator dzierżawy usługi Azure AD zawierającej `<home-tenant-ID>` Azure Digital Twins interfejsu wiersza polecenia:

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

Po zażądaniu tej tożsamości zostanie wyświetlony token wystawiony dla zasobu usługi Azure AD, który ma zgodne oświadczenie identyfikatora dzierżawy do *https://digitaltwins.azure.net* Azure Digital Twins wystąpienia. Użycie tego tokenu w żądaniach interfejsu API lub w kodzie powinno umożliwić `Azure.Identity` tożsamości federacji dostęp do Azure Digital Twins zasobów.