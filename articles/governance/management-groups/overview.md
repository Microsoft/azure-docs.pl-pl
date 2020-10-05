---
title: Organizowanie zasobów przy użyciu grup zarządzania — Zarządzanie platformą Azure
description: Dowiedz się więcej na temat grup zarządzania, sposobu działania ich uprawnień i korzystania z nich.
ms.date: 09/22/2020
ms.topic: overview
ms.custom: contperfq1
ms.openlocfilehash: e3bc3ee34227fd23ea9f56070f8ea7776a10a134
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91533810"
---
# <a name="what-are-azure-management-groups"></a>Co to są grupy zarządzania platformy Azure?

Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje warunki nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą warunki zastosowane do tej grupy zarządzania. Grupy zarządzania umożliwiają zarządzanie klasy korporacyjnej na dużą skalę niezależnie od typu subskrypcji.
Wszystkie subskrypcje w ramach jednej grupy zarządzania muszą ufać tej samej dzierżawie usługi Azure Active Directory.

Na przykład możesz zastosować do grupy zarządzania zasady ograniczające regiony dostępne na potrzeby tworzenia maszyny wirtualnej. Te zasady będą stosowane do wszystkich grup zarządzania, subskrypcji i zasobów w ramach tej grupy zarządzania, zezwalając na tworzenie maszyn wirtualnych tylko w tym regionie.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Hierarchia grup zarządzania i subskrypcji

Można utworzyć elastyczną strukturę grup zarządzania i subskrypcji w celu organizowania zasobów w hierarchię na potrzeby ujednoliconego zarządzania zasadami i dostępem. Na poniższym diagramie przedstawiono przykład tworzenia hierarchii dla nadzoru przy użyciu grup zarządzania.

:::image type="complex" source="./media/tree.png" alt-text="Diagram hierarchii przykładowej grupy zarządzania." border="false":::
   Diagram głównej grupy zarządzania przechowującej zarówno grupy zarządzania, jak i subskrypcje. W przypadku niektórych podrzędnych grup zarządzania są przechowywane grupy zarządzania, pewne subskrypcje i pewne. Jednym z przykładów w hierarchii przykładowej są cztery poziomy grup zarządzania z poziomem podrzędnym wszystkich subskrypcji.
:::image-end:::

Możesz utworzyć hierarchię stosującą zasady, na przykład ograniczające lokalizacje maszyn wirtualnych do regionu Zachodnie stany USA w grupie o nazwie „Produkcja”. Te zasady będą dziedziczone przez wszystkie subskrypcje umowy Enterprise Agreement (EA), które są elementami podrzędnymi tej grupy zarządzania i będą stosowane do wszystkich maszyn wirtualnych w ramach tych subskrypcji. Te zasady zabezpieczeń nie mogą zostać zmienione przez właściciela zasobu lub subskrypcji, co zapewnia ulepszony nadzór.

Innym scenariuszem, w którym można użyć grup zarządzania, jest zapewnienie użytkownikom dostępu do wielu subskrypcji. Przenosząc wiele subskrypcji w ramach tej grupy zarządzania, można utworzyć jedno [przypisanie roli platformy Azure](../../role-based-access-control/overview.md) w grupie zarządzania, co spowoduje dziedziczenie dostępu do wszystkich subskrypcji. Jednym przypisaniem w grupie zarządzania może być umożliwienie użytkownikom dostępu do wszystkiego, czego potrzebują, zamiast tworzenia skryptów RBAC na platformie Azure w różnych subskrypcjach.

### <a name="important-facts-about-management-groups"></a>Ważne fakty dotyczące grup zarządzania

- W jednym katalogu może być obsługiwane 10000 grup zarządzania.
- Drzewo grupy zarządzania może obsługiwać maksymalnie sześć poziomów głębokości.
  - Ten limit nie obejmuje poziomu głównego lub poziomu subskrypcji.
