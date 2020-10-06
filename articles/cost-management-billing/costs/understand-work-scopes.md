---
title: Opis zakresów usługi Azure Cost Management i praca z nimi
description: Ten artykuł pomaga zrozumieć zakresy zarządzania rozliczeniami i zasobami dostępne na platformie Azure oraz sposób korzystania z zakresów w usłudze Cost Management i interfejsach API.
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 16ddb9cfc2f0731381b1c92d8fdb5f4cffa1ca63
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372361"
---
# <a name="understand-and-work-with-scopes"></a>Opis zakresów i praca z nimi

Ten artykuł pomaga zrozumieć zakresy zarządzania rozliczeniami i zasobami dostępne na platformie Azure oraz sposób korzystania z zakresów w usłudze Cost Management i interfejsach API.

## <a name="scopes"></a>Zakresy

_Zakres_ to węzeł w hierarchii zasobów platformy Azure, w którym użytkownicy usługi Azure AD uzyskują dostęp do usług i zarządzają nimi. Większość zasobów platformy Azure jest tworzonych i wdrażanych w grupach zasobów, które są częścią subskrypcji. Firma Microsoft oferuje również dwie hierarchie powyżej subskrypcji platformy Azure, które mają wyspecjalizowane role do zarządzania danymi rozliczeń:
- Dane dotyczące rozliczeń, takie jak płatności i faktury
- Usługi w chmurze, takie jak zarządzanie kosztami i zasadami

Zakresy to miejsce, w którym zarządzasz danymi dotyczącymi rozliczeń, masz role specyficzne dla płatności, wyświetlasz faktury i ogólnie zarządzasz kontami. Rolami rozliczeń i kont zarządza się oddzielnie od ról służących do zarządzania zasobami, które używają [funkcji RBAC na platformie Azure](../../role-based-access-control/overview.md). Aby wyraźnie odróżnić przeznaczenie poszczególnych zakresów, a w tym różnice w kontroli dostępu, są one określane odpowiednio jako _zakresy rozliczeń_ i _zakresy RBAC platformy Azure_.

