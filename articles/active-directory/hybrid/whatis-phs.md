---
title: Co to jest synchronizacja skrótów haseł w usłudze Azure AD? | Microsoft Docs
description: Opisuje synchronizację skrótów haseł.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83e172e61411c7c1c098706b5ff4566f565d6bf1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "66253856"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Co to jest synchronizacja skrótów haseł w usłudze Azure AD?
Synchronizacja skrótów haseł to jedna z metod logowania używana do zapewnienia tożsamości hybrydowej. Program Azure AD Connect synchronizuje skróty haseł użytkowników z lokalnego wystąpienia usługi Active Directory do wystąpienia usługi Azure AD w chmurze.

Synchronizacja skrótów haseł jest rozszerzeniem funkcji synchronizacji katalogów zaimplementowanej przez Azure AD Connect Sync. Ta funkcja służy do logowania się do usług Azure AD, takich jak Office 365. Aby zalogować w usłudze, użyj tego samego hasła, którego używasz do zalogowania się w lokalnym wystąpieniu usługi Active Directory.

![Co to jest program Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

Synchronizacja skrótów haseł pomaga, ponieważ dzięki niej użytkownicy potrzebują tylko jednego hasła. Synchronizacja skrótów haseł umożliwia:

* Zwiększenie produktywność użytkowników.
* Obniżenie kosztów pomocy technicznej.  

Jeśli zdecydujesz się na użycie [federacji z usługami Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) jako metody logowania, możesz opcjonalnie skonfigurować synchronizację skrótów haseł jako awaryjną metodę logowania.

Aby użyć synchronizacji skrótów haseł w swoim środowisku, musisz:

* Zainstalować program Azure AD Connect.  
* Skonfigurować synchronizację katalogów pomiędzy lokalnym wystąpieniem usługi Active Directory a wystąpieniem usługi Azure Active Directory.
* Włączyć synchronizację skrótów haseł.



Aby uzyskać więcej informacji, zobacz [Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Następne kroki

- [Co to jest tożsamość hybrydowa?](whatis-hybrid-identity.md)
- [Co to są Azure AD Connect Health i Connect Health?](whatis-azure-ad-connect.md)
- [Co to jest uwierzytelnianie przekazywane?](how-to-connect-pta.md)
- [Co to jest federacja?](whatis-fed.md)
- [Co to jest logowanie jednokrotne?](how-to-connect-sso.md)
- [Jak działa synchronizacja skrótów haseł](how-to-connect-password-hash-synchronization.md)
