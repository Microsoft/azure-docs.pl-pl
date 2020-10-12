---
title: Zarządzanie sieciami wirtualnymi — Azure Portal-Azure Database for MySQL-elastyczny serwer
description: Tworzenie sieci wirtualnych i zarządzanie nimi dla Azure Database for MySQL-elastyczny serwer przy użyciu Azure Portal
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 28506ea5afe085e9f52e40495d9931ae0aeddf32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937312"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Tworzenie sieci wirtualnych i zarządzanie nimi dla Azure Database for MySQL-elastyczny serwer przy użyciu Azure Portal

> [!IMPORTANT]
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

Usługa Azure Database for MySQL — elastyczny serwer obsługuje dwa typy wzajemnie wykluczających się metod łączności sieciowej umożliwiających nawiązanie połączenia z serwerem elastycznym. Te dwie opcje są następujące:

- Dostęp publiczny (dozwolone adresy IP)
- Dostęp prywatny (integracja z siecią wirtualną)

W tym artykule opisano Tworzenie serwera MySQL z **dostępem prywatnym (Integracja z siecią wirtualną)** przy użyciu Azure Portal. Za pomocą dostępu prywatnego (Integracja z siecią wirtualną) można wdrożyć elastyczny serwer w ramach własnego [Virtual Network platformy Azure](../../virtual-network/virtual-networks-overview.md). Sieci wirtualne platformy Azure zapewniają prywatną i bezpieczną komunikację sieciową. W przypadku dostępu prywatnego połączenia z serwerem MySQL są ograniczone do sieci wirtualnej. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [dostępem prywatnym (Integracja z siecią wirtualną)](./concepts-networking.md#private-access-vnet-integration).

Serwer elastyczny można wdrożyć w sieci wirtualnej i podsieci podczas tworzenia serwera. Po wdrożeniu serwera elastycznego nie można przenieść go do innej sieci wirtualnej, podsieci ani do *dostępu publicznego (dozwolone adresy IP)* .

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć elastyczny serwer w sieci wirtualnej, potrzebne są:
- [Sieć wirtualna](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Sieć wirtualna i podsieć powinna znajdować się w tym samym regionie i subskrypcji co serwer elastyczny.

-  [Delegowanie podsieci](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) do elementu **Microsoft.DBforMySQL/flexibleServers**. Ta delegacja oznacza, że tylko usługa Azure Database for MySQL — elastyczny serwer może korzystać z tej podsieci. W podsieci delegowanej nie mogą znajdować się żadne inne typy zasobów platformy Azure.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Utwórz Azure Database for MySQL elastyczny serwer w już istniejącej sieci wirtualnej

1. Wybierz pozycję **Utwórz zasób** (+) w lewym górnym rogu portalu.
2. Wybierz pozycję **bazy danych**  >  **Azure Database for MySQL**. Możesz również wprowadzić **MySQL** w polu wyszukiwania, aby znaleźć usługę.
3. Wybierz pozycję **elastyczny serwer** jako opcję wdrożenia.
4. Wypełnij formularz **podstawy** .
5. Przejdź do karty **Sieć** , aby skonfigurować sposób nawiązywania połączenia z serwerem.
6. W polu **Metoda łączności**wybierz pozycję **dostęp prywatny (Integracja z siecią wirtualną)**. Przejdź do **Virtual Network** i wybierz już istniejącą *sieć wirtualną* i *podsieć* utworzoną w ramach wymagań wstępnych.
7. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć konfigurację elastycznego serwera.
8. Wybierz pozycję **Utwórz**, aby aprowizować serwer. Inicjowanie obsługi może potrwać kilka minut.

>[!Note]
> Po wdrożeniu elastycznego serwera do sieci wirtualnej i podsieci nie można przenieść go do dostępu publicznego (dozwolone adresy IP).

## <a name="next-steps"></a>Następne kroki
- [Utwórz Azure Database for MySQL elastyczną sieć wirtualną serwera i zarządzaj nią przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-virtual-network-cli.md).
- Dowiedz się więcej o [sieci w Azure Database for MySQL elastycznym serwerze](./concepts-networking.md)
- Dowiedz się więcej o [Azure Database for MySQL elastycznej sieci wirtualnej serwera](./concepts-networking.md#private-access-vnet-integration).
