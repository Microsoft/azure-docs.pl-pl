---
title: Delegowanie zarządzania użytkownikami (wersja zapoznawcza) za pomocą moich pracowników — usługa Azure AD | Dokumenty firmy Microsoft
description: Delegowanie zarządzania użytkownikami przy użyciu funkcji Mój personel i jednostek administracyjnych
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7c12612dbe37de6b08cb05a64af460296ade93
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394217"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Zarządzanie użytkownikami za pomocą mojego personelu (wersja zapoznawcza)

Mój personel umożliwia delegowanie do postaci urzędu, takich jak kierownik sklepu lub kierownik zespołu, uprawnienia, aby upewnić się, że ich pracownicy mają dostęp do swoich kont usługi Azure AD. Zamiast polegać na centralnym centrum pomocy technicznej, organizacje mogą delegować typowe zadania, takie jak resetowanie haseł lub zmienianie numerów telefonów do menedżera zespołu. Dzięki umiejszyemu użytkownik, który nie może uzyskać dostępu do swojego konta, może odzyskać dostęp za pomocą zaledwie kilku kliknięć, bez potrzeby obsługi technicznej ani personelu IT.

Przed skonfigurowaniem programu Mój personel dla organizacji zaleca się zapoznanie się z niniejszą dokumentacją oraz [dokumentacją użytkownika,](../user-help/my-staff-team-manager.md) aby upewnić się, że rozumiesz funkcjonalność i wpływ tej funkcji na użytkowników. Można wykorzystać dokumentację użytkownika, aby szkolić i przygotować użytkowników do nowego środowiska i pomóc w zapewnieniu pomyślnego wdrożenia.

## <a name="how-my-staff-works"></a>Jak działa mój personel

Mój personel opiera się na jednostkach administracyjnych (AU), które są kontenerem zasobów, które mogą być używane do ograniczania zakresu kontroli administracyjnej przypisania roli. W udziale mój personel jednostki organizacyjne są używane do definiowania podzbioru użytkowników organizacji, takich jak sklep lub dział. Następnie, na przykład, menedżer zespołu może być przypisany do roli, której zakres jest jeden lub więcej AUs. W poniższym przykładzie użytkownikowi przyznano rolę administracyjne uwierzytelniania, a trzy au są zakresem roli. Aby uzyskać więcej informacji na temat jednostek administracyjnych, zobacz [Zarządzanie jednostkami administracyjnymi w usłudze Azure Active Directory](directory-administrative-units.md).

## <a name="how-to-enable-my-staff"></a>Jak włączyć mój personel

Po skonfigurowaniu au, można zastosować ten zakres do użytkowników, którzy uzyskują dostęp do mojego personelu. Tylko użytkownicy, którym przypisano rolę administracyjną, mogą uzyskać dostęp do mojego personelu. Aby włączyć mój personel, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal jako administrator użytkownika.
2. Przejdź do ustawień **użytkownika usługi Azure Active Directory** > **Podgląd** > funkcji użytkowników Zarządzaj**ustawieniami** > **podglądu funkcji użytkownika**.
3. W obszarze **Administratorzy mogą uzyskiwać dostęp do moich pracowników**, można włączyć dla wszystkich użytkowników, wybranych użytkowników lub nie ma dostępu do użytkownika.

> [!Note]
> Tylko użytkownicy, którym przypisano rolę administratora, mogą uzyskać dostęp do moich pracowników. Jeśli włączysz mój personel dla użytkownika, któremu nie przypisano roli administratora, nie będzie on mógł uzyskać dostępu do moich pracowników.

## <a name="using-my-staff"></a>Korzystanie z mojego personelu

