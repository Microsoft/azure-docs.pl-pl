---
title: Co to jest tożsamość hybrydowa z Azure Active Directory?
description: Tożsamość hybrydowa ma wspólną tożsamość użytkownika do uwierzytelniania i autoryzacji zarówno lokalnie, jak i w chmurze.
keywords: wprowadzenie do programu Azure AD Connect, omówienie programu Azure AD Connect, co to jest program Azure AD Connect, instalowanie usługi Active Directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895355b5acebffe6ad24b15b0c709d21e8f20be4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90016652"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Co to jest tożsamość hybrydowa z Azure Active Directory?

Obecnie małe i duże firmy w coraz większym stopniu korzystają zarówno z aplikacji lokalnych, jak i aplikacji w chmurze.  Użytkownicy wymagają dostępu do aplikacji zarówno lokalnie, jak i w chmurze. Zarządzanie użytkownikami zarówno lokalnie, jak i w chmurze stanowi trudne scenariusze. 

Rozwiązania firmy Microsoft do obsługi tożsamości obejmują zarówno funkcje lokalne, jak i chmurowe.  Te rozwiązania tworzą wspólną tożsamość użytkownika na potrzeby uwierzytelniania i autoryzacji w kontekście wszystkich zasobów, niezależnie od lokalizacji. Nazywamy tę **tożsamość hybrydową**.

Dzięki tożsamości hybrydowej z usługą Azure AD i hybrydowym zarządzaniem tożsamościami te scenariusze stają się możliwe.

Aby uzyskać tożsamość hybrydową w usłudze Azure AD, można użyć jednej z trzech metod uwierzytelniania w zależności od scenariuszy.   Te trzy metody to: 

- **[Synchronizacja skrótów haseł](whatis-phs.md)**  
- **[Uwierzytelnianie przekazywane](how-to-connect-pta.md)**  
- **[Federacja (AD FS)](whatis-fed.md)** 

Te metody uwierzytelniania oferują również funkcję [logowania jednokrotnego](how-to-connect-sso.md).  Logowanie jednokrotne umożliwia automatyczne logowanie użytkowników, gdy ich urządzenia firmowe są połączone z siecią firmową.

Aby uzyskać więcej informacji, zobacz [Choose the right authentication method for your Azure Active Directory hybrid identity solution (Wybieranie właściwej metody uwierzytelniania w przypadku rozwiązania hybrydowego do obsługi tożsamości w usłudze Azure Active Directory)](./choose-ad-authn.md). 

## <a name="common-scenarios-and-recommendations"></a>Typowe scenariusze i zalecenia 

Poniżej przedstawiono kilka typowych scenariuszy związanych z tożsamościami hybrydowymi i zarządzaniem dostępem wraz z zaleceniami co do tego, która opcja (lub opcje) obsługi tożsamości hybrydowych może być najbardziej odpowiednia w danym przypadku. 

|Wymagana funkcjonalność:|PHS i SSO<sup>1</sup>| PTA i SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Automatyczne synchronizowanie z chmurą nowych kont użytkowników, kontaktów i grup utworzonych w lokalnej usłudze Active Directory.|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| ![Zalecane](./media/whatis-hybrid-identity/ic195031.png) |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| 
|Skonfiguruj moją dzierżawcę dla Microsoft 365 scenariuszy hybrydowych.|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| ![Zalecane](./media/whatis-hybrid-identity/ic195031.png) |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| 
|Umożliwienie użytkownikom logowania się i uzyskiwania dostępu do usług w chmurze przy użyciu swojego lokalnego hasła.|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| ![Zalecane](./media/whatis-hybrid-identity/ic195031.png) |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| 
|Zaimplementowanie logowania jednokrotnego przy użyciu poświadczeń firmowych.|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| ![Zalecane](./media/whatis-hybrid-identity/ic195031.png) |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|  
|Zagwarantowanie, że żadne skróty haseł nie będą przechowywane w chmurze.| |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| 
|Umożliwienie działania rozwiązań obsługujących uwierzytelnianie wieloskładnikowe oparte na chmurze.|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)|![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| 
|Umożliwienie działania rozwiązań obsługujących lokalne uwierzytelnianie wieloskładnikowe.| | |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| 
|Obsługa uwierzytelniania użytkowników za pomocą kart inteligentnych.<sup>4</sup>| | |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| 
|Wyświetlanie powiadomień dotyczących wygasania haseł w portalu pakietu Office i na pulpicie systemu Windows 10.| | |![Zalecane](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Synchronizacja skrótów haseł za pomocą logowania jednokrotnego. 
> 
> <sup>2</sup> Uwierzytelnianie przekazywane i logowanie jednokrotne.  
> 
> <sup>3</sup> Federacyjne logowanie jednokrotne z użyciem usług AD FS.  
>  
> <sup>4</sup> Usługi AD FS można zintegrować z infrastrukturą kluczy publicznych przedsiębiorstwa, aby umożliwić logowanie przy użyciu certyfikatów. Mogą to być certyfikaty programowe wdrażane za pośrednictwem zaufanych kanałów aprowizowania, takich jak zarządzanie danymi głównymi lub obiekt zasad grupy, lub certyfikaty na kartach inteligentnych (w tym na kartach PIV/CAC) albo w usłudze Hello dla firm (zaufanie certyfikatu). Aby uzyskać więcej informacji na temat obsługi uwierzytelniania za pomocą kart inteligentnych, zobacz [ten blog](/archive/blogs/samueld/adfs-certauth-aad-o365). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Wymagania licencyjne dotyczące korzystania z Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Następne kroki 

- [Co to są Azure AD Connect Health i Connect Health?](whatis-azure-ad-connect.md) 
- [Co to jest synchronizacja skrótów haseł?](whatis-phs.md) 
- [Co to jest uwierzytelnianie przekazywane?](how-to-connect-pta.md) 
- [Co to jest federacja?](whatis-fed.md) 
- [Co to jest logowanie jednokrotne?](how-to-connect-sso.md)