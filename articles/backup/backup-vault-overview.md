---
title: Omówienie magazynów kopii zapasowych
description: Omówienie magazynów kopii zapasowych.
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: e2d720da9474a35870de01559201d22c9e5b567f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739084"
---
# <a name="backup-vaults-overview"></a>Omówienie magazynów kopii zapasowych

W tym artykule opisano funkcje magazynu usługi Backup. Magazyn usługi Backup to jednostka magazynu na platformie Azure, w którym znajdują się dane kopii zapasowej dla niektórych nowych obciążeń, Azure Backup obsługuje. Magazynów kopii zapasowych można używać do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takich jak serwery Azure Database for PostgreSQL i nowsze obciążenia, Azure Backup będą obsługiwać. Magazyny kopii zapasowych ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zminimalizowaniu narzutów związanych z zarządzaniem. Magazyny kopii zapasowych są oparte na Azure Resource Manager platformy Azure, który udostępnia takie funkcje jak:

- **Ulepszone możliwości zabezpieczania danych kopii zapasowych:** magazyny kopii zapasowych Azure Backup zapewniają funkcje zabezpieczeń w celu ochrony kopii zapasowych w chmurze. Funkcje zabezpieczeń zapewniają bezpieczeństwo kopii zapasowych i bezpieczne odzyskiwanie danych nawet w przypadku naruszenia zabezpieczeń serwerów produkcyjnych i kopii zapasowych. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Kontrola dostępu oparta na rolach (RBAC)** na platformie Azure: kontrola dostępu oparta na rolach platformy Azure zapewnia precyzyjne zarządzanie dostępem na platformie Azure. [Platforma Azure udostępnia różne wbudowane role,](../role-based-access-control/built-in-roles.md)a Azure Backup ma trzy wbudowane role do zarządzania [punktami odzyskiwania.](backup-rbac-rs-vault.md) Magazyny kopii zapasowych są zgodne z funkcją RBAC platformy Azure, która ogranicza dostęp do kopii zapasowych i przywracania do zdefiniowanego zestawu ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Ustawienia magazynu w magazynie kopii zapasowych

Magazyn kopii zapasowych to jednostka, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn usługi Backup zawiera również zasady tworzenia kopii zapasowych skojarzone z chronionymi maszynami wirtualnymi.

- Azure Backup automatycznie obsługuje magazyn dla magazynu. Wybierz nadmiarowość magazynu, która odpowiada potrzebom biznesowym podczas tworzenia magazynu usługi Backup.

