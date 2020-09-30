---
title: Używanie grup w chmurze do zarządzania przypisaniami ról w Azure Active Directory | Microsoft Docs
description: Wyświetl podgląd niestandardowych ról usługi Azure AD na potrzeby delegowania zarządzania tożsamościami. Zarządzanie przypisaniami ról platformy Azure w Azure Portal, PowerShell lub interfejs API programu Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/11/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6c39fd6ceb9e5623d2b857ce8196ef29e73d78a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542312"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Zarządzanie przypisaniami ról w programie Azure Active Directory przy użyciu grup chmurowych (wersja zapoznawcza)

Azure Active Directory (Azure AD) wprowadza publiczną wersję zapoznawczą, w której można przypisać grupę chmurową do wbudowanych ról usługi Azure AD. Za pomocą tej funkcji można używać grup do udzielania dostępu administratora w usłudze Azure AD z minimalnym nakładem pracy od administratorów roli globalnego i uprzywilejowanego.

Rozważmy ten przykład: contoso zatrudniał osoby w lokalizacje geograficzneie, aby zarządzać hasłami i resetować je dla pracowników w swojej organizacji usługi Azure AD. Zamiast pytania administratora roli uprzywilejowanej lub administratora globalnego o przypisanie roli administratora pomocy technicznej do poszczególnych osób, można utworzyć grupę Contoso_Helpdesk_Administrators i przypisać ją do roli. Osoby, które dołączają do grupy, są przypisane do roli pośrednio. Istniejący przepływ pracy ładu może następnie przejąć proces zatwierdzania i inspekcję członkostwa w grupie, aby upewnić się, że tylko legalni użytkownicy są członkami grupy i są przypisane do roli administratora pomocy technicznej.

## <a name="how-this-feature-works"></a>Jak działa ta funkcja

Utwórz nową Microsoft 365 lub grupę zabezpieczeń z właściwością "isAssignableToRole" ustawioną na wartość "true". Możesz również włączyć tę właściwość podczas tworzenia grupy w Azure Portal przez włączenie **ról usługi Azure AD można przypisać do grupy**. W obu przypadkach można przypisać grupę do co najmniej jednej roli usługi Azure AD w taki sam sposób, jak w przypadku przypisywania ról do użytkowników. Maksymalnie 200 grup można tworzyć w jednej organizacji usługi Azure AD (dzierżawie).

Jeśli nie chcesz, aby członkowie grupy mieli stały dostęp do roli, możesz użyć Azure AD Privileged Identity Management. Przypisz grupę jako uprawniony członek roli usługi Azure AD. Każdy członek grupy jest następnie uprawniony do przypisywania do roli, do której przypisano grupę. Mogą oni następnie aktywować ich przypisanie roli przez ustalony czas trwania.

