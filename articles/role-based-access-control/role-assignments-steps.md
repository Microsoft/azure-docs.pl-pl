---
title: Procedura dodawania przypisania roli — Azure RBAC
description: Zapoznaj się z instrukcjami przypisywania ról platformy Azure użytkownikom, grupom, podmiotom usług lub tożsamościom zarządzanym przy użyciu kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.openlocfilehash: badf10da8af0ed3829deb2498b51b5d5c8ce6a93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91604411"
---
# <a name="steps-to-add-a-role-assignment"></a>Kroki umożliwiające dodanie przypisania roli

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] W tym artykule opisano procedurę wysokiego poziomu służącą do dodawania przypisania roli przy użyciu [Azure Portal](role-assignments-portal.md), [Azure PowerShell](role-assignments-powershell.md), interfejsu [wiersza polecenia platformy Azure](role-assignments-cli.md)lub [interfejsu API REST](role-assignments-rest.md).

## <a name="step-1-determine-who-needs-access"></a>Krok 1. Określanie, kto potrzebuje dostępu

Najpierw należy określić, kto ma mieć dostęp. Rolę można przypisać do użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej. Jest to również nazywane *podmiotem zabezpieczeń*.

![Podmiot zabezpieczeń w przypisaniu roli](./media/shared/rbac-security-principal.png)

- Użytkownik — osoba mająca profil w usłudze Azure Active Directory. Można również przypisywać role do użytkowników w innych dzierżawach. Aby uzyskać informacje na temat użytkowników w innych organizacjach, zobacz [Azure Active Directory B2B (Współpraca B2B w usłudze Azure Active Directory)](../active-directory/b2b/what-is-b2b.md).
- Grupa — zbiór użytkowników, utworzony w usłudze Azure Active Directory. W przypadku przypisania roli do grupy wszyscy użytkownicy w grupie otrzymają tę rolę. 
- Jednostka usługi — tożsamość zabezpieczeń używana przez aplikacje lub usługi w celu uzyskania dostępu do określonych zasobów platformy Azure. Można traktować ją jako odpowiednik *tożsamości użytkownika* (nazwy użytkownika i hasła lub certyfikatu) w przypadku aplikacji.
- Tożsamość zarządzana — tożsamość w usłudze Azure Active Directory, która jest automatycznie zarządzana przez platformę Azure. [Tożsamości zarządzanych](../active-directory/managed-identities-azure-resources/overview.md) używa się zazwyczaj podczas tworzenia aplikacji w chmurze w celu zarządzania poświadczeniami do uwierzytelniania w usługach platformy Azure.

## <a name="step-2-find-the-appropriate-role"></a>Krok 2. Znajdowanie odpowiedniej roli

Uprawnienia są pogrupowane w *definicji roli*. Zwykle jest nazywana *rolą*. Możesz wybrać jedną z kilku wbudowanych ról. Jeśli role wbudowane nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne role niestandardowe.

![Definicja roli w przypisaniu roli](./media/shared/rbac-role-definition.png)

Poniżej wymieniono cztery podstawowe role wbudowane. Pierwsze trzy są stosowane do wszystkich typów zasobów.

