---
title: Szybki Start — dostęp & Tworzenie nowej dzierżawy — Azure AD
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
ms.openlocfilehash: eeea88d8c21ba754fbeadbb24891126b639616c7
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437244"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Szybki Start: Tworzenie nowej dzierżawy w Azure Active Directory
Wszystkie zadania administracyjne, w tym tworzenie nowej dzierżawy dla swojej organizacji, możesz wykonywać przy użyciu portalu usługi Azure Active Directory (Azure AD). 

W tym przewodniku Szybki start dowiesz się, jak uzyskać dostęp do witryny Azure Portal i usługi Azure Active Directory oraz jak utworzyć podstawową dzierżawę dla organizacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="create-a-new-tenant-for-your-organization"></a>Tworzenie nowej dzierżawy dla organizacji
Po zalogowaniu się do witryny Azure Portal możesz utworzyć nową dzierżawę dla swojej organizacji. Nowa dzierżawa reprezentuje Twoją organizację i pomaga w zarządzaniu konkretnym wystąpieniem usług firmy Microsoft w chmurze na potrzeby użytkowników wewnętrznych i zewnętrznych.

### <a name="to-create-a-new-tenant"></a>Aby utworzyć nową dzierżawę

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)organizacji.

1. Z menu Azure Portal wybierz pozycję **Azure Active Directory**.  

    <kbd>![Azure Active Directory — przegląd — tworzenie dzierżawy](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Wybierz pozycję **Utwórz dzierżawę**.

1. Na karcie podstawowe wybierz typ dzierżawy, którą chcesz utworzyć, **Azure Active Directory** lub **Azure Active Directory (B2C)**.

1. Wybierz pozycję **Dalej: Konfiguracja** , która ma zostać przeniesiona na kartę Konfiguracja.

    <kbd>![Azure Active Directory — Tworzenie strony dzierżawy — Karta Konfiguracja ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  Na karcie Konfiguracja wprowadź następujące informacje:
    
    - Wpisz _organizację contoso_ w polu **Nazwa organizacji** .

    - Wpisz _Contosoorg_ w polu **nazwa domeny początkowej** .

    - Pozostaw opcję _Stany Zjednoczone_ w polu **Kraj lub region**.

1. Wybierz pozycję **Dalej: przegląd + Utwórz**. Przejrzyj wprowadzone informacje i jeśli informacje są poprawne, wybierz pozycję **Utwórz**.

    <kbd>![Azure Active Directory — przeglądanie i Tworzenie strony dzierżawy](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

Twoja nowa dzierżawa zostanie utworzona i będzie miała domenę contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie chcesz nadal korzystać z tej aplikacji, możesz usunąć dzierżawę, wykonując następujące czynności:

- Upewnij się, że logujesz się do katalogu, który chcesz usunąć za pomocą filtru **katalogów i subskrypcji** w Azure Portal, i w razie potrzeby przełączasz się do katalogu docelowego.
- Wybierz usługę **Azure Active Directory**, a następnie na stronie **Contoso — przegląd** wybierz pozycję **Usuń katalog**.

    Dzierżawa zostanie usunięta wraz ze skojarzonymi informacjami.

    <kbd>![Strona przegląd z wyróżnionym przyciskiem Usuń katalog](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Następne kroki
- Zmiana lub dodawanie kolejnych nazw domen — zobacz [Jak dodać nazwę domeny niestandardowej do usługi Azure Active Directory](add-custom-domain.md)

- Dodawanie użytkowników — zobacz [Dodawanie lub usuwanie nowego użytkownika](add-users-azure-active-directory.md)

- Dodawanie grup i członków — zobacz [Tworzenie podstawowej grupy i dodawanie członków](active-directory-groups-create-azure-portal.md)

- Informacje na temat [dostępu opartego na rolach przy użyciu Privileged Identity Management](../../role-based-access-control/best-practices.md) i [dostępu warunkowego](../../role-based-access-control/conditional-access-azure-management.md) w celu ułatwienia zarządzania dostępem do aplikacji i zasobów organizacji.

- Uzyskaj informacje o usłudze Azure AD, w tym [podstawowe informacje o licencjonowaniu oraz informacje o terminologii i skojarzonych funkcjach](active-directory-whatis.md).
