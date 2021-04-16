---
title: Wyłączanie tożsamości zarządzanej Azure Automation konta zarządzanego
description: W tym artykule wyjaśniono, jak wyłączyć i usunąć tożsamość zarządzaną dla Azure Automation konta.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2021
ms.topic: conceptual
ms.openlocfilehash: 74d029db48f64b38eb323150068e173746d379b7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495100"
---
# <a name="disable-your-azure-automation-account-managed-identity"></a>Wyłączanie tożsamości zarządzanej Azure Automation konta zarządzanego

Istnieją dwa sposoby wyłączenia tożsamości przypisanej przez system w Azure Automation. To zadanie można wykonać z Azure Portal lub przy użyciu szablonu usługi Azure Resource Manager (ARM).

## <a name="disable-managed-identity-in-the-azure-portal"></a>Wyłączanie tożsamości zarządzanej w Azure Portal

Tożsamość zarządzaną można wyłączyć z Azure Portal niezależnie od tego, jak tożsamość zarządzana została pierwotnie ustawiona.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do konta usługi Automation i wybierz pozycję **Tożsamość w** obszarze **Ustawienia konta.**

1. Ustaw opcję **Przypisana przez system** na **wartość Wył.,** a następnie naciśnij przycisk **Zapisz.** Po wyświetleniu monitu o potwierdzenie naciśnij przycisk **Tak.**

Tożsamość zarządzana jest usuwana i nie ma już dostępu do zasobu docelowego.

## <a name="disable-using-azure-resource-manager-template"></a>Wyłączanie przy użyciu Azure Resource Manager szablonu

Jeśli tożsamość zarządzana została utworzona dla konta usługi Automation przy użyciu szablonu usługi Azure Resource Manager, możesz wyłączyć tożsamość zarządzaną, ponownie używając tego szablonu i modyfikując jego ustawienia. Ustaw typ właściwości podrzędnej obiektu tożsamości na **Brak,** jak pokazano w poniższym przykładzie, a następnie ponownie uruchom szablon.

```json
"identity": { 
   "type": "None" 
} 
```

Usunięcie tożsamości przypisanej przez system przy użyciu tej metody powoduje również usunięcie jej z usługi Azure AD. Tożsamości przypisane przez system są również automatycznie usuwane z usługi Azure AD po usunięciu zasobu aplikacji, do którym są przypisane.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat włączania tożsamości zarządzanej w programie Azure Automation, zobacz Włączanie i używanie tożsamości [zarządzanej dla usługi Automation.](enable-managed-identity-for-automation.md)

- Aby uzyskać omówienie zabezpieczeń konta usługi Automation, zobacz [Omówienie uwierzytelniania konta usługi Automation](automation-security-overview.md).