- Aby dowiedzieć się więcej na temat nadmiarowości magazynu, zobacz następujące artykuły dotyczące [nadmiarowości geograficznej](../storage/common/storage-redundancy.md#geo-redundant-storage) [i](../storage/common/storage-redundancy.md#locally-redundant-storage) lokalnej.

## <a name="encryption-settings-in-the-backup-vault"></a>Ustawienia szyfrowania w magazynie usługi Backup

W tej sekcji omówiono dostępne opcje szyfrowania danych kopii zapasowej przechowywanych w magazynie usługi Backup. Azure Backup używa aplikacji **Usługi zarządzania** kopiami zapasowymi do uzyskiwania Azure Key Vault, ale nie tożsamości zarządzanej magazynu usługi Backup.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Szyfrowanie danych kopii zapasowej przy użyciu kluczy zarządzanych przez platformę

Domyślnie wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę. Aby włączyć to szyfrowanie, nie musisz nic jawnie akcję. Dotyczy to wszystkich obciążeń, których kopia zapasowa jest dawana w magazynie usługi Backup.

## <a name="create-a-backup-vault"></a>Tworzenie magazynu kopii zapasowych

Magazyn usługi Backup to jednostka zarządzania, która przechowuje punkty odzyskiwania utworzone w czasie i udostępnia interfejs do wykonywania operacji związanych z kopiami zapasowymi. Obejmują one wykonywanie kopii zapasowych na żądanie, wykonywanie operacji przywracania i tworzenie zasad kopii zapasowych.

Aby utworzyć magazyn kopii zapasowych, wykonaj następujące kroki.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Tworzenie magazynu kopii zapasowych

1. W **polu wyszukiwania** wpisz Magazyny kopii zapasowych.
1. W **obszarze Usługi** wybierz pozycję **Magazyny kopii zapasowych.**
1. Na stronie **Magazyny kopii zapasowych** wybierz pozycję **Dodaj**.
1. Na karcie **Podstawowe w obszarze** Szczegóły **projektu** upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz pozycję **Utwórz nową** grupę zasobów. Wpisz nazwę *myResourceGroup*.

  ![Tworzenie nowej grupy zasobów](./media/backup-vault-overview/new-resource-group.png)

1. W **obszarze Szczegóły wystąpienia** wpisz *myVault* jako nazwę magazynu **kopii** zapasowych i wybierz swój region, w tym przypadku *Wschodnie* wystąpienia USA dla **regionu**.
1. Teraz wybierz **nadmiarowość magazynu.** Nadmiarowości magazynu nie można zmienić po ochronie elementów w magazynie.
1. Zalecamy, aby w przypadku korzystania z platformy Azure jako podstawowego punktu końcowego magazynu kopii zapasowych nadal używać domyślnego ustawienia **geograficznie nadmiarowego.**
1. Jeśli nie używasz platformy Azure jako punktu końcowego podstawowego magazynu kopii zapasowych, wybierz pozycję **Lokalnie nadmiarowy**, co zmniejszy koszty magazynów platformy Azure.
1. Dowiedz się więcej o [nadmiarowości geograficznej](../storage/common/storage-redundancy.md#geo-redundant-storage) [i](../storage/common/storage-redundancy.md#locally-redundant-storage) lokalnej.

  ![Wybieranie nadmiarowości magazynu](./media/backup-vault-overview/storage-redundancy.png)

1. Wybierz przycisk Przejrzyj i utwórz znajdujący się u dołu strony.

    ![Wybierz pozycję Przejrzyj i utwórz](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Usuwanie magazynu kopii zapasowych

W tej sekcji opisano sposób usuwania magazynu usługi Backup. Zawiera on instrukcje dotyczące usuwania zależności, a następnie usuwania magazynu.

### <a name="before-you-start"></a>Przed rozpoczęciem

Nie można usunąć magazynu usługi Backup z dowolną z następujących zależności:

- Nie można usunąć magazynu zawierającego chronione źródła danych (na przykład serwery usługi Azure Database for PostgreSQL).
- Nie można usunąć magazynu zawierającego dane kopii zapasowej.

Jeśli spróbujesz usunąć magazyn bez usuwania zależności, wystąpią następujące komunikaty o błędach:

>Nie można usunąć magazynu kopii zapasowych, ponieważ w magazynie istnieją wystąpienia kopii zapasowych lub zasady tworzenia kopii zapasowych. Usuń wszystkie wystąpienia kopii zapasowych i zasady tworzenia kopii zapasowych, które znajdują się w magazynie, a następnie spróbuj usunąć magazyn.

### <a name="proper-way-to-delete-a-vault"></a>Prawidłowy sposób usuwania magazynu

>[!WARNING]
Następująca operacja jest destruktywna i nie można jej cofnąć. Wszystkie dane kopii zapasowej i elementy kopii zapasowej skojarzone z chronionym serwerem zostaną trwale usunięte. Zachowaj przy tym ostrożność.

Aby prawidłowo usunąć magazyn, należy wykonać kroki opisane w tej kolejności:

- Sprawdź, czy istnieją jakieś chronione elementy:
  - Przejdź do **usługi Backup Instances** na pasku nawigacyjnym po lewej stronie. Wszystkie elementy wymienione w tym miejscu należy najpierw usunąć.

Po ukończeniu tych kroków możesz kontynuować usuwanie magazynu.

### <a name="delete-the-backup-vault"></a>Usuwanie magazynu kopii zapasowych

Jeśli w magazynie nie ma już żadnych elementów, wybierz pozycję **Usuń na pulpicie** nawigacyjnym magazynu. Zostanie wyświetlony tekst potwierdzenia z pytaniem, czy chcesz usunąć magazyn.

![Usuwanie magazynu](./media/backup-vault-overview/delete-vault.png)

1. Wybierz **pozycję Tak,** aby sprawdzić, czy chcesz usunąć magazyn. Magazyn zostanie usunięty. Portal powróci do menu **Nowa** usługa.

## <a name="monitor-and-manage-the-backup-vault"></a>Monitorowanie magazynu kopii zapasowych i zarządzanie tym magazynem

W tej sekcji wyjaśniono,  jak używać pulpitu nawigacyjnego Omówienie magazynu kopii zapasowych do monitorowania magazynów kopii zapasowych i zarządzania nimi. Okienko przeglądu zawiera dwa kafelki: **Zadania** i **Wystąpienia**.

![Omówienie pulpitu nawigacyjnego](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Zarządzanie wystąpieniami kopii zapasowych

Kafelek **Zadania** zawiera podsumowany widok wszystkich powiązanych zadań tworzenia kopii zapasowych i przywracania w magazynie usługi Backup. Wybranie dowolnej liczby na tym kafelku umożliwia wyświetlenie dodatkowych informacji o zadaniach dla określonego typu źródła danych, typu operacji i stanu.

![Wystąpienia kopii zapasowych](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Zarządzanie zadaniami tworzenia kopii zapasowych

Kafelek **Wystąpienia kopii** zapasowych zawiera podsumowanie wszystkich wystąpień kopii zapasowych w magazynie kopii zapasowych. Wybranie dowolnej liczby na tym kafelku umożliwia wyświetlenie dodatkowych informacji o wystąpieniach kopii zapasowych dla określonego typu źródła danych i stanu ochrony.

![Zadania tworzenia kopii zapasowej](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie kopii zapasowej w bazach danych Azure PostgreSQL](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
