---
title: Znane problemy z tożsamościami zarządzanymi — Azure Active Directory
description: Znane problemy związane z tożsamościami zarządzanymi dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226926"
---
# <a name="known-issues-with-managed-identities"></a>Znane problemy związane z tożsamościami zarządzanymi

W tym artykule omówiono kilka problemów związanych z tożsamościami zarządzanymi i sposoby ich rozwiązywania. Często zadawane pytania dotyczące tożsamości zarządzanych są udokumentowane w artykułach [często zadawanych pytań](managed-identities-faq.md) .
## <a name="vm-fails-to-start-after-being-moved"></a>Nie można uruchomić maszyny wirtualnej po przeniesieniu 

Jeśli przeniesiesz maszynę wirtualną w stan uruchomienia z grupy zasobów lub subskrypcji, będzie ona nadal działać podczas przenoszenia. Jednak po przeniesieniu, jeśli maszyna wirtualna zostanie zatrzymana i uruchomiona ponownie, nie zostanie uruchomiona. Ten problem występuje, ponieważ maszyna wirtualna nie aktualizuje odwołania do zarządzanych tożsamości dla tożsamości zasobów platformy Azure i kontynuuje wskazywanie jej w starej grupie zasobów.

**Obejście** 
 
Wyzwól aktualizację na maszynie wirtualnej, aby możliwe było uzyskanie prawidłowych wartości dla zarządzanych tożsamości dla zasobów platformy Azure. Aby zaktualizować odwołanie do zarządzanych tożsamości dla tożsamości zasobów platformy Azure, można zmienić wartość właściwości maszyny wirtualnej. Na przykład można ustawić nową wartość tagu na maszynie wirtualnej przy użyciu następującego polecenia:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
To polecenie ustawia nowy tag "fixVM" o wartości 1 na maszynie wirtualnej. 
 
Ustawiając tę właściwość, maszyna wirtualna ma aktualizacje z poprawnymi tożsamościami zarządzanymi dla identyfikatora URI zasobu zasobów platformy Azure, a następnie powinno być możliwe uruchomienie maszyny wirtualnej. 
 
Po uruchomieniu maszyny wirtualnej tag można usunąć za pomocą następującego polecenia:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Przenoszenie subskrypcji między katalogami usługi Azure AD

Tożsamości zarządzane nie są aktualizowane, gdy subskrypcja jest przenoszona/przenoszona do innego katalogu. W związku z tym wszystkie istniejące tożsamości zarządzane przypisane do systemu lub przypisane przez użytkownika zostaną przerwane. 

Obejście dla zarządzanych tożsamości w ramach subskrypcji, która została przeniesiona do innego katalogu:

 - Dla tożsamości zarządzanych przypisanych przez system: Wyłącz i ponownie włącz. 
 - W przypadku tożsamości zarządzanych przypisanych przez użytkownika: Usuń, ponownie utwórz i Dołącz je do niezbędnych zasobów (np. maszyn wirtualnych)

Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji platformy Azure do innego katalogu usługi Azure AD](../../role-based-access-control/transfer-subscription.md).


## <a name="next-steps"></a>Następne kroki

Możesz zapoznać się z artykułem zawierającym listę [usług, które obsługują tożsamości zarządzane](services-support-managed-identities.md) i [często zadawane pytania](managed-identities-faq.md)
