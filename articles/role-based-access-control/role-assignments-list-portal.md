---
title: Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal — RBAC
description: Dowiedz się, jak określić zasoby, których użytkownicy, grupy, nazwy główne usług lub tożsamości zarządzane mają dostęp do usługi przy użyciu Azure Portal i kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.openlocfilehash: 06a9a955e8a963f94b1885abf5a920cb96c01940
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007440"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] W tym artykule opisano sposób wyświetlania listy przypisań ról przy użyciu Azure Portal.

> [!NOTE]
> Jeśli organizacja ma funkcje zarządzania, które są używane przez usługę zarządzania [zasobami delegowanymi przez platformę Azure](../lighthouse/concepts/azure-delegated-resource-management.md), w tym miejscu nie będą wyświetlane przypisania ról autoryzowane przez tego dostawcę usług.

## <a name="list-role-assignments-for-a-user-or-group"></a>Wyświetl listę przypisań ról dla użytkownika lub grupy

Szybki sposób wyświetlania ról przypisanych do użytkownika lub grupy w ramach subskrypcji to użycie okienka **przypisania ról platformy Azure** .

1. W Azure Portal wybierz pozycję **wszystkie usługi** z menu Azure Portal.

1. Wybierz pozycję **Azure Active Directory** a następnie wybierz pozycję **Użytkownicy** lub **grupy**.

1. Kliknij użytkownika lub grupę, dla której chcesz wyświetlić listę przypisań ról.

1. Kliknij pozycję **przypisania ról platformy Azure**.

    Zostanie wyświetlona lista ról przypisanych do wybranego użytkownika lub grupy w różnych zakresach, takich jak grupa zarządzania, subskrypcja, Grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla użytkownika](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. Aby zmienić subskrypcję, kliknij listę **subskrypcje** .

## <a name="list-owners-of-a-subscription"></a>Wyświetl listę właścicieli subskrypcji

Użytkownicy, którym przypisano rolę [właściciela](built-in-roles.md#owner) dla subskrypcji, mogą zarządzać wszystkimi subskrypcjami w ramach subskrypcji. Wykonaj następujące kroki, aby wyświetlić listę właścicieli subskrypcji.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Kliknij subskrypcję, dla której chcesz wyświetlić listę właścicieli.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

1. Przewiń do sekcji **właściciele** , aby wyświetlić wszystkich użytkowników, którym przypisano rolę właściciela dla tej subskrypcji.

   ![Kontrola dostępu do subskrypcji — karta przypisania ról](./media/role-assignments-list-portal/sub-access-control-role-assignments-owners.png)

## <a name="list-role-assignments-at-a-scope"></a>Wyświetl listę przypisań ról w zakresie

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów** lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

   ![Kontrola dostępu — karta przypisania ról](./media/role-assignments-list-portal/rg-access-control-role-assignments.png)

   Na karcie przypisania roli można zobaczyć, kto ma dostęp w tym zakresie. Należy zauważyć, że niektóre role należą do zakresu **tego zasobu**, a inne są **dziedziczone** z innego zakresu. Dostęp jest przypisany do tego zasobu lub Dziedziczony z przypisania do zakresu nadrzędnego.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Wyświetlanie listy przypisań ról dla użytkownika w zakresie

Aby wyświetlić listę dostępu dla użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej, należy wyświetlić listę przypisań ról. Wykonaj następujące kroki, aby wyświetlić listę przypisań ról dla pojedynczego użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej w określonym zakresie.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów** lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Sprawdź dostęp**.

    ![Kontrola dostępu do grupy zasobów — sprawdzanie dostępu do karty](./media/role-assignments-list-portal/rg-access-control-check-access.png)

1. Z listy **Znajdź** wybierz użytkownika, grupę, nazwę główną usługi lub tożsamość zarządzaną, dla której chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail lub identyfikatory obiektów.

    ![Sprawdzanie dostępu — lista wyboru](./media/shared/rg-check-access-select.png)

1. Kliknij podmiot zabezpieczeń, aby otworzyć okienko **przypisania**.

    W tym okienku można zobaczyć dostęp do wybranego podmiotu zabezpieczeń w tym zakresie i dziedziczyć ten zakres. Przypisania w zakresach podrzędnych nie są wyświetlane. Zobaczysz następujące przypisania:

    - Przypisań ról dodano do usługi Azure RBAC.
    - Odmowa przypisań dodanych przy użyciu planów platformy Azure lub aplikacji zarządzanych przez platformę Azure.
    - Klasyczny administrator usługi lub Co-Administrator przypisań klasycznych wdrożeń. 

    ![Okienko przypisania](./media/shared/rg-check-access-assignments-user.png)

## <a name="list-role-assignments-for-a-managed-identity"></a>Wyświetlanie listy przypisań ról dla tożsamości zarządzanej

Można wyświetlić listę przypisań ról dla tożsamości zarządzanych przez system i przypisanej przez użytkownika w określonym zakresie przy użyciu bloku **kontroli dostępu (IAM)** zgodnie z wcześniejszym opisem. W tej sekcji opisano, jak wyświetlić listę przypisań ról tylko dla tożsamości zarządzanej.

### <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

1. W Azure Portal Otwórz przypisaną przez system tożsamość zarządzaną.

1. W menu po lewej stronie kliknij pozycję **tożsamość**.

    ![Tożsamość zarządzana przypisana przez system](./media/shared/identity-system-assigned.png)

1. W obszarze **uprawnienia** kliknij pozycję **przypisania ról platformy Azure**.

    Zostanie wyświetlona lista ról przypisanych do wybranej tożsamości zarządzanej przypisanej przez system w różnych zakresach, takich jak grupa zarządzania, subskrypcja, Grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla tożsamości zarządzanej przypisanej do systemu](./media/shared/role-assignments-system-assigned.png)

1. Aby zmienić subskrypcję, kliknij listę **subskrypcji** .

### <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

1. W Azure Portal Otwórz tożsamość zarządzaną przypisaną przez użytkownika.

1. Kliknij pozycję **przypisania ról platformy Azure**.

    Zostanie wyświetlona lista ról przypisanych do wybranej tożsamości zarządzanej przypisanej przez użytkownika w różnych zakresach, takich jak grupa zarządzania, subskrypcja, Grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Zrzut ekranu pokazujący przypisania ról dla tożsamości zarządzanej przypisanej przez użytkownika.](./media/shared/role-assignments-user-assigned.png)

