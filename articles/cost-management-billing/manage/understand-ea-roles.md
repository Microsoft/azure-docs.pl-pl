---
title: Omówienie ról administracyjnych dla umów Enterprise Agreement (EA) na platformie Azure
description: Dowiedz się więcej na temat ról administratorów przedsiębiorstwa na platformie Azure. Możesz przypisać pięć odrębnych ról administracyjnych.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 653eacd11c4a3c7ab500abff809a6b9bf8229c1f
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492073"
---
# <a name="managing-azure-enterprise-agreement-roles"></a>Zarządzanie rolami w umowie Enterprise Agreement platformy Azure

Aby ułatwić zarządzanie użyciem i wydatkami w organizacji, klienci platformy Azure z umową Enterprise Agreement mogą przypisywać pięć odrębnych ról administracyjnych:

- Administratora przedsiębiorstwa
- Administrator przedsiębiorstwa (tylko odczyt)<sup>1</sup>
- Zakup EA
- Administrator działu
- Administrator działu (tylko odczyt)
- Właściciel konta<sup>2</sup>

<sup>1</sup> Kontakt dla rozliczeń dotyczących umowy EA będzie objęty tą rolą.

<sup>2</sup> Kontaktu dla rozliczeń nie można dodać ani zmienić w witrynie Azure EA Portal i zostanie on dodany do rejestracji umowy EA na podstawie użytkownika, który został skonfigurowany jako kontakt dla rozliczeń na poziomie umowy. Aby zmienić kontakt dla rozliczeń, należy przesłać żądanie do regionalnego centrum operacyjnego (ROC) za pośrednictwem partnera/doradcy ds. oprogramowania.

Pierwszy administrator rejestracji skonfigurowany podczas aprowizacji rejestracji określa typ uwierzytelnienia konta kontaktu dla rozliczeń. Gdy kontakt dla rozliczeń zostanie dodany do portalu EA Portal jako administrator tylko do odczytu, zostanie mu nadane uwierzytelnianie konta Microsoft. 

Na przykład jeśli typ uwierzytelniania początkowego jest ustawiony na wartość Mieszane, umowa EA zostanie dodana jako konto Microsoft, a kontakt dla rozliczeń będzie miał uprawnienia administratora EA tylko do odczytu. Jeśli administrator EA nie zatwierdzi autoryzacji konta Microsoft dla istniejącego kontaktu dla rozliczeń, może usunąć danego użytkownika i poprosić klienta o dodanie użytkownika jako administratora tylko do odczytu z kontem służbowym ustawionym tylko na poziomie rejestracji w portalu EA.

Te role są specyficzne dla procesu zarządzania umowami Enterprise Agreement platformy Azure. Są one dodatkiem do wbudowanych ról platformy Azure, które umożliwiają kontrolowanie dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

## <a name="azure-enterprise-portal-hierarchy"></a>Hierarchia witryny Azure Enterprise Portal

Hierarchia witryny Azure Enterprise Portal obejmuje następujące elementy:

- **Azure Enterprise Portal** — portal zarządzania online, który ułatwia zarządzanie kosztami usług w ramach umowy EA platformy Azure. Możesz:

  - Tworzyć hierarchię umowy EA platformy Azure, w tym działy, konta i subskrypcje.
  - Uzgadniać koszty używanych usług, pobierać raporty użycia i wyświetlać cenniki.
  - Tworzyć klucze interfejsu API dla rejestracji.

- **Działy** ułatwiają dzielenie kosztów na logiczne grupy. Działy umożliwiają ustawianie budżetu lub limitu przydziału na poziomie działu.

- **Konta** są jednostkami organizacyjnymi w witrynie Azure Enterprise Portal. Konta umożliwiają zarządzanie subskrypcjami i wyświetlanie raportów.

- **Subskrypcje** są najmniejszymi jednostkami w witrynie Azure Enterprise Portal. Są to kontenery dla usług platformy Azure zarządzanych przez administratora usługi.

Na poniższym diagramie przedstawiono proste hierarchie witryny Azure EA Portal.

