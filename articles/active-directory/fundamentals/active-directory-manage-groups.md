---
title: Zarządzanie dostępem do zasobów & aplikacji przy użyciu grup — Azure AD
description: Dowiedz się więcej na temat zarządzania dostępem do aplikacji w chmurze w organizacji, lokalnych aplikacjach i zasobów przy użyciu grup Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25dace3ad7d467d6add236782c5e39f85d6462a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87797311"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Zarządzanie dostępem do aplikacji i zasobów za pomocą grup usługi Azure Active Directory
Azure Active Directory (Azure AD) umożliwia używanie grup do zarządzania dostępem do aplikacji w chmurze, aplikacji lokalnych i zasobów. Twoje zasoby mogą być częścią organizacji usługi Azure AD, np. uprawnieniami do zarządzania obiektami za pomocą ról w usłudze Azure AD lub spoza organizacji, na przykład w przypadku aplikacji oprogramowania jako usługi (SaaS), usług platformy Azure, witryn programu SharePoint i zasobów lokalnych.

>[!NOTE]
> W Azure Portal można zobaczyć niektóre grupy, których szczegóły członkostwa i grupy nie są zarządzane w portalu:
>
> - Grupy synchronizowane z lokalnym Active Directory mogą być zarządzane tylko w Active Directory lokalnym.
> - Inne typy grup, takie jak listy dystrybucyjne i grupy zabezpieczeń z włączoną obsługą poczty, są zarządzane tylko w centrum administracyjnym programu Exchange lub w centrum administracyjnym Microsoft 365. Aby zarządzać tymi grupami, musisz zalogować się w centrum administracyjnym programu Exchange lub Microsoft 365 centrum administracyjnym.

## <a name="how-access-management-in-azure-ad-works"></a>Jak działa zarządzanie dostępem w usłudze Azure AD

Usługa Azure AD pomaga zapewnić dostęp do zasobów organizacji, zapewniając prawa dostępu do pojedynczego użytkownika lub do całej grupy usługi Azure AD. Dzięki grupom właściciel zasobu (lub właściciel katalogu usługi Azure AD) nie musi pojedynczo przypisywać uprawnień — może przypisać zestaw uprawnień dostępu do wszystkich członków grupy. Właściciel zasobu lub katalogu może również udzielić innym osobom praw do zarządzania na liście członków, takich jak Menedżer działu lub administrator pomocy technicznej, co pozwala tej osobie dodawać i usuwać członków w razie potrzeby. Więcej informacji o sposobach zarządzania właścicielami grupy znajduje się w temacie [Zarządzanie właścicielami grupy](active-directory-accessmanagement-managing-group-owners.md)

![Diagram zarządzania dostępem w usłudze Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Sposoby przypisywania praw dostępu

Istnieją cztery sposoby przypisywania praw dostępu do zasobów użytkownikom:

- **Przypisanie bezpośrednie.** Właściciel zasobu bezpośrednio przypisuje użytkownika do zasobu.

- **Przypisanie grupy.** Właściciel zasobu przypisuje grupę usługi Azure AD do zasobu, co powoduje automatyczne nadaje wszystkim członkom grupy dostęp do zasobu. Członkostwo w grupie jest zarządzane przez właściciela grupy i właściciela zasobu, co umożliwia właścicielowi Dodawanie lub usuwanie członków grupy. Aby uzyskać więcej informacji na temat dodawania lub usuwania członkostwa w grupach, zobacz [How to: Dodawanie lub usuwanie grupy z innej grupy przy użyciu portalu Azure Active Directory](active-directory-groups-membership-azure-portal.md). 

- **Przypisanie oparte na regułach.** Właściciel zasobu tworzy grupę i używa reguły do definiowania użytkowników, którzy są przypisani do określonego zasobu. Reguła jest oparta na atrybutach przypisanych do poszczególnych użytkowników. Właściciel zasobu zarządza tą regułą i określa, które atrybuty i wartości są wymagane, aby umożliwić dostęp do zasobu. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](../users-groups-roles/groups-create-rule.md).

    Możesz również obejrzeć ten krótki film, aby zapoznać się z szybkim wyjaśnieniem tworzenia i używania grup dynamicznych:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Przypisanie urzędu zewnętrznego.** Dostęp pochodzi z zewnętrznego źródła, takiego jak katalog lokalny lub aplikacja SaaS. W takiej sytuacji właściciel zasobu przypisuje grupę w celu zapewnienia dostępu do zasobu, a następnie zewnętrzne źródło zarządza członkami grupy.

   ![Diagram przedstawiający przegląd zarządzania dostępem](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Czy użytkownicy mogą sprzęgać grupy bez przypisywania?
Właściciel grupy może pozwolić użytkownikom na znalezienie własnych grup do przyłączenia, zamiast ich przypisywania. Właściciel może również skonfigurować grupę w taki sposób, aby automatycznie akceptowała wszystkich użytkowników, którzy dołączyli się lub aby wymagały zatwierdzenia.

Gdy użytkownik zażąda dołączenia do grupy, żądanie jest przekazywane do właściciela grupy. Jeśli jest to wymagane, właściciel może zatwierdzić żądanie, a użytkownik zostanie powiadomiony o członkostwie w grupie. Jeśli jednak masz wielu właścicieli, a jeden z nich nie zostanie zaakceptowany, użytkownik zostanie powiadomiony, ale nie zostanie dodany do grupy. Aby uzyskać więcej informacji i instrukcje dotyczące zezwalania użytkownikom na żądanie dołączenia do grup, zobacz [Konfigurowanie usługi Azure AD, aby umożliwić użytkownikom dołączanie do grup](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz już wprowadzenie do zarządzania dostępem przy użyciu grup, zacznij zarządzać zasobami i aplikacjami.

- [Utwórz nową grupę przy użyciu Azure Active Directory](active-directory-groups-create-azure-portal.md) lub [Utwórz nową grupę i zarządzaj nią za pomocą poleceń cmdlet programu PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Przypisywanie dostępu do zintegrowanej aplikacji SaaS przy użyciu grup](../users-groups-roles/groups-saasapps.md)

- [Synchronizuj grupę lokalną z platformą Azure przy użyciu Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