- [Właściciel](built-in-roles.md#owner) — ma pełny dostęp do wszystkich zasobów i jest uprawniony do udzielania dostępu innym osobom.
- [Współautor](built-in-roles.md#contributor) — może tworzyć wszystkie typy zasobów platformy Azure i zarządzać nimi, ale nie może udzielać dostępu innym osobom.
- [Reader](built-in-roles.md#reader) — może wyświetlać istniejące zasoby platformy Azure.
- [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) — może zarządzać dostępem użytkowników do zasobów platformy Azure.

Pozostałe role wbudowane umożliwiają zarządzanie określonymi zasobami platformy Azure. Na przykład rola [współautora maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) umożliwia użytkownikowi tworzenie maszyn wirtualnych i zarządzanie nimi.

1. Zacznij od kompleksowego artykułu, [wbudowane role platformy Azure](built-in-roles.md). Tabela znajdująca się w górnej części artykułu stanowi indeks szczegółowych informacji znajdujących się później w artykule.

1. W tym artykule przejdź do kategorii usługi (na przykład COMPUTE, Storage i Databases) dla zasobu, do którego chcesz przyznać uprawnienia. Najprostszym sposobem znalezienia tego, czego szukasz, jest przeszukanie na stronie odpowiedniego słowa kluczowego, takiego jak "BLOB", "maszyna wirtualna" i tak dalej.

1. Przejrzyj role wymienione w kategorii usługi i zidentyfikuj konkretne potrzebne operacje. Zawsze należy zacząć od najbardziej restrykcyjnej roli.

    Na przykład, jeśli podmiot zabezpieczeń musi odczytywać obiekty blob na koncie usługi Azure Storage, ale nie musi mieć dostępu do zapisu, wybierz opcję [czytnik danych magazynu obiektów BLOB](built-in-roles.md#storage-blob-data-reader) , a nie [współautor danych obiektów blob magazynu](built-in-roles.md#storage-blob-data-contributor) (a nie rolę [właściciela danych obiektu BLOB](built-in-roles.md#storage-blob-data-owner) na poziomie administratora). W razie konieczności można zawsze aktualizować przypisania ról.

1. Jeśli nie znajdziesz odpowiedniej roli, możesz utworzyć [rolę niestandardową](custom-roles.md).

## <a name="step-3-identify-the-needed-scope"></a>Krok 3. identyfikowanie wymaganego zakresu

*Zakres* to zestaw zasobów, w ramach którego jest przydzielany dostęp. Na platformie Azure można określić zakres na czterech poziomach: [Grupa zarządzania](../governance/management-groups/overview.md), subskrypcja, [Grupa zasobów](../azure-resource-manager/management/overview.md#resource-groups)i zasób. Zakresy mają strukturę opartą na relacji nadrzędny-podrzędny. Każdy poziom hierarchii sprawia, że zakres jest bardziej szczegółowy. Role można przypisywać na dowolnym z tych poziomów zakresu. Wybrany poziom określa, jak szeroko ma być stosowana rola. Niższe poziomy dziedziczą uprawnienia roli z wyższego poziomu. 

![Zakres w przypisaniu roli](./media/shared/rbac-scope.png)

Po przypisaniu roli w zakresie nadrzędnym uprawnienia te są dziedziczone do zakresów podrzędnych. Na przykład:

- Jeśli przypiszesz rolę [czytelnik](built-in-roles.md#reader) do użytkownika w zakresie grupy zarządzania, ten użytkownik będzie mógł odczytywać wszystkie wszystkie subskrypcje w grupie zarządzania.
- Jeśli przypiszesz rolę [czytelnik rozliczeń](built-in-roles.md#billing-reader) do grupy w zakresie subskrypcji, członkowie tej grupy mogą odczytywać dane dotyczące rozliczeń dla każdej grupy zasobów i zasobu w subskrypcji.
- Jeśli przypiszesz rolę [współautora](built-in-roles.md#contributor) do aplikacji na poziomie grupy zasobów, ta aplikacja będzie mogła zarządzać zasobami dowolnego typu w tej grupie zasobów, ale nie w innych grupach zasobów w ramach subskrypcji.

 Aby uzyskać więcej informacji, zobacz [Opis zakresu](scope-overview.md).

## <a name="step-4-check-your-prerequisites"></a>Krok 4. Sprawdzanie wymagań wstępnych

Aby przypisać role, należy zalogować się do użytkownika, do którego przypisano rolę z przypisaniami ról, takimi jak [właściciel](built-in-roles.md#owner) lub [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) w zakresie, w którym próbujesz przypisać rolę. Analogicznie, aby usunąć przypisanie roli, musisz mieć uprawnienie do usuwania przypisań ról.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Jeśli Twoje konto użytkownika nie ma uprawnień do przypisania roli w ramach subskrypcji, zostanie wyświetlony komunikat o błędzie informujący, że Twoje konto "nie ma autoryzacji do wykonania akcji" Microsoft. Authorization/roleAssignments/Write ". W takim przypadku należy skontaktować się z administratorami subskrypcji, ponieważ mogą oni przypisywać uprawnienia w Twoim imieniu.

## <a name="step-5-add-role-assignment"></a>Krok 5. Dodaj przypisanie roli

Po poznaniu podmiotu zabezpieczeń, roli i zakresu można przypisać rolę. Przypisania ról można dodawać przy użyciu interfejsów API Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure, zestawów SDK platformy Azure lub protokołu REST. W każdej subskrypcji można korzystać z maksymalnie **2000** przypisań ról. Ten limit obejmuje przypisania ról w ramach subskrypcji, grupy zasobów i zakresów zasobów. W każdej grupie zarządzania może istnieć maksymalnie **500** przypisań ról.

Zapoznaj się z następującymi artykułami, aby uzyskać szczegółowe instrukcje dotyczące dodawania przypisań ról.

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](role-assignments-portal.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu programu Azure PowerShell](role-assignments-powershell.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](role-assignments-cli.md)
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu API REST](role-assignments-rest.md)

## <a name="next-steps"></a>Następne kroki

- [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure Portal](quickstart-assign-role-user-portal.md)
