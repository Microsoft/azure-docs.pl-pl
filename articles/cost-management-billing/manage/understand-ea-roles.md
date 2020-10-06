---
title: Omówienie ról administracyjnych dla przedsiębiorstwa na platformie Azure
description: Dowiedz się więcej na temat ról administratorów przedsiębiorstwa na platformie Azure. Możesz przypisać pięć odrębnych ról administracyjnych.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: 13b344d3f13993dc7b6acf7bfe9a0ccdea0c866b
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371358"
---
# <a name="managing-azure-enterprise-roles"></a>Zarządzanie rolami w usłudze Azure Enterprise

Aby ułatwić zarządzanie użyciem i wydatkami w organizacji, klienci platformy Azure z umową Enterprise Agreement (EA) mogą przypisywać pięć odrębnych ról administracyjnych:

- Administratora przedsiębiorstwa
- Administrator przedsiębiorstwa (tylko odczyt)<sup>1</sup>
- Administrator działu
- Administrator działu (tylko odczyt)
- Właściciel konta<sup>2</sup>

<sup>1</sup> Kontakt dla rozliczeń dotyczących umowy EA będzie objęty tą rolą.

<sup>2</sup> Kontaktu dla rozliczeń nie można dodać ani zmienić w witrynie Azure EA Portal i zostanie on dodany do rejestracji umowy EA na podstawie użytkownika, który został skonfigurowany jako kontakt dla rozliczeń na poziomie umowy. Aby zmienić kontakt dla rozliczeń, należy przesłać żądanie do regionalnego centrum operacyjnego (ROC) za pośrednictwem partnera/doradcy ds. oprogramowania.

Pierwszy administrator rejestracji skonfigurowany podczas aprowizacji rejestracji określa typ uwierzytelnienia konta kontaktu dla rozliczeń. Gdy kontakt dla rozliczeń zostanie dodany do portalu EA Portal jako administrator tylko do odczytu, zostanie mu nadane uwierzytelnianie konta Microsoft. 

Na przykład jeśli typ uwierzytelniania początkowego jest ustawiony na wartość Mieszane, umowa EA zostanie dodana jako konto Microsoft, a kontakt dla rozliczeń będzie miał uprawnienia administratora EA tylko do odczytu. Jeśli administrator EA nie zatwierdzi autoryzacji konta Microsoft dla istniejącego kontaktu dla rozliczeń, może usunąć danego użytkownika i poprosić klienta o dodanie użytkownika jako administratora tylko do odczytu z kontem służbowym ustawionym tylko na poziomie rejestracji w portalu EA.

Te role są specyficzne dla procesu zarządzania umowami Enterprise Agreement platformy Azure. Są one dodatkiem do wbudowanych ról platformy Azure, które umożliwiają kontrolowanie dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

W poniższych sekcjach opisano ograniczenia i możliwości poszczególnych ról.

## <a name="user-limit-for-admin-roles"></a>Limit liczby użytkowników dla ról administratorów

|Rola| Limit użytkowników|
|---|---|
|Administratora przedsiębiorstwa|Nieograniczona liczba|
|Administrator przedsiębiorstwa (tylko odczyt)|Nieograniczona liczba|
|Administrator działu|Nieograniczona liczba|
|Administrator działu (tylko odczyt)|Nieograniczona liczba|
|Właściciel konta|1 na konto<sup>3</sup>|

<sup>3</sup> Każde konto wymaga unikatowego konta Microsoft lub konta służbowego.

## <a name="organization-structure-and-permissions-by-role"></a>Struktura i uprawnienia organizacji według roli

|Zadania| Administratora przedsiębiorstwa|Administrator przedsiębiorstwa (tylko odczyt)|Administrator działu|Administrator działu (tylko odczyt)|Właściciel konta| Partner|
|---|---|---|---|---|---|---|
|Wyświetlanie administratorów przedsiębiorstwa|✔|✔|✘|✘|✘|✔|
|Dodawanie lub usuwanie administratorów przedsiębiorstwa|✔|✘|✘|✘|✘|✘|
|Wyświetlanie kontaktów dla powiadomień<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|Dodawanie lub usuwanie kontaktów dla powiadomień<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|Tworzenie działów i zarządzanie nimi |✔|✘|✘|✘|✘|✘|
|Wyświetlanie administratorów działu|✔|✔|✔|✔|✘|✔|
|Dodawanie lub usuwanie administratorów działu|✔|✘|✔|✘|✘|✘|
|Wyświetlanie kont w rejestracji |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Dodawanie kont do rejestracji i zmienianie właściciela konta|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|Tworzenie subskrypcji i uprawnień subskrypcji oraz zarządzanie nimi|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Kontakty dla powiadomień otrzymują wiadomości e-mail dotyczące umowy Enterprise Agreement platformy Azure.
- <sup>5</sup> Zadanie jest ograniczone do kont w Twoim dziale.

## <a name="add-a-new-enterprise-administrator"></a>Dodawanie nowego administratora przedsiębiorstwa

