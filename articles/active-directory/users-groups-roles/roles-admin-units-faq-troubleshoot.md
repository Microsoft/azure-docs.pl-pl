---
title: Rozwiązywanie problemów z jednostkami administracyjnymi i często zadawane pytania — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Zbadaj jednostki administracyjne, aby udzielić uprawnień o ograniczonym zakresie w usłudze Azure Active Directory.
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
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684853"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Jednostki administracyjne usługi Azure AD: rozwiązywanie problemów i często zadawane pytania

Aby uzyskać bardziej szczegółową kontrolę administracyjną w usłudze Azure Active Directory (Azure AD), można przypisać użytkowników do roli usługi Azure AD o zakresie ograniczonym do co najmniej jednej jednostki administracyjnej (AU). Aby zapoznać się z przykładowymi skryptami programu PowerShell dla typowych zadań, zobacz [Praca z jednostkami administracyjnymi](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P: Dlaczego nie mogę utworzyć jednostki administracyjnej?**

**Odp.:** Tylko *administrator globalny* lub *administrator ról uprzywilejowanych* może utworzyć jednostkę administracyjną w usłudze Azure AD. Sprawdź, czy użytkownikowi, który próbuje utworzyć jednostkę administracyjną, jest przypisana rola *Administratora globalnego* lub *Administratora ról uprzywilejowanych.*

**P: Dodałem grupę do jednostki administracyjnej. Dlaczego członkowie grupy nadal się tam nie pojawiają?**

**Odp.:** Po dodaniu grupy do jednostki administracyjnej nie powoduje to dodania do niej wszystkich członków grupy. Użytkownicy muszą być przypisani bezpośrednio do jednostki administracyjnej.

**P: Właśnie dodałem (lub usunąłem) członka jednostki administracyjnej. Dlaczego element członkowski nie jest wyświetlany (lub nadal się wyświetla) w interfejsie użytkownika?**

**Odp.:** Czasami przetworzenie dodania lub usunięcia jednego lub większej liczby członków jednostki administracyjnej może potrwać kilka minut, aby odzwierciedlić na stronie **Jednostki administracyjne.** Alternatywnie można przejść bezpośrednio do właściwości skojarzonego zasobu i sprawdzić, czy akcja została ukończona. Aby uzyskać więcej informacji o użytkownikach i grupach w jednostkach AU, zobacz [Lista jednostek administracyjnych dla użytkownika](roles-admin-units-add-manage-users.md) i Lista [jednostek administracyjnych dla grupy](roles-admin-units-add-manage-groups.md).

**P: Jestem administratorem haseł delegowanych w jednostce administracyjnej. Dlaczego nie mogę zresetować hasła określonego użytkownika?**

**Odp.:** Jako administrator jednostki administracyjnej można resetować hasła tylko dla użytkowników przypisanych do jednostki administracyjnej. Upewnij się, że użytkownik, którego resetowanie hasła nie powiódł się, należy do jednostki administracyjnej, do której został przypisany. Jeśli użytkownik należy do tej samej jednostki administracyjnej, ale nadal nie można zresetować hasła, sprawdź role, które są przypisane do użytkownika. 

Aby zapobiec podwyższeniu uprawnień, administrator jednostki administracyjnej nie może zresetować hasła użytkownika przypisanego do roli z zakresem całej organizacji.

**P: Dlaczego jednostki administracyjne są niezbędne? Czy nie mogliśmy użyć grup zabezpieczeń jako sposobu definiowania zakresu?**

**Odp.:** Grupy zabezpieczeń mają istniejący model przeznaczenia i autoryzacji. *Administrator użytkownika*, na przykład, można zarządzać członkostwem wszystkich grup zabezpieczeń w organizacji usługi Azure AD. Rola może używać grup do zarządzania dostępem do aplikacji, takich jak Salesforce. *Administrator użytkownika* nie powinien być w stanie zarządzać samym modelem delegowania, co byłoby wynikiem, gdyby grupy zabezpieczeń zostały rozszerzone na obsługę scenariuszy "grupowanie zasobów". Jednostki administracyjne, takie jak jednostki organizacyjne w usłudze Active Directory systemu Windows Server, mają na celu zapewnienie sposobu administrowania zakresem szerokiego zakresu obiektów katalogu. Same grupy zabezpieczeń mogą być członkami zakresów zasobów. Używanie grup zabezpieczeń do definiowania zestawu grup zabezpieczeń, którymi administrator może zarządzać, może stać się mylące.

**P: Co to znaczy dodać grupę do jednostki administracyjnej?**

**Odp.:** Dodanie grupy do jednostki administracyjnej powoduje, że sama grupa wchodzi w zakres zarządzania dowolnego *administratora użytkownika,* który jest również objęty zakresem tej jednostki administracyjnej. Administratorzy użytkowników jednostki administracyjnej mogą zarządzać nazwą i członkostwem samej grupy. Nie udziela *administratorowi użytkownika* uprawnień jednostki administracyjnej do zarządzania użytkownikami grupy (na przykład resetowania haseł). Aby zapewnić *Administratorowi użytkownika* możliwość zarządzania użytkownikami, użytkownicy muszą być bezpośrednimi członkami jednostki administracyjnej.

**Pyt.: Czy zasób (użytkownik lub grupa) może być członkiem więcej niż jednej jednostki administracyjnej?**

**Odp.:** Tak, zasób może być członkiem więcej niż jednej jednostki administracyjnej. Zasób może być zarządzany przez wszystkich administratorów w całej organizacji i administracyjnych o zakresie jednostki, którzy mają uprawnienia do zasobu.

**P: Czy jednostki administracyjne są dostępne w organizacjach B2C?**

**Odp.:** Nie, jednostki administracyjne nie są dostępne dla organizacji B2C.

**Pyt.: Czy zagnieżdżone jednostki administracyjne są obsługiwane?**

**Odp.:** Nie, zagnieżdżone jednostki administracyjne nie są obsługiwane.

**Pyt.: Czy jednostki administracyjne są obsługiwane w programie PowerShell i interfejsie API wykresu?**

**Odpowiedź:** tak. W [dokumentacji polecenia cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) i [przykładowych skryptach](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)znajdziesz obsługę jednostek administracyjnych. 

Znajdź obsługę [administracyjnegoujednostka typu zasobu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) w programie Microsoft Graph.

## <a name="next-steps"></a>Następne kroki

- [Ograniczanie zakresu ról przy użyciu jednostek administracyjnych](directory-administrative-units.md)
- [Zarządzanie jednostkami administracyjnymi](roles-admin-units-manage.md)
