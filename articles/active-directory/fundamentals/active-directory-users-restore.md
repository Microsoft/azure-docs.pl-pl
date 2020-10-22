---
title: Przywracanie lub trwałe usuwanie ostatnio usuniętego użytkownika — Azure AD
description: Jak wyświetlić dostępnych użytkowników, przywrócić usuniętego użytkownika lub trwale usunąć użytkownika z Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/01/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bba8a0898c11cf1af150f4824d9bef5e9e03576e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370886"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Przywracanie lub usuwanie ostatnio usuniętego użytkownika przy użyciu Azure Active Directory
Po usunięciu użytkownika konto pozostaje w stanie wstrzymania przez 30 dni. W tym 30-dniowym oknie można przywrócić konto użytkownika wraz ze wszystkimi jego właściwościami. Po upływie tego 30-dniowego okna użytkownik zostanie automatycznie i trwale usunięty.

Możesz wyświetlić dostępnych użytkowników, przywrócić usuniętego użytkownika lub trwale usunąć użytkownika przy użyciu Azure Active Directory (Azure AD) w Azure Portal.

>[!Important]
>Nie można przywrócić użytkownika, który został trwale usunięty.

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby przywrócić i trwale usunąć użytkowników, musisz mieć jedną z następujących ról:

- Administrator globalny

- Obsługa pomoc partnera

- Obsługa SVR partnera

- Administrator użytkowników

## <a name="view-your-restorable-users"></a>Wyświetlanie użytkowników dostępnych
Zobaczysz wszystkich użytkowników, którzy zostali usunięci poniżej 30 dni temu. Tych użytkowników można przywrócić.

### <a name="to-view-your-restorable-users"></a>Aby wyświetlić użytkowników dostępnych
1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla organizacji.

2. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **usunięci użytkownicy**.

    Zapoznaj się z listą użytkowników, którzy są dostępni do przywracania.

    ![Strona użytkownicy usunięci użytkownicy z innymi użytkownikami, którzy nadal mogą być przywracani](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Przywrócenie ostatnio usuniętego użytkownika

Po usunięciu konta użytkownika z organizacji konto jest w stanie wstrzymania i wszystkie powiązane informacje organizacji są zachowywane. Podczas przywracania użytkownika są również przywracane informacje o organizacji.

> [!Note]
> Po przywróceniu użytkownika zostaną również przywrócone licencje przypisane do użytkownika w czasie usuwania, nawet jeśli dla tych licencji nie ma dostępnych stanowisk. Jeśli zużywasz więcej licencji więcej niż zakupiono, Twoja organizacja może być tymczasowo niezgodna z użyciem licencji.

### <a name="to-restore-a-user"></a>Aby przywrócić użytkownika
1. Na stronie użytkownicy z **usuniętymi** użytkownikami Wyszukaj i wybierz jednego z dostępnych użytkowników. Na przykład, _Maria Parker_.

2. Wybierz pozycję **Przywróć użytkownika**.

    ![Strona użytkownicy usunięci użytkownicy z wyróżnioną opcją Przywróć użytkownika](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Trwałe usuwanie użytkownika
Można trwale usunąć użytkownika z organizacji bez czekania 30 dni na automatyczne usunięcie. Trwale usunięty użytkownik nie może zostać przywrócony przez Ciebie, innego administratora ani przez dział pomocy technicznej firmy Microsoft.

>[!Note]
>Jeśli użytkownik trwale usunie użytkownika przez pomyłkę, należy utworzyć nowego użytkownika i ręcznie wprowadzić wszystkie poprzednie informacje. Aby uzyskać więcej informacji na temat tworzenia nowego użytkownika, zobacz [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Aby trwale usunąć użytkownika

1. Na stronie użytkownicy z **usuniętymi** użytkownikami Wyszukaj i wybierz jednego z dostępnych użytkowników. Na przykład _Rae Huff_.

2. Wybierz pozycję **Usuń trwale**.

    ![Strona użytkownicy usunięci użytkownicy z wyróżnioną opcją Usuń użytkownika](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Następne kroki
Po przywróceniu lub usunięciu użytkowników można wykonywać następujące podstawowe procesy:

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Dodawanie lub zmiana informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Dodawanie użytkowników-Gości z innej organizacji](../external-identities/what-is-b2b.md)

Aby uzyskać więcej informacji na temat innych dostępnych zadań zarządzania użytkownikami, zapoznaj się z [dokumentacją zarządzania użytkownikami usługi Azure AD](../enterprise-users/index.yml).
