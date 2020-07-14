---
title: 'Szybki Start: Konfigurowanie właściwości aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki Start używa Azure Portal do konfigurowania aplikacji, która została zarejestrowana w dzierżawie Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8418a45c9eba596339999c2bddf937a516d3e2d9
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223350"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Szybki Start: Konfigurowanie właściwości aplikacji w dzierżawie usługi Azure Active Directory (Azure AD)

W poprzednim przewodniku szybki start dodano aplikację do dzierżawy usługi Azure Active Directory (Azure AD). Po dodaniu aplikacji będziesz mieć pewność, że dzierżawa usługi Azure AD jest dostawcą tożsamości dla aplikacji. Teraz skonfigurujesz niektóre właściwości aplikacji.
 
## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować właściwości aplikacji w dzierżawie usługi Azure AD, należy wykonać następujące działania:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- Opcjonalne: kończenie [wyświetlania aplikacji](view-applications-portal.md).
- Opcjonalne: kończenie [dodawania aplikacji](add-application-portal.md).

>[!IMPORTANT]
>W celu przetestowania kroków w tym przewodniku szybki start Użyj środowiska nieprodukcyjnego.

## <a name="configure-app-properties"></a>Konfigurowanie właściwości aplikacji

Po zakończeniu dodawania aplikacji do dzierżawy usługi Azure AD zostanie wyświetlona strona przegląd. Jeśli konfigurujesz aplikację, która została już dodana, zapoznaj się z pierwszym przewodnikiem Szybki Start. Przeprowadzi Cię przez proces wyświetlania aplikacji dodanych do dzierżawy. 

Aby edytować właściwości aplikacji:

1. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie Znajdź i wybierz aplikację, którą chcesz skonfigurować.
1. W sekcji **Zarządzanie** wybierz pozycję **Właściwości** , aby otworzyć okienko **Właściwości** do edycji.

    ![Zrzut ekranu przedstawiający ekran właściwości, który pokazuje edytowalne właściwości aplikacji.](media/add-application-portal/edit-properties.png)

1. Poświęć chwilę na zapoznanie się z opcjami dostępnymi do konfiguracji:
    - **Włączono, aby użytkownicy mogli się zalogować?** Określa, czy użytkownicy przypisani do aplikacji mogą się zalogować.
    - **Czy przypisanie użytkownika jest wymagane?** Określa, czy użytkownicy, którzy nie są przypisani do aplikacji, mogą się zalogować.
    - **Widoczne dla użytkowników?** Określa, czy użytkownicy przypisani do aplikacji mogą ją zobaczyć w [panelu dostępu](https://myapps.microsoft.com) i uruchamiania aplikacji pakietu Office 365. (Zobacz menu Gofr w lewym górnym rogu witryny sieci Web pakietu Office 365 lub Microsoft 365).
1. Poniższe tabele ułatwiają wybranie najlepszych opcji dla potrzeb użytkownika.

   - Zachowanie w przypadku użytkowników *przypisanych*:

       | Właściwość aplikacji | Właściwość aplikacji | Właściwość aplikacji | Środowisko użytkowników przypisanych | Środowisko użytkowników przypisanych |
       |---|---|---|---|---|
       | Włączono, aby użytkownicy mogli się zalogować? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy przypisani użytkownicy mogą się logować? | Czy przypisani użytkownicy widzą aplikację?* |
       | Tak | Tak | Tak | Tak | Tak  |
       | Tak | Tak | Nie  | Yes | Nie   |
       | Yes | Nie  | Tak | Tak | Tak  |
       | Tak | Nie  | Nie  | Yes | Nie   |
       | Nie  | Tak | Tak | Nie  | Nie   |
       | Nie  | Yes | Nie  | Nie  | Nie   |
       | Nie  | Nie  | Yes | Nie  | Nie   |
       | Nie  | Nie  | Nie  | Nie  | Nie   |

   - Zachowanie w przypadku użytkowników *nieprzypisanych*:

       | Właściwość aplikacji | Właściwość aplikacji | Właściwość aplikacji | Środowisko użytkowników nieprzypisanych | Środowisko użytkowników nieprzypisanych |
       |---|---|---|---|---|
       | Włączono, aby użytkownicy mogli się zalogować? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy nieprzypisani użytkownicy mogą się logować? | Czy nieprzypisani użytkownicy widzą aplikację?* |
       | Tak | Tak | Tak | Nie  | Nie   |
       | Tak | Tak | Nie  | Nie  | Nie   |
       | Yes | Nie  | Tak | Tak | Nie   |
       | Yes | Nie  | Nie  | Yes | Nie   |
       | Nie  | Tak | Tak | Nie  | Nie   |
       | Nie  | Yes | Nie  | Nie  | Nie   |
       | Nie  | Nie  | Yes | Nie  | Nie   |
       | Nie  | Nie  | Nie  | Nie  | Nie   |

     *Czy użytkownik widzi aplikację w panelu dostępu i narzędziu do uruchamiania aplikacji usługi Office 365?

## <a name="use-a-custom-logo"></a>Używanie logo niestandardowego

Aby użyć logo niestandardowego:

1. Utwórz logo o 215 do 215 pikseli i Zapisz je w formacie PNG.
1. W portalu usługi Azure AD wybierz pozycję **aplikacje dla przedsiębiorstw**. Następnie Znajdź i wybierz aplikację, którą chcesz skonfigurować.
1. W sekcji **Zarządzanie** wybierz pozycję **Właściwości** , aby otworzyć okienko **Właściwości** do edycji. 
1. Wybierz ikonę, aby przekazać logo.
1. Gdy skończysz, wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający ekran właściwości, który pokazuje, jak zmienić logo.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatura wyświetlana w tym okienku **Właściwości** nie jest natychmiast aktualizowana. Możesz zamknąć i ponownie otworzyć okienko **Właściwości** , aby wyświetlić zaktualizowaną ikonę.

## <a name="next-steps"></a>Następne kroki

Teraz, po skonfigurowaniu właściwości aplikacji, można kontynuować konfigurowanie rejestracji jednokrotnej.

- [Konfigurowanie logowania jednokrotnego](add-application-portal-setup-sso.md)
- [Usuwanie aplikacji](delete-application-portal.md)