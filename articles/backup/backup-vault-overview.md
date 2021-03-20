---
title: Omówienie magazynów kopii zapasowych
description: Omówienie magazynów kopii zapasowych.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: c189997ecc4814917182246b35003649d317ac77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92091291"
---
# <a name="backup-vaults-overview"></a>Magazyny kopii zapasowych — Omówienie

W tym artykule opisano funkcje magazynu kopii zapasowych. Magazyn kopii zapasowych to jednostka magazynowa na platformie Azure, która przechowuje dane kopii zapasowej dla niektórych nowszych obciążeń obsługiwanych przez Azure Backup. Magazynów kopii zapasowych można używać do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takich Azure Database for PostgreSQL serwerów i nowszych obciążeń, które Azure Backup będą obsługiwane. Magazyny kopii zapasowych ułatwiają organizowanie danych kopii zapasowej, jednocześnie minimalizując koszty zarządzania. Magazyny kopii zapasowych bazują na modelu Azure Resource Manager platformy Azure, który zapewnia następujące funkcje:

- **Udoskonalone funkcje ułatwiające zabezpieczanie danych kopii zapasowych**: z magazynami kopii zapasowych, Azure Backup zapewnia funkcje zabezpieczeń, aby chronić kopie zapasowe w chmurze. Funkcje zabezpieczeń zapewniają możliwość zabezpieczenia kopii zapasowych i bezpiecznego odzyskiwania danych, nawet jeśli naruszone są serwery produkcyjne i zapasowe. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)**: usługa Azure RBAC zapewnia precyzyjną kontrolę zarządzania dostępem na platformie Azure. [Platforma Azure udostępnia różne wbudowane role](../role-based-access-control/built-in-roles.md), a Azure Backup ma trzy [wbudowane role do zarządzania punktami odzyskiwania](backup-rbac-rs-vault.md). Magazyny kopii zapasowych są zgodne z funkcją RBAC platformy Azure, która ogranicza dostęp i przywracanie do zdefiniowanego zestawu ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Ustawienia magazynu w magazynie kopii zapasowych

Magazyn kopii zapasowych jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn kopii zapasowych zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z chronionymi maszynami wirtualnymi.

- Azure Backup automatycznie obsługuje magazyn dla magazynu. Wybierz nadmiarowość magazynu odpowiadającą potrzebom firmy podczas tworzenia magazynu kopii zapasowych.

