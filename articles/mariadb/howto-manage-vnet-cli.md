---
title: Zarządzanie punktami końcowymi sieci wirtualnej — interfejs wiersza polecenia platformy Azure — Azure Database for MariaDB
description: W tym artykule opisano sposób tworzenia reguł i punktów końcowych usługi Azure Database for MariaDB sieci wirtualnej przy użyciu wiersza polecenia interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8eaf87865fb2fc70251e1e417361333cfd750d6e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783669"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Tworzenie punktów końcowych usługi sieci Azure Database for MariaDB wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi

Punkty końcowe i reguły usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową sieci wirtualnej na serwer usługi Azure Database for MariaDB. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, można tworzyć, aktualizować, usuwać, wyświetlać i wyświetlać punkty końcowe usługi sieci wirtualnej oraz reguły zarządzania serwerem. Aby uzyskać omówienie punktów końcowych Azure Database for MariaDB sieci wirtualnej, w tym ograniczenia, zobacz [Azure Database for MariaDB server VNet service endpoints](concepts-data-access-security-vnet.md)(Punkty końcowe usługi sieci wirtualnej serwera). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach dla Azure Database for MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Potrzebujesz serwera [Azure Database for MariaDB bazy danych](quickstart-create-mariadb-server-database-using-azure-cli.md).

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.

## <a name="configure-vnet-service-endpoints"></a>Konfigurowanie punktów końcowych usługi sieci wirtualnej

Polecenia [az network vnet](/cli/azure/network/vnet) służą do konfigurowania sieci wirtualnych.

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az_account_set). Zastąp właściwość **id** z danych wyjściowych polecenia **az login** dla subskrypcji symbolem zastępczym identyfikatora subskrypcji.

- Konto musi mieć niezbędne uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

Punkty końcowe usługi mogą być konfigurowane niezależnie w sieciach wirtualnych przez użytkownika z dostępem do zapisu w sieci wirtualnej.

Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md) i przypisywaniu określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny być w tej samej dzierżawie usługi Active Directory (AD). Upewnij się, że w obu subskrypcjach zarejestrowano dostawcę zasobów **Microsoft.Sql.** Aby uzyskać więcej informacji, [zobacz rejestracja menedżera zasobów][resource-manager-portal]

> [!IMPORTANT]
> Zdecydowanie zaleca się przeczytanie tego artykułu na temat konfiguracji i kwestii dotyczących punktów końcowych usługi przed skonfigurowaniem punktów końcowych usługi. **Virtual Network punktu końcowego usługi:** Punkt [końcowy Virtual Network to](../virtual-network/virtual-network-service-endpoints-overview.md) podsieć, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. Punkty końcowe usług sieci wirtualnej używają nazwy typu usługi **Microsoft.Sql,** która odwołuje się do usługi platformy Azure o nazwie SQL Database. Ten tag usługi dotyczy również usług Azure SQL Database, Azure Database for MariaDB, PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania tagu usługi **Microsoft.Sql** do punktu końcowego usługi sieci wirtualnej konfiguruje ona ruch punktu końcowego usługi dla wszystkich usług Azure Database, w tym serwerów Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB i Azure Database for MySQL w podsieci.

### <a name="sample-script"></a>Przykładowy skrypt

Ten przykładowy skrypt służy do tworzenia serwera Azure Database for MariaDB sieci wirtualnej, tworzenia sieci wirtualnej i punktu końcowego usługi sieci wirtualnej oraz zabezpieczania serwera w podsieci za pomocą reguły sieci wirtualnej. W tym przykładowym skrypcie zmień nazwę użytkownika i hasło administratora. Zastąp identyfikator Subskrypcji używany w `az account set --subscription` poleceniu własnym identyfikatorem subskrypcji.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md