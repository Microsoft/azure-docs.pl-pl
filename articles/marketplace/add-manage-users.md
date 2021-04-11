---
title: Dodawanie użytkowników do komercyjnego portalu Marketplace i zarządzanie nimi w portalu Azure Marketplace
description: Dowiedz się, jak zarządzać użytkownikami w komercyjnym programie Marketplace dla konta komercyjnego portalu Microsoft Marketplace w centrum partnerskim.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: d5b9197bfd2526dd414406ebf1aca509d3b3fa91
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108282"
---
# <a name="add-and-manage-users-for-the-commercial-marketplace"></a>Dodawanie użytkowników do komercyjnej witryny Marketplace i zarządzanie nimi

**Odpowiednie role**

- Właściciel
- Menedżer

Sekcja **Użytkownicy** Centrum partnerskiego (w obszarze **Ustawienia konta**) umożliwia zarządzanie użytkownikami, grupami i aplikacjami usługi Azure AD, które mają dostęp do Twojego konta Centrum partnerskiego za pomocą usługi Azure AD. Twoje konto musi mieć uprawnienia na poziomie menedżera dla [konta służbowego (dzierżawy usługi Azure AD)](company-work-accounts.md) , w którym chcesz dodać lub edytować użytkowników. Aby zarządzać użytkownikami w ramach innego konta służbowego/dzierżawy, musisz wylogować się, a następnie ponownie zalogować się jako użytkownik z uprawnieniami **Menedżera** dla tego konta służbowego/dzierżawy.

Po zalogowaniu się przy użyciu konta służbowego (dzierżawy usługi Azure AD) można dodawać użytkowników i zarządzać nimi.

## <a name="add-existing-users"></a>Dodaj istniejących użytkowników

Aby dodać użytkowników do konta Centrum partnerskiego, które już istnieje na [koncie służbowym firmy (dzierżawy usługi Azure AD)](company-work-accounts.md):

1. Przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**) i wybierz pozycję **Dodaj użytkowników**.
1. Wybierz co najmniej jednego użytkownika z wyświetlonej listy. Możesz użyć pola wyszukiwania, aby wyszukać określonych użytkowników. * Jeśli wybierzesz więcej niż jednego użytkownika do dodania do konta Centrum partnerskiego, musisz przypisać im tę samą rolę lub zestaw uprawnień niestandardowych. Aby dodać wielu użytkowników z różnymi rolami/uprawnieniami, Powtórz te kroki dla każdej roli lub zestawu uprawnień niestandardowych.
1. Po zakończeniu wybierania użytkowników wybierz pozycję **Dodaj wybrane**.
1. W sekcji **role** Określ role lub dostosowane uprawnienia dla wybranych użytkowników.
1. Wybierz pozycję **Zapisz**.

## <a name="create-new-users"></a>Utwórz nowych użytkowników

Aby utworzyć nowe konta użytkowników, musisz mieć konto z uprawnieniami [administratora globalnego](/azure/active-directory/roles/permissions-reference) .

1. Przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz pozycję **Dodaj użytkowników**, a następnie wybierz pozycję **Utwórz nowych użytkowników**.
1. Wprowadź imię i nazwisko oraz nazwę użytkownika dla każdego nowego użytkownika.
1. Jeśli chcesz, aby nowy użytkownik miał konto administratora globalnego w katalogu organizacji, zaznacz pole wyboru nadaj **temu użytkownikowi uprawnienia administratora globalnego w usłudze Azure AD, mając pełną kontrolę nad wszystkimi zasobami katalogu**. Dzięki temu użytkownik będzie miał pełny dostęp do wszystkich funkcji administracyjnych w usłudze Azure AD Twojej firmy. Będą oni mogli dodawać użytkowników i zarządzać nimi w ramach konta służbowego organizacji (dzierżawy usługi Azure AD), ale nie w centrum partnerskim, chyba że zostanie przyznane konto odpowiedniej roli/uprawnień.
1. Jeśli zaznaczono pole wyboru **tego użytkownika jako administratora globalnego**, należy podać *wiadomość e-mail z odzyskiwaniem hasła* , aby użytkownik mógł odzyskać swoje hasło w razie potrzeby.
1. W sekcji **członkostwo w grupie** wybierz wszystkie grupy, do których ma należeć nowy użytkownik.
1. W sekcji **role** Określ role lub dostosowane uprawnienia dla użytkownika.
1. Wybierz pozycję **Zapisz**.

