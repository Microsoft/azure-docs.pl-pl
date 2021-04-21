---
title: Zarządzanie sieciami wirtualnymi — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL — elastyczny serwer
description: Tworzenie sieci wirtualnych i zarządzanie nimi na Azure Database for MySQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7da8062f18d737af9d19df54863bc56c7268910c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776920"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Tworzenie sieci wirtualnych i zarządzanie nimi na Azure Database for MySQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

Usługa Azure Database for MySQL — elastyczny serwer obsługuje dwa typy wzajemnie wykluczających się metod łączności sieciowej umożliwiających nawiązanie połączenia z serwerem elastycznym. Te dwie opcje są następujące:

- Dostęp publiczny (dozwolone adresy IP)
- Dostęp prywatny (integracja z siecią wirtualną)

W tym artykule skupimy się na tworzeniu serwera MySQL z dostępem prywatnym (integracja z siecią **wirtualną) przy** użyciu interfejsu wiersza polecenia platformy Azure. Dzięki *dostępowi prywatnemu (integracja z siecią wirtualną)* możesz wdrożyć serwer elastyczny na własnym serwerze [azure Virtual Network.](../../virtual-network/virtual-networks-overview.md) Sieci wirtualne platformy Azure zapewniają prywatną i bezpieczną komunikację sieciową. W obszarze Dostęp prywatny połączenia z serwerem MySQL są ograniczone tylko do sieci wirtualnej. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [tematem Private access (VNet Integration) (Integracja z siecią wirtualną).](./concepts-networking.md#private-access-vnet-integration)

W Azure Database for MySQL elastycznym można wdrożyć serwer tylko w sieci wirtualnej i podsieci podczas tworzenia serwera. Po wdrożeniu serwera elastycznego w sieci wirtualnej i podsieci nie można przenieść go do innej sieci wirtualnej, podsieci ani do dostępu publicznego *(dozwolone adresy IP).*

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Ta [Azure Cloud Shell](../../cloud-shell/overview.md) to bezpłatna interaktywna powłoka, za pomocą których można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz **pozycję** Kopiuj, aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz klawisz **Enter,** aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i używać go lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login) **Zanotuj właściwość ID,** która odnosi się do **identyfikatora subskrypcji** dla twojego konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję w ramach swojego konta za [pomocą polecenia az account set.](/cli/azure/account#az_account_set) Zanotuj wartość **identyfikatora** z danych wyjściowych **polecenia az login,** która ma być wartością **argumentu** subskrypcji w poleceniu . Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Tworzenie Azure Database for MySQL serwera elastycznego przy użyciu interfejsu wiersza polecenia
Za pomocą polecenia możesz utworzyć serwer elastyczny z dostępem `az mysql flexible-server` *prywatnym (integracja z siecią wirtualną).* To polecenie używa dostępu prywatnego (integracja z siecią wirtualną) jako domyślnej metody łączności. Jeśli żadna sieć wirtualna i podsieć nie zostaną podane, zostaną utworzone automatycznie. Możesz również podać istniejącą sieć wirtualną i podsieć przy użyciu identyfikatora podsieci. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Istnieją różne opcje tworzenia serwera elastycznego przy użyciu interfejsu wiersza polecenia, jak pokazano w poniższych przykładach.

>[!Important]
> Użycie tego polecenia spowoduje delegowanie podsieci do **serwera Microsoft.DBforMySQL/flexibleServers.** Ta delegacja oznacza, że tylko usługa Azure Database for MySQL — elastyczny serwer może korzystać z tej podsieci. W podsieci delegowanej nie mogą znajdować się żadne inne typy zasobów platformy Azure.
>

Pełną listę konfigurowalnych parametrów [interfejsu](/cli/azure/mysql/flexible-server) wiersza polecenia można znaleźć w dokumentacji interfejsu wiersza polecenia platformy Azure. Na przykład w poniższych poleceniach możesz opcjonalnie określić grupę zasobów.

- Tworzenie serwera elastycznego przy użyciu domyślnej sieci wirtualnej, podsieci z domyślnym prefiksem adresu
    ```azurecli-interactive
    az mysql flexible-server create
    ```
- Utwórz serwer elastyczny przy użyciu istniejącej sieci wirtualnej i podsieci. Jeśli podsieć i sieć wirtualna nie istnieją, zostanie utworzona sieć wirtualna i podsieć z domyślnym prefiksem adresu.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```

- Utwórz serwer elastyczny przy użyciu istniejącej sieci wirtualnej, podsieci i identyfikatora podsieci. W podanej podsieci nie powinien być wdrożony żaden inny zasób, a ta podsieć zostanie delegowana do serwera **Microsoft.DBforMySQL/flexibleServers,** jeśli nie została jeszcze delegowana.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Sieć wirtualna i podsieć powinny być w tym samym regionie i subskrypcji co serwer elastyczny.
<
- Utwórz serwer elastyczny przy użyciu nowej sieci wirtualnej, podsieci z prefiksem adresu innym niż domyślny.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Pełną listę konfigurowalnych parametrów [interfejsu](/cli/azure/mysql/flexible-server) wiersza polecenia można znaleźć w dokumentacji interfejsu wiersza polecenia platformy Azure.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na [temat sieci Azure Database for MySQL serwerze elastycznym.](./concepts-networking.md)
- [Tworzenie sieci wirtualnej Azure Database for MySQL serwera elastycznego i](./how-to-manage-virtual-network-portal.md)zarządzanie nimi przy użyciu Azure Portal .
- Dowiedz się więcej o [Azure Database for MySQL wirtualnej serwera elastycznego.](./concepts-networking.md#private-access-vnet-integration)