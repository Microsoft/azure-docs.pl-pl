---
title: Jednostki administracyjne Rozwiązywanie problemów i często zadawane pytania — Azure Active Directory | Microsoft Docs
description: Zbadaj jednostki administracyjne, aby przyznać uprawnienia z ograniczonym zakresem w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f9af8ee3bf87cfd5d2e74adfce618c7cf8c7e63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450325"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Jednostki administracyjne usługi Azure AD: Rozwiązywanie problemów i często zadawane pytania

Aby uzyskać bardziej szczegółową kontrolę administracyjną w usłudze Azure Active Directory (Azure AD), można przypisać użytkowników do roli usługi Azure AD z zakresem ograniczonym do co najmniej jednej jednostki administracyjnej (np.). Aby zapoznać się z przykładowymi skryptami programu PowerShell dla typowych zadań, zobacz [pracy z jednostkami administracyjnymi](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P: Dlaczego nie mogę utworzyć jednostki administracyjnej?**

Odp **.:** Tylko *administrator globalny* lub *administrator ról uprzywilejowanych* może utworzyć jednostkę administracyjną w usłudze Azure AD. Upewnij się, że użytkownik, który próbuje utworzyć jednostkę administracyjną, ma przypisaną rolę Administrator *globalny* lub *administrator ról uprzywilejowanych* .

**P: dodano grupę do jednostki administracyjnej. Dlaczego członkowie grupy wciąż nie są wyświetlani?**

Odp **.:** Po dodaniu grupy do jednostki administracyjnej, która nie powoduje dodania do niej wszystkich członków grupy. Użytkownicy muszą być bezpośrednio przypisani do jednostki administracyjnej.

**P: po prostu dodaliśmy (lub usunąć) członka jednostki administracyjnej. Dlaczego element członkowski nie jest wyświetlany (lub nadal wyświetlany) w interfejsie użytkownika?**

Odp **.:** Czasami przetwarzanie lub usunięcie co najmniej jednego członka jednostki administracyjnej może potrwać kilka minut na stronie **jednostki administracyjne** . Alternatywnie możesz przejść bezpośrednio do właściwości skojarzonego zasobu i sprawdzić, czy akcja została ukończona. Aby uzyskać więcej informacji na temat użytkowników i grup w programie, zobacz [listę jednostek administracyjnych dla użytkownika](roles-admin-units-add-manage-users.md) i [listę jednostek administracyjnych dla grupy](roles-admin-units-add-manage-groups.md).

**P: jestem delegowanym administratorem haseł w jednostce administracyjnej. Dlaczego nie mogę zresetować hasła określonego użytkownika?**

Odp **.:** Jako administrator jednostki administracyjnej można resetować hasła tylko dla użytkowników, którzy są przypisani do jednostki administracyjnej. Upewnij się, że użytkownik, którego hasło zostało zresetowane, należy do jednostki administracyjnej, do której została przypisana. Jeśli użytkownik należy do tej samej jednostki administracyjnej, ale nadal nie może zresetować hasła, należy sprawdzić role przypisane do użytkownika. 

Aby zapobiec podwyższeniu poziomu uprawnień, administrator z zakresem jednostek administracyjnych nie może zresetować hasła użytkownika, który jest przypisany do roli z zakresem całej organizacji.

**P: Dlaczego są wymagane jednostki administracyjne? Nie można używać grup zabezpieczeń jako sposobu definiowania zakresu?**

Odp **.:** Grupy zabezpieczeń mają istniejący cel i model autoryzacji. *Administrator użytkowników*może na przykład zarządzać członkostwem wszystkich grup zabezpieczeń w organizacji usługi Azure AD. Rola może używać grup do zarządzania dostępem do aplikacji, takich jak Salesforce. *Administrator użytkownika* nie powinien być w stanie zarządzać samym modelem delegowania, co miałoby wynik, jeśli grupy zabezpieczeń zostały rozszerzone w celu obsługi scenariuszy "grupowanie zasobów". Jednostki administracyjne, takie jak jednostki organizacyjne w systemie Windows Server Active Directory, mają służyć do określania zakresu administrowania szeroką gamę obiektów katalogu. Grupy zabezpieczeń mogą być członkami zakresów zasobów. Używanie grup zabezpieczeń do definiowania zestawu grup zabezpieczeń, którymi administrator może zarządzać, może stać się mylący.

**P: co oznacza dodanie grupy do jednostki administracyjnej?**

Odp **.:** Dodanie grupy do jednostki administracyjnej powoduje przeniesienie grupy do zakresu zarządzania dowolnego *administratora użytkownika* , który również należy do zakresu tej jednostki administracyjnej. Administratorzy użytkowników dla jednostki administracyjnej mogą zarządzać nazwą i członkostwem samej grupy. Nie przyznaje *administratorowi* uprawnień do zarządzania użytkownikami grupy (na przykład w celu resetowania haseł). Aby umożliwić *administratorowi użytkownika* zarządzanie użytkownikami, użytkownicy muszą być bezpośrednimi członkami jednostki administracyjnej.

**P: czy zasób (użytkownik lub Grupa) należy do więcej niż jednej jednostki administracyjnej?**

Odp **.:** Tak, zasób może być członkiem więcej niż jednej jednostki administracyjnej. Zasobem może zarządzać wszyscy administratorzy w zakresie całej organizacji i administracyjnej, którzy mają uprawnienia do zasobów.

**P: czy jednostki administracyjne są dostępne w organizacjach B2C?**

Odp **.:** Nie, jednostki administracyjne nie są dostępne dla organizacji B2C.

**P: czy zagnieżdżone jednostki administracyjne są obsługiwane?**

Odp **.:** Nie, zagnieżdżone jednostki administracyjne nie są obsługiwane.

**P: czy jednostki administracyjne są obsługiwane w programie PowerShell i interfejs API programu Graph?**

**Odpowiedź:** tak. W [dokumentacji poleceń cmdlet programu PowerShell](/powershell/module/Azuread/?view=azureadps-2.0&preserve-view=true) i [przykładowych skryptach](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true)znajdziesz obsługę jednostek administracyjnych.

Znajdź obsługę [typu zasobu administrativeUnit](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true) w Microsoft Graph.

## <a name="next-steps"></a>Następne kroki

- [Ograniczanie zakresu dla ról przy użyciu jednostek administracyjnych](directory-administrative-units.md)
- [Zarządzanie jednostkami administracyjnymi](roles-admin-units-manage.md)