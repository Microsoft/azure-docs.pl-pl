---
title: Charakterystyki wielu interakcji dzierżawców — Azure AD | Microsoft Docs
description: Zrozumienie niezależności danych organizacji Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1db812fb9c1503771bec0b1795ee8c15bb70014a
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379143"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Informacje o tym, jak działa wiele Azure Active Directory organizacji

W Azure Active Directory (Azure AD) każda organizacja usługi Azure AD jest w pełni niezależna: element równorzędny logicznie niezależny od innych organizacji usługi Azure AD, którymi zarządzasz. Ta niezależność między organizacjami obejmuje niezależność zasobów, niezależność administracyjną i niezależność synchronizacji. Między organizacjami nie ma relacji nadrzędny-podrzędny.

## <a name="resource-independence"></a>Niezależność zasobów

* Jeśli utworzysz lub usuniesz zasób usługi Azure AD w jednej organizacji, nie będzie to miało wpływu na żaden zasób w innej organizacji, z częściowym wyjątkiem użytkowników zewnętrznych.
* Jeśli jedna z nazw domen zostanie zarejestrowana w jednej organizacji, nie będzie ona używana przez żadną inną organizację.

## <a name="administrative-independence"></a>Niezależność administracyjna

Jeśli użytkownik niebędący administratorami organizacji "contoso" utworzy test w organizacji testowej, to:

* Domyślnie użytkownik, który tworzy organizację, jest dodawany jako użytkownik zewnętrzny w nowej organizacji i ma przypisaną rolę administratora globalnego w tej organizacji.
* Administratorzy organizacji "contoso" nie mają bezpośrednich uprawnień administracyjnych do testowania w organizacji, chyba że administrator "test" jawnie przyznał im te uprawnienia. Administratorzy programu "contoso" mogą jednak kontrolować dostęp do testu w organizacji, jeśli kontroluje konto użytkownika, które utworzyło "test".
* W przypadku dodania lub usunięcia roli usługi Azure AD dla użytkownika w jednej organizacji zmiana nie ma wpływu na role, do których użytkownik jest przypisany w żadnej innej organizacji usługi Azure AD.

## <a name="synchronization-independence"></a>Niezależność synchronizacji

Każdą organizację usługi Azure AD można skonfigurować niezależnie, aby pobierać dane zsynchronizowane z jednego wystąpienia:

* Narzędzie Azure AD Connect do synchronizowania danych z pojedynczym lasem usługi AD.
* Łącznik Azure Active Directory dla programu Forefront Identity Manager, służący do synchronizowania danych z co najmniej jednymi lokalnymi lasami i/lub źródłami danych spoza usługi Azure AD.

## <a name="add-an-azure-ad-organization"></a>Dodawanie organizacji usługi Azure AD

Aby dodać organizację usługi Azure AD do Azure Portal, zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta, które jest administratorem globalnym usługi Azure AD, a następnie wybierz pozycję **Nowy**.

> [!NOTE]
> W przeciwieństwie do innych zasobów platformy Azure, organizacje usługi Azure AD nie są zasobami podrzędnymi subskrypcji platformy Azure. Jeśli subskrypcja platformy Azure została anulowana lub wygasła, nadal możesz uzyskać dostęp do danych organizacji usługi Azure AD za pomocą Azure PowerShell, interfejsu API Microsoft Graph lub centrum administracyjnego Microsoft 365. Możesz również [skojarzyć kolejną subskrypcję z organizacją](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Następne kroki

Zagadnienia dotyczące licencjonowania usługi Azure AD i najlepsze rozwiązania można znaleźć w artykule [co to jest Azure Active Directory Licencjonowanie?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
