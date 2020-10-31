---
title: Co to jest funkcja aprowizacji oparta na HR przy użyciu Azure Active Directory? | Microsoft Docs
description: Opisuje Omówienie aprowizacji opartej na usłudze kadr.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b8ed75a87d1ac638e5ca55058a3d1ad7fcdffa
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135351"
---
# <a name="what-is-hr-driven-provisioning"></a>Co to jest obsługa administracyjna oparta na HR?

![Inicjowanie obsługi kadr](./media/what-is-hr-driven-provisioning/cloud2a.png)

Obsługa oparta na usłudze HR jest procesem tworzenia tożsamości cyfrowych na podstawie systemu kadr.  Systemy KADRowe staną się punktem początkowym urzędu dla tych nowo utworzonych tożsamości cyfrowych i często zaczynają obowiązywać w przypadku wielu procesów aprowizacji.  Na przykład jeśli nowy pracownik dołączy swoją firmę, są one tworzone w systemie zasobów ludzkich.  Tworzenie, wyzwalanie aprowizacji konta użytkownika w Active Directory, a następnie Azure AD Connect Inicjuje obsługę tego konta w usłudze Azure AD itd.

Obsługa administracyjna oparta na usłudze kadr może być oparta na środowisku lokalnym lub w chmurze.

## <a name="on-premises-based-hr-provisioning"></a>Zainicjowanie obsługi kadr w środowisku lokalnym
Lokalne Inicjowanie obsługi kadr jest realizowane przy użyciu lokalnego systemu kadr i środków aprowizacji nowych tożsamości cyfrowych.

Systemy KADRowe są dostępne w różnych pakietach, pakietach oprogramowania i mogą korzystać z serwerów SQL, katalogów LDAP itp.

Obecnie firmy Microsoft lokalne rozwiązania do aprowizacji w usłudze HR nie używają Microsoft Identity Manager do wyzwalania aprowizacji po utworzeniu nowej tożsamości w tych systemach KADRy.

Korzystając z programu MIM, możesz udostępnić użytkownikom z lokalnych systemów kadr w celu Active Directory lub Azure AD.

Informacje na temat Microsoft Identity Manager i obsługiwanych przez nią systemów można znaleźć w dokumentacji dotyczącej [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) .

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Następne kroki 
- [Co to jest zarządzanie cyklem życia tożsamości](what-is-identity-lifecycle-management.md)
- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest inicjowanie obsługi aplikacji?](what-is-app-provisioning.md)
- [Co to jest inicjowanie obsługi administracyjnej między katalogami?](what-is-inter-directory-provisioning.md)
- [Co to jest inicjowanie obsługi katalogów?](what-is-inter-directory-provisioning.md)