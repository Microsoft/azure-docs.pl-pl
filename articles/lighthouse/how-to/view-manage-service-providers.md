---
title: Wyświetlanie dostawców usług i zarządzanie nimi
description: Klienci mogą używać strony dostawcy usług w Azure Portal do wyświetlania informacji o dostawcach usług, ofertach dostawcy usług i delegowanych zasobach.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f6ee5fb154d75ff715acf99c5184cd1652ccdb80
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555586"
---
# <a name="view-and-manage-service-providers"></a>Wyświetlanie dostawców usług i zarządzanie nimi

Strona **dostawcy usług** w [Azure Portal](https://portal.azure.com) zapewnia klientom kontrolę i widoczność dla dostawców usług korzystających z [usługi Azure Lighthouse](../overview.md). Klienci mogą wyświetlać szczegółowe informacje o dostawcach usług, delegować określone zasoby, wyszukiwać nowe oferty, usuwać dostęp dostawcy usług i nie tylko.

Aby wyświetlić stronę **dostawcy usług** w Azure Portal, wybierz pozycję **wszystkie usługi**, a następnie wyszukaj **dostawców usług** i wybierz ją. Możesz również znaleźć Tę stronę, wprowadzając "dostawcy usług" lub "Azure Lighthouse" w polu wyszukiwania w górnej części Azure Portal.

> [!NOTE]
> Aby wyświetlić stronę **dostawcy usług** , użytkownik w dzierżawie klienta musi mieć [wbudowaną rolę czytnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika).
>
> Aby dodać lub zaktualizować oferty, delegować zasoby i usuwać oferty, użytkownik musi mieć rolę z `Microsoft.Authorization/roleAssignments/write` uprawnieniem, takim jak [właściciel](../../role-based-access-control/built-in-roles.md#owner).

Należy pamiętać, że na stronie **dostawcy usług** są wyświetlane tylko informacje o dostawcach usług, którzy mają dostęp do subskrypcji lub grup zasobów klienta za pomocą usługi Azure Lighthouse. Jeśli klient współpracuje z dodatkowymi dostawcami usług, którzy nie korzystają z usługi Azure Lighthouse, informacje o tych dostawcach usług nie będą widoczne w tym miejscu.

## <a name="view-service-provider-details"></a>Wyświetl szczegóły dostawcy usług

Aby wyświetlić szczegółowe informacje o bieżących dostawcach usług przy użyciu usługi Azure Lighthouse do pracy z dzierżawcą klienta, wybierz pozycję **oferty dostawcy usług** w lewej części strony **dostawcy usług** .

Dla każdej oferty zobaczysz nazwę dostawcy usług i skojarzoną z nią ofertę. Możesz wybrać ofertę, aby wyświetlić opis i inne szczegóły, w tym przydziały ról, do których przyznano dostawcę usług.

W kolumnie **delegacje** można zobaczyć, ile subskrypcji i/lub grup zasobów zostały delegowane do dostawcy usług dla tej oferty. Dostawca usług będzie mógł uzyskiwać dostęp do tych subskrypcji i/lub grup zasobów oraz nimi zarządzać zgodnie z poziomami dostępu określonymi w ofercie.

## <a name="add-or-remove-service-provider-offers"></a>Dodaj lub Usuń oferty dostawcy usług

Aby dodać nową ofertę dostawcy usług na stronie **oferty dostawcy usług** , wybierz pozycję **Dodaj ofertę**. Wybierz pozycję **oferty prywatne** , aby wyświetlić oferty, które dostawca usług opublikował dla tego klienta. Następnie możesz wybrać tę ofertę na ekranie **oferty prywatne** , a następnie wybrać pozycję **Skonfiguruj + Subskrybuj**.

W dowolnym momencie możesz usunąć ofertę dostawcy usług, wybierając ikonę kosza w wierszu dla tej oferty. Po potwierdzeniu usunięcia ten dostawca usług nie będzie już miał dostępu do zasobów, które były wcześniej delegowane dla tej oferty.

## <a name="delegate-resources"></a>Delegowanie zasobów

Aby dostawca usług mógł uzyskać dostęp do zasobów klienta i zarządzać nimi, należy delegować co najmniej jedną określoną subskrypcję i grupę zasobów. Jeśli klient zaakceptuje ofertę, ale nie oddelegowano jeszcze żadnych zasobów, zobaczy uwagę w górnej części sekcji **oferty dostawcy usług** . Pozwala to klientowi na uzyskanie dostępu do dowolnych zasobów klienta, którzy muszą podjąć odpowiednie działania.

Aby delegować subskrypcje lub grupy zasobów:

1. Zaznacz pole wyboru dla wiersza zawierającego dostawcę usług, ofertę i nazwę. Następnie wybierz pozycję **Deleguj zasoby** w górnej części ekranu.
1. W sekcji **szczegóły oferty** na stronie **delegowanie zasobów** Przejrzyj szczegóły dotyczące dostawcy usług i oferty. Aby przejrzeć przypisania ról dla oferty, wybierz **pozycję kliknij tutaj, aby wyświetlić szczegóły wybranej oferty**.
1. W sekcji **Delegat** wybierz opcję **Deleguj subskrypcje** lub **delegowanie grup zasobów**.
1. Wybierz subskrypcje i/lub grupy zasobów, które chcesz delegować dla tej oferty, a następnie wybierz pozycję **Dodaj**.
1. Zaznacz pole wyboru u dołu strony, aby potwierdzić, że chcesz udzielić temu dostawcy usług dostępu do wybranych zasobów, a następnie wybierz opcję **Delegat**.

## <a name="update-service-provider-offers"></a>Oferty aktualizacji dostawcy usług

Po dodaniu oferty przez klienta dostawca usług może opublikować zaktualizowaną wersję tej samej oferty w witrynie Azure Marketplace. Na przykład mogą chcieć dodać nową definicję roli. Jeśli nowa wersja oferty została opublikowana, na stronie **oferty dostawcy usług** zostanie wyświetlona ikona "Aktualizacja" w wierszu dla tej oferty. Wybierz tę ikonę, aby zobaczyć różnice między bieżącą wersją oferty a nową.

 ![Ikona aktualizacji oferty](../media/update-offer.jpg)

Po przejrzeniu zmian klient może zdecydować się na zaktualizowanie nowej wersji. Autoryzacje i inne ustawienia określone w nowej wersji będą następnie stosowane do wszystkich subskrypcji i/lub grup zasobów, które zostały delegowane dla tej oferty.

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
- Dowiedz się, jak [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą korzystać z usługi Azure Lighthouse, aby skonsolidować swoje środowisko zarządzania.

