---
title: Zarządzanie sieciami wirtualnymi — Azure Portal-Azure Database for PostgreSQL-elastyczny serwer
description: Tworzenie sieci wirtualnych i zarządzanie nimi dla Azure Database for PostgreSQL-elastyczny serwer przy użyciu Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 746f15d2d712f4b571d3f27e3535c69f5f4f9732
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732772"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Tworzenie sieci wirtualnych i zarządzanie nimi dla Azure Database for PostgreSQL-elastyczny serwer przy użyciu Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Serwer elastyczny Azure Database for PostgreSQL obsługuje dwa typy wzajemnie wykluczających się metod łączności sieciowej w celu nawiązania połączenia z serwerem elastycznym. Te dwie opcje są następujące:

* Dostęp publiczny (dozwolone adresy IP)
* Dostęp prywatny (integracja z siecią wirtualną)

W tym artykule skupmy się na tworzeniu serwera PostgreSQL z **dostępem prywatnym (Integracja z siecią wirtualną)** przy użyciu Azure Portal. Za pomocą dostępu prywatnego (Integracja z siecią wirtualną) można wdrożyć elastyczny serwer w ramach własnego [Virtual Network platformy Azure](../../virtual-network/virtual-networks-overview.md). Sieci wirtualne platformy Azure zapewniają prywatną i bezpieczną komunikację sieciową. W przypadku dostępu prywatnego połączenia z serwerem PostgreSQL są ograniczone do sieci wirtualnej. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [dostępem prywatnym (Integracja z siecią wirtualną)](./concepts-networking.md#private-access-vnet-integration).

Serwer elastyczny można wdrożyć w sieci wirtualnej i podsieci podczas tworzenia serwera. Po wdrożeniu serwera elastycznego nie można przenieść go do innej sieci wirtualnej, podsieci ani do *dostępu publicznego (dozwolone adresy IP)* .

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć elastyczny serwer w sieci wirtualnej, potrzebne są:
- [Sieć wirtualna](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Sieć wirtualna i podsieć powinna znajdować się w tym samym regionie i subskrypcji co serwer elastyczny.

-  Aby [delegować podsieć](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) do **firmy Microsoft. DBforPostgreSQL/flexibleServers**. To delegowanie oznacza, że tylko Azure Database for PostgreSQL elastycznych serwerów może korzystać z tej podsieci. W podsieci delegowanej nie mogą znajdować się żadne inne typy zasobów platformy Azure.
-  Dodaj `Microsoft.Storage` do punktu końcowego usługi dla podsieci delegowanej do elastycznych serwerów. W tym celu wykonaj następujące czynności:
     1. Przejdź do strony sieci wirtualnej.
     2. Wybierz sieć wirtualną, w której planujesz wdrożyć serwer elastyczny.
     3. Wybierz podsieć delegowaną dla serwera elastycznego.
     4. Na ekranie ściągania w obszarze **punkt końcowy usługi** wybierz pozycję `Microsoft.storage` z listy rozwijanej.
     5. Zapisz zmiany.


## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Utwórz serwer elastyczny Azure Database for PostgreSQL w już istniejącej sieci wirtualnej

1. Wybierz pozycję **Utwórz zasób** (+) w lewym górnym rogu portalu.
2. Wybierz pozycję **bazy danych**  >  **Azure Database for PostgreSQL**. Możesz również wprowadzić **PostgreSQL** w polu wyszukiwania, aby znaleźć usługę.
3. Wybierz pozycję **elastyczny serwer** jako opcję wdrożenia.
4. Wypełnij formularz **podstawy** .
5. Przejdź do karty **Sieć** , aby skonfigurować sposób nawiązywania połączenia z serwerem.
6. W polu **Metoda łączności** wybierz pozycję **dostęp prywatny (Integracja z siecią wirtualną)**. Przejdź do **Virtual Network** i wybierz już istniejącą *sieć wirtualną* i *podsieć* utworzoną w ramach wymagań wstępnych.
7. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć konfigurację elastycznego serwera.
8. Wybierz pozycję **Utwórz**, aby aprowizować serwer. Inicjowanie obsługi może potrwać kilka minut.

>[!Note]
> Po wdrożeniu elastycznego serwera do sieci wirtualnej i podsieci nie można przenieść go do dostępu publicznego (dozwolone adresy IP).
## <a name="next-steps"></a>Następne kroki
- [Utwórz sieć wirtualną serwera Azure Database for PostgreSQL elastyczną i zarządzaj nią przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-virtual-network-cli.md).
- Dowiedz się więcej o [sieci w Azure Database for PostgreSQL-elastycznym serwerze](./concepts-networking.md)
- Dowiedz się więcej o [Azure Database for PostgreSQL-elastyczną sieć wirtualną serwera](./concepts-networking.md#private-access-vnet-integration).