- Aby dowiedzieć się więcej o nadmiarowości magazynu, zapoznaj się z tymi artykułami dotyczącymi nadmiarowości [geograficznej](../storage/common/storage-redundancy.md#geo-redundant-storage) i [lokalnej](../storage/common/storage-redundancy.md#locally-redundant-storage) .

## <a name="encryption-settings-in-the-backup-vault"></a>Ustawienia szyfrowania w magazynie kopii zapasowych

W tej sekcji omówiono opcje szyfrowania danych kopii zapasowej przechowywanych w magazynie kopii zapasowych.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez platformę

Domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Aby włączyć szyfrowanie, nie trzeba podejmować żadnych działań jawnych. Ma to zastosowanie do wszystkich obciążeń, których kopie zapasowe są tworzone w magazynie kopii zapasowych.

## <a name="create-a-backup-vault"></a>Tworzenie magazynu kopii zapasowych

Magazyn kopii zapasowych to jednostka zarządzania, która przechowuje punkty odzyskiwania utworzone w czasie i udostępnia interfejs do wykonywania operacji związanych z tworzeniem kopii zapasowych. Obejmują one wykonywanie kopii zapasowych na żądanie, wykonywanie operacji przywracania i tworzenie zasad kopii zapasowych.

Aby utworzyć magazyn kopii zapasowych, wykonaj następujące kroki.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Utwórz magazyn kopii zapasowych

1. W polu wyszukiwania wpisz **magazyny kopii zapasowych** .
1. W obszarze **usługi** wybierz pozycję **magazyny kopii zapasowych**.
1. Na stronie **magazyny kopii zapasowych** wybierz pozycję **Dodaj**.
1. Na **karcie podstawy** w obszarze **szczegóły projektu** upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz pozycję **Utwórz nową** grupę zasobów. Wpisz nazwę *myResourceGroup*.

  ![Utwórz nową grupę zasobów](./media/backup-vault-overview/new-resource-group.png)

1. W **obszarze Szczegóły wystąpienia**, *wpisz webmagazyn* dla **nazwy magazynu kopii zapasowych** i wybierz wybrany region, w tym przypadku *Wschodnie stany USA* dla **regionu**.
1. Teraz wybierz **nadmiarowość magazynu**. Nie można zmienić nadmiarowości magazynu po włączeniu ochrony elementów do magazynu.
1. Zalecamy, aby Jeśli używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, Kontynuuj, aby użyć domyślnego ustawienia **geograficznie nadmiarowego** .
1. Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure.
1. Dowiedz się więcej o nadmiarowości [geograficznym](../storage/common/storage-redundancy.md#geo-redundant-storage) i [lokalnym](../storage/common/storage-redundancy.md#locally-redundant-storage) .

  ![Wybieranie nadmiarowości magazynu](./media/backup-vault-overview/storage-redundancy.png)

1. Wybierz przycisk Recenzja + tworzenie w dolnej części strony.

    ![Wybieranie opcji Recenzja + tworzenie](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Usuwanie magazynu kopii zapasowych

W tej sekcji opisano sposób usuwania magazynu kopii zapasowych. Zawiera instrukcje dotyczące usuwania zależności, a następnie usuwania magazynu.

### <a name="before-you-start"></a>Przed rozpoczęciem

Nie można usunąć magazynu kopii zapasowych z następującymi zależnościami:

- Nie można usunąć magazynu zawierającego chronione źródła danych (na przykład serwery usługi Azure Database for PostgreSQL).
- Nie można usunąć magazynu, który zawiera dane kopii zapasowej.

Jeśli spróbujesz usunąć magazyn bez usuwania zależności, pojawią się następujące komunikaty o błędach:

>Nie można usunąć magazynu kopii zapasowych, ponieważ istnieją wystąpienia kopii zapasowej lub zasady kopii zapasowych w magazynie. Usuń wszystkie wystąpienia kopii zapasowych i zasady tworzenia kopii zapasowych, które znajdują się w magazynie, a następnie spróbuj usunąć magazyn.

### <a name="proper-way-to-delete-a-vault"></a>Właściwy sposób usuwania magazynu

>[!WARNING]
Następująca operacja jest destrukcyjne i nie można jej cofnąć. Wszystkie dane kopii zapasowej i elementy kopii zapasowej skojarzone z chronionym serwerem zostaną trwale usunięte. Zachowaj przy tym ostrożność.

Aby prawidłowo usunąć magazyn, należy wykonać czynności opisane w następującej kolejności:

- Należy sprawdzić, czy istnieją jakieś chronione elementy:
  - Przejdź do pozycji **wystąpienia kopii zapasowej** na lewym pasku nawigacyjnym. Wszystkie elementy wymienione tutaj muszą zostać usunięte jako pierwsze.

Po wykonaniu tych kroków można nadal usunąć magazyn.

### <a name="delete-the-backup-vault"></a>Usuwanie magazynu kopii zapasowych

Jeśli w magazynie nie ma więcej elementów, wybierz pozycję **Usuń** na pulpicie nawigacyjnym magazynu. Zostanie wyświetlony tekst potwierdzenia z pytaniem, czy chcesz usunąć magazyn.

![Usuwanie magazynu](./media/backup-vault-overview/delete-vault.png)

1. Wybierz pozycję **tak** , aby sprawdzić, czy chcesz usunąć magazyn. Magazyn zostanie usunięty. Portal powróci do menu **Nowa** usługa.

## <a name="monitor-and-manage-the-backup-vault"></a>Monitorowanie magazynu kopii zapasowych i zarządzanie nim

W tej sekcji opisano sposób użycia pulpitu nawigacyjnego **przeglądu** magazynu kopii zapasowych do monitorowania magazynów kopii zapasowych i zarządzania nimi. Okienko przegląd zawiera dwa kafelki: **zadania** i **wystąpienia**.

![Pulpit nawigacyjny przeglądu](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Zarządzanie wystąpieniami kopii zapasowych

Na kafelku **zadania** uzyskasz podsumowanie wszystkich zadań związanych z tworzeniem kopii zapasowych i przywracaniem w magazynie kopii zapasowych. Wybranie dowolnej liczby z tego kafelka pozwala wyświetlić więcej informacji na temat zadań dla określonego typu źródła danych, typu operacji i stanu.

![Wystąpienia kopii zapasowej](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Zarządzanie zadaniami tworzenia kopii zapasowych

Na kafelku **wystąpienia kopii zapasowej** można uzyskać widok podsumowania wszystkich wystąpień kopii zapasowych w magazynie kopii zapasowych. Wybranie dowolnej liczby z tego kafelka umożliwia wyświetlenie dodatkowych informacji o wystąpieniach kopii zapasowych dla określonego typu źródła danych i stanu ochrony.

![Zadania tworzenia kopii zapasowej](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie kopii zapasowych w bazach danych usługi Azure PostgreSQL](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
