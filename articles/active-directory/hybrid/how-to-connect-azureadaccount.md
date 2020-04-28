---
title: Zmień hasło konta łącznika usługi Azure AD | Microsoft Docs
description: W tym temacie opisano sposób przywracania konta łącznika usługi Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67204529"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Zmienianie hasła do konta łącznika usługi Azure AD
Konto łącznika usługi Azure AD powinno być wolne od usługi. Jeśli musisz zresetować swoje poświadczenia, ten temat jest dla Ciebie. Na przykład jeśli administrator globalny ma błąd resetowania hasła na koncie przy użyciu programu PowerShell.

## <a name="reset-the-credentials"></a>Zresetuj poświadczenia
Jeśli konto łącznika usługi Azure AD nie może skontaktować się z usługą Azure AD z powodu problemów z uwierzytelnianiem, można zresetować hasło.

1. Zaloguj się do serwera synchronizacji Azure AD Connect i uruchom program PowerShell.
2. Uruchom polecenie `Add-ADSyncAADServiceAccount`.  
   ![Addadsyncaadserviceaccount polecenia cmdlet programu PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Podaj poświadczenia administratora globalnego usługi Azure AD.

To polecenie cmdlet resetuje hasło do konta usługi i aktualizuje je zarówno w usłudze Azure AD, jak i w aparacie synchronizacji.

## <a name="known-issues-these-steps-can-solve"></a>Znane problemy, które mogą rozwiązać te czynności
Ta sekcja zawiera listę błędów raportowanych przez klientów, którzy zostali usunięci przy użyciu poświadczeń resetowania na koncie łącznika usługi Azure AD.

---
Zdarzenie 6900  
Serwer napotkał nieoczekiwany błąd podczas przetwarzania powiadomienia o zmianie hasła:  
AADSTS70002: błąd podczas walidacji poświadczeń. AADSTS50054: stare hasło jest używane do uwierzytelniania.

---
Zdarzenie 659  
Wystąpił błąd podczas pobierania konfiguracji synchronizacji zasad haseł. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: błąd podczas walidacji poświadczeń. AADSTS50054: stare hasło jest używane do uwierzytelniania.

## <a name="next-steps"></a>Następne kroki
**Tematy dotyczące omówienia**

* [Azure AD Connect Sync: omówienie i dostosowanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

