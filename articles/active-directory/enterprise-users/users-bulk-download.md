---
title: Pobierz listę użytkowników w portalu Azure Active Directory | Microsoft Docs
description: Pobieranie rekordów użytkowników zbiorczo w centrum administracyjnym platformy Azure w Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57e3a059a5dd846250ba162513ef118e084c4b87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861591"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Pobieranie listy użytkowników w portalu Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje importowania użytkowników zbiorczych (tworzenia).

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby pobrać listę użytkowników z centrum administracyjnego usługi Azure AD, należy się zalogować przy użyciu konta użytkownika z przypisaną co najmniej jedną rolą administratora na poziomie organizacji w usłudze Azure AD (minimalna wymagana rola to administrator użytkowników). Role osoby zapraszającej gości i dewelopera aplikacji nie są uznawane za role administratorów.

## <a name="to-download-a-list-of-users"></a>Aby pobrać listę użytkowników

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora użytkownika w organizacji.
2. Przejdź do ekranu Azure Active Directory > Użytkownicy. Wybierz użytkowników, którzy mają zostać uwzględnieni podczas pobierania, zaznaczając pola w lewej kolumnie obok każdego użytkownika. Uwaga: Aktualnie nie ma możliwości wybrania wszystkich użytkowników do eksportowania. Każdy użytkownik musi być wybrany indywidualnie.
3. W usłudze Azure AD wybierz opcję **Użytkownicy**  >  **pobierają użytkowników**.
4. Na stronie **pobieranie użytkowników** wybierz pozycję **Rozpocznij** , aby wyświetlić plik CSV z listą właściwości profilu użytkownika. W przypadku wystąpienia błędów plik z wynikami można pobrać i wyświetlić na stronie wyników operacji zbiorczej. Plik zawiera przyczynę każdego błędu.

   ![Wybierz, gdzie chcesz wyświetlić listę użytkowników, których chcesz pobrać](./media/users-bulk-download/bulk-download.png)

   Pobrany plik będzie zawierać przefiltrowaną listę użytkowników.

   Uwzględniane są następujące atrybuty użytkownika:

   - userPrincipalName
   - displayName
   - surname
   - mail (poczta)
   - givenName
   - objectId
   - userType
   - jobTitle
   - działu,
   - accountEnabled
   - usageLocation
   - streetAddress
   - stan
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - telefon komórkowy
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - grupa_wiekowa
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Sprawdzanie stanu

Stan oczekujących żądań zbiorczych można zobaczyć na stronie **wyników operacji zbiorczej** .

[![Sprawdź stan na stronie wyniki operacji zbiorczych.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity usługi pobierania zbiorczego

Każde działanie zbiorcze służące do tworzenia listy użytkowników może być uruchamiane przez maksymalnie jedną godzinę. Umożliwia to utworzenie i pobranie listy dla co najmniej 500 000 użytkowników.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze dodawanie użytkowników](users-bulk-add.md)
- [Zbiorcze usuwanie użytkowników](users-bulk-delete.md)
- [Zbiorcze przywracanie użytkowników](users-bulk-restore.md)