> [!Note]
> Aby można było przypisać grupę do roli usługi Azure AD za pośrednictwem programu PIM, należy mieć zaktualizowaną wersję programu Privileged Identity Management. Możesz korzystać ze starszej wersji usługi PIM, ponieważ organizacja usługi Azure AD korzysta z interfejsu API Privileged Identity Management. Skontaktuj się z aliasem, pim_preview@microsoft.com Aby przenieść swoją organizację i zaktualizować interfejs API. Dowiedz się więcej na temat [ról i funkcji usługi Azure AD w programie PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Dlaczego wymuszamy tworzenie specjalnej grupy do przypisywania jej do roli

Jeśli do grupy przypisano rolę, wszyscy administratorzy IT, którzy mogą zarządzać członkostwem w grupie, mogą również pośrednio zarządzać członkostwem w tej roli. Załóżmy na przykład, że grupa Contoso_User_Administrators jest przypisana do roli administratora konta użytkownika. Administrator programu Exchange, który może zmodyfikować członkostwo w grupie, może dodać siebie do grupy Contoso_User_Administrators i w ten sposób stać się administratorem konta użytkownika. Jak widać, administrator może podnieść poziom uprawnień w taki sposób, w jaki nie było to planowane.

Usługa Azure AD umożliwia ochronę grupy przypisanej do roli przy użyciu nowej właściwości o nazwie isAssignableToRole dla grup. Tylko grupy w chmurze, dla których właściwość isAssignableToRole ma ustawioną wartość "true" w czasie tworzenia, można przypisać do roli. Ta właściwość jest niezmienna; gdy grupa zostanie utworzona z tą właściwością ustawioną na wartość "true", nie można jej zmienić. Nie można ustawić właściwości dla istniejącej grupy.
Zaprojektowano sposób, w jaki grupy są przypisane do ról, aby zapobiec występowaniu potencjalnego naruszenia:

- Tylko Administratorzy globalni i Administratorzy ról uprzywilejowanych mogą utworzyć grupę z możliwością przypisania (z włączoną właściwością "isAssignableToRole").
- Nie może być grupą dynamiczną usługi Azure AD; oznacza to, że musi mieć typ członkostwa "przypisano". Automatyczne wypełnianie grup dynamicznych może prowadzić do dodania niechcianego konta do grupy i w ten sposób przypisane do roli.
- Domyślnie tylko Administratorzy globalni i Administratorzy ról uprzywilejowanych mogą zarządzać członkostwem grupy przypisanej do roli, ale można delegować zarządzanie grupami przypisanymi do ról, dodając właścicieli grup.
- Aby zapobiec podwyższeniu poziomu uprawnień, poświadczenia członków i właścicieli grupy, do których można przypisać rolę, mogą zostać zmienione tylko przez administratora uwierzytelniania uprzywilejowanego lub administratora globalnego.
- Brak zagnieżdżania. Nie można dodać grupy jako elementu członkowskiego grupy z możliwością przypisania.

## <a name="limitations"></a>Ograniczenia

Następujące scenariusze nie są obecnie obsługiwane:  

- Przypisywanie grup chmur do ról niestandardowych usługi Azure AD
- Przypisywanie grup chmur do ról usługi Azure AD (wbudowanych lub niestandardowych) za pośrednictwem jednostki administracyjnej lub zakresu aplikacji.
- Przypisywanie grup lokalnych do ról usługi Azure AD (wbudowanych lub niestandardowych)

## <a name="known-issues"></a>Znane problemy

- **Włączenie wdrażania etapowego dla funkcji logowania użytkownika zarządzanego** nie obsługuje przypisywania przez grupę.
- *Tylko licencjonowani klienci usługi Azure AD P2*: Nie przypisuj grupy jako aktywnej do roli za pomocą usługi Azure AD i PRIVILEGED Identity Management (PIM). W przeciwnym razie nie przypisuj roli do grupy możliwej do przypisania, gdy zostanie ona utworzona, *i* przypiszesz rolę do grupy przy użyciu usługi PIM później. Dzięki temu użytkownicy nie mogą zobaczyć aktywnych przypisań ról w programie PIM, jak również uniemożliwić usunięcie tego przypisania programu PIM. W tym scenariuszu nie ma żadnych zmian w kwalifikujących się przypisaniach. Jeśli podjęto próbę wykonania tego przypisania, może wystąpić nieoczekiwane zachowanie, takie jak:
  - Godzina zakończenia przypisywania roli może być błędnie wyświetlana.
  - W portalu PIM **role** mogą wyświetlać tylko jedno przypisanie roli bez względu na liczbę metod, do których przypisano przypisanie (za pomocą jednej lub kilku grup i bezpośrednio).
- *Tylko licencjonowani klienci usługi Azure AD P2* Nawet po usunięciu grupy nadal jest widoczny uprawniony członek roli w interfejsie użytkownika PIM. Funkcjonalnie nie ma problemu; jest to tylko problem z pamięcią podręczną w Azure Portal.  
- Użyj nowego [Centrum administracyjnego programu Exchange](https://admin.exchange.microsoft.com/) do przypisywania ról za pośrednictwem członkostwa w grupie. Stare centrum administracyjne programu Exchange nie obsługuje jeszcze tej funkcji. Polecenia cmdlet programu Exchange PowerShell będą działały zgodnie z oczekiwaniami.
- Portal Azure Information Protection (Portal klasyczny) nie rozpoznaje jeszcze członkostwa w roli za pośrednictwem grupy. Można [przeprowadzić migrację na platformę o ujednoliconej czułości](/azure/information-protection/configure-policy-migrate-labels) , a następnie użyć Centrum zgodności z pakietem Office 365 Security &, aby użyć przypisań grup do zarządzania rolami.

Naprawiamy te problemy.

## <a name="required-license-plan"></a>Wymagany plan licencji

Korzystanie z tej funkcji wymaga posiadania dostępnej licencji Azure AD — wersja Premium P1 w organizacji usługi Azure AD. Aby można było użyć również Privileged Identity Management aktywacji roli just-in-Time wymaga, aby masz dostępną licencję Azure AD — wersja Premium P2. Aby znaleźć odpowiednią licencję dla wymagań, zobacz [porównanie ogólnie dostępnych funkcji planów bezpłatnych i Premium](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie grupy z możliwością przypisania do roli](roles-groups-create-eligible.md)
- [Przypisywanie roli do grupy przypisanej do roli](roles-groups-assign-role.md)
