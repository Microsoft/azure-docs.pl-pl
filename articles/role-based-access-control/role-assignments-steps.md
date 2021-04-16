---
title: Kroki przypisywania roli platformy Azure — Azure RBAC
description: Dowiedz się, jak przypisać role platformy Azure do użytkowników, grup, podmiotów usługi lub tożsamości zarządzanych przy użyciu kontroli dostępu opartej na rolach (RBAC) platformy Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.openlocfilehash: 40a17da6383fb1f368c74a82fefa71991cdc1b19
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517678"
---
# <a name="steps-to-assign-an-azure-role"></a>Kroki przypisywania roli platformy Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)]W tym artykule opisano kroki wysokiego poziomu dotyczące przypisywania ról platformy Azure przy użyciu interfejsu [Azure Portal,](role-assignments-portal.md) [Azure PowerShell,](role-assignments-powershell.md) [interfejsu](role-assignments-cli.md)wiersza polecenia platformy Azure lub interfejsu [API REST.](role-assignments-rest.md)

## <a name="step-1-determine-who-needs-access"></a>Krok 1. Określanie, kto potrzebuje dostępu

Najpierw należy określić, kto potrzebuje dostępu. Rolę można przypisać do użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej. Jest to również nazywane *podmiotem zabezpieczeń*.

![Podmiot zabezpieczeń w przypisaniu roli](./media/shared/rbac-security-principal.png)

- Użytkownik — osoba mająca profil w usłudze Azure Active Directory. Można również przypisywać role do użytkowników w innych dzierżawach. Aby uzyskać informacje na temat użytkowników w innych organizacjach, zobacz [Azure Active Directory B2B (Współpraca B2B w usłudze Azure Active Directory)](../active-directory/external-identities/what-is-b2b.md).
- Grupa — zbiór użytkowników, utworzony w usłudze Azure Active Directory. W przypadku przypisania roli do grupy wszyscy użytkownicy w grupie otrzymają tę rolę. 
- Jednostka usługi — tożsamość zabezpieczeń używana przez aplikacje lub usługi w celu uzyskania dostępu do określonych zasobów platformy Azure. Można traktować ją jako odpowiednik *tożsamości użytkownika* (nazwy użytkownika i hasła lub certyfikatu) w przypadku aplikacji.
- Tożsamość zarządzana — tożsamość w usłudze Azure Active Directory, która jest automatycznie zarządzana przez platformę Azure. [Tożsamości zarządzanych](../active-directory/managed-identities-azure-resources/overview.md) używa się zazwyczaj podczas tworzenia aplikacji w chmurze w celu zarządzania poświadczeniami do uwierzytelniania w usługach platformy Azure.

## <a name="step-2-select-the-appropriate-role"></a>Krok 2. Wybieranie odpowiedniej roli

Uprawnienia są grupowane w *definicję roli*. Zazwyczaj jest to po prostu *nazywana* rolą . Możesz wybrać z listy kilka wbudowanych ról. Jeśli role wbudowane nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe.

![Definicja roli w przypisaniu roli](./media/shared/rbac-role-definition.png)

Poniżej wymieniono cztery podstawowe role wbudowane. Pierwsze trzy są stosowane do wszystkich typów zasobów.