Utworzenie nowego użytkownika w centrum partnerskim spowoduje również utworzenie konta dla tego użytkownika w ramach konta służbowego (dzierżawy usługi Azure AD), z którym użytkownik jest zalogowany. Wprowadzenie zmian w nazwie użytkownika w centrum partnerskim spowoduje takie same zmiany w ramach konta służbowego organizacji (dzierżawy usługi Azure AD).

## <a name="invite-new-users-by-email"></a>Zaproś nowych użytkowników pocztą e-mail

Aby zapraszać użytkowników, którzy nie są obecnie częścią firmowego konta służbowego (Azure AD dzierżawcą) za pośrednictwem poczty e-mail, musisz mieć konto z uprawnieniami [administratora globalnego](/azure/active-directory/roles/permissions-reference) .

1. Przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz pozycję **Dodaj użytkowników**, a następnie wybierz opcję **Zapraszanie użytkowników za pośrednictwem poczty e-mail**.
1. Wprowadź co najmniej jeden adres e-mail (do 10), rozdzielony przecinkami lub średnikami.
1. W sekcji **role** Określ role lub dostosowane uprawnienia dla użytkownika.
1. Wybierz pozycję **Zapisz**.

Zaproszeni użytkownicy otrzymają wiadomość e-mail z zaproszeniem do wzięcia udziału w Twoim koncie Centrum partnerskiego. Nowe konto użytkownika Gość zostanie utworzone na koncie służbowym (dzierżawy usługi Azure AD). Każdy użytkownik będzie musiał zaakceptować zaproszenie przed uzyskaniem dostępu do konta.

Jeśli musisz ponownie wysłać zaproszenie, odwiedź stronę *Użytkownicy* i Znajdź zaproszenie na liście użytkowników, wybierz swój adres e-mail (lub tekst mówiący *Oczekujące zaproszenia*). Następnie w dolnej części strony wybierz pozycję **Wyślij ponownie zaproszenie**.

Jeśli Twoja organizacja używa [integracji katalogu](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)) do synchronizowania lokalnej usługi katalogowej z usługą Azure AD, nie będziesz w stanie tworzyć nowych użytkowników, grup ani aplikacji usługi Azure AD w centrum partnerskim. Użytkownik (lub inny administrator w katalogu lokalnym) będzie musiał je utworzyć bezpośrednio w katalogu lokalnym, aby można było je zobaczyć i dodać do Centrum partnerskiego.

## <a name="remove-a-user"></a>Usuwanie użytkownika

Aby usunąć użytkownika z konta służbowego (dzierżawy usługi Azure AD), przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz użytkownika, który chcesz usunąć, przy użyciu pola wyboru w prawej kolumnie, a następnie wybierz pozycję **Usuń** z dostępnych akcji. Zostanie wyświetlone okno podręczne, aby potwierdzić, że chcesz usunąć wybranych użytkowników.

## <a name="change-a-user-password"></a>Zmiana hasła użytkownika

Jeśli jeden z użytkowników musi zmienić hasło, może to zrobić, jeśli podczas tworzenia konta użytkownika podano *wiadomość e-mail z odzyskiwaniem haseł* . Możesz również zaktualizować hasło użytkownika, wykonując poniższe kroki. Aby zmienić hasło użytkownika na koncie służbowym firmy (dzierżawy usługi Azure AD), użytkownik musi być zalogowany na koncie z uprawnieniami [administratora globalnego](/azure/active-directory/roles/permissions-reference) . Spowoduje to zmianę hasła użytkownika w dzierżawie usługi Azure AD wraz z hasłem używanym do uzyskiwania dostępu do Centrum partnerskiego.

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz nazwę konta użytkownika, które chcesz edytować.
1. Wybierz przycisk **resetowania hasła** w dolnej części strony.
1. Zostanie wyświetlona strona potwierdzenia z informacjami logowania użytkownika, w tym hasła tymczasowego. Pamiętaj, aby wydrukować lub skopiować te informacje i udostępnić je użytkownikowi, ponieważ nie będzie można uzyskać dostępu do hasła tymczasowego po opuszczeniu tej strony.
