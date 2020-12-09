---
title: Rozwiązywanie problemów z rolami przypisanymi do grupy chmury — często zadawane pytania — Azure Active Directory | Microsoft Docs
description: Poznaj typowe pytania i porady dotyczące rozwiązywania problemów dotyczących przypisywania ról do grup w Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8735a0d34b9fcf5b86b6592980ffc5c7c3e3073c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861939"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>Rozwiązywanie problemów z rolami przypisanymi do grup w chmurze

Poniżej przedstawiono kilka typowych pytań i porad dotyczących rozwiązywania problemów dotyczących przypisywania ról do grup w Azure Active Directory (Azure AD).

**P:** Jestem administratorem grup, ale nie widzę **ról usługi Azure AD, które można przypisać do przełącznika grupy** .

Odp **.:** Tylko Administratorzy ról uprzywilejowanych lub Administratorzy globalni mogą tworzyć grupy uprawnione do przypisywania ról. Ta kontrolka jest widoczna tylko dla użytkowników z tych ról.

**P:** Kto może modyfikować członkostwo grup przypisanych do ról usługi Azure AD?

Odp **.:** Domyślnie tylko administrator ról uprzywilejowanych i Administrator globalny zarządzają członkostwem grupy przypisanej do roli, ale można delegować zarządzanie grupami przypisanymi do ról, dodając właścicieli grup.

**P**: jestem administratorem pomocy technicznej w mojej organizacji, ale nie mogę zaktualizować hasła użytkownika, który jest czytnikiem katalogów. Dlaczego tak się dzieje?

Odp.: użytkownik może uzyskać **dostęp do czytnika** katalogów przez grupę z możliwością przypisania. Wszyscy członkowie i właściciele grup, do których można przypisać role, są chronieni. Tylko użytkownicy z rolą Administrator uwierzytelniania uprzywilejowanego lub Administrator globalny mogą resetować poświadczenia dla chronionego użytkownika.

**P:** Nie mogę zaktualizować hasła użytkownika. Nie mają przypisanej żadnej wyższej roli uprzywilejowanej. Dlaczego tak się dzieje?

Odp **.:** Użytkownik może być właścicielem grupy przypisanej do roli. Chronimy właścicieli grup, do których można przypisać role, aby uniknąć podniesienia uprawnień. Przykładem może być sytuacja, w której Grupa Contoso_Security_Admins jest przypisana do roli administratora zabezpieczeń, gdzie Robert jest właścicielem grupy, a Alicja jest administratorem haseł w organizacji. Jeśli ta ochrona nie istnieje, Alicja może zresetować poświadczenia Roberta i przejąć swoją tożsamość. Następnie Alicja może dodać do grupy Contoso_Security_Admins grupę, aby stać się administratorem zabezpieczeń w organizacji. Aby dowiedzieć się, czy użytkownik jest właścicielem grupy, Pobierz listę obiektów należących do tego użytkownika i sprawdź, czy dowolna z tych grup ma isAssignableToRole ustawioną wartość true. Jeśli tak, ten użytkownik jest chroniony i zachowanie jest zaprojektowane. Zapoznaj się z tymi dokumentami w celu uzyskania własnych obiektów:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject)  
- [OwnedObjects listy](/graph/api/user-list-ownedobjects?tabs=http)

**P:** Czy można utworzyć przegląd dostępu dla grup, które mogą być przypisane do ról usługi Azure AD (w odniesieniu do grup, dla których właściwość isAssignableToRole ma wartość true)?  

