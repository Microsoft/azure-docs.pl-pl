---
title: Wyświetlanie dostawców usług i zarządzanie nimi
description: Klienci mogą używać strony dostawcy usług w Azure Portal do wyświetlania informacji o dostawcach usług, ofertach dostawcy usług i delegowanych zasobach.
ms.date: 12/16/2020
ms.topic: how-to
ms.openlocfilehash: 5ee897503c997ab10fdb489f7921c9d2d001e472
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617208"
---
# <a name="view-and-manage-service-providers"></a>Wyświetlanie dostawców usług i zarządzanie nimi

Strona **dostawcy usług** w [Azure Portal](https://portal.azure.com) zapewnia klientom kontrolę i widoczność dla dostawców usług korzystających z [usługi Azure Lighthouse](../overview.md). Klienci mogą wyświetlać szczegółowe informacje o dostawcach usług, delegować określone zasoby, kupować nowe oferty dostawców usług, usuwać dostęp dostawcy usług i nie tylko.

> [!TIP]
> Mimo że będziemy odnieść się do dostawców usług i klientów w tym miejscu, [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą używać tego samego procesu do konsolidacji ich środowiska zarządzania.

Aby uzyskać dostęp do strony **dostawcy usług** w Azure Portal, klient może wybrać **wszystkie usługi**, a następnie wyszukać **dostawców usług** i wybrać ją. Można je również znaleźć, wprowadzając "dostawcy usług" lub "Azure Lighthouse" w polu wyszukiwania w górnej części Azure Portal.

> [!NOTE]
> Aby wyświetlić stronę **dostawcy usług** , użytkownik w dzierżawie klienta musi mieć [wbudowaną rolę czytnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika).
>
> Aby dodać lub zaktualizować oferty, delegować zasoby i usuwać oferty, użytkownik musi mieć [wbudowaną rolę właściciela](../../role-based-access-control/built-in-roles.md#owner) subskrypcji.

Należy pamiętać, że na stronie **dostawcy usług** są wyświetlane tylko informacje o dostawcach usług, którzy mają dostęp do subskrypcji lub grup zasobów klienta za pomocą usługi Azure Lighthouse. Jeśli klient współpracuje z dodatkowymi dostawcami usług, którzy nie korzystają z usługi Azure Lighthouse w celu uzyskania dostępu do zasobów klienta, informacje o tych dostawcach usług nie są wyświetlane w tym miejscu.

> [!TIP]
> Dostawcy usług mogą przeglądać informacje o swoich klientach, przechodząc do obszaru **moi klienci** w Azure Portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i zarządzanie klientami oraz delegowanymi zasobami](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Wyświetl szczegóły dostawcy usług

Aby wyświetlić szczegółowe informacje o dostawcach usług, klient może wybrać **oferty dostawcy usług** po lewej stronie strony **dostawcy usług** .

Dla każdej oferty dostawcy usług klient zobaczy nazwę dostawcy usług i skojarzoną z nim ofertę, a także nazwę wprowadzoną przez klienta podczas procesu dołączania.

W kolumnie **delegacje** klient widzi, ile subskrypcji i/lub grup zasobów zostało delegowanych do dostawcy usług dla tej oferty. Dostawca usług będzie mógł uzyskiwać dostęp do tych subskrypcji i/lub grup zasobów oraz nimi zarządzać zgodnie z poziomami dostępu określonymi w ofercie.

## <a name="add-or-remove-service-provider-offers"></a>Dodaj lub Usuń oferty dostawcy usług

Klient może dodać nową ofertę dostawcy usług na stronie **oferty dostawcy usług** , wybierając pozycję **Dodaj ofertę**. Dostawca usług musi mieć opublikowaną ofertę dla tego klienta. Klient może następnie wybrać tę ofertę na ekranie **oferty prywatne** , a następnie wybrać pozycję **Utwórz**.

Jeśli klient chce usunąć ofertę dostawcy usług, może to zrobić w dowolnym momencie, wybierając ikonę kosza w wierszu dla tej oferty. Po potwierdzeniu usunięcia ten dostawca usług nie będzie już miał dostępu do zasobów klienta, które były wcześniej delegowane dla tej oferty.

## <a name="delegate-resources"></a>Delegowanie zasobów

Zanim dostawca usług będzie mógł uzyskać dostęp do zasobów klienta i zarządzać nim, muszą być delegowane. Jeśli klient zaakceptuje ofertę, ale nie oddelegowano jeszcze żadnych zasobów, zobaczy uwagę w górnej części sekcji **oferty dostawcy usług** . Pozwala to klientowi na uzyskanie dostępu do dowolnych zasobów klienta, którzy muszą podjąć odpowiednie działania.

Aby delegować subskrypcje lub grupy zasobów:

1. Zaznacz pole wyboru dla wiersza zawierającego dostawcę usług, ofertę i nazwę. Następnie wybierz pozycję **Deleguj zasoby** w górnej części ekranu.
1. W sekcji **szczegóły oferty** na stronie **delegowanie zasobów** Przejrzyj szczegóły dotyczące dostawcy usług i oferty. Aby przejrzeć przypisania ról dla oferty, wybierz **pozycję kliknij tutaj, aby wyświetlić szczegóły wybranej oferty**.
1. W sekcji **Delegat** wybierz opcję **Deleguj subskrypcje** lub **delegowanie grup zasobów**.
1. Wybierz subskrypcje i/lub grupy zasobów, które chcesz delegować dla tej oferty, a następnie wybierz pozycję **Dodaj**.
1. Zaznacz pole wyboru u dołu strony, aby potwierdzić, że chcesz udzielić temu dostawcy usług dostępu do wybranych zasobów, a następnie wybierz opcję **Delegat**.

## <a name="update-service-provider-offers"></a>Oferty aktualizacji dostawcy usług

Po dodaniu oferty przez klienta dostawca usług może opublikować zaktualizowaną wersję tej samej oferty w witrynie Azure Marketplace. Na przykład mogą chcieć dodać nową definicję roli. Jeśli nowa wersja oferty została opublikowana, na stronie **oferty dostawcy usług** zostanie wyświetlona ikona "Aktualizacja" w wierszu dla tej oferty. Klient może wybrać tę ikonę, aby zobaczyć różnice między bieżącą wersją oferty a nową.

 ![Ikona aktualizacji oferty](../media/update-offer.jpg)

Po przejrzeniu zmian klient może zdecydować się na zaktualizowanie nowej wersji. Po wykonaniu tych czynności autoryzacje i inne ustawienia określone w nowej wersji będą stosowane do wszystkich subskrypcji i/lub grup zasobów, które zostały delegowane dla tej oferty.

## <a name="view-delegations"></a>Wyświetl delegowania

Delegacje reprezentują przypisania ról, które przyznają uprawnienia dostawcy usług dla zasobów delegowanych przez klienta. Aby wyświetlić te informacje, wybierz pozycję **delegacje** w lewej części strony **dostawcy usług** .

Filtry w górnej części strony pozwalają sortować i grupować informacje o delegowaniu. Możesz również filtrować według określonych klientów, ofert lub słów kluczowych.

> [!NOTE]
> Klienci nie będą widzieć tych przypisań ról ani żadnych użytkowników z dzierżawy dostawcy usług, którzy uzyskali te role, podczas [wyświetlania przypisań ról dla delegowanego zakresu w Azure Portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) lub za pośrednictwem interfejsów API.

## <a name="audit-delegations-in-your-environment"></a>Inspekcja delegowania w środowisku

Klienci mogą chcieć uzyskać wgląd w subskrypcje i/lub grupy zasobów, które zostały delegowane do usługi Azure Lighthouse. Jest to szczególnie przydatne w przypadku klientów korzystających z dużej liczby subskrypcji lub wielu użytkowników, którzy wykonują zadania zarządzania.

Zapewniamy [wbudowaną Azure Policy definicję zasad](../../governance/policy/samples/built-in-policies.md#lighthouse) [służącą do inspekcji delegowania zakresów do dzierżawy zarządzającej](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Te zasady można przypisać do grupy zarządzania zawierającej wszystkie subskrypcje, które mają być poddane inspekcji. Po sprawdzeniu zgodności z tymi zasadami wszystkie delegowane subskrypcje i/lub grupy zasobów (w grupie zarządzania, do których przypisane są zasady) będą wyświetlane w stanie niezgodnym. Następnie możesz przejrzeć wyniki i potwierdzić, że nie ma żadnych nieoczekiwanych delegowania.

Inna [wbudowana definicja zasad](../../governance/policy/samples/built-in-policies.md#lighthouse) umożliwia [ograniczenie delegowania do określonych dzierżaw związanych z zarządzaniem](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). Te zasady można w podobny sposób zastosować do grupy zarządzania zawierającej wszystkie subskrypcje, dla których chcesz ograniczyć delegowania. Po wdrożeniu zasad wszystkie próby delegowania subskrypcji do dzierżawy poza tymi określonymi przez użytkownika zostaną odrzucone.

Aby uzyskać więcej informacji na temat sposobu przypisywania zasad i wyświetlania wyników stanu zgodności, zobacz [Szybki Start: Tworzenie przypisania zasad](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Lighthouse](../overview.md).
- Dowiedz się, jak [przeprowadzić inspekcję działania dostawcy usług](view-service-provider-activity.md).
- Dowiedz się, jak dostawcy usług mogą [wyświetlać klientów i zarządzać nimi](view-manage-customers.md) na stronie **moi klienci** w Azure Portal.