1. Aby zmienić subskrypcję, kliknij listę **subskrypcji** .

## <a name="list-number-of-role-assignments"></a>Liczba przypisań ról

W każdej subskrypcji można korzystać z maksymalnie **2000** przypisań ról. Ten limit obejmuje przypisania ról w ramach subskrypcji, grupy zasobów i zakresów zasobów. Aby ułatwić śledzenie tego limitu, karta **przypisania ról** zawiera wykres zawierający listę przypisań ról dla bieżącej subskrypcji.

![Kontrola dostępu — liczba wykresów przypisań ról](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Jeśli zbliżasz się do maksymalnej liczby i podjęto próbę dodania kolejnych przypisań ról, w okienku **Dodaj przypisanie roli** zostanie wyświetlone ostrzeżenie. Aby dowiedzieć się, jak można zmniejszyć liczbę przypisań ról, zobacz [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Kontrola dostępu — ostrzeżenie o przypisaniu roli](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>Pobieranie przypisań ról

Przypisania ról można pobrać w zakresie w formacie CSV lub JSON. Może to być przydatne, jeśli musisz sprawdzić listę w arkuszu kalkulacyjnym lub pobrać spis podczas migrowania subskrypcji.

Podczas pobierania przypisań ról należy pamiętać o następujących kryteriach:

- Jeśli nie masz uprawnień do odczytu katalogu, takiego jak rola czytelnicy katalogów, kolumny DisplayName, SignInName i ObjectType będą puste.
- Przypisania ról, których podmiot zabezpieczeń został usunięty, nie są uwzględniane.
- Dostęp przyznany do klasycznych administratorów nie jest uwzględniany.

Wykonaj następujące kroki, aby pobrać przypisania ról w zakresie.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres, w którym chcesz pobrać przypisania roli. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów** lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij pozycję **Pobierz przypisania ról** , aby otworzyć okienko pobieranie przypisań ról.

    ![Kontrola dostępu — pobieranie przypisań ról](./media/role-assignments-list-portal/download-role-assignments.png)

1. Użyj pól wyboru, aby wybrać przypisania ról, które chcesz dołączyć do pobranego pliku.

    - **Odziedziczone** — Dołącz dziedziczone przypisania ról dla bieżącego zakresu.
    - **W bieżącym zakresie** — Uwzględnij przypisania ról dla bieżącego zakresu.
    - **Elementy podrzędne** — Uwzględnij przypisania ról na poziomach poniżej bieżącego zakresu. To pole wyboru jest wyłączone dla zakresu grupy zarządzania.

1. Wybierz format pliku, który może być wartościami rozdzielonymi przecinkami (CSV) lub JavaScript Object Notation (JSON).

1. Określ nazwę pliku.

1. Kliknij przycisk **Rozpocznij** , aby rozpocząć pobieranie.

    Poniżej przedstawiono przykłady danych wyjściowych dla każdego formatu pliku.

    ![Pobieranie przypisań ról jako woluminów CSV](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![Zrzut ekranu przedstawiający pobrane przypisania ról w formacie JSON.](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](role-assignments-portal.md)
- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)