- [Właściciel](built-in-roles.md#owner) — ma pełny dostęp do wszystkich zasobów i jest uprawniony do udzielania dostępu innym osobom.
- [Współautor](built-in-roles.md#contributor) — może tworzyć wszystkie typy zasobów platformy Azure i zarządzać nimi, ale nie może udzielać dostępu innym osobom.
- [Czytelnik](built-in-roles.md#reader) — może wyświetlać istniejące zasoby platformy Azure.
- [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) — może zarządzać dostępem użytkowników do zasobów platformy Azure.

Pozostałe role wbudowane umożliwiają zarządzanie określonymi zasobami platformy Azure. Na przykład rola [współautora maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) umożliwia użytkownikowi tworzenie maszyn wirtualnych i zarządzanie nimi.

1. Zacznij od kompleksowego artykułu Wbudowane role [platformy Azure.](built-in-roles.md) Tabela w górnej części artykułu zawiera indeks szczegółów w dalszej części artykułu.

1. W tym artykule przejdź do kategorii usługi (takiej jak obliczenia, magazyn i bazy danych) dla zasobu, do którego chcesz udzielić uprawnień. Najprostszym sposobem znalezienia szukanego słowa kluczowego jest zwykle wyszukanie na stronie odpowiedniego słowa kluczowego, takiego jak "blob", "maszyna wirtualna" itp.

1. Przejrzyj role wymienione dla kategorii usługi i zidentyfikuj potrzebne operacje. Zawsze zaczynaj od najbardziej restrykcyjnej roli.

    Jeśli na przykład podmiot zabezpieczeń musi odczytywać obiekty blob na koncie usługi Azure Storage, ale nie potrzebuje dostępu do zapisu, wybierz pozycję Czytelnik danych obiektu blob usługi [Storage,](built-in-roles.md#storage-blob-data-reader) a nie Współautor danych obiektu [blob](built-in-roles.md#storage-blob-data-contributor) magazynu (i zdecydowanie nie rolę właściciela danych obiektu blob usługi [Storage](built-in-roles.md#storage-blob-data-owner) na poziomie administratora). Przypisania ról można zawsze zaktualizować później, w razie potrzeby.

1. Jeśli nie znajdziesz odpowiedniej roli, możesz utworzyć rolę [niestandardową](custom-roles.md).

## <a name="step-3-identify-the-needed-scope"></a>Krok 3. Określenie wymaganego zakresu

*Zakres* to zestaw zasobów, w ramach którego jest przydzielany dostęp. Na platformie Azure można określić zakres na czterech poziomach: [grupa](../governance/management-groups/overview.md)zarządzania, subskrypcja, [grupa zasobów](../azure-resource-manager/management/overview.md#resource-groups)i zasób. Zakresy mają strukturę opartą na relacji nadrzędny-podrzędny. Każdy poziom hierarchii sprawia, że zakres jest bardziej specyficzny. Role można przypisywać na dowolnym z tych poziomów zakresu. Wybór poziomu określa, jak szeroko rola jest stosowana. Niższe poziomy dziedziczą uprawnienia roli z wyższych poziomów. 

![Zakres w przypisaniu roli](./media/shared/rbac-scope.png)

Po przypisaniu roli w zakresie nadrzędnym te uprawnienia są dziedziczone do zakresów podrzędnych. Na przykład:

- Jeśli przypiszesz [użytkownikowi](built-in-roles.md#reader) rolę Czytelnik w zakresie grupy zarządzania, ten użytkownik będzie w stanie odczytać wszystko we wszystkich subskrypcjach w grupie zarządzania.
- Jeśli przypiszesz [rolę Czytelnik](built-in-roles.md#billing-reader) rozliczeń do grupy w zakresie subskrypcji, członkowie tej grupy będą w stanie odczytywać dane dotyczące rozliczeń dla każdej grupy zasobów i zasobu w subskrypcji.
- Jeśli przypiszesz rolę [współautora](built-in-roles.md#contributor) do aplikacji na poziomie grupy zasobów, ta aplikacja będzie mogła zarządzać zasobami dowolnego typu w tej grupie zasobów, ale nie w innych grupach zasobów w ramach subskrypcji.

 Aby uzyskać więcej informacji, zobacz [Opis zakresu](scope-overview.md).

## <a name="step-4-check-your-prerequisites"></a>Krok 4. Sprawdzanie wymagań wstępnych

Aby przypisać role, musisz zalogować się przy użyciu użytkownika, który ma przypisaną rolę [](built-in-roles.md#user-access-administrator) z uprawnieniami do zapisu przypisań ról, taką jak [Właściciel](built-in-roles.md#owner) lub Administrator dostępu użytkowników w zakresie, do który próbujesz przypisać rolę. Podobnie, aby usunąć przypisanie roli, musisz mieć uprawnienie do usuwania przypisań ról.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Jeśli Konto użytkownika nie ma uprawnień do przypisywania roli w ramach subskrypcji, zostanie wyświetlony komunikat o błędzie informujący, że twoje konto "nie ma autoryzacji do wykonania akcji Microsoft.Authorization/roleAssignments/write". W takim przypadku skontaktuj się z administratorami subskrypcji, którzy mogą przypisywać uprawnienia w Twoim imieniu.

Jeśli do przypisywania ról używasz jednostki usługi, może wystąpić błąd "Uprawnienia niewystarczające do ukończenia operacji". Ten błąd jest prawdopodobnie, ponieważ platforma Azure próbuje znaleźć tożsamość przypisaną w usłudze Azure Active Directory (Azure AD), a jednostki usługi nie może domyślnie odczytać usługi Azure AD. W takim przypadku należy udzielić jednostki usługi uprawnień do odczytu danych w katalogu. Alternatywnie, jeśli używasz interfejsu wiersza polecenia platformy Azure, możesz utworzyć przypisanie roli przy użyciu przypisanego identyfikatora obiektu, aby pominąć wyszukiwania w usłudze Azure AD. Aby uzyskać więcej informacji, zobacz [Troubleshoot Azure RBAC (Rozwiązywanie problemów z RBAC platformy Azure).](troubleshooting.md)

## <a name="step-5-assign-role"></a>Krok 5. Przypisywanie roli

Gdy znasz podmiot zabezpieczeń, rolę i zakres, możesz przypisać rolę. Role można przypisywać przy użyciu interfejsów Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure, zestawów Azure SDK lub interfejsów API REST. W każdej subskrypcji **można mieć maksymalnie 2000** przypisań ról. Ten limit obejmuje przypisania ról w ramach subskrypcji, grupy zasobów i zakresów zasobów. W każdej grupie zarządzania może być **maksymalnie 500** przypisań ról.

Zapoznaj się z następującymi artykułami, aby uzyskać szczegółowe instrukcje dotyczące przypisywania ról.

- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md)
- [Przypisywanie ról platformy Azure przy użyciu Azure PowerShell](role-assignments-powershell.md)
- [Przypisywanie ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](role-assignments-cli.md)
- [Przypisywanie ról platformy Azure przy użyciu interfejsu API REST](role-assignments-rest.md)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: udzielanie użytkownikowi dostępu do zasobów platformy Azure przy użyciu Azure Portal](quickstart-assign-role-user-portal.md)