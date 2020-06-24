---
title: Jak zarządzać administratorami lokalnymi na urządzeniach dołączonych do usługi Azure AD
description: Dowiedz się, jak przypisać role platformy Azure do lokalnej grupy administratorów na urządzeniu z systemem Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4285c7d8cd6c9feee84eaae3c49ffd1f21d5df27
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253090"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Jak zarządzać lokalną grupą administratorów na urządzeniach dołączonych do usługi Azure AD

Aby zarządzać urządzeniem z systemem Windows, musisz być członkiem lokalnej grupy administratorów. W ramach procesu dołączania Azure Active Directory (Azure AD) usługa Azure AD aktualizuje członkostwo tej grupy na urządzeniu. Aktualizację członkostwa można dostosować w celu spełnienia wymagań firmy. Aktualizacja członkostwa jest na przykład przydatna, jeśli chcesz umożliwić personelowi pomocy technicznej wykonywanie zadań wymagających uprawnień administratora na urządzeniu.

W tym artykule wyjaśniono, jak działa aktualizacja członkostwa oraz jak można ją dostosować podczas dołączania do usługi Azure AD. Zawartość tego artykułu nie ma zastosowania do **hybrydowego** sprzężenia usługi Azure AD.

## <a name="how-it-works"></a>Jak to działa

Po połączeniu urządzenia z systemem Windows z usługą Azure AD przy użyciu funkcji Azure AD Join usługa Azure AD dodaje następujące podmioty zabezpieczeń do lokalnej grupy administratorów na urządzeniu:

- Rola administratora globalnego usługi Azure AD
- Rola administratora urządzenia usługi Azure AD 
- Użytkownik wykonujący dołączanie do usługi Azure AD   

Dodając role usługi Azure AD do lokalnej grupy administratorów, można zaktualizować użytkowników, którzy mogą zarządzać urządzeniem w dowolnym momencie w usłudze Azure AD bez konieczności modyfikowania urządzeń. Obecnie nie można przypisywać grup do roli administratora.
Usługa Azure AD dodaje rolę administratora urządzenia usługi Azure AD do lokalnej grupy administratorów, aby obsługiwała zasadę najniższych uprawnień (PoLP). Oprócz administratorów globalnych można także umożliwić użytkownikom z przypisaną *tylko* rolą administratora urządzenia zarządzanie urządzeniem. 

## <a name="manage-the-global-administrators-role"></a>Zarządzanie rolą administratorów globalnych

Aby wyświetlić i zaktualizować członkostwo roli administratora globalnego, zobacz:

- [Wyświetl wszystkich członków roli administratora w Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Przypisywanie użytkownika do ról administratorów w usłudze Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Zarządzanie rolą administratora urządzenia 

W Azure Portal można zarządzać rolą administratora urządzenia na stronie **urządzenia** . Aby otworzyć stronę **urządzenia** :

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny.
1. Wyszukaj i wybierz pozycję *Azure Active Directory*.
1. W sekcji **Zarządzanie** kliknij pozycję **urządzenia**.
1. Na stronie **urządzenia** kliknij pozycję **Ustawienia urządzenia**.

Aby zmodyfikować rolę administratora urządzenia, należy skonfigurować **dodatkowych administratorów lokalnych na urządzeniach dołączonych do usługi Azure AD**.  

![Dodatkowi Administratorzy lokalni](./media/assign-local-admin/10.png)

>[!NOTE]
> Ta opcja wymaga dzierżawy Azure AD — wersja Premium. 

Administratorzy urządzeń są przypisani do wszystkich urządzeń przyłączonych do usługi Azure AD. Nie można określić zakresu administratorów urządzeń do określonego zestawu urządzeń. Aktualizacja roli administratora urządzenia nie musi mieć natychmiastowego wpływu na użytkowników, których dotyczy problem. Na urządzeniach, na których użytkownik jest już zalogowany, aktualizacja uprawnień odbywa się po *obu* poniższych akcjach:

- 4 godziny przekazały, że usługa Azure AD wystawia nowy podstawowy token odświeżania z odpowiednimi uprawnieniami. 
- Użytkownik wyloguje się i zaloguje się ponownie, nie blokując/Odblokuj, aby odświeżyć swój profil.

## <a name="manage-regular-users"></a>Zarządzanie regularnymi użytkownikami

Domyślnie usługa Azure AD dodaje użytkownika wykonującego usługę Azure AD Join do grupy administratorów na urządzeniu. Aby uniemożliwić regularnym użytkownikom administratorów lokalnych, dostępne są następujące opcje:

- [Windows autopilotaż](/windows/deployment/windows-autopilot/windows-10-autopilot) — system Windows autopilotaż zapewnia możliwość uniemożliwienia Użytkownikowi podstawowemu przełączenia się do administratora lokalnego. Możesz to zrobić, [tworząc profil autopilotażu](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Rejestracja Zbiorcza](/intune/windows-bulk-enroll) — dołączanie usługi Azure AD, które jest wykonywane w kontekście rejestracji zbiorczej, odbywa się w kontekście użytkownika, który został utworzony przez Ciebie. Użytkownicy logujący się po przyłączeniu urządzenia nie zostaną dodani do grupy administratorów.   

## <a name="manually-elevate-a-user-on-a-device"></a>Ręczne podnoszenie poziomu uprawnień użytkownika na urządzeniu 

Oprócz korzystania z procesu przyłączania do usługi Azure AD można także ręcznie podnieść poziom zwykłych użytkowników, aby stał się administratorem lokalnym na jednym określonym urządzeniu. Ten krok wymaga, aby użytkownik był już członkiem lokalnej grupy administratorów. 

Począwszy od wersji **Windows 10 1709** , można wykonać to zadanie na podstawie **ustawień konta > — > innych użytkowników**. Wybierz pozycję **Dodaj użytkownika służbowego**, wprowadź nazwę UPN użytkownika w obszarze **konto użytkownika** , a następnie wybierz pozycję *administrator* w obszarze **Typ konta** .  
 
Dodatkowo można również dodać użytkowników przy użyciu wiersza polecenia:

- Jeśli użytkownicy dzierżawy są synchronizowani z Active Directory lokalnych, użyj programu `net localgroup administrators /add "Contoso\username"` .
- Jeśli w usłudze Azure AD są tworzone użytkownicy dzierżawy, użyj`net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Zagadnienia do rozważenia 

Nie można przypisać grup do roli administratora urządzenia. dozwolone są tylko indywidualni użytkownicy.

Administratorzy urządzeń są przypisani do wszystkich urządzeń przyłączonych do usługi Azure AD. Nie mogą one być ograniczone do określonego zestawu urządzeń.

Usunięcie użytkowników z roli administratora urządzenia nadal ma uprawnienia administratora lokalnego na urządzeniu, o ile są z nim zalogowane. Uprawnienie jest odwoływane podczas następnego logowania lub po 4 godzinach, gdy zostanie wystawiony nowy podstawowy token odświeżania.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać omówienie sposobu zarządzania urządzeniami w witrynie Azure Portal, zobacz [zarządzanie urządzeniami przy użyciu witryny Azure Portal](device-management-azure-portal.md)
- Aby dowiedzieć się więcej o dostępie warunkowym opartym na urządzeniach, zobacz [Konfigurowanie zasad dostępu warunkowego opartego na urządzeniach Azure Active Directory](../conditional-access/require-managed-devices.md).
