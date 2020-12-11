---
title: Zarządzanie sieciami wirtualnymi — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL — elastyczny serwer
description: Tworzenie sieci wirtualnych i zarządzanie nimi dla Azure Database for MySQL-elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: a41cd2ce14ceb452d783b472955de347199d0870
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109474"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Tworzenie sieci wirtualnych i zarządzanie nimi dla Azure Database for MySQL-elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

Usługa Azure Database for MySQL — elastyczny serwer obsługuje dwa typy wzajemnie wykluczających się metod łączności sieciowej umożliwiających nawiązanie połączenia z serwerem elastycznym. Te dwie opcje są następujące:

- Dostęp publiczny (dozwolone adresy IP)
- Dostęp prywatny (integracja z siecią wirtualną)

W tym artykule będziemy skupić się na tworzeniu serwera MySQL z **dostępem prywatnym (Integracja z siecią wirtualną)** przy użyciu interfejsu wiersza polecenia platformy Azure. Za pomocą *dostępu prywatnego (Integracja z siecią wirtualną)* można wdrożyć elastyczny serwer w ramach własnego [Virtual Network platformy Azure](../../virtual-network/virtual-networks-overview.md). Sieci wirtualne platformy Azure zapewniają prywatną i bezpieczną komunikację sieciową. W przypadku dostępu prywatnego połączenia z serwerem MySQL są ograniczone tylko do sieci wirtualnej. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [dostępem prywatnym (Integracja z siecią wirtualną)](./concepts-networking.md#private-access-vnet-integration).

W Azure Database for MySQL elastycznym serwerze można wdrożyć serwer tylko w sieci wirtualnej i podsieci podczas tworzenia serwera. Po wdrożeniu elastycznego serwera do sieci wirtualnej i podsieci nie można przenieść go do innej sieci wirtualnej, podsieci ani *dostępu publicznego (dozwolone adresy IP)*.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) to bezpłatna interaktywna powłoka, za pomocą której można wykonać kroki opisane w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również otworzyć Cloud Shell na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz pozycję **Kopiuj** , aby skopiować bloki kodu, wklej je do Cloud Shell i wybierz **klawisz ENTER** , aby go uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) . Zanotuj Właściwość **ID** , która odwołuje się do **identyfikatora subskrypcji** dla Twojego konta platformy Azure.

```azurecli-interactive
az login
```

Wybierz określoną subskrypcję na koncie za pomocą polecenia [AZ Account Set](/cli/azure/account#az-account-set) . Zanotuj wartość **identyfikatora** z polecenia **AZ login** Output to use jako wartość argumentu **Subscription** w poleceniu. Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Aby uzyskać całą subskrypcję, użyj [AZ Account List](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Tworzenie Azure Database for MySQL elastyczny serwer przy użyciu interfejsu wiersza polecenia
Możesz użyć polecenia, `az mysql flexible-server` Aby utworzyć elastyczny serwer z *dostępem prywatnym (Integracja z siecią wirtualną)*. To polecenie używa prywatnego dostępu (integracji sieci wirtualnej) jako domyślnej metody łączności. Sieć wirtualna i podsieć zostanie utworzona dla Ciebie, jeśli żaden nie zostanie podany. Istnieje również możliwość udostępnienia istniejącej sieci wirtualnej i podsieci przy użyciu identyfikatora podsieci. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Istnieją różne opcje tworzenia elastycznego serwera przy użyciu interfejsu wiersza polecenia, jak pokazano w poniższych przykładach.

>[!Important]
> Użycie tego polecenia spowoduje oddelegowanie podsieci do **firmy Microsoft. DBforMySQL/flexibleServers**. Ta delegacja oznacza, że tylko usługa Azure Database for MySQL — elastyczny serwer może korzystać z tej podsieci. W podsieci delegowanej nie mogą znajdować się żadne inne typy zasobów platformy Azure.
>

Zapoznaj się z [dokumentacją](/cli/azure/mysql/flexible-server) interfejsu wiersza polecenia platformy Azure, aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. Na przykład w poniższych poleceniach można opcjonalnie określić grupę zasobów.

- Utwórz elastyczny serwer przy użyciu domyślnej sieci wirtualnej, podsieć z prefiksem adresu domyślnego
    ```azurecli-interactive
    az mysql flexible-server create
    ```
- Utwórz elastyczny serwer przy użyciu już istniejącej sieci wirtualnej i podsieci. Jeśli podana Sieć wirtualna i podsieć nie istnieje, zostanie utworzona sieć wirtualna i podsieć z prefiksem adresu domyślnego.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```

- Utwórz elastyczny serwer przy użyciu już istniejącej sieci wirtualnej, podsieci i używania identyfikatora podsieci. Podana podsieć nie powinna zawierać żadnych innych zasobów wdrożonych w nim i ta podsieć zostanie delegowana do **firmy Microsoft. DBforMySQL/flexibleServers**, jeśli nie została jeszcze delegowana.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Sieć wirtualna i podsieć powinna znajdować się w tym samym regionie i subskrypcji co serwer elastyczny.
<
- Utwórz elastyczny serwer przy użyciu nowej sieci wirtualnej, podsieci z prefiksem adresu innego niż domyślny.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Zapoznaj się z [dokumentacją](/cli/azure/mysql/flexible-server) interfejsu wiersza polecenia platformy Azure, aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sieci w Azure Database for MySQL elastycznym serwerze](./concepts-networking.md).
- [Utwórz Azure Database for MySQL elastyczną sieć wirtualną serwera i zarządzaj nią przy użyciu Azure Portal](./how-to-manage-virtual-network-portal.md).
- Dowiedz się więcej o [Azure Database for MySQL elastycznej sieci wirtualnej serwera](./concepts-networking.md#private-access-vnet-integration).