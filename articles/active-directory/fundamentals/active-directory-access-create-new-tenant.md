---
title: Szybki start — uzyskiwanie dostępu & tworzenia nowej dzierżawy — Azure AD
description: Instrukcje umożliwiające znalezienie usługi Azure Active Directory oraz utworzenie nowej dzierżawy dla organizacji.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc51c645c470f2b5b0a009eaf831db2f1957617e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780142"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Szybki start: tworzenie nowej dzierżawy w Azure Active Directory

Wszystkie zadania administracyjne, w tym tworzenie nowej dzierżawy dla swojej organizacji, możesz wykonywać przy użyciu portalu usługi Azure Active Directory (Azure AD). 

W tym przewodniku Szybki start dowiesz się, jak uzyskać dostęp do witryny Azure Portal i usługi Azure Active Directory oraz jak utworzyć podstawową dzierżawę dla organizacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-new-tenant-for-your-organization"></a>Tworzenie nowej dzierżawy dla organizacji

Po zalogowaniu się do witryny Azure Portal możesz utworzyć nową dzierżawę dla swojej organizacji. Nowa dzierżawa reprezentuje Twoją organizację i pomaga w zarządzaniu konkretnym wystąpieniem usług firmy Microsoft w chmurze na potrzeby użytkowników wewnętrznych i zewnętrznych.

### <a name="to-create-a-new-tenant"></a>Aby utworzyć nową dzierżawę

1. Zaloguj się do aplikacji Azure Portal [.](https://portal.azure.com/)

1. W menu Azure Portal wybierz pozycję **Azure Active Directory**.  

    <kbd>![Azure Active Directory — strona Przegląd — Tworzenie dzierżawy](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Wybierz **pozycję Utwórz dzierżawę.**

1. Na karcie Podstawowe wybierz typ dzierżawy, którą chcesz  utworzyć, albo Azure Active Directory **lub Azure Active Directory (B2C).**

1. Wybierz **pozycję Dalej: Konfiguracja,** aby przejść do karty Konfiguracja.

    <kbd>![Azure Active Directory — tworzenie dzierżawy — karta konfiguracji ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  Na karcie Konfiguracja wprowadź następujące informacje:
    
    - Wpisz _Contoso Organization_ w polu Nazwa **organizacji.**

    - Wpisz _Contosoorg_ w **polu Początkowa nazwa** domeny.

    - Pozostaw opcję _Stany Zjednoczone_ w polu **Kraj lub region**.

1. Wybierz **pozycję Dalej: Przeglądanie + tworzenie.** Przejrzyj wprowadzone informacje i jeśli informacje są poprawne, wybierz pozycję **utwórz .**

    <kbd>![Azure Active Directory — przeglądanie i tworzenie strony dzierżawy](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

Twoja nowa dzierżawa zostanie utworzona i będzie miała domenę contoso.onmicrosoft.com.

## <a name="your-user-account-in-the-new-tenant"></a>Twoje konto użytkownika w nowej dzierżawie

Podczas tworzenia nowej dzierżawy usługi Azure AD stajesz się pierwszym użytkownikiem tej dzierżawy. Jako pierwszy użytkownik masz automatycznie przypisaną rolę [administratora globalnego.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference#global-administrator) Sprawdź swoje konto użytkownika, przechodząc do [**strony**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers) Użytkownicy.

Domyślnie jesteś również wymieniony jako kontakt techniczny [dla](https://docs.microsoft.com/microsoft-365/admin/manage/change-address-contact-and-more?view=o365-worldwide#what-do-these-fields-mean) dzierżawy. Techniczne informacje kontaktowe to coś, co można zmienić w oknie [**Właściwości**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

> [!WARNING]
> Upewnij się, że katalog ma co najmniej dwa konta z przypisanymi do nich uprawnieniami administratora globalnego. Pomoże to w przypadku zablokowania jednego administratora globalnego. Aby uzyskać więcej informacji, zobacz artykuł [Zarządzanie kontami dostępu awaryjnego w usłudze Azure AD.](../roles/security-emergency-access.md)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz nadal korzystać z tej aplikacji, możesz usunąć dzierżawę, korzystając z następujących kroków:

- Upewnij się, że zalogowano się do katalogu, który chcesz usunąć, za pomocą filtru **Katalog i** subskrypcja w Azure Portal. W razie potrzeby przełącz się do katalogu docelowego.
- Wybierz usługę **Azure Active Directory**, a następnie na stronie **Contoso — przegląd** wybierz pozycję **Usuń katalog**.

    Dzierżawa zostanie usunięta wraz ze skojarzonymi informacjami.

    <kbd>![Strona Przegląd z wyróżnionym przyciskiem Usuń katalog](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Następne kroki

- Zmiana lub dodawanie kolejnych nazw domen — zobacz [Jak dodać nazwę domeny niestandardowej do usługi Azure Active Directory](add-custom-domain.md)

- Dodawanie użytkowników — zobacz [Dodawanie lub usuwanie nowego użytkownika](add-users-azure-active-directory.md)

- Dodawanie grup i członków — zobacz [Tworzenie podstawowej grupy i dodawanie członków](active-directory-groups-create-azure-portal.md)

- Dowiedz się [więcej o dostępie opartym](../../role-based-access-control/best-practices.md) na rolach przy Privileged Identity Management [dostępu](../../role-based-access-control/conditional-access-azure-management.md) warunkowego, aby ułatwić zarządzanie dostępem do aplikacji i zasobów w organizacji.

- Uzyskaj informacje o usłudze Azure AD, w tym [podstawowe informacje o licencjonowaniu oraz informacje o terminologii i skojarzonych funkcjach](active-directory-whatis.md).
