---
title: Zasoby, role i kontrola dostępu na platformie Azure Application Insights | Microsoft Docs
description: Właściciele, współautorzy i czytelnicy w sprawie wglądu w dane organizacji.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 1e57af269c4052d0dcd4a8f7970ca23017024299
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79473136"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Zasoby, role i kontrola dostępu w Application Insights

Za pomocą [kontroli dostępu opartej na rolach w Microsoft Azure](../../role-based-access-control/role-assignments-portal.md)można kontrolować, kto ma dostęp do danych w usłudze Azure [Application Insights][start].

> [!IMPORTANT]
> Przypisz dostęp do użytkowników w **grupie zasobów lub subskrypcji** , do której należy zasób aplikacji, a nie z samego zasobu. Przypisz rolę **współautora składnika Application Insights** . Zapewnia to jednolitą kontrolę dostępu do testów i alertów sieci Web wraz z zasobem aplikacji. [Dowiedz się więcej](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Zasoby, grupy i subskrypcje

Najpierw niektóre definicje:

* **Zasób** — wystąpienie usługi Microsoft Azure. Zasób Application Insights zbiera, analizuje i wyświetla dane telemetryczne wysyłane z aplikacji.  Inne typy zasobów platformy Azure obejmują aplikacje sieci Web, bazy danych i maszyny wirtualne.
  
    Aby wyświetlić zasoby, Otwórz [Azure Portal][portal], zaloguj się, a następnie kliknij pozycję wszystkie zasoby. Aby znaleźć zasób, wpisz część jego nazwy w polu filtru.
  
    ![Lista zasobów platformy Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupa zasobów**][group] — każdy zasób należy do jednej grupy. Grupa to wygodny sposób zarządzania powiązanymi zasobami, szczególnie w przypadku kontroli dostępu. Na przykład w jednej grupie zasobów można umieścić aplikację sieci Web, Application Insights zasób do monitorowania aplikacji oraz zasób magazynu, aby zachować eksportowane dane.

* [**Subskrypcja**](https://portal.azure.com) — aby używać Application Insights lub innych zasobów platformy Azure, zaloguj się do subskrypcji platformy Azure. Każda grupa zasobów należy do jednej subskrypcji platformy Azure, w której jest wybierany pakiet cenowy, a jeśli jest to subskrypcja organizacji, wybierz członków i ich uprawnienia dostępu.
* [**Konto Microsoft**][account] — nazwa użytkownika i hasło, których używasz do logowania się w usłudze Microsoft Azure subscriptions, Xbox Live, Outlook.com i innych usługach firmy Microsoft.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a>Kontrola dostępu w grupie zasobów

Ważne jest, aby zrozumieć, że oprócz zasobu utworzonego dla aplikacji istnieją również oddzielne zasoby dla alertów i testów sieci Web. Są one dołączone do tej samej [grupy zasobów](#resource-group) co zasób Application Insights. W tym miejscu mogą być również umieszczone inne usługi platformy Azure, takie jak witryny sieci Web lub magazyn.

W celu kontrolowania dostępu do tych zasobów zaleca się:

* Kontroluj dostęp na poziomie **grupy zasobów lub subskrypcji** .
* Przypisz rolę **współautora składnika Application Insights** do użytkowników. Dzięki temu można edytować testy sieci Web, alerty i zasoby Application Insights, bez zapewniania dostępu do innych usług w grupie.

## <a name="to-provide-access-to-another-user"></a>Aby zapewnić dostęp innemu użytkownikowi

Użytkownik musi mieć uprawnienia właściciela do subskrypcji lub grupy zasobów.

Użytkownik musi mieć [konto Microsoft][account]lub mieć dostęp do jego [organizacyjnego konta Microsoft](../../active-directory/fundamentals/sign-up-organization.md). Możesz zapewnić dostęp do poszczególnych użytkowników, a także do grup użytkowników zdefiniowanych w Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Przejdź do grupy zasobów lub bezpośrednio do samego zasobu

Wybierz pozycję **Kontrola dostępu (IAM)** w menu po lewej stronie.

![Zrzut ekranu przycisku kontroli dostępu w Azure Portal](./media/resources-roles-access-control/0001-access-control.png)

Wybierz pozycję **Dodaj przypisanie roli**

![Zrzut ekranu przedstawiający menu kontroli dostępu z wyróżnionym przyciskiem Dodaj na czerwono](./media/resources-roles-access-control/0002-add.png)

Poniższy widok **dodawania uprawnień** dotyczy głównie zasobów Application Insights, jeśli użytkownik przeglądał uprawnienia kontroli dostępu na wyższych poziomach, takich jak grupy zasobów, zobaczysz dodatkowe role skoncentrowane nieApplication Insights.

Aby wyświetlić informacje o wszystkich wbudowanych rolach kontroli dostępu opartej na rolach platformy Azure, użyj [oficjalnej zawartości referencyjnej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Zrzut ekranu listy roli użytkownika kontroli dostępu](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Wybierz rolę

Tam, gdzie ma to zastosowanie, prowadzimy do powiązanej oficjalnej dokumentacji referencyjnej.

| Rola | W grupie zasobów |
| --- | --- |
| [Właściciel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Może zmienić wszystko, w tym dostęp użytkownika. |
| [Współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Można edytować wszystkie elementy, w tym wszystkie zasoby. |
| [Współautor składnika Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Można edytować zasoby Application Insights. |
| [Czytelnik](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Może wyświetlać, ale nie zmieniać żadnych elementów. |
| [Application Insights Snapshot Debugger](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Nadaje użytkownikowi uprawnienia do korzystania z funkcji Snapshot Debugger Application Insights. Należy zauważyć, że ta rola jest zawarta w roli właściciel lub współautor. |
| Współautor Release Management wdrożenia usługi platformy Azure | Rola współautor dla usług wdrażanych za pomocą usługi Azure Service Deploy. |
| [Przeczyszczanie danych](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Specjalna rola służąca do przeczyszczania danych osobowych. Aby uzyskać więcej informacji, zobacz nasze [wskazówki dotyczące danych osobowych](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) .   |
| ExpressRoute administrator | Można utworzyć usuwanie tras ekspresowych i zarządzać nimi.|
| [Współautor usługi Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Współautor Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; Odczytywanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage; Tworzenie i Konfigurowanie kont usługi Automation; Dodawanie rozwiązań; i Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure.  |
| [Czytelnik usługi Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym Wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. |
| masterreader | Umożliwia użytkownikowi wyświetlanie wszystkiego, ale nie wprowadzaj zmian. |
| [Współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Może odczytywać wszystkie dane monitorowania i aktualizować ustawienia monitorowania.|
| [Wydawca metryk monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Umożliwia publikowanie metryk dla zasobów platformy Azure. |
| [Czytnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Może odczytywać wszystkie dane monitorowania. |
| Współautor zasad zasobów (wersja zapoznawcza) | Wypełnianie użytkowników z umowy EA z prawami do tworzenia/modyfikowania zasad zasobów, tworzenia biletu pomocy technicznej i odczytywania zasobów/hierarchii.  |
| [Administrator dostępu użytkowników](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Umożliwia użytkownikowi zarządzanie dostępem innych użytkowników do zasobów platformy Azure.|
| [Współautor witryny sieci Web](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Umożliwia zarządzanie witrynami sieci Web (nie planami internetowymi), ale nie umożliwia uzyskiwania do nich dostępu.|

Element "Edycja" obejmuje tworzenie, usuwanie i aktualizowanie:

* Zasoby
* Testy sieci Web
* Alerty
* Eksport ciągły

#### <a name="select-the-user"></a>Wybierz użytkownika

Jeśli użytkownik, którego potrzebujesz, nie znajduje się w katalogu, możesz zaprosić każdego z konto Microsoft.
(Jeśli korzystają z usług takich jak Outlook.com, OneDrive, Windows Phone lub XBox Live, mają konto Microsoft).

## <a name="related-content"></a>Zawartość pokrewna

* [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Zapytanie programu PowerShell w celu określenia przynależności do roli

Ponieważ niektóre role mogą być połączone z powiadomieniami i alertami e-mail, może być przydatne do generowania listy użytkowników, którzy należą do danej roli. Aby ułatwić generowanie tych typów list, oferujemy następujące przykładowe zapytania, które można dostosować do własnych potrzeb:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Wykonaj zapytanie dotyczące całej subskrypcji ról administratora + role współautor

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Zapytanie w kontekście określonego Application Insights zasobów dla właścicieli i współautorów

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Zapytanie w kontekście określonej grupy zasobów dla właścicieli i współautorów

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
