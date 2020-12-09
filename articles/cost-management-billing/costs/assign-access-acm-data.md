---
title: Przypisywanie dostępu do danych usługi Azure Cost Management
description: W tym artykule omówiono przypisywanie uprawnień do danych usługi Azure Cost Management dla różnych zakresów dostępu.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: 660b5751e3b3cbc632331e99d797af3392a8aea4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "91371970"
---
# <a name="assign-access-to-cost-management-data"></a>Przypisywanie dostępu do danych usługi Cost Management

W przypadku użytkowników platformy Azure korzystających z umów Enterprise Agreement połączenie uprawnień udzielonych w witrynie Azure Portal i portalu Enterprise (EA) definiuje poziom dostępu użytkownika do danych usługi Azure Cost Management. W przypadku użytkowników z innymi typami kont platformy Azure definiowanie poziomu dostępu użytkownika do danych usługi Cost Management jest prostsze przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure. W tym artykule przedstawiono sposób przypisywania dostępu do danych usługi Cost Management. Po przypisaniu kombinacji uprawnień użytkownik wyświetla dane w usłudze Cost Management na podstawie jego zakresu dostępu oraz w zakresie wybranym przez niego w witrynie Azure Portal.

Wybrany przez użytkownika zakres będzie stosowany w całej usłudze Cost Management w celu zapewnienia konsolidacji danych i kontrolowania dostępu do informacji o kosztach. W przypadku korzystania z zakresów użytkownicy nie dokonują ich wielokrotnego wyboru. Zamiast tego wybierają większy zakres, na który są rzutowane zakresy podrzędne, a następnie filtrują go w dół do elementów, które chcą wyświetlić. Konsolidacja danych jest ważna, ponieważ niektóre osoby nie powinny mieć dostępu do zakresu nadrzędnego, który obejmuje zakresy podrzędne.

