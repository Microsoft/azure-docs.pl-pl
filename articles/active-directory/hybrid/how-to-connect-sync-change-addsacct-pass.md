---
title: 'Azure AD Connect Sync: zmiana hasła konta AD DS | Microsoft Docs'
description: W tym dokumencie opisano sposób aktualizacji Azure AD Connect po zmianie hasła konta AD DS.
services: active-directory
keywords: Konto AD DS, Active Directory konto, hasło
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4077146292db1266d5dbc51cc577f952b2bff191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85357515"
---
# <a name="changing-the-ad-ds-account-password"></a>Zmienianie hasła konta usług AD DS
Konto AD DS odnosi się do konta użytkownika używanego przez program Azure AD Connect do komunikowania się z lokalnym Active Directoryem. W przypadku zmiany hasła konta AD DS należy zaktualizować usługę synchronizacji Azure AD Connect przy użyciu nowego hasła. W przeciwnym razie synchronizacja nie będzie już prawidłowo synchronizowana z lokalnym Active Directory i wystąpią następujące błędy:

* W Synchronization Service Manager każda operacja importowania lub eksportowania z lokalną usługą AD kończy się niepowodzeniem z powodu błędu **nie-Start-Credentials** .

* W obszarze Podgląd zdarzeń systemu Windows dziennik zdarzeń aplikacji zawiera błąd o **identyfikatorze 6000** i komunikat **"contoso.com" agenta zarządzania, ponieważ poświadczenia były nieprawidłowe**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Jak zaktualizować usługę synchronizacji przy użyciu nowego hasła dla konta AD DS
Aby zaktualizować usługę synchronizacji przy użyciu nowego hasła:

1. Uruchom Synchronization Service Manager (Uruchom usługę synchronizacji →).
</br>![Service Manager synchronizacji](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Przejdź do karty **Łączniki** .

3. Wybierz **Łącznik usługi AD** odpowiadający kontu AD DS, dla którego hasło zostało zmienione.

4. W obszarze **Akcje**wybierz pozycję **Właściwości**.

5. W podręcznym oknie dialogowym wybierz pozycję **Połącz z lasem Active Directory**:

6. Wprowadź nowe hasło konta AD DS w polu tekstowym **hasło** .

7. Kliknij przycisk **OK** , aby zapisać nowe hasło i zamknąć okno dialogowe.

8. Uruchom ponownie usługę synchronizacji Azure AD Connect w obszarze Menedżer sterowania usługami systemu Windows. Jest to konieczne, aby upewnić się, że wszystkie odwołania do starego hasła zostaną usunięte z pamięci podręcznej pamięci.

## <a name="next-steps"></a>Następne kroki
**Tematy dotyczące omówienia**

* [Azure AD Connect Sync: omówienie i dostosowanie synchronizacji](how-to-connect-sync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
