---
title: Wyświetlaj klientów i delegowane zasoby w Azure Portal i zarządzaj nimi
description: Jako dostawca usług lub przedsiębiorstwo korzystające z usługi Azure Lighthouse można wyświetlić wszystkie delegowane zasoby i subskrypcje, przechodząc do moich klientów w Azure Portal.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 78344015ee027b9844b6339fa7cd95d348488a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419334"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Wyświetlaj klientów i delegowane zasoby w Azure Portal i zarządzaj nimi

Dostawcy usług korzystający z usługi [Azure Lighthouse](../overview.md) mogą użyć strony **moi klienci** w [Azure Portal](https://portal.azure.com) , aby wyświetlić delegowane zasoby i subskrypcje klientów.

> [!TIP]
> Mimo że będziemy odnieść się do dostawców usług i klientów w tym miejscu, [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą używać tego samego procesu do konsolidacji ich środowiska zarządzania.

Aby uzyskać dostęp do strony **moi klienci** w Azure Portal, wybierz pozycję **wszystkie usługi**, a następnie wyszukaj pozycję **moi klienci** i wybierz ją. Można ją również znaleźć, wpisując "My Customers" w polu wyszukiwania w górnej części Azure Portal.

Należy pamiętać, że na stronie **moi klienci** są wyświetlane informacje **o klientach** , którzy mają delegowane subskrypcje lub grupy zasobów do dzierżawy usługi Azure Active Directory (Azure AD) za pomocą usługi Azure Lighthouse. Jeśli pracujesz z innymi klientami (na przykład za pośrednictwem [programu Cloud Solution Provider)](/partner-center/csp-overview), nie zobaczysz informacji o tych klientach w sekcji **klienci** , chyba że dołączysz [swoje zasoby do usługi Azure Lighthouse](onboard-customer.md) (mimo że możesz zobaczyć szczegółowe informacje o niektórych klientach dostawcy usług kryptograficznych w [sekcji dostawca rozwiązań w chmurze (wersja zapoznawcza)](#cloud-solution-provider-preview) w dolnej części strony).

> [!NOTE]
> Klienci mogą wyświetlać informacje o dostawcach usług, przechodząc do **dostawców usług** w Azure Portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i zarządzanie dostawcami usług](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Wyświetlanie szczegółów klienta i zarządzanie nimi

Aby wyświetlić szczegóły klienta, wybierz pozycję **klienci** po lewej stronie strony **moi klienci** .

> [!IMPORTANT]
> Aby można było wyświetlić te informacje, użytkownicy muszą mieć przyznaną rolę [czytelnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika) w procesie dołączania.

Dla każdego klienta zobaczysz nazwę klienta, identyfikator klienta (identyfikator dzierżawy) i **Identyfikator oferty** oraz **wersję oferty** skojarzoną z zaangażowaniem. W kolumnie **delegacje** zostanie wyświetlona liczba delegowanych subskrypcji i/lub liczba delegowanych grup zasobów.

Opcje w górnej części strony umożliwiają sortowanie, filtrowanie i grupowanie informacji o klientach według określonych klientów, ofert lub słów kluczowych.

Na tej stronie można wyświetlić następujące informacje:

- Aby wyświetlić wszystkie subskrypcje, oferty i delegacje skojarzone z klientem, wybierz nazwę klienta.
- Aby wyświetlić więcej szczegółów na temat oferty i jej delegowania, wybierz nazwę oferty.
- Aby wyświetlić więcej szczegółów na temat przypisań ról dla delegowanych subskrypcji lub grup zasobów, wybierz wpis w kolumnie **delegacje** .

## <a name="view-and-manage-delegations"></a>Wyświetl delegowane i zarządzaj nimi

Delegacje pokazują subskrypcję lub grupę zasobów, która została delegowana, wraz z użytkownikami i uprawnieniami, które mają do niego dostęp. Aby wyświetlić te informacje, wybierz opcję **delegacje** po lewej stronie strony **moi klienci** .

Opcje w górnej części strony umożliwiają sortowanie, filtrowanie i grupowanie tych informacji według określonych klientów, ofert lub słów kluczowych.

### <a name="view-role-assignments"></a>Wyświetlanie przypisań ról

Użytkownicy i uprawnienia skojarzone z każdym delegowaniem są wyświetlane w kolumnie **przypisania ról** . Można wybrać każdy wpis, aby wyświetlić pełną listę użytkowników, grup i jednostek usługi, którym udzielono dostępu do subskrypcji lub grupy zasobów. W tym miejscu możesz wybrać określonego użytkownika, grupę lub nazwę główną usługi, aby uzyskać więcej szczegółów.

### <a name="remove-delegations"></a>Usuń delegowania

Jeśli podczas dołączania klienta do usługi Azure Lighthouse dołączysz użytkowników z [rolą usuwania przypisania rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , użytkownicy mogą usunąć delegowanie, wybierając ikonę kosza w wierszu dla tego delegowania. W takim przypadku żaden użytkownik w dzierżawie dostawcy usług nie będzie w stanie uzyskać dostępu do zasobów, które zostały wcześniej delegowane.

Aby uzyskać więcej informacji, zobacz [Usuwanie dostępu do delegowania](remove-delegation.md).

## <a name="view-delegation-change-activity"></a>Wyświetl działanie zmiany delegowania

Sekcja **Dziennik aktywności** na stronie **moi klienci** śledzi każdą subskrypcję klienta, a grupy zasobów są delegowane do dzierżawy, a każdy z nich jest usuwany. Te informacje mogą być wyświetlane tylko przez użytkowników, którym [przypisano rolę czytelnik monitorowania w zakresie głównym](monitor-delegation-changes.md).

Aby uzyskać więcej informacji, zobacz [Wyświetlanie zmian delegowania w Azure Portal](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal).

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Pracuj w kontekście delegowanej subskrypcji

Możesz współpracować bezpośrednio w kontekście delegowanej subskrypcji w ramach Azure Portal bez przełączania katalogu, do którego się zalogowano. W tym celu:

1. Wybierz ikonę **katalogu i subskrypcji** w górnej części Azure Portal.
2. W **filtrze domyślnej subskrypcji** upewnij się, że wybrano tylko pole dla tej delegowanej subskrypcji. Za pomocą pola listy rozwijanej **bieżące i delegowane katalogi** można wyświetlić tylko subskrypcje w określonym katalogu. (Nie należy używać opcji **Switch Directory** , ponieważ zmienia katalog, z którym użytkownik jest zalogowany).

Jeśli następnie uzyskasz dostęp do usługi, która obsługuje [środowiska zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md), usługa będzie domyślnie w kontekście wybranej subskrypcji delegowanej. Możesz to zmienić, wykonując powyższe kroki i zaznaczając pole **wyboru Zaznacz wszystko** (lub wybierając co najmniej jedną subskrypcję, w której ma zostać zadziałała).

> [!NOTE]
> Jeśli udzielono dostępu do co najmniej jednej grupy zasobów, a nie dostęp do całej subskrypcji, wybierz subskrypcję, do której należy ta grupa zasobów. Następnie będziesz działać w kontekście tej subskrypcji, ale będzie można uzyskać dostęp tylko do określonych grup zasobów.

Możesz również uzyskać dostęp do funkcji związanych z delegowanymi subskrypcjami lub grupami zasobów z poziomu usług, które obsługują obsługę wielu dzierżawców, wybierając subskrypcję lub grupę zasobów z tej usługi.

## <a name="cloud-solution-provider-preview"></a>Dostawca rozwiązań w chmurze (wersja zapoznawcza)

Oddzielna sekcja **dostawcy rozwiązań w chmurze (wersja zapoznawcza)** na stronie **My Customers** zawiera informacje o rozliczeniach i zasoby dla klientów programu CSP, którzy [podpisali umowę klienta firmy Microsoft (MCA)](/partner-center/confirm-customer-agreement) i są [objęte planem platformy Azure](/partner-center/azure-plan-get-started). Aby uzyskać więcej informacji, zobacz artykuł Wprowadzenie [do rozliczeń umowy partnerskiej firmy Microsoft](../../cost-management-billing/understand/mpa-overview.md).

Tacy klienci korzystający z tego dostawcy usług kryptograficznych będą wyświetlani w tej sekcji, niezależnie od tego, czy zostały również dołączone do usługi Azure Lighthouse. Podobnie klient dostawcy usług kryptograficznych nie musi być widoczny w sekcji **dostawca rozwiązań w chmurze (wersja zapoznawcza)** w obszarze **moi klienci** w celu dołączenia ich do usługi Azure Lighthouse.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- Dowiedz się, w jaki sposób klienci mogą [wyświetlać dostawców usług i zarządzać nimi](view-manage-service-providers.md) , przechodząc do **dostawców usług** w Azure Portal.
