---
title: Zarządzanie sieciami wirtualnymi — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — elastyczny serwer
description: Tworzenie sieci wirtualnych i zarządzanie nimi dla Azure Database for PostgreSQL-elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 727eb4cd7e7c3de090e1573cb5358ef23118385e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936964"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Tworzenie sieci wirtualnych i zarządzanie nimi dla Azure Database for PostgreSQL-elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Serwer elastyczny Azure Database for PostgreSQL obsługuje dwa typy wzajemnie wykluczających się metod łączności sieciowej w celu nawiązania połączenia z serwerem elastycznym. Te dwie opcje są następujące:

* Dostęp publiczny (dozwolone adresy IP)
* Dostęp prywatny (integracja z siecią wirtualną)

W tym artykule skupmy się na tworzeniu serwera PostgreSQL z **dostępem prywatnym (Integracja z siecią wirtualną)** przy użyciu interfejsu wiersza polecenia platformy Azure. Za pomocą *dostępu prywatnego (Integracja z siecią wirtualną)* można wdrożyć elastyczny serwer w ramach własnego [Virtual Network platformy Azure](../../virtual-network/virtual-networks-overview.md). Sieci wirtualne platformy Azure zapewniają prywatną i bezpieczną komunikację sieciową. W przypadku dostępu prywatnego połączenia z serwerem PostgreSQL są ograniczone tylko do sieci wirtualnej. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [dostępem prywatnym (Integracja z siecią wirtualną)](./concepts-networking.md#private-access-vnet-integration).

Na serwerze elastycznym Azure Database for PostgreSQL można wdrożyć serwer tylko w sieci wirtualnej i podsieci podczas tworzenia serwera. Po wdrożeniu elastycznego serwera do sieci wirtualnej i podsieci nie można przenieść go do innej sieci wirtualnej, podsieci ani *dostępu publicznego (dozwolone adresy IP)*.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz **klawisz ENTER** , aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) . Zanotuj Właściwość **ID** , która odwołuje się do **identyfikatora subskrypcji** dla Twojego konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję na koncie za pomocą polecenia [AZ Account Set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) . Zanotuj wartość **identyfikatora** z polecenia **AZ login** Output to use jako wartość argumentu **Subscription** w poleceniu. Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [AZ Account List](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>Utwórz serwer elastyczny Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia
Możesz użyć polecenia, `az postgres flexible-server` Aby utworzyć elastyczny serwer z *dostępem prywatnym (Integracja z siecią wirtualną)*. To polecenie używa prywatnego dostępu (integracji sieci wirtualnej) jako domyślnej metody łączności. Sieć wirtualna i podsieć zostanie utworzona dla Ciebie, jeśli żaden nie zostanie podany. Istnieje również możliwość udostępnienia istniejącej sieci wirtualnej i podsieci przy użyciu identyfikatora podsieci. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Istnieją różne opcje tworzenia elastycznego serwera przy użyciu interfejsu wiersza polecenia, jak pokazano w poniższych przykładach.

>[!Important]
> Użycie tego polecenia spowoduje oddelegowanie podsieci do **firmy Microsoft. DBforPostgreSQL/flexibleServers**. To delegowanie oznacza, że tylko Azure Database for PostgreSQL elastycznych serwerów może korzystać z tej podsieci. W podsieci delegowanej nie mogą znajdować się żadne inne typy zasobów platformy Azure.
>
Zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure <!--FIXME --> Aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. Na przykład w poniższych poleceniach można opcjonalnie określić grupę zasobów.

- Utwórz elastyczny serwer przy użyciu domyślnej sieci wirtualnej, podsieć z prefiksem adresu domyślnego
    ```azurecli-interactive
    az postgres flexible-server create
    ```
<!--- Create a flexible server using already existing virtual network and subnet
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```-->
- Utwórz elastyczny serwer przy użyciu już istniejącej sieci wirtualnej, podsieci i używania identyfikatora podsieci. Podana podsieć nie powinna zawierać żadnych innych zasobów wdrożonych w nim i ta podsieć zostanie delegowana do **firmy Microsoft. DBforPostgreSQL/flexibleServers**, jeśli nie została jeszcze delegowana.
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Sieć wirtualna i podsieć powinna znajdować się w tym samym regionie i subskrypcji co serwer elastyczny.
<!--  
- Create a flexible server using new virtual network, subnet with non-default address prefix
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --vnet-address-prefix 10.0.0.0/24 --subnet mySubnet --subnet-address-prefix 10.0.0.0/24
    ```-->
Zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure <!--FIXME --> Aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sieci w Azure Database for PostgreSQL-elastycznym serwerze](./concepts-networking.md).
- [Utwórz Azure Database for PostgreSQL elastyczną sieć wirtualną serwera i zarządzaj nią przy użyciu Azure Portal](./how-to-manage-virtual-network-portal.md).
- Dowiedz się więcej o [Azure Database for PostgreSQL-elastyczną sieć wirtualną serwera](./concepts-networking.md#private-access-vnet-integration).