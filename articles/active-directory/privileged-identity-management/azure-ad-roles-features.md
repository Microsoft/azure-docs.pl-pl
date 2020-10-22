---
title: Funkcje roli usługi Azure AD w Privileged Identity Management | Microsoft Docs
description: Jak zarządzać rolami usługi Azure AD na potrzeby przypisywania Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4478c9c286c06d5d6c5593195a0e93abd286b8c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371515"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Możliwości zarządzania dla ról usługi Azure AD w Privileged Identity Management

Środowisko zarządzania dla ról usługi Azure AD w Privileged Identity Management zostało zaktualizowane w celu ujednolicenia sposobu zarządzania rolami usługi Azure AD i rolami zasobów platformy Azure. Wcześniej Privileged Identity Management dla ról zasobów platformy Azure miały kilka najważniejszych funkcji, które nie były dostępne dla ról usługi Azure AD.

Gdy aktualizacja jest obecnie wdrażana, scalamy te dwa usługi w jednym środowisku zarządzania, a w tym samym działaniu ról usługi Azure AD jako ról zasobów platformy Azure. Ten artykuł informuje o zaktualizowanych funkcjach i wymaganiach.

## <a name="time-bound-assignments"></a>Przypisania powiązane z czasem

Wcześniej były dostępne dwa stany przypisywania ról: *kwalifikujące się* i *trwałe*. Teraz można również ustawić godzinę początkową i końcową dla każdego typu przypisania. To dodanie powoduje cztery możliwe stany, w których można umieścić przypisanie:

- Kwalifikujące się trwale
- Aktywne trwale
- Kwalifikujące się, z określonymi datami rozpoczęcia i zakończenia przypisywania
- Aktywne, z określonymi datami rozpoczęcia i zakończenia przypisywania

W wielu przypadkach nawet jeśli nie chcesz, aby użytkownicy mieli uprawnienia do przypisywania i uaktywniać role za każdym razem, możesz nadal chronić swoją organizację usługi Azure AD, ustawiając czas wygaśnięcia dla przypisań. Jeśli na przykład masz pewnych użytkowników tymczasowych, należy rozważyć ustawienie wygaśnięcia, aby usunąć je automatycznie z przypisania roli po zakończeniu pracy.

## <a name="new-role-settings"></a>Nowe ustawienia roli

Dodawane są również nowe ustawienia dla ról usługi Azure AD.

- **Wcześniej**można skonfigurować tylko ustawienia aktywacji dla poszczególnych ról. Oznacza to, że ustawienia aktywacji, takie jak wymagania dotyczące uwierzytelniania wieloskładnikowego i zdarzenia/bilet żądania zostały zastosowane do wszystkich użytkowników uprawnionych do określonej roli.
- **Teraz**można skonfigurować, czy indywidualni użytkownicy muszą przeprowadzić uwierzytelnianie wieloskładnikowe, zanim będą mogli aktywować rolę. Ponadto możesz mieć zaawansowaną kontrolę nad Privileged Identity Managementymi wiadomościami e-mail związanymi z określonymi rolami.

## <a name="extend-and-renew-assignments"></a>Przedłużanie i odnawianie przypisań

Po rozwiązaniu przydziału związanego z czasem pierwsze pytanie, na które może się zdarzyć, ma wpływ na to, co się stanie, Jeśli rola wygasła? W tej nowej wersji udostępniamy dwie opcje w tym scenariuszu:

- **Rozszerzanie**: gdy przypisanie roli zbliża się do jego wygaśnięcia, użytkownik może użyć Privileged Identity Management, aby zażądać rozszerzenia dla tego przypisania roli
- **Odnów**: po wygaśnięciu przypisania roli użytkownik może użyć Privileged Identity Management, aby zażądać odnowienia dla tego przypisania roli

Obie akcje inicjowane przez użytkownika wymagają zatwierdzenia od administratora globalnego lub administratora roli uprzywilejowanej. Administratorzy nie będą już musieli zarządzać tymi wygasami. Po prostu muszą czekać na żądanie rozszerzenia lub odnowienia i zatwierdzić je w przypadku poprawnego żądania.

## <a name="api-changes"></a>Zmiany interfejsu API

Gdy klienci mają zaktualizowaną wersję w organizacji usługi Azure AD, istniejący interfejs API programu Graph przestanie działać. Musisz przejść do korzystania z [interfejs API programu Graph dla ról zasobów platformy Azure](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Aby zarządzać rolami usługi Azure AD przy użyciu tego interfejsu API, należy zamienić na `/azureResources` `/aadroles` w podpisie i użyć identyfikatora katalogu dla `resourceId` .

Wszyscy klienci, którzy korzystają z poprzedniego interfejsu API, chętnie poinformują nas o tej zmianie. Jeśli Twoja organizacja usługi Azure AD została przeniesiona do nowej wersji i nadal zależy od starego interfejsu API, skontaktuj się z zespołem pod adresem pim_preview@microsoft.com .

## <a name="powershell-change"></a>Zmiana programu PowerShell

W przypadku klientów, którzy korzystają z modułu Privileged Identity Management PowerShell dla ról usługi Azure AD, program PowerShell przestanie działać z aktualizacją. Zamiast poprzednich poleceń cmdlet należy użyć poleceń cmdlet Privileged Identity Management w module programu PowerShell usługi Azure AD w wersji zapoznawczej. Zainstaluj moduł Azure AD PowerShell z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Teraz możesz [zapoznać się z dokumentacją i przykładami dotyczącymi operacji PIM w tym module programu PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuń lub zaktualizuj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Skonfiguruj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-configure.md)
- [Definicje ról w usłudze Azure AD](../roles/permissions-reference.md)