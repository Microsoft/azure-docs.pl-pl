---
title: Dodawanie lub usuwanie użytkowników — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące dodawania nowych użytkowników lub usuwania istniejących użytkowników przy użyciu Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36455b3f6395216bd1ed26c8bd193f0e3ca10b5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594601"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Dodawanie lub usuwanie użytkowników przy użyciu Azure Active Directory

Dodaj nowych użytkowników lub Usuń istniejących użytkowników z organizacji usługi Azure Active Directory (Azure AD). Aby dodać lub usunąć użytkowników, musisz być administratorem użytkownika lub administratorem globalnym.

## <a name="add-a-new-user"></a>Dodawanie nowego użytkownika

Nowy użytkownik można utworzyć przy użyciu portalu Azure Active Directory.

Aby dodać nowego użytkownika, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator użytkownika w organizacji.

1. Wyszukaj i wybierz *Azure Active Directory* z dowolnej strony.

1. Wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **nowy użytkownik**.

    ![Dodawanie użytkownika przez użytkowników — wszyscy użytkownicy w usłudze Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Na stronie **użytkownik** wprowadź informacje dla tego użytkownika:

   - **Nazwa**. Wymagane. Imię i nazwisko nowego użytkownika. Na przykład, *Maria Parker*.

   - **Nazwa użytkownika**. Wymagane. Nazwa użytkownika nowego użytkownika. Na przykład `mary@contoso.com`.

     Część domeny nazwa użytkownika musi używać początkowej domyślnej nazwy domeny, *\<yourdomainname> . onmicrosoft.com* lub niestandardowej nazwy domeny, takiej jak *contoso.com*. Aby uzyskać więcej informacji na temat tworzenia niestandardowej nazwy domeny, zobacz [Dodawanie niestandardowej nazwy domeny za pomocą portalu Azure Active Directory](add-custom-domain.md).

   - **Grupy**. Opcjonalnie możesz dodać użytkownika do jednej lub kilku istniejących grup. Możesz również dodać użytkownika do grup w późniejszym czasie. Aby uzyskać więcej informacji na temat dodawania użytkowników do grup, zobacz [Tworzenie grupy podstawowej i Dodawanie członków przy użyciu Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Rola katalogu**: Jeśli wymagane są uprawnienia administracyjne usługi Azure AD dla użytkownika, można je dodać do roli usługi Azure AD. Użytkownika można przypisać do administratora globalnego lub co najmniej jednej z ograniczonych ról administratora w usłudze Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Jak przypisać role do użytkowników](active-directory-users-assign-role-azure-portal.md).

   - **Informacje o zadaniu**: więcej informacji o użytkowniku można dodać tutaj lub później. Aby uzyskać więcej informacji na temat dodawania informacji o użytkowniku, zobacz [jak dodać lub zmienić informacje o profilu użytkownika](active-directory-users-profile-azure-portal.md).

1. Skopiuj automatycznie generowane hasło podane w polu **hasło** . Musisz podać to hasło użytkownikowi, aby zalogować się po raz pierwszy.

1. Wybierz przycisk **Utwórz**.

Użytkownik zostanie utworzony i dodany do organizacji usługi Azure AD.

## <a name="add-a-new-guest-user"></a>Dodawanie nowego użytkownika-gościa

Możesz również zaprosić nowego użytkownika-gościa do współpracy z organizacją, wybierając pozycję **Zaproś użytkownika** na stronie **nowego użytkownika** . Jeśli ustawienia współpracy zewnętrznej w organizacji są skonfigurowane tak, aby można było zapraszać Gości, użytkownik otrzyma wiadomość e-mail z zaproszeniem, które musi zaakceptować, aby rozpocząć współpracę. Aby uzyskać więcej informacji na temat zapraszania użytkowników współpracy B2B, zobacz [Zapraszanie użytkowników B2B do Azure Active Directory](../external-identities/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Dodawanie użytkownika odbiorcy

Mogą istnieć scenariusze, w których chcesz ręcznie utworzyć konta konsumentów w katalogu Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji na temat tworzenia kont klientów, zobacz [Tworzenie i usuwanie użytkowników konsumentów w Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Dodawanie nowego użytkownika w środowisku hybrydowym

Jeśli masz środowisko z zarówno Azure Active Directory (Cloud), jak i Active Directory systemu Windows Server (lokalnie), możesz dodać nowych użytkowników, synchronizując istniejące dane konta użytkownika. Aby uzyskać więcej informacji na temat środowisk hybrydowych i użytkowników, zobacz [integrowanie katalogów lokalnych z Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Usuwanie użytkownika

Istniejącego użytkownika można usunąć przy użyciu portalu Azure Active Directory.

>[!Note]
>Aby usunąć użytkowników w organizacji, musisz mieć przypisaną rolę administratora globalnego lub administratora użytkowników. Administratorzy globalni mogą usunąć wszystkich użytkowników, w tym innych administratorów. Administratorzy użytkowników mogą usunąć wszystkich użytkowników niebędących administratorami, administratorów pomocy technicznej i innych administratorów. Aby uzyskać więcej informacji, zobacz [uprawnienia roli administratora w usłudze Azure AD](../roles/permissions-reference.md).

Aby usunąć użytkownika, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora użytkownika w organizacji.

1. Wyszukaj i wybierz *Azure Active Directory* z dowolnej strony.

1. Wyszukaj i wybierz użytkownika, który chcesz usunąć z dzierżawy usługi Azure AD. Na przykład, _Maria Parker_.

1. Wybierz pozycję **Usuń użytkownika**.

    ![Użytkownicy — wszyscy użytkownicy z wyróżnioną pozycją Usuń użytkownika](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

Użytkownik zostanie usunięty i nie będzie już wyświetlany na stronie **Użytkownicy — wszyscy użytkownicy** . Użytkownik może być widoczny na stronie **usunięci użytkownicy** przez następne 30 dni i może zostać przywrócony w tym czasie. Aby uzyskać więcej informacji o przywracaniu użytkownika, zobacz [przywracanie lub usuwanie ostatnio usuniętego użytkownika przy użyciu Azure Active Directory](active-directory-users-restore.md).

Po usunięciu użytkownika wszystkie licencje używane przez użytkownika są udostępniane innym użytkownikom.

>[!Note]
>Aby zaktualizować tożsamość, informacje o kontakcie lub informacje o zadaniu dla użytkowników, których źródłem jest urząd certyfikacji systemu Windows Server Active Directory, należy użyć Active Directory systemu Windows Server. Po zakończeniu aktualizacji należy poczekać na zakończenie następnego cyklu synchronizacji, zanim zobaczysz zmiany.

## <a name="next-steps"></a>Następne kroki

Po dodaniu użytkowników można wykonywać następujące podstawowe procesy:

- [Dodawanie lub zmiana informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

- [Współpraca z grupami dynamicznymi i użytkownikami](../enterprise-users/groups-create-rule.md)

Można też wykonywać inne zadania zarządzania użytkownikami, takie jak [Dodawanie użytkowników-Gości z innego katalogu](../external-identities/what-is-b2b.md) lub [Przywracanie usuniętego użytkownika](active-directory-users-restore.md). Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [Azure Active Directory dokumentacja dotycząca zarządzania użytkownikami](../enterprise-users/index.yml).