- Każda grupa zarządzania i subskrypcja może obsługiwać tylko jeden element nadrzędny.
- Każda grupa zarządzania może mieć wiele elementów podrzędnych.
- Wszystkie subskrypcje i grupy zarządzania znajdują się w jednej hierarchii w każdym katalogu. Zobacz [Ważne informacje dotyczące głównej grupy zarządzania](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Główna grupa zarządzania dla każdego katalogu

Do każdego katalogu jest przypisywana jedna grupa zarządzania najwyższego poziomu nazywana „główną” grupą zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Ta główna Grupa zarządzania pozwala na stosowanie zasad globalnych i przypisań ról platformy Azure na poziomie katalogu. [Administrator globalny usługi Azure AD musi podnieść swój poziom uprawnień](../../role-based-access-control/elevate-access-global-admin.md) do roli Administrator dostępu użytkowników, aby początkowo być właścicielem tej głównej grupy. Po podwyższeniu poziomu dostępu administrator może przypisać dowolną rolę platformy Azure do innych użytkowników lub grup katalogu w celu zarządzania hierarchią. Jako administrator możesz przypisać własne konto jako właściciela głównej grupy zarządzania.

### <a name="important-facts-about-the-root-management-group"></a>Ważne informacje dotyczące głównej grupy zarządzania

- Domyślnie nazwa wyświetlana głównej grupy zarządzania to **Grupa główna dzierżawy**. Identyfikatorem jest identyfikator usługi Azure Active Directory.
- Aby zmienić nazwę wyświetlaną, Twoje konto musi mieć przypisaną rolę właściciela lub współautora w głównej grupie zarządzania. Aby zaktualizować nazwę grupy zarządzania, zobacz [zmiana nazwy grupy zarządzania](manage.md#change-the-name-of-a-management-group) .
- Głównej grupy zarządzania nie można przenieść ani usunąć, w odróżnieniu od innych grup zarządzania.  
- Wszystkie subskrypcje i grupy zarządzania składają się do jednej głównej grupy zarządzania w katalogu.
  - Wszystkie zasoby w katalogu składają się do głównej grupy zarządzania dla globalnego zarządzania.
  - Nowe subskrypcje są automatycznie domyślnie dodawane do głównej grupy zarządzania podczas tworzenia.
- Główna grupa zarządzania jest widoczna dla wszystkich klientów platformy Azure, ale nie wszyscy klienci mają dostęp do zarządzania tą główną grupą zarządzania.
  - Każdy, kto ma dostęp do subskrypcji, może zobaczyć kontekst, w którym ta subskrypcja znajduje się w hierarchii.  
  - Nikt nie otrzymuje domyślnego dostępu do głównej grupy zarządzania. Administratorzy globalni usługi Azure AD są jedynymi użytkownikami, którzy mogą samodzielnie podnieść swój poziom uprawnień, aby uzyskać dostęp. Po uzyskaniu dostępu do głównej grupy zarządzania administratorzy globalni mogą przypisywać dowolną rolę platformy Azure do innych użytkowników w celu zarządzania nimi  
    go.
- W zestawie SDK, główna Grupa zarządzania lub "główny dzierżawca" działa jako Grupa zarządzania.

> [!IMPORTANT]
> Wszystkie przypisania dostępu użytkowników lub zasad w głównej grupie zarządzania **dotyczą wszystkich zasobów w katalogu**. W związku z tym wszyscy klienci powinni ocenić potrzebę posiadania elementów zdefiniowanych w tym zakresie. Uprawnienia dostępu użytkowników i zasad powinny mieć wartość "musi mieć" tylko w tym  
> Scope.

## <a name="initial-setup-of-management-groups"></a>Konfiguracja początkowa grup zarządzania

Kiedy dowolny użytkownik rozpoczyna korzystanie z grup zarządzania, wykonywany jest proces konfiguracji początkowej. Pierwszym jego krokiem jest utworzenie głównej grupy zarządzania w katalogu. Po utworzeniu tej grupy wszystkie istniejące subskrypcje, które znajdują się w katalogu, stają się elementami podrzędnymi głównej grupy zarządzania.
Celem tego procesu jest upewnienie się, że istnieje tylko jedna hierarchia grup zarządzania w katalogu. Pojedyncza hierarchia w katalogu umożliwia klientom administracyjnym klientom stosowanie globalnego dostępu i zasad, których inni klienci w katalogu nie mogą obejść. Wszystkie przypisania w głównej grupie będą stosowane do całej hierarchii, która obejmuje wszystkie grupy zarządzania, subskrypcje, grupy zasobów i zasoby w dzierżawie usługi Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Problemy z wyświetlaniem wszystkich subskrypcji

W przypadku niektórych katalogów, które rozpoczęły korzystanie z grup zarządzania niedługo po udostępnieniu wersji zapoznawczej (przed 25 czerwca 2018 r.), mógł wystąpić problem polegający na tym, że nie wszystkie subskrypcje zostały umieszczone w hierarchii. Proces umieszczania subskrypcji w hierarchii był implementowany po przypisaniu roli lub zasad w głównej grupie zarządzania w katalogu.

### <a name="how-to-resolve-the-issue"></a>Jak rozwiązać ten problem

Istnieją dwie opcje rozwiązania tego problemu.

- Usuń wszystkie przypisania ról i zasad z głównej grupy zarządzania
  - Usunięcie wszystkich zasad i przypisań ról z głównej grupy zarządzania powoduje, że usługa uzupełnia wszystkie subskrypcje w hierarchii do następnego noclegowego cyklu. Ten proces ma na celu zapewnienie, że nie został przypadkowo udzielony dostęp lub nie przypisano przypadkowo zasad do wszystkich subskrypcji dzierżawy.
  - Najlepszym sposobem wykonania tego procesu bez wpływu na usługi jest zastosowanie przypisań ról lub zasad na poziomie o jeden niższym niż główna grupa zarządzania. Wtedy będzie można usunąć wszystkie przypisania z zakresu głównego.
- Bezpośrednio wywołaj interfejs API, aby rozpocząć proces wypełniania
  - Każdy klient w katalogu może wywołać interfejs API _TenantBackfillStatusRequest_ lub _StartTenantBackfillRequest_. Wywołanie interfejsu API StartTenantBackfillRequest powoduje rozpoczęcie procesu początkowej konfiguracji, który polega na przeniesieniu wszystkich subskrypcji do hierarchii. Ten proces rozpoczyna również wymuszanie dodawania wszystkich nowych subskrypcji jako elementu podrzędnego głównej grupy zarządzania.
    Ten proces można wykonać bez zmiany przydziałów na poziomie głównej grupy. Wywołując ten interfejs API, potwierdzasz, że dowolne zasady i przypisania dostępu określone w grupie głównej mogą być stosowane do wszystkich subskrypcji.

Jeśli masz pytania na temat tego procesu wypełniania, napisz na adres `managementgroups@microsoft.com`
  
## <a name="management-group-access"></a>Dostęp do grupy zarządzania

Grupy zarządzania platformy Azure obsługują [kontrolę dostępu opartą na rolach (Azure RBAC)](../../role-based-access-control/overview.md) dla wszystkich dostępu do zasobów i definicji ról.
Te uprawnienia są dziedziczone do zasobów podrzędnych, które istnieją w hierarchii. Każdą rolę platformy Azure można przypisać do grupy zarządzania, która będzie dziedziczyć hierarchię do zasobów. Na przykład współautor maszyny wirtualnej roli platformy Azure można przypisać do grupy zarządzania. Ta rola nie ma żadnej akcji wykonywanej na grupie zarządzania, ale będzie dziedziczona do wszystkich maszyn wirtualnych w ramach tej grupy zarządzania.

Na poniższym wykresie przedstawiono listę ról i obsługiwane akcje na grupach zarządzania.

| Nazwa roli platformy Azure             | Utwórz | Zmień nazwę | Przenieś\*\* | Usuwanie | Przypisywanie dostępu | Przypisywanie zasad | Odczyt  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Właściciel                       | X      | X      | X        | X      | X             | X             | X     |
|Współautor                 | X      | X      | X        | X      |               |               | X     |
|Współautor MG\*            | X      | X      | X        | X      |               |               | X     |
|Czytelnik                      |        |        |          |        |               |               | X     |
|Czytnik MG\*                 |        |        |          |        |               |               | X     |
|Współautor zasad zasobów |        |        |          |        |               | X             |       |
|Administrator dostępu użytkowników   |        |        |          |        | X             | X             |       |

\*: W przypadku współautora i oddziału MG można zezwolić użytkownikom na wykonywanie tych działań tylko w zakresie grupy zarządzania.  
\*\*: Przypisania ról w głównej grupie zarządzania nie są wymagane do przeniesienia subskrypcji lub grupy zarządzania do i z niej. Aby uzyskać szczegółowe informacje o przenoszeniu elementów w hierarchii, zobacz [Zarządzanie zasobami przy użyciu grup zarządzania](manage.md).

## <a name="azure-custom-role-definition-and-assignment"></a>Definiowanie i przypisywanie roli niestandardowej platformy Azure

Obsługa ról niestandardowych platformy Azure dla grup zarządzania jest obecnie w wersji zapoznawczej z pewnymi [ograniczeniami](#limitations). Zakres grupy zarządzania można zdefiniować w możliwym do przypisania zakresie definicji roli. Rola niestandardowa platformy Azure będzie następnie dostępna do przypisywania do tej grupy zarządzania oraz dla każdej grupy zarządzania, subskrypcji, grupy zasobów lub zasobu. Ta rola niestandardowa będzie dziedziczyć hierarchię tak jak każda wbudowana rola.  

### <a name="example-definition"></a>Przykładowa definicja

[Definiowanie i tworzenie roli niestandardowej](../../role-based-access-control/custom-roles.md) nie zmienia się wraz z włączeniem grup zarządzania. Użyj pełnej ścieżki, aby zdefiniować grupę zarządzania **/providers/Microsoft.Management/managementgroups/{GroupID}**.

Użyj identyfikatora grupy zarządzania, a nie nazwy wyświetlanej grupy zarządzania. Ten typowy błąd występuje, ponieważ obie są polami niestandardowymi podczas tworzenia grupy zarządzania.

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problemy ze uszkodzeniem definicji roli i ścieżki hierarchii przypisania

Definicje ról są przypisywane w dowolnym miejscu w hierarchii grupy zarządzania. Definicję roli można zdefiniować w nadrzędnej grupie zarządzania, podczas gdy rzeczywiste przypisanie roli istnieje w subskrypcji podrzędnej. Ponieważ istnieje relacja między tymi dwoma elementami, wystąpi błąd podczas próby oddzielenia przypisania od jego definicji.

Na przykład przyjrzyjmy się małej sekcji hierarchii dla wizualizacji.

:::image type="complex" source="./media/subtree.png" alt-text="Diagram hierarchii przykładowej grupy zarządzania." border="false":::
   Diagram koncentruje się na głównej grupie zarządzania z grupami zarządzania i marketingu. Grupa zarządzania I T ma jedną podrzędną grupę zarządzania o nazwie produkcja, podczas gdy grupa zarządzania Marketing ma dwie bezpłatne subskrypcje podrzędne w wersji próbnej.
:::image-end:::

Załóżmy, że istnieje rola niestandardowa zdefiniowana w grupie zarządzania Marketing. Ta rola niestandardowa jest następnie przypisywana do dwóch subskrypcji bezpłatnych wersji próbnych.  

Jeśli spróbujesz przenieść jedną z tych subskrypcji jako podrzędną grupy zarządzania produkcyjnego, spowoduje to przerwanie ścieżki z przypisania roli subskrypcji do definicji roli grupy zarządzania Marketing. W tym scenariuszu zostanie wyświetlony komunikat o błędzie informujący, że przeniesienie nie jest dozwolone, ponieważ spowoduje przerwanie tej relacji.  

Istnieje kilka różnych opcji umożliwiających rozwiązanie tego scenariusza:
- Przed przeniesieniem subskrypcji do nowego elementu nadrzędnego MG usuń przypisanie roli z subskrypcji.
- Dodaj subskrypcję do zakresu umożliwiającego przypisanie definicji roli.
- Zmień zakres możliwy do przypisania w ramach definicji roli. W powyższym przykładzie można zaktualizować zakresy przypisywane z marketingu do głównej grupy zarządzania, aby można było osiągnąć tę definicję w obu gałęziach hierarchii.  
- Utwórz dodatkową rolę niestandardową, która zostanie zdefiniowana w innej gałęzi. Ta nowa rola będzie wymagała również zmiany przypisania roli w subskrypcji.  

### <a name="limitations"></a>Ograniczenia  

Istnieją ograniczenia, które istnieją podczas korzystania z ról niestandardowych w grupach zarządzania. 

 - Można zdefiniować tylko jedną grupę zarządzania w przypisywanych zakresach nowej roli. To ograniczenie jest stosowane w celu zmniejszenia liczby sytuacji, w których definicje ról i przypisania ról są rozłączone. Taka sytuacja występuje, gdy subskrypcja lub Grupa zarządzania z przypisaniem roli przenosi się do innego elementu nadrzędnego, który nie ma definicji roli.  
 - Akcje płaszczyzny danych dostawcy zasobów nie mogą być zdefiniowane w rolach niestandardowych grupy zarządzania. To ograniczenie ma miejsce w przypadku problemów z opóźnieniem w przypadku aktualizowania dostawców zasobów płaszczyzny danych.
   Ten problem opóźnienia jest opracowywany, a akcje te zostaną wyłączone z definicji roli w celu ograniczenia ryzyka.
 - Azure Resource Manager nie sprawdza poprawności istnienia grupy zarządzania w zakresie możliwym do przypisania definicji roli. Jeśli na liście występuje literówka lub nieprawidłowy identyfikator grupy zarządzania, definicja roli nadal zostanie utworzona.  

## <a name="moving-management-groups-and-subscriptions"></a>Przeniesienie grup zarządzania i subskrypcji 

Aby przenieść grupę zarządzania lub subskrypcję jako podrzędną innej grupy zarządzania, należy ocenić trzy reguły jako prawdziwe.

Jeśli wykonujesz akcję Przenieś, potrzebujesz: 

- Uprawnienia Zapis grup zarządzania i przypisywanie ról w podrzędnej subskrypcji lub grupie zarządzania.
  - **Właściciel** przykładu wbudowanej roli
- Dostęp do zapisu grupy zarządzania w docelowej nadrzędnej grupie zarządzania.
  - Wbudowana rola — przykład: **właściciel**, **współautor**, **współautor grupy zarządzania**
- Dostęp do zapisu grupy zarządzania w istniejącej nadrzędnej grupie zarządzania.
  - Wbudowana rola — przykład: **właściciel**, **współautor**, **współautor grupy zarządzania**

**Wyjątek**: Jeśli obiekt docelowy lub istniejąca nadrzędna grupa zarządzania jest główną grupą zarządzania, wymagania dotyczące uprawnień nie są stosowane. Ponieważ główną grupą zarządzania jest domyślny punkt załadunkowy dla wszystkich nowych grup zarządzania i subskrypcji, nie musisz mieć uprawnień do przenoszenia elementu.

Jeśli rola właściciela w subskrypcji jest dziedziczona z bieżącej grupy zarządzania, cele przenoszenia są ograniczone. Subskrypcję można przenieść tylko do innej grupy zarządzania, w której masz rolę właściciela. Nie można przenieść go do grupy zarządzania, w której jesteś współautorem, ponieważ utracisz własność subskrypcji. Jeśli użytkownik jest bezpośrednio przypisany do roli właściciela subskrypcji (niedziedziczonej z grupy zarządzania), można przenieść ją do dowolnej grupy zarządzania, w której jesteś współautorem.

## <a name="audit-management-groups-using-activity-logs"></a>Inspekcja grup zarządzania przy użyciu dzienników aktywności

Grupy zarządzania są obsługiwane w [dzienniku aktywności platformy Azure](../../azure-monitor/platform/platform-logs-overview.md). Możesz wyszukiwać wszystkie zdarzenia, które wystąpiły w grupie zarządzania w tej samej lokalizacji centralnej co inne zasoby platformy Azure. Na przykład widoczne są wszystkie przypisania ról i zmiany przypisań zasad w określonej grupie zarządzania.

:::image type="content" source="./media/al-mg.png" alt-text="Diagram hierarchii przykładowej grupy zarządzania." border="false":::

Jeśli chcesz wykonać zapytanie dotyczące grup zarządzania spoza witryny Azure Portal, zakres docelowy grup zarządzania wygląda tak: **„/providers/Microsoft.Management/managementGroups/{identyfikator_grupy_zarządzania}”**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](./create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](./manage.md)
- Zobacz opcje [ochrony hierarchii zasobów](./how-to/protect-resource-hierarchy.md)