![Diagram prostych hierarchii witryny Azure EA Portal](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Role użytkownika przedsiębiorstwa

W ramach rejestracji przedsiębiorstwa występują następujące role użytkowników administracyjnych:

- Administrator przedsiębiorstwa
- Zakup EA
- Administrator działu
- Właściciel konta
- Administrator usługi
- Kontakt dla powiadomień

Role umożliwiają realizowanie zadań w dwóch różnych portalach. Witryna [Azure Enterprise Portal](https://ea.azure.com) służy do zarządzania rozliczeniami i kosztami, a witryna [Azure Portal](https://portal.azure.com) umożliwia zarządzanie usługami platformy Azure.

Role użytkowników są skojarzone z kontem użytkownika. Aby zweryfikować autentyczność użytkowników, każdy użytkownik musi mieć prawidłowe konto służbowe lub Microsoft. Upewnij się, że każde konto jest skojarzone z używanym adresem e-mail. Powiadomienia konta są wysyłane na adres e-mail.

Podczas konfigurowania użytkowników do roli administratora przedsiębiorstwa można przypisać wiele kont. Jednak tylko jedno konto może mieć rolę właściciela konta. Ponadto do jednego konta można przypisać zarówno rolę administratora przedsiębiorstwa, jak i rolę właściciela konta.

### <a name="enterprise-administrator"></a>Administrator przedsiębiorstwa

Użytkownicy z tą rolą mają najwyższy poziom dostępu. Mogą wykonywać następujące czynności:

- Zarządzanie kontami i właścicielami kont.
- Zarządzanie innymi administratorami przedsiębiorstwa.
- Zarządzanie administratorami działów.
- Zarządzanie kontaktami dla powiadomień.
- Kup usługi platformy Azure, w tym rezerwacje.
- Wyświetlanie użycia na wszystkich kontach.
- Wyświetlanie nienaliczonych opłat na wszystkich kontach.
- Wyświetlanie wszystkich rezerwacji i zamówień rezerwacji dotyczących umowy Enterprise Agreement, oraz zarządzanie nimi.
  - Administrator przedsiębiorstwa (tylko do odczytu) może wyświetlać rezerwacje i zamówienia rezerwacji. Nie może nimi zarządzać.

W ramach rejestracji przedsiębiorstwa może być wielu administratorów przedsiębiorstwa. Administratorom przedsiębiorstwa można przyznać dostęp tylko do odczytu. Wszyscy oni dziedziczą rolę administratora działu.

### <a name="ea-purchaser"></a>Zakup EA

Użytkownicy z tą rolą mają uprawnienia do kupowania usług platformy Azure, ale nie mogą zarządzać kontami. Mogą wykonywać następujące czynności:

- Kup usługi platformy Azure, w tym rezerwacje.
- Wyświetlanie użycia na wszystkich kontach.
- Wyświetlanie nienaliczonych opłat na wszystkich kontach.
- Wyświetlanie wszystkich rezerwacji i zamówień rezerwacji dotyczących umowy Enterprise Agreement, oraz zarządzanie nimi.

Rola zakupu EA jest obecnie włączona tylko dla dostępu opartego na SPN. Aby dowiedzieć się, jak przypisać rolę do nazwy głównej usługi, zobacz [Przypisywanie ról do usługi Azure Enterprise Agreement nazwach głównych usług](assign-roles-azure-service-principals.md).

### <a name="department-administrator"></a>Administrator działu

Użytkownicy z tą rolą mogą wykonywać następujące czynności:

- Tworzenie działów i zarządzanie nimi.
- Tworzenie nowych właścicieli kont.
- Wyświetlanie szczegółów użycia dla działów, którymi zarządzają.
- Wyświetlanie kosztów w przypadku posiadania niezbędnych uprawnień.

Dla każdej rejestracji przedsiębiorstwa można mieć wielu administratorów działów.

Tworząc lub edytując konto administratora działu, można przyznać mu dostęp tylko do odczytu. Dla opcji tylko do odczytu ustaw wartość **Tak**.

### <a name="account-owner"></a>Właściciel konta

Użytkownicy z tą rolą mogą wykonywać następujące czynności:

- Tworzenie subskrypcji i zarządzanie nimi.
- Zarządzanie administratorami usług.
- Wyświetlanie użycia dla subskrypcji.

Każde konto wymaga unikatowego konta służbowego lub konta Microsoft. Aby uzyskać więcej informacji na temat ról administracyjnych w witrynie Azure Enterprise Portal, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](understand-ea-roles.md).

### <a name="service-administrator"></a>Administrator usługi

Rola administratora usługi ma uprawnienia do zarządzania usługami w witrynie Azure Portal i przypisywania użytkowników do roli współadministratora.

### <a name="notification-contact"></a>Kontakt dla powiadomień

Kontakt dla powiadomień odbiera powiadomienia o użyciu dotyczące rejestracji.

W poniższych sekcjach opisano ograniczenia i możliwości poszczególnych ról.

## <a name="user-limit-for-admin-roles"></a>Limit liczby użytkowników dla ról administratorów

|Rola| Limit użytkowników|
|---|---|
|Administratora przedsiębiorstwa|Nieograniczona liczba|
|Administrator przedsiębiorstwa (tylko odczyt)|Nieograniczona liczba|
| Zakup EA przypisany do nazwy SPN | Nieograniczona liczba |
|Administrator działu|Nieograniczona liczba|
|Administrator działu (tylko odczyt)|Nieograniczona liczba|
|Właściciel konta|1 na konto<sup>3</sup>|

<sup>3</sup> Każde konto wymaga unikatowego konta Microsoft lub konta służbowego.

## <a name="organization-structure-and-permissions-by-role"></a>Struktura i uprawnienia organizacji według roli

|Zadania| Administratora przedsiębiorstwa|Administrator przedsiębiorstwa (tylko odczyt)| Zakup EA | Administrator działu|Administrator działu (tylko odczyt)|Właściciel konta| Partner|
|---|---|---|---|---|---|---|---|
|Wyświetlanie administratorów przedsiębiorstwa|✔|✔| ✔|✘|✘|✘|✔|
|Dodawanie lub usuwanie administratorów przedsiębiorstwa|✔|✘|✘|✘|✘|✘|✘|
|Wyświetlanie kontaktów dla powiadomień<sup>4</sup> |✔|✔|✔|✘|✘|✘|✔|
|Dodawanie lub usuwanie kontaktów dla powiadomień<sup>4</sup> |✔|✘|✘|✘|✘|✘|✘|
|Tworzenie działów i zarządzanie nimi |✔|✘|✘|✘|✘|✘|✘|
|Wyświetlanie administratorów działu|✔|✔|✔|✔|✔|✘|✔|
|Dodawanie lub usuwanie administratorów działu|✔|✘|✘|✔|✘|✘|✘|
|Wyświetlanie kont w rejestracji |✔|✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|Dodawanie kont do rejestracji i zmienianie właściciela konta|✔|✘|✘|✔<sup>5</sup>|✘|✘|✘|
|Zakup rezerwacji|✔|✘|✔|✘|✘|✘|✘|
|Tworzenie subskrypcji i uprawnień subskrypcji oraz zarządzanie nimi|✘|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> Kontakty dla powiadomień otrzymują wiadomości e-mail dotyczące umowy Enterprise Agreement platformy Azure.
- <sup>5</sup> Zadanie jest ograniczone do kont w Twoim dziale.

## <a name="add-a-new-enterprise-administrator"></a>Dodawanie nowego administratora przedsiębiorstwa

Administratorzy przedsiębiorstwa mają największe uprawnienia podczas zarządzania rejestracją w portalu Azure EA. Początkowy administrator platformy Azure EA został utworzony podczas konfigurowania umowy EA. Można jednak dodawać i usuwać nowych administratorów w dowolnym momencie. Nowi administratorzy są dodawani tylko przez istniejących administratorów. Aby uzyskać więcej informacji na temat dodawania dodatkowych administratorów przedsiębiorstwa, zobacz sekcję [Tworzenie innego administratora przedsiębiorstwa](ea-portal-administration.md#create-another-enterprise-administrator). Aby uzyskać więcej informacji o rolach i zadaniach dotyczących rozliczeń, zobacz sekcję [Role i zadania profilów rozliczeniowych](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-account-owner-state-from-pending-to-active"></a>Aktualizowanie stanu właściciela konta z „oczekujące” na „aktywne”

Gdy nowi właściciele konta są po raz pierwszy dodawani do rejestracji w ramach umowy EA, ich stan jest wyświetlany jako _oczekujące_. Gdy nowy właściciel konta otrzymuje powitalną wiadomość e-mail z informacjami o aktywacji, może się zalogować, aby aktywować swoje konto. Po aktywowaniu konta stan konta jest aktualizowany z _oczekujące_ na _aktywne_. Właściciel konta musi odczytać komunikat z ostrzeżeniem, a następnie wybrać pozycję **Kontynuuj**. Nowi użytkownicy mogą zobaczyć monit o wprowadzenie imienia i nazwiska, aby utworzyć konto komercyjne. W takiej sytuacji muszą dodać wymagane informacje, aby kontynuować, a konto jest aktywowane.

## <a name="add-a-department-admin"></a>Dodawanie administratora działu

Po utworzeniu działu przez administratora portalu Azure EA administrator oferty Azure Enterprise może dodać administratorów działu i skojarzyć ich z działem. Administrator działu może tworzyć nowe konta. Nowe konta są potrzebne do tworzenia subskrypcji portalu Azure EA.

Aby uzyskać więcej informacji o dodawaniu administratora działu, zobacz [Tworzenie administratora działu w portalu Azure EA](ea-portal-administration.md#add-a-department-administrator).

## <a name="usage-and-costs-access-by-role"></a>Dostęp do danych użycia i kosztów według roli

|Zadania| Administratora przedsiębiorstwa|Administrator przedsiębiorstwa (tylko odczyt)|Zakup EA|Administrator działu|Administrator działu (tylko odczyt) |Właściciel konta| Partner|
|---|---|---|---|---|---|---|---|
|Wyświetlanie salda środków, w tym przedpłaty za platformę Azure|✔|✔|✔|✘|✘|✘|✔|
|Wyświetlanie limitów przydziału wydatków dla działu|✔|✔|✔|✘|✘|✘|✔|
|Ustawianie limitów przydziału wydatków dla działu|✔|✘|✘|✘|✘|✘|✘|
|Wyświetlanie arkusza cen umów EA organizacji|✔|✔|✔|✘|✘|✘|✔|
|Wyświetlanie szczegółów użycia i kosztów|✔|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Zarządzanie zasobami w witrynie Azure Portal|✘|✘|✘|✘|✘|✔|✘|

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

Rola administratora przedsiębiorstwa i zasady wyświetlania opłat są ustawiane w witrynie Enterprise Portal. Rolę na platformie Azure można zaktualizować w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

- [Manage access to billing information for Azure (Zarządzanie dostępem do informacji rozliczeniowych dla platformy Azure)](manage-billing-access.md)
- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md)
