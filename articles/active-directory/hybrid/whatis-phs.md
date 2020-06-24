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
ms.openlocfilehash: 568f284a22f170c2a6a0d60d9e2cfed0645937ba
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261379"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Co to jest synchronizacja skrótów haseł w usłudze Azure AD?
Synchronizacja skrótów haseł to jedna z metod logowania używana do zapewnienia tożsamości hybrydowej. Azure AD Connect synchronizuje skrót skrótu hasła użytkownika z wystąpienia lokalnego Active Directory do wystąpienia usługi Azure AD opartego na chmurze.

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
