---
title: Jak aplikacje są wyświetlane w panelu dostępu | Microsoft Docs
description: Rozwiązywanie problemów z wyświetlaniem aplikacji w panelu dostępu
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa8ea75cc7fda05326c802c25a91d025b66b5ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "65784412"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Jak aplikacje są wyświetlane w panelu dostępu

Panel dostępu jest portalem opartym na sieci Web, który umożliwia użytkownikowi z kontem służbowym w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił im dostępu. Te aplikacje są konfigurowane w imieniu użytkownika w portalu usługi Azure AD. Administrator może udostępnić aplikację bezpośrednio użytkownikowi lub grupie, która jest częścią aplikacji, która jest wyświetlana w panelu dostępu użytkownika.

## <a name="general-issues-to-check-first"></a>Ogólne problemy do sprawdzenia w pierwszej kolejności

-   Jeśli aplikacja została usunięta z użytkownika lub grupy, do której należy użytkownik, spróbuj zalogować się i ponownie w panelu dostępu użytkownika po kilku minutach, aby sprawdzić, czy aplikacja zostanie usunięta.

-   Jeśli licencja została usunięta z użytkownika lub grupy, użytkownik jest członkiem tego programu może zająć dużo czasu, w zależności od rozmiaru i złożoności grupy w celu wprowadzenia zmian. Zezwalaj na dodatkowy czas przed zalogowaniem się do panelu dostępu.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemy związane z przypisywaniem aplikacji do użytkowników

Użytkownik może wyświetlić aplikację w panelu dostępu, ponieważ została wcześniej przypisana do niej. Poniżej przedstawiono kilka sposobów sprawdzenia:

-   [Sprawdź, czy użytkownik jest przypisany do aplikacji](#check-if-a-user-is-assigned-to-the-application)

-   [Sprawdź, czy użytkownik jest objęty licencją powiązaną z aplikacją](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Sprawdź, czy użytkownik jest przypisany do aplikacji

Aby sprawdzić, czy użytkownik jest przypisany do aplikacji, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

6. **Wyszukaj** nazwę aplikacji.

7. Kliknij pozycję **Użytkownicy i grupy**.

8. Sprawdź, czy użytkownik jest przypisany do aplikacji.

   * Jeśli chcesz usunąć użytkownika z aplikacji, **kliknij wiersz** użytkownika i wybierz pozycję **Usuń**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Sprawdź, czy użytkownik jest objęty licencją powiązaną z aplikacją

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5. Kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7. Kliknij pozycję **licencje** , aby zobaczyć, które licencje aktualnie przypisane do użytkownika.

   * Jeśli użytkownik jest przypisany do licencji pakietu Office, spowoduje to, że aplikacje pakietu Office w pierwszej kolejności będą wyświetlane w panelu dostępu użytkownika.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemy związane z przypisywaniem aplikacji do grup

Użytkownik może wyświetlić aplikację w panelu dostępu, ponieważ są one częścią grupy, do której przypisano aplikację. Poniżej przedstawiono kilka sposobów sprawdzenia:

-   [Sprawdź członkostwo w grupach użytkowników](#check-a-users-group-memberships)

-   [Sprawdź, czy użytkownik jest członkiem grupy przypisanej do licencji](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Sprawdź członkostwo w grupach użytkowników

Aby sprawdzić członkostwo grupy, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5. Kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7. Kliknij pozycję **grupy.**

8. Sprawdź, czy użytkownik jest częścią grupy przypisanej do aplikacji.

   * Jeśli chcesz usunąć użytkownika z grupy, **kliknij wiersz** grupy i wybierz pozycję Usuń.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Sprawdź, czy użytkownik jest członkiem grupy przypisanej do licencji

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5. Kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7. Kliknij pozycję **grupy.**

8. Kliknij wiersz konkretnej grupy.

9. Kliknij pozycję **licencje** , aby sprawdzić, które licencje zostały przypisane do grupy.

   * Jeśli grupa jest przypisana do licencji pakietu Office, może to spowodować, że niektóre aplikacje pakietu Office w pierwszej kolejności będą widoczne w panelu dostępu użytkownika.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiązują problemu

Otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   Nazwa UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i czas/przedział czasu w trakcie wystąpienia błędu

-   Ślady programu Fiddler

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą Azure Active Directory](what-is-application-management.md)