Gdy użytkownik przejdzie do strony Mój personel, zostaną wyświetlone nazwy [jednostek administracyjnych,](directory-administrative-units.md) nad którymi ma uprawnienia administracyjne. W [dokumentacji użytkownika Mój personel](../user-help/my-staff-team-manager.md)używamy terminu "lokalizacja" w odniesieniu do jednostek administracyjnych. Jeśli uprawnienia administratora nie mają zakresu UA, uprawnienia mają zastosowanie w całej organizacji. Po włączeniu funkcji Mój personel użytkownicy, którym przypisano rolę administracyjną, [https://mystaff.microsoft.com](https://mystaff.microsoft.com)mogą uzyskać do niej dostęp za pośrednictwem programu . Mogą wybrać aU, aby wyświetlić użytkowników w tej UA i wybrać użytkownika, aby otworzyć swój profil.

## <a name="licenses"></a>Licencje

Każdy użytkownik, który jest włączony w moim personelu musi mieć licencję, nawet jeśli nie korzystają z portalu Mój personel. Każdy włączony użytkownik musi mieć jedną z następujących licencji usługi Azure AD lub Microsoft 365:

- Usługa Azure AD Premium P1 lub P2
- Microsoft 365 F1 lub F3

## <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Następujące role mają uprawnienia do resetowania hasła użytkownika:

- [Administrator uwierzytelniania](directory-assign-admin-roles.md#authentication-administrator)
- [Administrator uwierzytelniania uprzywilejowanego](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrator globalny](directory-assign-admin-roles.md#global-administrator--company-administrator)
- [Administrator działu pomocy technicznej](directory-assign-admin-roles.md#helpdesk-administrator)
- [Administrator użytkownika](directory-assign-admin-roles.md#user-administrator)
- [Administrator haseł](directory-assign-admin-roles.md#password-administrator)

Otwórz profil użytkownika z **poziomu moich pracowników.** Wybierz **pozycję Resetuj hasło**.

- Jeśli użytkownik jest tylko w chmurze, można zobaczyć tymczasowe hasło, które można przekazać użytkownikowi.
- Jeśli użytkownik jest synchronizowany z lokalnej usługi Active Directory, można wprowadzić hasło zgodne z lokalnymi zasadami usługi AD. Następnie można podać to hasło do użytkownika.

    ![Wskaźnik postępu resetowania hasła i powiadomienie o powodach](media/my-staff-configure/reset-password.png)

Użytkownik musi zmienić swoje hasło przy następnym loguchi.

## <a name="manage-a-phone-number"></a>Zarządzanie numerem telefonu

Otwórz profil użytkownika z **poziomu moich pracowników.**

- Wybierz **sekcję Dodaj numer telefonu,** aby dodać numer telefonu dla użytkownika
- Wybierz **edytuj numer telefonu,** aby zmienić numer telefonu
- Wybierz **pozycję Usuń numer telefonu,** aby usunąć numer telefonu użytkownika

W zależności od ustawień użytkownik może następnie użyć numeru telefonu skonfigurowany do logowania się za pomocą wiadomości SMS, wykonywania uwierzytelniania wieloskładnikowego i samodzielnego resetowania hasła.

Aby zarządzać numerem telefonu użytkownika, musisz przypisać jedną z następujących ról:

- [Administrator uwierzytelniania](directory-assign-admin-roles.md#authentication-administrator)
- [Administrator uwierzytelniania uprzywilejowanego](directory-assign-admin-roles.md#privileged-authentication-administrator)
- [Administrator globalny](directory-assign-admin-roles.md#global-administrator--company-administrator)

## <a name="search"></a>Wyszukiwanie

Za pomocą paska wyszukiwania w witrynie Mój personel można wyszukiwać w organizacji aus i użytkowników w organizacji. Możesz wyszukiwać we wszystkich ausach i użytkownikach w organizacji, ale zmiany można wprowadzać tylko w ui, nad którymi masz uprawnienia administratora.

Można również wyszukać użytkownika w uikw. Aby to zrobić, użyj paska wyszukiwania u góry listy użytkowników.

## <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji można wyświetlać dla akcji wykonanych w obszarze Mój personel w portalu usługi Azure Active Directory. Jeśli dziennik inspekcji został wygenerowany przez działanie podjęte w obszarze Mój personel, zobaczysz to wskazane w obszarze DODATKOWE SZCZEGÓŁY w zdarzeniu inspekcji.

## <a name="next-steps"></a>Następne kroki

[Dokumentacja](../user-help/my-staff-team-manager.md)
użytkownika mój personel[Dokumentacja jednostek administracyjnych](directory-administrative-units.md)
