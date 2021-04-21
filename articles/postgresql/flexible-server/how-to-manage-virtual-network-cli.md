---
title: Zarządzanie sieciami wirtualnymi — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — elastyczny serwer
description: Tworzenie sieci wirtualnych i zarządzanie nimi na Azure Database for PostgreSQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0a4bf648551be723007b0d8856fe0857896aad94
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778395"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Tworzenie sieci wirtualnych i zarządzanie nimi na Azure Database for PostgreSQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Azure Database for PostgreSQL — serwer elastyczny obsługuje dwa typy wzajemnie wykluczających się metod łączności sieciowej w celu nawiązywania połączenia z serwerem elastycznym. Te dwie opcje są następujące:

* Dostęp publiczny (dozwolone adresy IP)
* Dostęp prywatny (integracja z siecią wirtualną)

W tym artykule skupimy się na tworzeniu serwera PostgreSQL z dostępem prywatnym (integracja z siecią **wirtualną) przy** użyciu interfejsu wiersza polecenia platformy Azure. Dzięki *dostępowi prywatnemu (integracja z siecią wirtualną)* możesz wdrożyć serwer elastyczny na własnym serwerze [Virtual Network Azure.](../../virtual-network/virtual-networks-overview.md) Sieci wirtualne platformy Azure zapewniają prywatną i bezpieczną komunikację sieciową. W przypadku dostępu prywatnego połączenia z serwerem PostgreSQL są ograniczone tylko do sieci wirtualnej. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [tematem Private access (VNet Integration) (Integracja z siecią wirtualną).](./concepts-networking.md#private-access-vnet-integration)

W Azure Database for PostgreSQL — serwer elastyczny serwer można wdrożyć tylko w sieci wirtualnej i podsieci podczas tworzenia serwera. Po wdrożeniu serwera elastycznego w sieci wirtualnej i podsieci nie można przenieść go do innej sieci wirtualnej, podsieci ani do dostępu publicznego *(dozwolone adresy IP).*

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Ta [Azure Cloud Shell](../../cloud-shell/overview.md) to bezpłatna interaktywna powłoka, za pomocą których można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz **pozycję** Kopiuj, aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz klawisz **Enter,** aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i używać go lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login) **Zanotuj** właściwość ID, która odwołuje się **do identyfikatora subskrypcji** konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję w ramach swojego konta za [pomocą polecenia az account set.](/cli/azure/account#az_account_set) Zanotuj wartość **identyfikatora** z danych wyjściowych **polecenia az login,** która ma być wartością **argumentu** subskrypcji w poleceniu . Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>Tworzenie Azure Database for PostgreSQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia
Możesz użyć polecenia , aby utworzyć serwer elastyczny z dostępem `az postgres flexible-server` *prywatnym (integracja z siecią wirtualną).* To polecenie używa dostępu prywatnego (integracja z siecią wirtualną) jako domyślnej metody łączności. Jeśli żadna sieć wirtualna i podsieć nie zostaną utworzone, zostaną utworzone automatycznie. Możesz również podać istniejącą sieć wirtualną i podsieć przy użyciu identyfikatora podsieci. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Istnieją różne opcje tworzenia serwera elastycznego przy użyciu interfejsu wiersza polecenia, jak pokazano w poniższych przykładach.

>[!Important]
> Użycie tego polecenia spowoduje delegowanie podsieci do **serwera Microsoft.DBforPostgreSQL/flexibleServers.** To delegowanie oznacza, że tylko Azure Database for PostgreSQL elastyczne serwery mogą używać tej podsieci. W podsieci delegowanej nie mogą znajdować się żadne inne typy zasobów platformy Azure.
>
Zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure <!--FIXME --> Aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. Na przykład w poniższych poleceniach możesz opcjonalnie określić grupę zasobów.

- Tworzenie serwera elastycznego przy użyciu domyślnej sieci wirtualnej, podsieci z domyślnym prefiksem adresu
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- Utwórz serwer elastyczny przy użyciu istniejącej sieci wirtualnej i podsieci. Jeśli podsieć i sieć wirtualna nie istnieją, zostanie utworzona sieć wirtualna i podsieć z domyślnym prefiksem adresu.
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- Utwórz serwer elastyczny przy użyciu istniejącej sieci wirtualnej, podsieci i identyfikatora podsieci. Podsieć nie powinna mieć wdrożonych żadnych innych zasobów, a ta podsieć zostanie delegowana do serwera **Microsoft.DBforPostgreSQL/flexibleServers,** jeśli nie została jeszcze delegowana.
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Sieć wirtualna i podsieć powinny być w tym samym regionie i subskrypcji co serwer elastyczny.

- Tworzenie serwera elastycznego przy użyciu nowej sieci wirtualnej, podsieci z prefiksem adresu innym niż domyślny
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Pełną listę konfigurowalnych parametrów [interfejsu](/cli/azure/postgres/flexible-server) wiersza polecenia można znaleźć w dokumentacji interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sieci w Azure Database for PostgreSQL — serwer elastyczny.](./concepts-networking.md)
- [Tworzenie maszyn wirtualnych i Azure Database for PostgreSQL nimi — sieć wirtualna serwera elastycznego przy użyciu Azure Portal](./how-to-manage-virtual-network-portal.md).
- Dowiedz się więcej na [Azure Database for PostgreSQL — sieć wirtualna serwera elastycznego.](./concepts-networking.md#private-access-vnet-integration)