Administratorzy przedsiębiorstwa mają największe uprawnienia podczas zarządzania rejestracją w portalu Azure EA. Początkowy administrator platformy Azure EA został utworzony podczas konfigurowania umowy EA. Można jednak dodawać i usuwać nowych administratorów w dowolnym momencie. Nowi administratorzy są dodawani tylko przez istniejących administratorów. Aby uzyskać więcej informacji na temat dodawania dodatkowych administratorów przedsiębiorstwa, zobacz sekcję [Tworzenie innego administratora przedsiębiorstwa](ea-portal-get-started.md#create-another-enterprise-administrator). Aby uzyskać więcej informacji o rolach i zadaniach dotyczących rozliczeń, zobacz sekcję [Role i zadania profilów rozliczeniowych](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Aktualizowanie stanu właściciela konta z „oczekujące” na „aktywne”

Gdy nowi właściciele konta są po raz pierwszy dodawani do rejestracji w ramach umowy EA, ich stan jest wyświetlany jako _oczekujące_. Gdy nowy właściciel konta otrzymuje powitalną wiadomość e-mail z informacjami o aktywacji, może się zalogować, aby aktywować swoje konto. Po aktywowaniu konta stan konta jest aktualizowany z _oczekujące_ na _aktywne_. Właściciel konta musi odczytać komunikat z ostrzeżeniem, a następnie wybrać pozycję **Kontynuuj**. Nowi użytkownicy mogą zobaczyć monit o wprowadzenie imienia i nazwiska, aby utworzyć konto komercyjne. W takiej sytuacji muszą dodać wymagane informacje, aby kontynuować, a konto jest aktywowane.

## <a name="add-a-department-admin"></a>Dodawanie administratora działu

Po utworzeniu działu przez administratora portalu Azure EA administrator oferty Azure Enterprise może dodać administratorów działu i skojarzyć ich z działem. Administrator działu może tworzyć nowe konta. Nowe konta są potrzebne do tworzenia subskrypcji portalu Azure EA.

Aby uzyskać więcej informacji o dodawaniu administratora działu, zobacz [Tworzenie administratora działu w portalu Azure EA](ea-portal-get-started.md#add-a-department-administrator).

## <a name="usage-and-costs-access-by-role"></a>Dostęp do danych użycia i kosztów według roli

|Zadania| Administratora przedsiębiorstwa|Administrator przedsiębiorstwa (tylko odczyt)|Administrator działu|Administrator działu (tylko odczyt) |Właściciel konta| Partner|
|---|---|---|---|---|---|---|
|Wyświetlanie salda środków, w tym przedpłaty za platformę Azure|✔|✔|✘|✘|✘|✔|
|Wyświetlanie limitów przydziału wydatków dla działu|✔|✔|✘|✘|✘|✔|
|Ustawianie limitów przydziału wydatków dla działu|✔|✘|✘|✘|✘|✘|
|Wyświetlanie arkusza cen umów EA organizacji|✔|✔|✘|✘|✘|✔|
|Wyświetlanie szczegółów użycia i kosztów|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Zarządzanie zasobami w witrynie Azure Portal|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> Wymaga, aby administrator przedsiębiorstwa włączył zasady **Administrator przedsiębiorstwa — wyświetlanie opłat** w witrynie Enterprise Portal. Administrator działu będzie następnie widzieć szczegóły kosztów dla działu.
- <sup>7</sup> Wymaga, aby administrator przedsiębiorstwa włączył zasady **Właściciel konta — wyświetlanie opłat** w witrynie Enterprise Portal. Właściciel konta będzie następnie widzieć szczegóły kosztów dla konta.

## <a name="see-pricing-for-different-user-roles"></a>Zobacz cennik dla różnych ról użytkownika

W zależności od roli administracyjnej i sposobu ustawienia zasad wyświetlania opłat przez administratora przedsiębiorstwa w witrynie Azure Portal mogą być widoczne różne ceny. Dwie zasady w witrynie Enterprise Portal, które mają wpływ na ceny widoczne w witrynie Azure Portal, to:

- Administrator działu — wyświetlanie opłat
- Właściciel konta — wyświetlanie opłat

Aby dowiedzieć się, jak ustawić te zasady, zobacz temat [Manage access to billing information for Azure](manage-billing-access.md) (Zarządzanie dostępem do informacji rozliczeniowych dotyczących platformy Azure).

W poniższej tabeli przedstawiono relację między rolami administracyjnymi umów Enterprise Agreement, zasadami wyświetlania opłat, rolą na platformie Azure w witrynie Azure Portal oraz cenami widocznymi w witrynie Azure Portal. Administrator przedsiębiorstwa zawsze widzi szczegóły użycia w oparciu o ceny umowy EA organizacji. Natomiast administrator działu i właściciel konta zobaczą różne widoki cen w zależności od zasad wyświetlania opłat i odpowiedniej roli na platformie Azure. Rola Administrator działu wymieniona w poniższej tabeli odnosi się do ról Administrator działu i Administrator działu (tylko odczyt).

|Rola administratora umowy Enterprise Agreement|Zasady wyświetlania opłat dla roli|Rola na platformie Azure|Widok cen|
|---|---|---|---|
|Właściciel konta LUB administrator działu|✔ Włączone|Właściciel|Ceny umowy EA organizacji|
|Właściciel konta LUB administrator działu|✘ Wyłączone|Właściciel|Ceny detaliczne|
|Właściciel konta LUB administrator działu|✔ Włączone |brak|Brak cen|
|Właściciel konta LUB administrator działu|✘ Wyłączone |brak|Brak cen|
|Brak|Nie dotyczy |Właściciel|Ceny detaliczne|

Rola administratora przedsiębiorstwa i zasady wyświetlania opłat są ustawiane w witrynie Enterprise Portal. Rolę na platformie Azure można zaktualizować w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md).



## <a name="next-steps"></a>Następne kroki

- [Manage access to billing information for Azure (Zarządzanie dostępem do informacji rozliczeniowych dla platformy Azure)](manage-billing-access.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md)
