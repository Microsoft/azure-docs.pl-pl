---
title: Pobieranie listy użytkowników w portalu usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Pobieranie rekordów użytkowników zbiorczo w centrum administracyjnym platformy Azure w usłudze Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b35163387ed4ce71f7a2019835a1d9fdbff3051
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532655"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Pobieranie listy użytkowników w portalu usługi Azure Active Directory

Usługa Azure Active Directory (Azure AD) obsługuje operacje importowania (tworzenia) użytkowników zbiorczych.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby pobrać listę użytkowników z centrum administracyjnego usługi Azure AD, musisz zalogować się z użytkownikiem przypisanym do jednej lub więcej ról administratora na poziomie organizacji w usłudze Azure AD (Administrator użytkownika jest minimalną wymaganą rolą). Zaproszony i deweloper aplikacji nie są uznawane za role administratora.

## <a name="to-download-a-list-of-users"></a>Aby pobrać listę użytkowników

1. [Zaloguj się do organizacji usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta administratora użytkownika w organizacji.
2. Przejdź do usługi Azure Active Directory > użytkowników. Następnie wybierz użytkowników, których chcesz uwzględnić w pobraniu, zaznaczając pole w lewej kolumnie obok każdego użytkownika. Uwaga: W tej chwili nie ma możliwości wybrania wszystkich użytkowników do eksportu. Każdy z nich musi być dobierany indywidualnie.
3. W usłudze Azure AD wybierz pozycję **Użytkownicy** > **Pobierz użytkowników**.
4. Na stronie **Pobierz użytkowników** wybierz pozycję **Start,** aby otrzymać plik CSV zawierający polecenie zawierający polecenie właściwości profilu użytkownika. Jeśli występują błędy, można pobrać i wyświetlić plik wyników na stronie Wyniki operacji zbiorczej. Plik zawiera przyczynę każdego błędu.

   ![Wybierz miejsce, w którym mają zostać pobrane listy użytkowników, których chcesz pobrać](./media/users-bulk-download/bulk-download.png)

   Plik pobierania będzie zawierał filtrowane listy użytkowników.

   Uwzględniono następujące atrybuty użytkownika:

   - userPrincipalName
   - displayName
   - surname
   - mail (poczta)
   - givenName
   - Objectid
   - Usertype
   - jobTitle (Tytuł pracy)
   - department
   - kontoWłasny
   - usageLocation (korzystanieLokalizacja)
   - Streetaddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - Postalcode
   - Telephonenumber
   - telefon komórkowy
   - numer telefonu uwierzytelnianiaNumer
   - uwierzytelnianieAlternativePhoneNumber
   - uwierzytelnianieEmail
   - alternateEmailAddress
   - grupa_wiekowa
   - zgodaProvidedForMinor
   - legalAgeGroupKlasyfikacja

## <a name="check-status"></a>Sprawdzanie stanu

Stan oczekujących żądań zbiorczych można zobaczyć na stronie **Wyniki operacji zbiorczej.**

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity usług pobierania zbiorczego

Każde działanie zbiorcze w celu utworzenia listy użytkowników może działać przez maksymalnie jedną godzinę. Umożliwia to tworzenie i pobieranie listy co najmniej 500 000 użytkowników.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze dodawanie użytkowników](users-bulk-add.md)
- [Zbiorcze usuwanie użytkowników](users-bulk-delete.md)
- [Użytkownicy przywracania zbiorczego](users-bulk-restore.md)