Obejrzyj wideo [Kontrolowanie dostępu w usłudze Cost Management](https://www.youtube.com/watch?v=_uQzQ9puPyM), aby dowiedzieć się więcej o przypisaniu dostępu do wyświetlania kosztów i opłat za pomocą kontroli dostępu na podstawie ról (RBAC) platformy Azure. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>Zakresy usługi Cost Management

Zarządzanie kosztami obsługuje wiele różnych typów kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Typ konta określa dostępne zakresy.

### <a name="azure-ea-subscription-scopes"></a>Zakresy subskrypcji EA platformy Azure

Aby wyświetlać dane dotyczące kosztów dla subskrypcji EA platformy Azure, użytkownik musi mieć co najmniej prawa dostępu do odczytu do co najmniej jednego z poniższych zakresów.

| **Zakres** | **Lokalizacja definicji** | **Wymagany dostęp do wyświetlania danych** | **Wstępnie wymagane ustawienie umowy EA** | **Konsolidowanie danych do** |
| --- | --- | --- | --- | --- |
| Konto rozliczeniowe<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Administrator przedsiębiorstwa | Brak | Wszystkie subskrypcje z umowy Enterprise Agreement |
| Dział | [https://ea.azure.com](https://ea.azure.com/) | Administrator działu | Włączona opcja **Wyświetlanie opłat dla administratora działu** | Wszystkie subskrypcje należące do konta rejestracji połączonego z działem |
| Konto rejestracji<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Właściciel konta | Włączona opcja **Wyświetlanie opłat dla właściciela konta** | Wszystkie subskrypcje z konta rejestracji |
| Grupa zarządzania | [https://portal.azure.com](https://portal.azure.com/) | Czytelnik usługi Cost Management (lub Czytelnik) | Włączona opcja **Wyświetlanie opłat dla właściciela konta** | Wszystkie subskrypcje poniżej grupy zarządzania |
| Subskrypcja | [https://portal.azure.com](https://portal.azure.com/) | Czytelnik usługi Cost Management (lub Czytelnik) | Włączona opcja **Wyświetlanie opłat dla właściciela konta** | Wszystkie zasoby lub grupy zasobów w subskrypcji |
| Grupa zasobów | [https://portal.azure.com](https://portal.azure.com/) | Czytelnik usługi Cost Management (lub Czytelnik) | Włączona opcja **Wyświetlanie opłat dla właściciela konta** | Wszystkie zasoby w grupie zasobów |

<sup>1</sup> Konto rozliczeniowe jest także nazywane kontem umowy Enterprise Agreement lub kontem rejestracji.

<sup>2</sup> Konto rejestracji jest także nazywane właścicielem konta.


## <a name="other-azure-account-scopes"></a>Inne zakresy kont platformy Azure

Aby wyświetlać dane dotyczące kosztów dla innych subskrypcji platformy Azure, użytkownik musi mieć co najmniej prawa dostępu do odczytu do co najmniej jednego z poniższych zakresów:

- Konto platformy Azure
- Grupa zarządzania
- Grupa zasobów

Po dołączeniu klientów do umowy klienta firmy Microsoft przez partnerów dostępne są różne zakresy. Klienci programu CSP będą mogli wtedy korzystać z funkcji usługi Cost Management, jeśli zostały one włączone przez ich partnera CSP. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Cost Management dla partnerów](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Zapewnianie dostępu do kosztów w witrynie Azure Portal

Zakres działu wymaga, aby opcja **administratorzy działu mogli wyświetlać opłaty** (Administrator działu — wyświetlanie opłat) miała ustawioną wartość **Włączone**. Skonfiguruj opcję w witrynie Azure Portal lub w portalu EA. Wszystkie inne zakresy wymagają, aby opcja **właściciele konta mogli wyświetlać opłaty** (Właściciel konta — wyświetlanie opłat) miała ustawioną wartość **Włączone**.

Aby włączyć opcję w witrynie Azure Portal:

1. Zaloguj się do portalu EA na stronie https://portal.azure.com przy użyciu konta administratora przedsiębiorstwa.
1. Wybierz element menu **Zarządzanie kosztami i rozliczenia**.
1. Wybierz pozycję **Zakresy rozliczeniowe**, aby wyświetlić listę dostępnych zakresów i kont rozliczeniowych.
1. Wybierz **konto rozliczeniowe** z listy dostępnych kont rozliczeniowych.
1. W obszarze **Ustawienia** wybierz element menu **Zasady**, a następnie skonfiguruj ustawienie.  
    ![Zasady zakresu rozliczeniowego z opcjami wyświetlania opłat](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

Po włączeniu opcji wyświetlania opłat większość zakresów wymaga również konfiguracji uprawnień kontroli dostępu na podstawie ról (RBAC) platformy Azure w witrynie Azure Portal.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Zapewnianie dostępu do kosztów w portalu EA

Zakres działu wymaga, aby opcja **Wyświetlanie opłat dla administratora działu** miała stan **Włączone** w portalu EA. Skonfiguruj opcję w witrynie Azure Portal lub w portalu EA. Wszystkie inne działy wymagają, aby opcja **Wyświetlanie opłat dla właściciela konta** miała stan **Włączone** w portalu EA.

Aby włączyć opcję w portalu EA:

1. Zaloguj się do portalu EA na stronie [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj**.
3. Dla zakresów zarządzania kosztami, do których chcesz zapewnić dostęp, włącz opcję opłat **Wyświetlanie opłat dla administratora działu** i/lub **Wyświetlanie opłat dla właściciela konta**.  
    ![Karta Rejestrowanie przedstawiająca opcje Wyświetlanie opłat dla administratora działu i Wyświetlanie opłat dla właściciela konta](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Po włączeniu opcji wyświetlania opłat większość zakresów wymaga również konfiguracji uprawnień kontroli dostępu na podstawie ról (RBAC) platformy Azure w witrynie Azure Portal.

## <a name="enterprise-administrator-role"></a>Rola administratora przedsiębiorstwa

Domyślnie administrator przedsiębiorstwa ma dostęp do konta rozliczeniowego (umowa Enterprise Agreement/rejestracja) i do wszystkich innych zakresów, które są zakresami podrzędnymi. Administrator przedsiębiorstwa przypisuje dostęp do zakresów innym użytkownikom. Najlepszym rozwiązaniem w celu zachowania ciągłości działania jest posiadanie zawsze dwóch użytkowników z dostępem administratora przedsiębiorstwa. Poniższe sekcje to przykłady instruktażowe przedstawiające administratora przedsiębiorstwa przypisującego dostęp do zakresów innym użytkownikom.

## <a name="assign-billing-account-scope-access"></a>Przypisywanie dostępu do zakresu konta rozliczeniowego

Dostęp do zakresu konta rozliczeniowego wymaga uprawnień administratora przedsiębiorstwa w portalu EA. Administrator przedsiębiorstwa ma dostęp do wyświetlania kosztów całej rejestracji w ramach umowy EA lub wielu rejestracji. W witrynie Azure Portal nie jest wymagane żadne działanie dla zakresu konta rozliczeniowego.

1. Zaloguj się do portalu EA na stronie [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj**.
3. Na karcie **Rejestrowanie** wybierz rejestrację, którą chcesz zarządzać.  
    ![Wybierz swoją rejestrację w portalu EA](./media/assign-access-acm-data/ea-portal.png)
4. Wybierz pozycję **+ Dodaj administratora**.
5. W oknie Dodawanie administratora wybierz typ uwierzytelniania, a następnie wpisz adres e-mail użytkownika.
6. Jeśli użytkownik powinien mieć dostęp tylko do odczytu do danych o kosztach i użyciu, w obszarze **Tylko do odczytu** wybierz opcję **Tak**.  W przeciwnym razie wybierz opcję **Nie**.
7. Wybierz pozycję **Dodaj**, aby utworzyć konto.  
    ![Przykładowe informacje wyświetlane w oknie Dodawanie administratora](./media/assign-access-acm-data/add-admin.png)

Może upłynąć do 30 minut, zanim nowy użytkownik będzie mógł uzyskać dostęp do danych w usłudze Cost Management.

### <a name="assign-department-scope-access"></a>Przypisywanie dostępu do zakresu działu

Dostęp do zakresu działu wymaga dostępu administratora działu (Wyświetlanie opłat dla administratora działu) w portalu EA. Administrator działu ma dostęp do wyświetlania kosztów i danych użycia skojarzonych z działem lub z wieloma działami. Dane dla działu obejmują wszystkie subskrypcje należące do konta rejestracji połączonego z działem. W witrynie Azure Portal nie jest wymagane żadne działanie.

1. Zaloguj się do portalu EA na stronie [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj**.
3. Na karcie **Rejestrowanie** wybierz rejestrację, którą chcesz zarządzać.
4. Wybierz kartę **Dział**, a następnie pozycję **Dodaj administratora**.
5. W oknie Dodawanie administratora działu wybierz typ uwierzytelniania, a następnie wpisz adres e-mail użytkownika.
6. Jeśli użytkownik powinien mieć dostęp tylko do odczytu do danych o kosztach i użyciu, w obszarze **Tylko do odczytu** wybierz opcję **Tak**.  W przeciwnym razie wybierz opcję **Nie**.
7. Wybierz działy, do których chcesz udzielić uprawnień administracyjnych działu.
8. Wybierz pozycję **Dodaj**, aby utworzyć konto.  
    ![Wprowadź wymagane informacje w oknie Dodawanie administratora działu](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Przypisywanie dostępu do zakresu konta rejestracji

Dostęp do zakresu konta rejestracji wymaga dostępu właściciela konta (Wyświetlanie opłat dla właściciela konta) w portalu EA. Właściciel konta może wyświetlać koszty i dane użycia skojarzone z subskrypcjami utworzonymi na podstawie tego konta rejestracji. W witrynie Azure Portal nie jest wymagane żadne działanie.

1. Zaloguj się do portalu EA na stronie [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj**.
3. Na karcie **Rejestrowanie** wybierz rejestrację, którą chcesz zarządzać.
4. Wybierz kartę **Konto**, a następnie pozycję **Dodaj konto**.
5. W oknie Dodawanie konta wybierz **Dział**, z którym chcesz skojarzyć konto, lub pozostaw je nieprzypisane.
6. Wybierz typ uwierzytelniania i wpisz nazwę konta.
7. Wpisz adres e-mail użytkownika, a następnie opcjonalnie wpisz centrum kosztu.
8. Wybierz pozycję **Dodaj**, aby utworzyć konto.  
    ![Wprowadź wymagane informacje w oknie Dodawanie konta dla konta rejestracji](./media/assign-access-acm-data/add-account.png)

Po wykonaniu powyższych czynności konto użytkownika stanie się kontem rejestracji w witrynie Enterprise Portal i będzie mogło tworzyć subskrypcje. Użytkownik może uzyskiwać dostęp do danych o kosztach i użyciu dla utworzonych przez siebie subskrypcji.

## <a name="assign-management-group-scope-access"></a>Przypisywanie dostępu do zakresu grupy zarządzania

Dostęp do wyświetlania zakresu grupy zarządzania wymaga co najmniej uprawnienia Cost Management — czytelnik (lub Czytelnik). W witrynie Azure Portal można skonfigurować uprawnienia dla grupy zarządzania. Musisz mieć co najmniej uprawnienie Administrator dostępu użytkowników (lub Właściciel) dla grupy zarządzania, aby umożliwić dostęp innym osobom. W przypadku kont EA platformy Azure należy również włączyć ustawienie **Wyświetlanie opłat dla właściciela konta** w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Na pasku bocznym wybierz pozycję **Wszystkie usługi**, wyszukaj _grupy zarządzania_, a następnie wybierz pozycję **Grupy zarządzania**.
3. Wybierz grupę zarządzania w hierarchii.
4. Obok nazwy swojej grupy zarządzania wybierz pozycję **Szczegóły**.
5. W lewym okienku wybierz pozycję **Kontrola dostępu (IAM)** .
6. Wybierz pozycję **Dodaj**.
7. W obszarze **Rola** wybierz pozycję **Cost Management — czytelnik**.
8. W obszarze **Przypisz dostęp do** wybierz pozycję **Użytkownik usługi Azure AD, grupa lub aplikacja**.
9. Aby przypisać dostęp, wyszukaj, a następnie wybierz użytkownika.
10. Wybierz pozycję **Zapisz**.  
    ![Przykładowe informacje w oknie Dodawanie uprawnień dla grupy zarządzania](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Przypisywanie dostępu do zakresu subskrypcji

Dostęp do subskrypcji wymaga co najmniej uprawnienia Cost Management — czytelnik (lub Czytelnik). W witrynie Azure Portal można skonfigurować uprawnienia do subskrypcji. Musisz mieć co najmniej uprawnienie Administrator dostępu użytkowników (lub Właściciel) dla subskrypcji, aby umożliwić dostęp innym osobom. W przypadku kont EA platformy Azure należy również włączyć ustawienie **Wyświetlanie opłat dla właściciela konta** w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Na pasku bocznym wybierz pozycję **Wszystkie usługi**, wyszukaj _subskrypcje_, a następnie wybierz pozycję **Subskrypcje**.
3. Wybierz subskrypcję.
4. W lewym okienku wybierz pozycję **Kontrola dostępu (IAM)** .
5. Wybierz pozycję **Dodaj**.
6. W obszarze **Rola** wybierz pozycję **Cost Management — czytelnik**.
7. W obszarze **Przypisz dostęp do** wybierz pozycję **Użytkownik usługi Azure AD, grupa lub aplikacja**.
8. Aby przypisać dostęp, wyszukaj, a następnie wybierz użytkownika.
9. Wybierz pozycję **Zapisz**.

## <a name="assign-resource-group-scope-access"></a>Przypisywanie dostępu do zakresu grupy zasobów

Dostęp do grupy zasobów wymaga co najmniej uprawnienia Cost Management — czytelnik (lub Czytelnik). W witrynie Azure Portal można skonfigurować uprawnienia do grupy zasobów. Musisz mieć co najmniej uprawnienie Administrator dostępu użytkowników (lub Właściciel) dla grupy zasobów, aby umożliwić dostęp innym osobom. W przypadku kont EA platformy Azure należy również włączyć ustawienie **Wyświetlanie opłat dla właściciela konta** w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Na pasku bocznym wybierz pozycję **Wszystkie usługi**, wyszukaj _grupy zasobów_, a następnie wybierz pozycję **Grupy zasobów**.
3. Wybierz swoją grupę zasobów.
4. W lewym okienku wybierz pozycję **Kontrola dostępu (IAM)** .
5. Wybierz pozycję **Dodaj**.
6. W obszarze **Rola** wybierz pozycję **Cost Management — czytelnik**.
7. W obszarze **Przypisz dostęp do** wybierz pozycję **Użytkownik usługi Azure AD, grupa lub aplikacja**.
8. Aby przypisać dostęp, wyszukaj, a następnie wybierz użytkownika.
9. Wybierz pozycję **Zapisz**.

## <a name="cross-tenant-authentication-issues"></a>Problemy z uwierzytelnianiem w wielu dzierżawach

Obecnie usługa Azure Cost Management ma ograniczoną obsługę uwierzytelniania między dzierżawami. W niektórych sytuacjach podczas próby uwierzytelnienia w dzierżawach może zostać wyświetlony błąd **Odmowa dostępu** w analizie kosztów. Ten problem może wystąpić, jeśli konfigurujesz kontrolę dostępu na podstawie ról (RBAC) platformy Azure w ramach subskrypcji innej dzierżawy, a następnie próbujesz wyświetlić dane dotyczące kosztów.

*Aby obejść ten problem*: Po skonfigurowaniu kontroli RBAC na platformie Azure w wielu dzierżawach odczekaj godzinę. Następnie spróbuj wyświetlić koszty w analizie kosztów lub udziel dostępu do usługi Cost Management użytkownikom w obu dzierżawach.  


## <a name="next-steps"></a>Następne kroki

- Jeśli pierwszy przewodnik Szybki start dla usługi Cost Management nie został jeszcze przez Ciebie ukończony, przeczytaj go w temacie [Rozpoczęcie analizowania kosztów](quick-acm-cost-analysis.md).
