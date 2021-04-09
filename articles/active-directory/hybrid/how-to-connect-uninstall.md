---
title: Odinstalowywanie programu Azure AD Connect
description: W tym dokumencie opisano sposób odinstalowywania Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96934187"
---
# <a name="uninstall-azure-ad-connect"></a>Odinstalowywanie programu Azure AD Connect

W tym dokumencie opisano sposób poprawnego odinstalowywania Azure AD Connect.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Odinstalowanie Azure AD Connect z serwera
Najpierw należy usunąć Azure AD Connect z serwera, na którym jest uruchomiony.  Wykonaj następujące kroki:

 1. Na serwerze z uruchomionym Azure AD Connect przejdź do **Panelu sterowania**.
 2. Kliknij pozycję **Odinstaluj program** 
  ![ Odinstaluj program.](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. Wybierz **Azure AD Connect**.
 ![Wybierz Azure AD Connect](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. Po wyświetleniu monitu kliknij przycisk **tak** , aby potwierdzić.
 5. To potwierdzenie spowoduje wyświetlenie Azure AD Connect ekranu.  Kliknij przycisk **Usuń**.
 ![Usuń](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. Po zakończeniu tej akcji kliknij przycisk **Zakończ**.
 7. ![Zakończ](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. Wróć do **Panelu sterowania** , kliknij przycisk **Odśwież** , a wszystkie składniki powinny zostać usunięte.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
- [Instalowanie programu Azure AD Connect przy użyciu istniejącej bazy danych programu ADSync](how-to-connect-install-existing-database.md)
- [Instalowanie programu Azure AD Connect za pomocą delegowanych uprawnień administratora usługi SQL](how-to-connect-install-sql-delegation.md)

