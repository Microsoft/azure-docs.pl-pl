---
title: View and manage customers and delegated resources (Wyświetlanie klientów i zasobów delegowanych oraz zarządzanie nimi)
description: Jako dostawca usług korzystający z funkcji zarządzania zasobami delegowanymi przez platformę Azure możesz wyświetlić wszystkie delegowane zasoby i subskrypcje klientów, przechodząc do moich klientów w Azure Portal.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76543430"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>View and manage customers and delegated resources (Wyświetlanie klientów i zasobów delegowanych oraz zarządzanie nimi)

Dostawcy usług korzystający z funkcji [zarządzania zasobami delegowanymi przez platformę Azure](../concepts/azure-delegated-resource-management.md) mogą używać strony **moi klienci** w [Azure Portal](https://portal.azure.com) , aby wyświetlić delegowane zasoby i subskrypcje klientów. Mimo że będziemy odnieść się do dostawców usług i klientów w tym miejscu, przedsiębiorstwa zarządzające wieloma dzierżawcami mogą używać tego samego procesu do konsolidacji ich środowiska zarządzania.

Aby uzyskać dostęp do strony **moi klienci** w Azure Portal, wybierz pozycję **wszystkie usługi**, a następnie wyszukaj pozycję **moi klienci** i wybierz ją. Można ją również znaleźć, wpisując "My Customers" w polu wyszukiwania w górnej części Azure Portal.

Należy pamiętać, że na stronie **moi klienci** są wyświetlane tylko informacje **o klientach** , którzy mają delegowane subskrypcje lub grupy zasobów. Jeśli pracujesz z innymi klientami (na przykład za pomocą [programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview)), nie zobaczysz informacji o klientach w sekcji **Customers** , chyba że dołączysz swoje zasoby do zarządzania zasobami delegowanymi przez platformę Azure.

Na tej stronie oddzielna sekcja o nazwie **dostawca rozwiązań w chmurze (wersja zapoznawcza)** zawiera informacje o rozliczeniach i zasoby dla klientów programu CSP, którzy [podpisali umowę klienta firmy Microsoft (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) i są [objęte planem platformy Azure](https://docs.microsoft.com/partner-center/azure-plan-get-started). Aby uzyskać więcej informacji, zobacz artykuł Wprowadzenie [do rozliczeń umowy partnerskiej firmy Microsoft](../../billing/mpa-overview.md). Należy zauważyć, że tacy klienci korzystający z programu CSP są wyświetlani w tej sekcji, niezależnie od tego, czy zostały również dołączone do zarządzania zasobami delegowanymi przez platformę Azure. Podobnie klient dostawcy usług kryptograficznych nie musi być widoczny w sekcji **dostawca rozwiązań w chmurze (wersja zapoznawcza)** w obszarze **moi klienci** w celu dodania ich do zarządzania zasobami delegowanymi przez platformę Azure.

> [!NOTE]
> Klienci mogą wyświetlać informacje o dostawcach usług, przechodząc do **dostawców usług** w Azure Portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i zarządzanie dostawcami usług](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Wyświetlanie szczegółów klienta i zarządzanie nimi

Aby wyświetlić szczegóły klienta, wybierz pozycję **klienci** po lewej stronie strony **moi klienci** .

Dla każdego klienta zobaczysz nazwę klienta, identyfikator klienta (identyfikator dzierżawy) i ofertę skojarzoną z zaangażowaniem. W kolumnie **delegacje** zostanie wyświetlona liczba delegowanych subskrypcji i/lub liczba delegowanych grup zasobów.

> [!IMPORTANT]
> Aby można było wyświetlić delegowanie, użytkownicy muszą mieć przyznaną rolę [czytelnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika) w procesie dołączania.

Filtry w górnej części strony pozwalają sortować i grupować informacje o kliencie lub filtrować według określonych klientów, ofert lub słów kluczowych.

Na tej stronie można wyświetlić następujące informacje:

- Aby wyświetlić wszystkie subskrypcje, oferty i delegacje skojarzone z klientem, wybierz nazwę klienta.
- Aby wyświetlić więcej szczegółów na temat oferty i jej delegowania, wybierz nazwę oferty.
- Aby wyświetlić więcej szczegółów na temat przypisań ról dla delegowanych subskrypcji lub grup zasobów, wybierz wpis w kolumnie **delegacje** .

## <a name="view-and-manage-delegations"></a>Wyświetl delegowane i zarządzaj nimi

Delegacje pokazują delegowaną subskrypcję/grupę zasobów wraz z użytkownikami i uprawnieniami, które mają do niej dostęp. Aby wyświetlić te informacje, wybierz opcję **delegacje** po lewej stronie strony **moi klienci** .

Filtry w górnej części strony pozwalają sortować i grupować informacje o przypisaniu dostępu lub filtrować według określonych klientów, ofert lub słów kluczowych.

### <a name="view-role-assignments"></a>Wyświetlanie przypisań ról

Użytkownicy i uprawnienia skojarzone z każdym delegowaniem są wyświetlane w kolumnie **przypisania ról** . Można wybrać każdy wpis, aby wyświetlić pełną listę użytkowników, grup i jednostek usługi, którym udzielono dostępu do subskrypcji lub grupy zasobów. W tym miejscu możesz wybrać określonego użytkownika, grupę lub nazwę główną usługi, aby uzyskać więcej szczegółów.

### <a name="remove-delegations"></a>Usuń delegowania

W przypadku uwzględnienia użytkowników z [przypisaniem przypisania rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) podczas dołączania klienta do zarządzania zasobami delegowanymi przez platformę Azure Ci użytkownicy mogą usunąć delegowanie, wybierając ikonę kosza, która pojawia się w wierszu dla tego delegowania. W takim przypadku żaden użytkownik w dzierżawie dostawcy usług nie będzie w stanie uzyskać dostępu do zasobów, które zostały wcześniej delegowane.


## <a name="work-in-the-context-of-a-delegated-subscription"></a>Pracuj w kontekście delegowanej subskrypcji

Możesz pracować bezpośrednio w kontekście delegowanej subskrypcji w ramach Azure Portal bez przełączania katalogu, w którym pracujesz. W tym celu:

1. Wybierz ikonę **katalogu i subskrypcji** w górnej części Azure Portal.
2. W **globalnym** filtrze subskrypcji upewnij się, że wybrano tylko pole dla tej delegowanej subskrypcji. Za pomocą pola listy rozwijanej **bieżące i delegowane katalogi** można wyświetlić tylko subskrypcje w określonym katalogu. (Nie należy używać opcji **Switch Directory** , ponieważ zmienia katalog, z którym użytkownik jest zalogowany).

Jeśli następnie uzyskasz dostęp do usługi, która obsługuje [środowiska zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md), usługa będzie domyślnie w kontekście wybranej subskrypcji delegowanej. Możesz to zmienić, wykonując powyższe kroki i zaznaczając pole **wyboru Zaznacz wszystko** (lub wybierając co najmniej jedną subskrypcję, w której ma zostać zadziałała).

> [!NOTE]
> Jeśli udzielono dostępu do co najmniej jednej grupy zasobów, a nie dostęp do całej subskrypcji, możesz wybrać subskrypcję, do której należy ta grupa zasobów. Następnie będziesz działać w kontekście tej subskrypcji, ale będzie można uzyskać dostęp tylko do określonych grup zasobów.

Możesz również uzyskać dostęp do funkcji związanych z delegowanymi subskrypcjami lub grupami zasobów z poziomu usług, które obsługują obsługę wielu dzierżawców, wybierając subskrypcję lub grupę zasobów z tej usługi.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- Dowiedz się, w jaki sposób klienci mogą [wyświetlać dostawców usług i zarządzać nimi](view-manage-service-providers.md) , przechodząc do **dostawców usług** w Azure Portal.