Odp **.:** Tak. Jeśli korzystasz z najnowszej wersji przeglądu dostępu, recenzenci są domyślnie kierowani do mojego dostępu i tylko Administratorzy globalni mogą tworzyć przeglądy dostępu w grupach z możliwością przypisania ról. Jeśli jednak korzystasz ze starszej wersji przeglądu dostępu, recenzenci są domyślnie kierowani do panelu dostępu, a zarówno Administratorzy globalni, jak i administrator użytkowników mogą tworzyć przeglądy dostępu w grupach, które można przypisać do ról. Nowe środowisko zostanie udostępnione wszystkim klientom 28 lipca 2020, ale jeśli chcesz uaktualnić wcześniej, Prześlij żądanie do [przeglądu dostępu do usługi Azure AD — zaktualizowane środowisko recenzenta w ramach rejestracji dostępu](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

**P:** Czy mogę utworzyć pakiet dostępu i umieścić grupy, które można przypisać do ról usługi Azure AD?

Odp **.:** Tak. Administrator globalny i administrator użytkownika mogą wprowadzić dowolną grupę w pakiecie dostępu. Brak zmian dla administratora globalnego, ale istnieje niewielka zmiana uprawnień roli administratora użytkowników. Aby umieścić grupę z możliwością przypisania do pakietu dostępu, musisz być administratorem użytkownika, a także właścicielem grupy przypisanej do roli. Oto pełna tabela przedstawiająca, kto może utworzyć pakiet dostępu w usłudze zarządzania licencjami przedsiębiorstwa:

Rola katalogu usługi Azure AD | Rola zarządzania uprawnieniami | Może dodać grupę zabezpieczeń\* | Może dodać grupę Microsoft 365\* | Można dodać aplikację | Może dodać witrynę usługi SharePoint Online
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Administrator globalny | n/d | ✔️ | ✔️ | ✔️  | ✔️
Administrator użytkowników  | n/d  | ✔️  | ✔️  | ✔️
Administrator usługi Intune | Właściciel katalogu | ✔️  | ✔️  | &nbsp;  | &nbsp;
Administrator programu Exchange  | Właściciel katalogu  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Administrator usługi Teams | Właściciel katalogu  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Administrator programu SharePoint | Właściciel katalogu | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Administrator aplikacji | Właściciel katalogu  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Administrator aplikacji w chmurze | Właściciel katalogu  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Użytkownik | Właściciel katalogu | Tylko wtedy, gdy właściciel grupy | Tylko wtedy, gdy właściciel grupy | Tylko wtedy, gdy właściciel aplikacji  | &nbsp;

\*Grupa nie jest przypisana do roli; oznacza to, że isAssignableToRole = false. Jeśli grupa jest przypisana do roli, osoba tworząca pakiet dostępu musi być również właścicielem grupy, do której można przypisać role.

**P:** Nie mogę znaleźć opcji "Usuń przypisanie" w "przypisanych rolach". Jak mogę usunąć przypisanie roli do użytkownika?

Odp **.:** Ta odpowiedź dotyczy tylko organizacji Azure AD — wersja Premium P1.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Otwórz **Azure Active Directory**.
1. Wybierz pozycję Użytkownicy i Otwórz profil użytkownika.
1. Wybierz **przypisane role**.
1. Wybierz ikonę koła zębatego. Zostanie otwarte okienko, które może dać te informacje. Istnieje przycisk "Usuń" obok przypisań bezpośrednich. Aby usunąć przypisanie roli pośredniej, Usuń użytkownika z grupy, do której przypisano rolę.

**P:** Jak mogę wyświetlić wszystkie grupy, które są przypisane do roli?

Odp **.:** Wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Otwórz **Azure Active Directory**.
1. Wybierz kolejno pozycje **grupy**  >  **wszystkie grupy**.
1. Wybierz pozycję **Dodaj filtry**.
1. Filtrowanie do **roli do przypisania**.

**P:** Jak mogę wiedzieć, która rola jest bezpośrednio i pośrednio przypisana do podmiotu zabezpieczeń?

Odp **.:** Wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i Otwórz **Azure Active Directory**.
1. Wybierz pozycję Użytkownicy i Otwórz profil użytkownika.
1. Wybierz **przypisane role**, a następnie:

    - W Azure AD — wersja Premium organizacje licencjonowane P1: wybierz ikonę koła zębatego. Zostanie otwarte okienko, które może dać te informacje.
    - W Azure AD — wersja Premium w organizacji z licencją P2: w kolumnie **członkostwo** znajdziesz bezpośrednie i odziedziczone informacje licencyjne.

**P:** Dlaczego należy wymusić utworzenie nowej grupy w chmurze w celu przypisania jej do roli?  

Odp **.:** Jeśli przypiszesz istniejącą grupę do roli, istniejący Właściciel grupy może dodać innych członków do tej grupy bez nowych członków, którzy będą mieć rolę. Ze względu na to, że grupy przypisane do ról są zaawansowane, wprowadzamy wiele ograniczeń w celu ich ochrony. Nie potrzebujesz zmian w grupie, które byłyby zaskakujące do osoby zarządzającej grupą.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie przypisaniami ról za pomocą grup w chmurze](groups-concept.md)
- [Tworzenie grupy z możliwością przypisania do roli](groups-create-eligible.md)
