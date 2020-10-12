---
title: Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal — RBAC
description: Dowiedz się, jak określić zasoby, których użytkownicy, grupy, nazwy główne usług lub tożsamości zarządzane mają dostęp do usługi przy użyciu Azure Portal i kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f7d56ecc7fc6bd850fced33c2c1cf20902bb2df4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85361864"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] W tym artykule opisano sposób wyświetlania listy przypisań ról przy użyciu Azure Portal.

> [!NOTE]
> Jeśli organizacja ma funkcje zarządzania, które są używane przez usługę zarządzania [zasobami delegowanymi przez platformę Azure](../lighthouse/concepts/azure-delegated-resource-management.md), w tym miejscu nie będą wyświetlane przypisania ról autoryzowane przez tego dostawcę usług.

## <a name="list-role-assignments-for-a-user-or-group"></a>Wyświetl listę przypisań ról dla użytkownika lub grupy

Najprostszym sposobem wyświetlenia ról przypisanych do użytkownika lub grupy w ramach subskrypcji jest użycie okienka **przypisania ról platformy Azure** .

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

   ![Kontrola dostępu do subskrypcji — karta przypisania ról](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Wyświetl listę przypisań ról w zakresie

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

   ![Kontrola dostępu — karta przypisania ról](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Na karcie przypisania roli można zobaczyć, kto ma dostęp w tym zakresie. Należy zauważyć, że niektóre role należą do zakresu **tego zasobu**, a inne są **dziedziczone** z innego zakresu. Dostęp jest przypisany do tego zasobu lub Dziedziczony z przypisania do zakresu nadrzędnego.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Wyświetlanie listy przypisań ról dla użytkownika w zakresie

Aby wyświetlić listę dostępu dla użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej, należy wyświetlić listę przypisań ról. Wykonaj następujące kroki, aby wyświetlić listę przypisań ról dla pojedynczego użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej w określonym zakresie.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **Sprawdź dostęp**.

    ![Kontrola dostępu — karta Sprawdź dostęp](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na liście **Znajdź** wybierz typ podmiotu zabezpieczeń, dla którego chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail lub identyfikatory obiektów.

    ![Sprawdzanie dostępu — lista wyboru](./media/role-assignments-list-portal/check-access-select.png)

1. Kliknij podmiot zabezpieczeń, aby otworzyć okienko **przypisania**.

    ![Okienko przypisania](./media/role-assignments-list-portal/check-access-assignments.png)

    W tym okienku są wyświetlane role przypisane do wybranego podmiotu zabezpieczeń i zakresu. Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

## <a name="list-role-assignments-for-a-managed-identity"></a>Wyświetlanie listy przypisań ról dla tożsamości zarządzanej

Można wyświetlić listę przypisań ról dla tożsamości zarządzanych przez system i przypisanej przez użytkownika w określonym zakresie przy użyciu bloku **kontroli dostępu (IAM)** zgodnie z wcześniejszym opisem. W tej sekcji opisano, jak wyświetlić listę przypisań ról tylko dla tożsamości zarządzanej.

### <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

1. W Azure Portal Otwórz przypisaną przez system tożsamość zarządzaną.

1. W menu po lewej stronie kliknij pozycję **tożsamość**.

    ![Tożsamość zarządzana przypisana przez system](./media/shared/identity-system-assigned.png)

1. W obszarze **uprawnienia**kliknij pozycję **przypisania ról platformy Azure**.

    Zostanie wyświetlona lista ról przypisanych do wybranej tożsamości zarządzanej przypisanej przez system w różnych zakresach, takich jak grupa zarządzania, subskrypcja, Grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla tożsamości zarządzanej przypisanej do systemu](./media/shared/role-assignments-system-assigned.png)

1. Aby zmienić subskrypcję, kliknij listę **subskrypcji** .

### <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

1. W Azure Portal Otwórz tożsamość zarządzaną przypisaną przez użytkownika.

1. Kliknij pozycję **przypisania ról platformy Azure**.

    Zostanie wyświetlona lista ról przypisanych do wybranej tożsamości zarządzanej przypisanej przez użytkownika w różnych zakresach, takich jak grupa zarządzania, subskrypcja, Grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla tożsamości zarządzanej przypisanej do systemu](./media/shared/role-assignments-user-assigned.png)

1. Aby zmienić subskrypcję, kliknij listę **subskrypcji** .

## <a name="list-number-of-role-assignments"></a>Liczba przypisań ról

W każdej subskrypcji można korzystać z maksymalnie **2000** przypisań ról. Ten limit obejmuje przypisania ról w ramach subskrypcji, grupy zasobów i zakresów zasobów. Aby ułatwić śledzenie tego limitu, karta **przypisania ról** zawiera wykres zawierający listę przypisań ról dla bieżącej subskrypcji.

![Kontrola dostępu — liczba wykresów przypisań ról](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Jeśli zbliżasz się do maksymalnej liczby i podjęto próbę dodania kolejnych przypisań ról, w okienku **Dodaj przypisanie roli** zostanie wyświetlone ostrzeżenie. Aby dowiedzieć się, jak można zmniejszyć liczbę przypisań ról, zobacz [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Kontrola dostępu — ostrzeżenie o przypisaniu roli](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments-preview"></a>Pobieranie przypisań ról (wersja zapoznawcza)

Przypisania ról można pobrać w zakresie w formacie CSV lub JSON. Może to być przydatne, jeśli musisz sprawdzić listę w arkuszu kalkulacyjnym lub pobrać spis podczas migrowania subskrypcji.

> [!IMPORTANT]
> Pobieranie przypisań ról jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podczas pobierania przypisań ról należy pamiętać o następujących kryteriach:

- Jeśli nie masz uprawnień do odczytu katalogu, takiego jak rola czytelnicy katalogów, kolumny DisplayName, SignInName i ObjectType będą puste.
- Przypisania ról, których podmiot zabezpieczeń został usunięty, nie są uwzględniane.
- Dostęp przyznany do klasycznych administratorów nie jest uwzględniany.

Wykonaj następujące kroki, aby pobrać przypisania ról w zakresie.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres, w którym chcesz pobrać przypisania roli. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij pozycję **Pobierz przypisania ról (wersja zapoznawcza)** , aby otworzyć okienko pobieranie przypisań ról.

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

    ![Pobieranie przypisań ról jako woluminów CSV](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](role-assignments-portal.md)
- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)
