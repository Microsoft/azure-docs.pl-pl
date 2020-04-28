---
title: Zamknij konto służbowe w niezarządzanym katalogu usługi Azure AD
description: Jak zamknąć konto służbowe w niezarządzanym Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73815720"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Zamknij konto służbowe w niezarządzanym katalogu

Jeśli jesteś użytkownikiem w organizacji niezarządzanej Azure Active Directory (Azure AD), a nie potrzebujesz już korzystać z aplikacji z tej organizacji lub nie masz żadnych skojarzonych z nimi powiązań, możesz zamknąć konto w dowolnym momencie. Niezarządzany katalog nie ma administratora globalnego. Użytkownicy w niezarządzanym katalogu mogą samodzielnie zamknąć swoje konta bez konieczności kontaktowania się z administratorem.

Użytkownicy w niezarządzanym katalogu są często tworzeniu podczas tworzenia konta samoobsługowego. Przykładem może być pracownik przetwarzający informacje w organizacji, który zarejestruje się w celu uzyskania bezpłatnej usługi. Aby uzyskać więcej informacji na temat rejestracji samoobsługowej, zobacz [co to jest samoobsługowe rejestrowanie w usłudze Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed zamknięciem konta należy potwierdzić następujące elementy:

* Upewnij się, że jesteś użytkownikiem niezarządzanego katalogu usługi Azure AD. Nie można zamknąć konta, jeśli użytkownik należy do katalogu zarządzanego. Jeśli należysz do katalogu zarządzanego i chcesz zamknąć konto, musisz skontaktować się z administratorem. Aby uzyskać informacje na temat sposobu ustalania, czy należysz do niezarządzanego katalogu, zobacz [Usuwanie użytkownika z niezarządzanej dzierżawy](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Zapisz wszystkie dane, które chcesz zachować. Informacje o sposobach przesyłania żądania eksportu znajdują się w temacie [Uzyskiwanie dostępu do dzienników generowanych przez system i eksportowanie ich dla niezarządzanych dzierżawców](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Zamykanie konta jest nieodwracalne. Po zamknięciu konta zostaną usunięte wszystkie dane osobowe. Nie będziesz już mieć dostępu do Twojego konta i danych skojarzonych z Twoim kontem.

## <a name="close-your-account"></a>Zamykanie konta

Aby zamknąć niezarządzane konto służbowe, wykonaj następujące czynności:

1. Zaloguj się, aby [zamknąć konto](https://go.microsoft.com/fwlink/?linkid=873123), używając konta, które chcesz zamknąć.

1. Na **stronie Moje żądania danych**wybierz pozycję **Zamknij konto**.

    ![Moje żądania danych — zamykanie konta](./media/users-close-account/close-account.png)

1. Przejrzyj komunikat z potwierdzeniem, a następnie wybierz pozycję **tak**.

    ![Moje żądania danych — Potwierdź zamknięcie](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Następne kroki

- [Co to jest samoobsługowe rejestrowanie się w Azure Active Directory?](directory-self-service-signup.md)
- [Usuwanie użytkownika z dzierżawy niezarządzanej](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Uzyskiwanie dostępu do dzienników generowanych przez system i ich eksportowanie dla dzierżaw niezarządzanych](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
