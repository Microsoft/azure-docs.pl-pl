---
title: Logowanie się do aplikacji usługi Azure AD — platforma tożsamości firmy Microsoft
description: Skonfiguruj połączone Logowanie jednokrotne do aplikacji usługi Azure AD Enterprise w usłudze Microsoft Identity platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063547"
---
# <a name="configure-linked-sign-on"></a>Konfigurowanie połączonego logowania

Po dodaniu galerii lub aplikacji sieci Web bez galerii jedna z [opcji logowania jednokrotnego jest dostępna](what-is-single-sign-on.md)dla użytkownika. Wybierz tę opcję, aby dodać link do aplikacji w panelu dostępu usługi Azure AD w organizacji lub w portalu pakietu Office 365. Za pomocą tej metody można dodawać linki do niestandardowych aplikacji sieci Web, które obecnie używają Active Directory Federation Services (lub innej usługi federacyjnej) zamiast usługi Azure AD do uwierzytelniania. Możesz też dodać głębokie linki do określonych stron programu SharePoint lub innych stron sieci Web, które mają być wyświetlane w panelu dostępu użytkownika.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli aplikacja nie została dodana do dzierżawy usługi Azure AD, zobacz [Dodawanie aplikacji galerii](add-gallery-app.md) lub Dodawanie aplikacji niebędącej [galerią](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Otwórz aplikację i wybierz pozycję połączono logowanie

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator aplikacji w chmurze lub administrator aplikacji dla dzierżawy usługi Azure AD.

1. Przejdź do **Azure Active Directory** > **aplikacji przedsiębiorstwa**. Zostanie wyświetlona Losowa przykład aplikacji w dzierżawie usługi Azure AD. 

1. W menu **Typ aplikacji** wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Zastosuj**.

1. Wprowadź nazwę aplikacji w polu wyszukiwania, a następnie wybierz aplikację z wyników.

1. W sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**. 

1. Wybierz pozycję **połączone**.

1. Wprowadź adres URL aplikacji, z którą ma zostać utworzone połączenie. Wpisz adres URL i wybierz pozycję **Zapisz**. 
 
1. Do aplikacji można przypisywać użytkowników i grupy, co powoduje, że aplikacja jest widoczna w obszarze [uruchamiania aplikacji pakietu Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) lub w [panelu dostępu usługi Azure AD](end-user-experiences.md) dla tych użytkowników.

1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](methods-for-assigning-users-and-groups.md)
- [Konfigurowanie automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
