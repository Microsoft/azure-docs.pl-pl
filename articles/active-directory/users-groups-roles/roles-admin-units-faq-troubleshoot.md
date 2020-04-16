---
title: Rozwiązywanie problemów z jednostkami administracyjnymi i często zadawane pytania — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Badanie jednostek administracyjnych do delegowania uprawnień o ograniczonym zakresie w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428149"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Rozwiązywanie problemów i często zadawane pytania dotyczące jednostek administracyjnych w usłudze Azure Active Directory

Aby uzyskać bardziej szczegółową kontrolę administracyjną w usłudze Azure Active Directory (Azure AD), można przypisać użytkowników do roli usługi Azure AD o zakresie ograniczonym do co najmniej jednej jednostki administracyjnej (AU). Przykładowe skrypty programu PowerShell dla https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0typowych zadań można znaleźć pod adresem .

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P: Nie mogę utworzyć jednostki administracyjnej**

**Odp.:** Tylko administrator globalny lub administrator ról uprzywilejowanych może utworzyć jednostkę administracyjną w usłudze Azure AD. Sprawdź, czy użytkownikowi próbującemu utworzyć jednostkę administracyjną jest przypisana rola Administratora globalnego lub Administratora ról uprzywilejowanych.

**P: Dodałem grupę do jednostki administracyjnej, ale członkowie grupy nadal nie są wyświetlani w jednostce administracyjnej**

**Odp.:** Po dodaniu grupy do jednostki administracyjnej nie powoduje to dodania wszystkich członków grupy do jednostki administracyjnej. Użytkownicy muszą być bezpośrednio przypisani do jednostki administracyjnej.

**P: Właśnie dodałem / usunąłem członka jednostki administracyjnej i nadal jest wyświetlany w interfejsie użytkownika**

**Odp.:** Czasami przetworzenie dodania lub usunięcia jednego lub większej liczby członków jednostki administracyjnej może potrwać kilka minut, aby odbić się na stronie **Jednostki administracyjne.** Możesz poczekać kilka minut, aż odbite w ramach jednostek administracyjnych. Alternatywnie można przejść bezpośrednio do właściwości skojarzonego zasobu i sprawdzić, czy akcja została ukończona. Aby uzyskać więcej informacji o użytkownikach i grupach w jednostkach AU, zobacz [Lista jednostek administracyjnych dla użytkownika](roles-admin-units-add-manage-users.md) i Lista [jednostek administracyjnych dla grupy](roles-admin-units-add-manage-groups.md).

**P: Jako administrator haseł delegowanych w jednostce administracyjnej nie mogę zresetować hasła określonego użytkownika**

**Odp.:** Administrator przypisany przez jednostkę administracyjną można zresetować hasło tylko dla użytkowników przypisanych do jednostki administracyjnej. Upewnij się, że użytkownik, dla którego resetowanie hasła nie powiódł się, należy do jednostek administracyjnych, do których przypisano rolę. Jeśli użytkownik należy do tej samej jednostki administracyjnej i nadal nie można zresetować hasła użytkownika, sprawdź role, które są przypisane do użytkownika. Aby zapobiec podwyższeniu uprawnień, administrator jednostki administracyjnej o zakresie nie może zresetować hasła użytkownika przypisanego do roli z zakresem całej organizacji.

**P: Dlaczego jednostki administracyjne są niezbędne? Czy nie mogliśmy użyć grup zabezpieczeń jako sposobu definiowania zakresu?**

**Odp.:** Grupy zabezpieczeń mają istniejący model przeznaczenia i autoryzacji. Administrator użytkownika, na przykład, można zarządzać członkostwem wszystkich grup zabezpieczeń w organizacji usługi Azure AD, takich jak używanie grup do zarządzania dostępem do aplikacji, takich jak Salesforce. Administrator użytkownika nie powinien mieć możliwości zarządzania samym modelem delegowania, co byłoby wynikiem, gdyby grupy zabezpieczeń zostały rozszerzone na obsługę scenariuszy "grupowanie zasobów". Jednostki administracyjne, takie jak jednostki organizacyjne w usłudze Active Directory systemu Windows Server, mają na celu zapewnienie możliwości administrowania zakresem szerokiego zakresu obiektów katalogu. Same grupy zabezpieczeń mogą być członkami zakresów zasobów. Używanie grup zabezpieczeń do definiowania zestawu grup zabezpieczeń, którymi może zarządzać administrator, może stać się mylące.

**P: Co to znaczy dodać grupę do jednostki administracyjnej?**

**Odp.:** Dodanie grupy do jednostki administracyjnej powoduje, że sama grupa wchodzi w zakres zarządzania dowolnego administratora użytkownika, który jest również objęty zakresem tej jednostki administracyjnej. Administratorzy użytkowników jednostki administracyjnej mogą zarządzać nazwą i członkostwem samej grupy. Nie udziela administratorowi użytkownika dla jednostki administracyjnej żadnych uprawnień do zarządzania użytkownikami grupy (na przykład resetowania haseł). Aby zapewnić Administratorowi użytkownika możliwość zarządzania użytkownikami, użytkownicy muszą być bezpośrednimi członkami jednostki administracyjnej.

**Pyt.: Czy zasób (użytkownik lub grupa) może być członkiem więcej niż jednej jednostki administracyjnej?**

**Odp.:** Tak, zasób może być członkiem więcej niż jednej jednostki administracyjnej. Zasób może być zarządzany przez wszystkich administratorów w całej organizacji i administracyjnych, którzy mają uprawnienia do zasobu.

**P: Czy jednostki administracyjne są dostępne w organizacjach B2C?**

**Odp.:** Nie, jednostki administracyjne nie są dostępne dla organizacji B2C.

**Pyt.: Czy zagnieżdżone jednostki administracyjne są obsługiwane?**

**Odp.:** Zagnieżdżone jednostki administracyjne nie są obsługiwane.

**Pyt.: Czy jednostki administracyjne są obsługiwane w programie PowerShell i Graph API?**

**Odpowiedź:** tak. Obsługa jednostek administracyjnych istnieje w [dokumentacji poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) i [przykładowych skryptach,](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)a obsługa jest dostępna w programie Microsoft Graph dla [typu zasobu jednostki administracyjnej](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit).

## <a name="next-steps"></a>Następne kroki

- [Jednostki administracyjne ograniczające zakres dla przegląd ról](directory-administrative-units.md)
- [Zarządzanie jednostkami administracyjnymi](roles-admin-units-manage.md)