Aby dowiedzieć się więcej o zakresach, obejrzyj wideo [Cost Management setting up hierarchies](https://www.youtube.com/watch?v=n3TLRaYJ1NY) (Konfigurowanie hierarchii w usłudze Cost Management). Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>Jak usługa Cost Management używa zakresów

Usługa Cost Management działa we wszystkich zakresach powyżej zasobów, umożliwiając organizacjom zarządzanie kosztami na poziomie, do którego mają dostęp, niezależnie od tego, czy jest to całe konto rozliczeniowe, czy pojedyncza grupa zasobów. Chociaż zakresy rozliczeniowe różnią się w zależności od umowy firmy Microsoft (typu subskrypcji), zakresy RBAC platformy Azure — nie.

## <a name="azure-rbac-scopes"></a>Zakresy RBAC na platformie Azure

Platforma Azure obsługuje trzy zakresy zarządzania zasobami. Każdy zakres obsługuje zarządzanie dostępem i nadzorem, w tym między innymi zarządzanie kosztami.

- [**Grupy zarządzania**](../../governance/management-groups/overview.md) — hierarchiczne kontenery (nawet do ośmiu poziomów) służące do organizowania subskrypcji platformy Azure.

    Typ zasobu: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Subskrypcje** — podstawowe kontenery zasobów platformy Azure.

    Typ zasobu: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Grupy zasobów**](../../azure-resource-manager/management/overview.md#resource-groups) — logiczne grupowania powiązanych zasobów dla rozwiązania platformy Azure, które współużytkują ten sam cykl życia. Na przykład zasoby, które są wdrażane i usuwane razem.

    Typ zasobu: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Grupy zarządzania umożliwiają organizowanie subskrypcji w hierarchię. Przy użyciu grup zarządzania można na przykład utworzyć logiczną hierarchię organizacyjną. Następnie przydzielić zespołom subskrypcje dla obciążeń produkcyjnych oraz deweloperskich/testowych. Po czym utworzyć grupy zasobów w subskrypcjach, aby zarządzać poszczególnymi podsystemami lub składnikami.

Utworzenie hierarchii organizacyjnej pozwala na zachowanie zgodności kosztów i zasad na poziomie organizacyjnym. Każdy lider może wówczas wyświetlać i analizować swoje bieżące koszty. Następnie może tworzyć budżety, aby ograniczać niewłaściwe wzorce wydatków i optymalizować koszty, korzystając z zaleceń usługi Advisor na najniższym poziomie.

Przyznanie dostępu do wyświetlania kosztów i opcjonalnego zarządzania konfiguracją kosztów, na przykład budżetami i eksportami, odbywa się w zakresach nadzoru przy użyciu funkcji RBAC platformy Azure. Funkcja RBAC platformy Azure umożliwia użytkownikom i grupom usługi Azure AD przyznawanie dostępu do wykonywania wstępnie określonego zestawu akcji, które są zdefiniowane w roli w określonym zakresie i poniżej. Na przykład rola przypisana do zakresu grupy zarządzania przyznaje również te same uprawnienia do zagnieżdżonych subskrypcji i grup zasobów.

Usługa Cost Management obsługuje następujące wbudowane role dla poszczególnych zakresów wymienionych poniżej:

- [**Właściciel**](../../role-based-access-control/built-in-roles.md#owner) — może wyświetlać koszty i wszystkim zarządzać, m.in. konfiguracją kosztów.
- [**Współautor**](../../role-based-access-control/built-in-roles.md#contributor) — może wyświetlać koszty i wszystkim zarządzać, m.in. konfiguracją kosztów, ale z wyłączeniem kontroli dostępu.
- [**Czytelnik**](../../role-based-access-control/built-in-roles.md#reader) – może wszystko wyświetlać, m.in. dane dotyczące kosztów i konfigurację, ale nie może wprowadzać żadnych zmian.
- [**Cost Management — współautor**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) — może wyświetlać koszty, zarządzać konfiguracją kosztów i wyświetlać rekomendacje.
- [**Cost Management — czytelnik**](../../role-based-access-control/built-in-roles.md#cost-management-reader) — może wyświetlać dane dotyczące kosztów, konfigurację kosztów i rekomendacje.

Cost Management — współautor jest zalecaną rolą o najniższych uprawnieniach. Umożliwia ona użytkownikom tworzenie budżetów i eksportów oraz zarządzanie nimi w celu skuteczniejszego monitorowania i tworzenia raportów dotyczących kosztów. Użytkownicy mający rolę Cost Management — współautor mogą również wymagać dodatkowych ról w celu obsługi kompleksowych scenariuszy zarządzania kosztami. Poniżej przedstawiono przykładowe scenariusze:

- **Raportowanie użycia zasobów** — koszty z usługi Azure Cost Management można znaleźć w witrynie Azure Portal. Obejmuje ona dane dotyczące użycia związanego z kosztami we wzorcach pełnego użycia. Ten raport może też obejmować opłaty za interfejs API i pobieranie, ale dobrym rozwiązaniem może być też wyświetlenie szczegółowych metryk użycia w usłudze Azure Monitor, co pozwoli dokładniej poznać dane. Rozważ przyznanie roli [Czytelnika monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader) również w przypadku każdego zakresu, który wymaga tworzenia raportów dotyczących szczegółowych metryk użycia.
- **Działanie w przypadku przekroczenia budżetów** — użytkownicy mający rolę Cost Management — współautor potrzebują również dostępu do tworzenia grup akcji i zarządzania nimi w celu automatycznego reagowania na nadwyżki. Rozważ przyznanie roli [Współautor monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-contributor) do grupy zasobów zawierającej grupę akcji, która ma być używana w przypadku przekroczenia progów budżetu. Automatyzacja określonych akcji wymaga dodatkowych ról dla określonych usług, które są używane, takich jak Automation i Azure Functions.
- **Planowanie eksportu danych dotyczących kosztów** — użytkownicy mający rolę Cost Management — współautor potrzebują także dostępu do zarządzania kontami magazynu, aby zaplanować eksport w celu skopiowania danych na konto magazynu. Rozważ przyznanie roli [Współautor konta magazynu](../../role-based-access-control/built-in-roles.md#storage-account-contributor) do grupy zasobów zawierającej konto magazynu, do którego są eksportowane dane dotyczące kosztów.
- **Wyświetlanie rekomendacji dotyczących obniżenia kosztów** — użytkownicy mający rolę Cost Management — czytelnik i Cost Management — współautor domyślnie mają dostęp do *wyświetlania* rekomendacji dotyczących kosztów. Jednak dostęp do działania zgodnie z rekomendacjami dotyczącymi kosztów wymaga dostępu do poszczególnych zasobów. Jeśli chcesz skorzystać z rekomendacji dotyczącej kosztów, rozważ przyznanie [roli specyficznej dla usługi](../../role-based-access-control/built-in-roles.md#all).

Grupy zarządzania są obsługiwane tylko wtedy, gdy zawierają subskrypcje z umową Enterprise, z płatnością zgodnie z rzeczywistym użyciem lub wewnętrzne subskrypcje firmy Microsoft. Grupy zarządzania z innymi typami subskrypcji, takimi jak umowa z Klientem Microsoft czy subskrypcja usługi Azure Active Directory, nie obsługują wyświetlania kosztów. Jeśli masz subskrypcje różnych typów, przenieś nieobsługiwane subskrypcje do oddzielnego odgałęzienia hierarchii grup zarządzania, aby umożliwić korzystanie z usługi Cost Management z obsługiwanymi subskrypcjami. Możesz na przykład utworzyć dwie grupy zarządzania w ramach głównej grupy zarządzania: **Azure AD** i **Moja organizacja**. Przenieś subskrypcję usługi Azure AD do grupy zarządzania **Azure AD**, a następnie wyświetlaj koszty i zarządzaj nimi w grupie zarządzania **Moja organizacja**.

## <a name="enterprise-agreement-scopes"></a>Zakresy umowy Enterprise Agreement

Konta rozliczeniowe umowy Enterprise Agreement (EA), nazywane również rejestracjami, mają następujące zakresy:

- [**Konto rozliczeniowe**](../manage/view-all-accounts.md) — reprezentuje rejestrację umowy EA. W tym zakresie są generowane faktury. Zakupy, które nie są oparte na użyciu, na przykład zakupy w witrynie Marketplace i rezerwacje, są dostępne tylko w tym zakresie. Nie są one reprezentowane w działach ani na kontach rejestracji. Użycie rezerwacji, a także całe pozostałe użycie, jest stosowane do poszczególnych zasobów. Użycie jest sumowane dla subskrypcji w ramach konta rozliczeniowego. Aby zobaczyć koszty rezerwacji podzielone na poszczególne zasoby, należy przełączyć się do widoku **Amortyzowany koszt** w analizie kosztów.

    Typ zasobu: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Dział** — opcjonalne zgrupowanie kont rejestracji.

    Typ zasobu: `Billing/billingAccounts/departments`

- **Konto rejestracji** — reprezentuje właściciela pojedynczego konta. Nie obsługuje udzielania dostępu wielu osobom.

    Typ zasobu: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Chociaż zakresy nadzoru są powiązane z pojedynczym katalogiem, zakresy rozliczeniowe umów EA nie są. Konto rozliczeniowe umowy EA może mieć subskrypcje w dowolnej liczbie katalogów usługi Azure AD.

Zakresy rozliczeniowe umowy EA obsługują następujące role:

- **Administrator przedsiębiorstwa** — może zarządzać ustawieniami konta rozliczeniowego i dostępem, może wyświetlać wszystkie koszty i może zarządzać konfiguracją kosztów. Przykład: budżety i eksporty. Zakres rozliczeniowy umowy EA działa tak samo jak [rola Cost Management — współautor funkcji na platformie Azure](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Użytkownik przedsiębiorstwa tylko do odczytu** — może wyświetlać ustawienia kont rozliczeniowych, dane dotyczące kosztów i konfigurację kosztów. Przykład: budżety i eksporty. Zakres rozliczeniowy umowy EA działa tak samo jak [rola Cost Management — czytelnik funkcji na platformie Azure](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrator działu** — może zarządzać ustawieniami działu, takimi jak centrum kosztów, może uzyskiwać dostęp do wszystkich kosztów i wyświetlać je oraz zarządzać konfiguracją kosztów. Przykład: budżety i eksporty.  Aby administratorzy działów i użytkownicy tylko do odczytu mogli wyświetlać koszty, musi być włączone ustawienie konta rozliczeniowego **Wyświetlanie opłat przez administratora działu**. Jeśli opcja **Wyświetlanie opłat przez administratora działu** jest wyłączona, użytkownicy działu nie widzą kosztów na żadnym poziomie, nawet jeśli są właścicielami konta lub subskrypcji.
- **Użytkownik działu tylko do odczytu** — może wyświetlać ustawienia działu, dane dotyczące kosztów i konfigurację kosztów. Przykład: budżety i eksporty. Jeśli opcja **Wyświetlanie opłat przez administratora działu** jest wyłączona, użytkownicy działu nie widzą kosztów na żadnym poziomie, nawet jeśli są właścicielami konta lub subskrypcji.
- **Właściciel konta** — może zarządzać ustawieniami konta rejestracji (na przykład centrum kosztów), wyświetlać wszystkie koszty i zarządzać konfiguracją kosztów (na przykład budżetami i eksportami) dla konta rejestracji. Aby właściciele kont i użytkownicy systemu RBAC platformy Azure mogli wyświetlać koszty, musi być włączone ustawienie konta rozliczeniowego **Wyświetlanie opłat przez właściciela konta**.

Użytkownicy konta rozliczeniowego umowy EA nie mają bezpośredniego dostępu do faktur. Faktury są dostępne z poziomu zewnętrznego systemu licencjonowania zbiorowego.

Subskrypcje platformy Azure są zagnieżdżone w obszarze kont rejestracji. Użytkownicy rozliczeń mają dostęp do danych dotyczących kosztów dla subskrypcji i grup zasobów, które znajdują się w odpowiednich, należących do nich zakresach. Nie mają dostępu do wyświetlania zasobów w witrynie Azure Portal ani zarządzania nimi. Użytkownicy mogą wyświetlać koszty, przechodząc do pozycji **Zarządzanie kosztami i rozliczenia** na liście usług witryny Azure Portal. Następnie mogą filtrować koszty, aby uzyskać dostęp do określonych subskrypcji i grup zasobów, których potrzebują do raportowania.

Użytkownicy rozliczeń nie mają dostępu do grup zarządzania, ponieważ nie należą one jawnie do określonego konta rozliczeniowego. Dostęp do grup zarządzania musi być przyznany jawnie. Grupy zarządzania gromadzą koszty ze wszystkich zagnieżdżonych subskrypcji. Jednak obejmują one tylko zakupy oparte na użyciu. Nie obejmują one zakupów, takich jak rezerwacje i oferty innych firm w witrynie Marketplace. Aby wyświetlić te koszty, użyj konta rozliczeniowego umowy EA.

## <a name="individual-agreement-scopes"></a>Zakresy umów indywidualnych

Subskrypcje platformy Azure utworzone na podstawie indywidualnych ofert, takich jak płatność zgodnie z rzeczywistym użyciem, i powiązane typy, takie jak bezpłatna wersja próbna i oferty deweloperskie/testowe, nie mają jawnego zakresu konta rozliczeniowego. Zamiast tego każda subskrypcja ma właściciela konta lub administratora konta, takiego jak właściciel konta EA.

- [**Konto rozliczeniowe**](../manage/view-all-accounts.md) — reprezentuje właściciela pojedynczego konta dla co najmniej jednej subskrypcji platformy Azure. Obecnie nie obsługuje udzielania dostępu wielu osobom ani dostępu do widoków zagregowanych kosztów.

    Typ zasobu: Nie dotyczy

Administratorzy indywidualnych konta subskrypcji platformy Azure mogą wyświetlać dane dotyczące rozliczeń, takie jak faktury i płatności, a także zarządzać nimi w [Centrum konta platformy Azure](https://account.azure.com/subscriptions). Jednak nie mogą wyświetlać danych dotyczących kosztów ani zarządzać zasobami w witrynie Azure Portal. Aby udzielić dostępu administratorowi konta, użyj wspomnianych wcześniej ról zarządzania kosztami.

W odróżnieniu od umowy EA administratorzy indywidualnych kont subskrypcji platformy Azure mogą zobaczyć swoje faktury w witrynie Azure Portal. Pamiętaj, że role Cost Management — czytelnik i Cost Management — współautor nie zapewniają dostępu do faktur. Aby uzyskać więcej informacji, zobacz [Jak nadać dostęp do faktur](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Zakresy umowy klienta firmy Microsoft

Konta rozliczeniowe umowy klienta firmy Microsoft mają następujące zakresy:

- **Konto rozliczeniowe** — reprezentuje umowę klienta dla wielu produktów i usług firmy Microsoft. Z punktu widzenia funkcjonalności konta rozliczeniowe umowy klienta nie są tym samym co rejestracje EA. Rejestracje EA są ściślej dopasowane do profilów rozliczeniowych.

    Typ zasobu: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profil rozliczeniowy** — definiuje subskrypcje uwzględnione na fakturze. Profile rozliczeniowe to funkcjonalny odpowiednik rejestracji umowy EA, ponieważ jest to zakres, w którym są generowane faktury. Podobnie zakupy, które nie są oparte na użyciu (na przykład zakupy w witrynie Marketplace i rezerwacje), są dostępne tylko w tym zakresie. Nie znajdują się one w sekcjach faktur.

    Typ zasobu: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Sekcja faktury** — reprezentuje grupę subskrypcji na fakturze lub w profilu rozliczeniowym. Sekcje faktur przypominają działy — dostęp do nich może mieć wiele osób.

    Typ zasobu: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Klient** — reprezentuje grupę subskrypcji skojarzonych z określonym klientem, który został dołączony do umowy klienta firmy Microsoft przez partnera. Ten zakres jest specyficzny dla dostawców rozwiązań w chmurze (CSP).

W odróżnieniu od zakresów rozliczeniowych umowy EA, konta rozliczeniowe umowy klienta _są_ powiązane z pojedynczym katalogiem i nie mogą mieć subskrypcji w wielu katalogach usługi Azure AD.

Zakresy rozliczeniowe umowy klienta nie dotyczą partnerów. Role i uprawnienia partnerów zostały udokumentowane w artykule [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

Zakresy rozliczeniowe umowy klienta obsługują następujące role:

- **Właściciel** — może zarządzać ustawieniami rozliczeń i dostępem, wyświetlać wszystkie koszty i zarządzać konfiguracją kosztów. Przykład: budżety i eksporty. Ten zakres rozliczeniowy umowy klienta działa tak samo jak [rola Cost Management — współautor funkcji na platformie Azure](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Współautor** — może zarządzać ustawieniami rozliczeń z wyjątkiem dostępu, wyświetlać wszystkie koszty i zarządzać konfiguracją kosztów. Przykład: budżety i eksporty. Ten zakres rozliczeniowy umowy klienta działa tak samo jak [rola Cost Management — współautor funkcji na platformie Azure](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Czytelnik** — może wyświetlać ustawienia rozliczeń, dane dotyczące kosztów i konfigurację kosztów. Przykład: budżety i eksporty. Ten zakres rozliczeniowy umowy klienta działa tak samo jak [rola Cost Management — czytelnik funkcji na platformie Azure](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Menedżer faktur** — może wyświetlać i opłacać faktury oraz może wyświetlać konfigurację i dane dotyczące kosztów. Przykład: budżety i eksporty. Ten zakres rozliczeniowy umowy klienta działa tak samo jak [rola Cost Management — czytelnik funkcji na platformie Azure](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Kreator subskrypcji platformy Azure** — może tworzyć subskrypcje platformy Azure, wyświetlać koszty i zarządzać konfiguracją kosztów. Przykład: budżety i eksporty. Ten zakres rozliczeniowy umowy klienta działa tak samo jak rola właściciela konta rejestracji umowy EA.

Subskrypcje platformy Azure są zagnieżdżone w sekcjach faktury, podobnie jak w przypadku kont rejestracji umowy EA. Użytkownicy rozliczeń mają dostęp do danych dotyczących kosztów dla subskrypcji i grup zasobów, które znajdują się w odpowiednich, należących do nich zakresach. Jednak nie mają dostępu do wyświetlania zasobów w witrynie Azure Portal ani zarządzania nimi. Użytkownicy rozliczeń mogą wyświetlać koszty, przechodząc do pozycji **Zarządzanie kosztami i rozliczenia** na liście usług witryny Azure Portal. Następnie filtrują koszty, aby uzyskać dostęp do określonych subskrypcji i grup zasobów, których potrzebują do raportowania.

Użytkownicy rozliczeń nie mają dostępu do grup zarządzania, ponieważ nie należą one jawnie do konta rozliczeniowego. Jednak po włączeniu grup zarządzania dla organizacji wszystkie koszty subskrypcji są gromadzone na koncie rozliczeniowym i w głównej grupie zarządzania, ponieważ są one ograniczone do pojedynczego katalogu. Grupy zarządzania obejmują tylko zakupy, które są oparte na użyciu. Zakupy, takie jak rezerwacje i oferty w innych firm w witrynie Marketplace, nie są uwzględniane w grupach zarządzania. W związku z tym konto rozliczeniowe i główna grupa zarządzania mogą raportować różne sumy. Aby wyświetlić te koszty, użyj konta rozliczeniowego lub odpowiedniego profilu rozliczeniowego.

## <a name="aws-scopes"></a>Zakresy platformy AWS

Po zakończeniu integracji z platformą AWS zobacz [Konfigurowanie integracji z platformą AWS](aws-integration-set-up-configure.md). Dostępne są następujące zakresy:

- **Zewnętrzne konto rozliczeniowe** — reprezentuje umowę klienta z innym dostawcą. Przypomina to konto rozliczeniowe umowy EA.

    Typ zasobu: `Microsoft.CostManagement/externalBillingAccounts`

- **Subskrypcja zewnętrzna** — reprezentuje konto operacyjne klienta u innego dostawcy. Przypomina to subskrypcję platformy Azure.

    Typ zasobu: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Zakresy dostawcy rozwiązań w chmurze (CSP)

W przypadku dostawców rozwiązań w chmurze mających klientów z umową klienta firmy Microsoft obsługiwane są następujące zakresy:

- **Konto rozliczeniowe** — reprezentuje umowę klienta dla wielu produktów i usług firmy Microsoft. Z punktu widzenia funkcjonalności konta rozliczeniowe umowy klienta nie są tym samym co rejestracje EA. Rejestracje EA są ściślej dopasowane do profilów rozliczeniowych.

    Typ zasobu: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profil rozliczeniowy** — definiuje subskrypcje uwzględnione na fakturze. Profile rozliczeniowe to funkcjonalny odpowiednik rejestracji umowy EA, ponieważ jest to zakres, w którym są generowane faktury. Podobnie zakupy, które nie są oparte na użyciu (na przykład zakupy w witrynie Marketplace i rezerwacje), są dostępne tylko w tym zakresie.

    Typ zasobu: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Klient** — reprezentuje grupę subskrypcji skojarzonych z określonym klientem, który został dołączony do umowy klienta firmy Microsoft przez partnera.

Tylko użytkownicy mający role *Administrator globalny* i *Agent administratora* mogą wyświetlać koszty dla kont rozliczeniowych, profilów rozliczeniowych i klientów bezpośrednio w dzierżawie platformy Azure partnera oraz zarządzać nimi. Aby uzyskać więcej informacji na temat ról Centrum partnerskiego, zobacz [Przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

Usługa Azure Cost Management obsługuje klientów partnerów CSP tylko w przypadku, gdy mają oni umowę klienta firmy Microsoft. W przypadku klientów obsługiwanych przez dostawców rozwiązań w chmurze, którzy nie mają jeszcze umowy klienta firmy Microsoft, zobacz [Centrum partnerskie](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

Grupy zarządzania w zakresach dostawców rozwiązań w chmurze nie są obsługiwane w usłudze Cost Management. Jeśli masz subskrypcję dostawcy CSP i w analizie kosztów ustawisz zakres grupy zarządzania, zostanie wyświetlony błąd podobny do następującego:

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>Przełączanie między zakresami w usłudze Cost Management

W lewym górnym rogu wszystkich widoków usługi Cost Management w witrynie Azure Portal znajduje się element wyboru **Zakres**. Umożliwia on szybką zmianę zakresu. Wybierz kapsułkę **Zakres**, aby otworzyć selektor zakresów. Będą w nim wyświetlane konta rozliczeniowe, główna grupa zarządzania i wszystkie subskrypcje, które nie są zagnieżdżone w głównej grupie zarządzania. W celu określenia zakresu wybierz tło, aby je wyróżnić, a następnie wybierz pozycję **Wybierz** u dołu. Aby przejść do szczegółów zagnieżdżonych zakresów, takich jak grupy zasobów w subskrypcji, wybierz link z nazwą zakresu. Aby wybrać zakres nadrzędny na dowolnym zagnieżdżonym poziomie, wybierz pozycję **Wybierz ten &lt;zakres&gt;** u góry selektora zakresów.

## <a name="identify-the-resource-id-for-a-scope"></a>Określanie identyfikatora zasobu dla zakresu

Podczas pracy z interfejsami API usługi Cost Management znajomość zakresu jest kluczowa. Poniższe informacje umożliwiają utworzenie prawidłowego identyfikatora URI zakresu dla interfejsów API usługi Cost Management.

### <a name="billing-accounts"></a>Konta rozliczeniowe

1. Otwórz witrynę Azure Portal, a następnie przejdź do pozycji **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W menu konta rozliczeniowego wybierz pozycję **Właściwości**.
3. Skopiuj identyfikator konta rozliczeniowego.
4. Twój zakres to: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Profile rozliczeniowe

1. Otwórz witrynę Azure Portal, a następnie przejdź do pozycji **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W menu konta rozliczeniowego wybierz pozycję **Profile rozliczeniowe**.
3. Wybierz nazwę profilu rozliczeniowego.
4. W menu profilu rozliczeniowego wybierz pozycję **Właściwości**.
5. Skopiuj identyfikatory konta rozliczeniowego i profilu rozliczeniowego.
6. Twój zakres to: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Sekcje faktury

1. Otwórz witrynę Azure Portal, a następnie przejdź do pozycji **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W menu konta rozliczeniowego wybierz pozycję **Sekcje faktur**.
3. Wybierz nazwę sekcji faktury.
4. W menu sekcji faktury wybierz pozycję **Właściwości**.
5. Skopiuj identyfikatory konta rozliczeniowego i sekcji faktury.
6. Twój zakres to: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Działy umowy EA

1. Otwórz witrynę Azure Portal, a następnie przejdź do pozycji **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W menu konta rozliczeniowego wybierz pozycję **Działy**.
3. Wybierz nazwę działu.
4. W menu działu wybierz pozycję **Właściwości**.
5. Skopiuj identyfikatory konta rozliczeniowego i działu.
6. Twój zakres to: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Konto rejestracji umowy EA

1. Otwórz witrynę Azure Portal i przejdź do pozycji **Zarządzanie kosztami i rozliczenia** na liście usług.
2. W menu konta rozliczeniowego wybierz pozycję **Konta rejestracji**.
3. Wybierz nazwę konta rejestracji.
4. W menu konta rejestracji wybierz pozycję **Właściwości**.
5. Skopiuj identyfikatory konta rozliczeniowego i konta rejestracji.
6. Twój zakres to: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Grupa zarządzania

1. Otwórz witrynę Azure Portal i przejdź do pozycji **Grupy zarządzania** na liście usług.
2. Przejdź do grupy zarządzania.
3. Skopiuj identyfikator grupy zarządzania z tabeli.
4. Twój zakres to: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subskrypcja

1. Otwórz witrynę Azure Portal i przejdź do pozycji **Subskrypcje** na liście usług.
2. Skopiuj identyfikator subskrypcji z tabeli.
3. Twój zakres to: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Grupy zasobów

1. Otwórz witrynę Azure Portal i przejdź do pozycji **Grupy zasobów** na liście usług.
2. Wybierz nazwę grupy zasobów.
3. W menu grupy zasobów wybierz pozycję **Właściwości**.
4. Skopiuj wartość pola identyfikatora zasobu.
5. Twój zakres to: `"/subscriptions/{id}/resourceGroups/{name}"`

Usługa Cost Management jest obecnie obsługiwana na platformach [Azure Global](https://management.azure.com) i [Azure Government](https://management.usgovcloudapi.net). Aby uzyskać więcej informacji na temat platformy Azure Government, zobacz [Punkty końcowe interfejsów API platform Azure Global i Government](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping).

## <a name="next-steps"></a>Następne kroki

- Jeśli pierwszy przewodnik Szybki start dla usługi Cost Management nie został jeszcze przez Ciebie ukończony, przeczytaj go w temacie [Rozpoczęcie analizowania kosztów](quick-acm-cost-analysis.md).
