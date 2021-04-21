---
title: Zarządzanie punktami końcowymi sieci wirtualnej — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: W tym artykule opisano sposób tworzenia reguł i punktów końcowych usługi Azure Database for MySQL sieci wirtualnej przy użyciu wiersza polecenia interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0bc686efbd07cf39d7932b175b6f9800b1ff185f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774669"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Tworzenie punktów końcowych usługi Azure Database for MySQL sieci wirtualnej i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure
Punkty końcowe i reguły usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową sieci wirtualnej na serwer usługi Azure Database for MySQL. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, można tworzyć, aktualizować, usuwać, wyświetlać i wyświetlać punkty końcowe usługi sieci wirtualnej oraz reguły zarządzania serwerem. Aby uzyskać omówienie punktów końcowych Azure Database for MySQL sieci wirtualnej, w tym ograniczenia, zobacz [Azure Database for MySQL server VNet service endpoints](concepts-data-access-and-security-vnet.md)(Punkty końcowe usługi sieci wirtualnej serwera). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach dla Azure Database for MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Potrzebujesz serwera [Azure Database for MySQL bazy danych](quickstart-create-mysql-server-database-using-azure-cli.md).
 
- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.
> W przypadku komunikacji równorzędnej sieci wirtualnych, jeśli ruch przepływa przez wspólną sieć VNet Gateway z punktami końcowymi usługi i ma przepływać do elementu równorzędnego, utwórz regułę listy ACL/sieci wirtualnej, aby umożliwić usłudze Azure Virtual Machines w sieci wirtualnej bramy dostęp do serwera Azure Database for MySQL.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurowanie punktów końcowych usługi sieci wirtualnej dla Azure Database for MySQL
Polecenia [az network vnet](/cli/azure/network/vnet) służą do konfigurowania sieci wirtualnych.

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az_account_set). Zastąp właściwość **id** z danych **wyjściowych az login** subskrypcji symbolem zastępczym identyfikatora subskrypcji.

- Konto musi mieć niezbędne uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

Punkty końcowe usługi mogą być konfigurowane niezależnie w sieciach wirtualnych przez użytkownika z dostępem do zapisu w sieci wirtualnej.

Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md) i przypisywaniu określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny być w tej samej dzierżawie usługi Active Directory (AD). Upewnij się, że obie subskrypcje mają zarejestrowanego dostawcę zasobów **Microsoft.Sql.** Aby uzyskać więcej informacji, [zobacz rejestracja menedżera zasobów][resource-manager-portal]

> [!IMPORTANT]
> Zdecydowanie zaleca się przeczytanie tego artykułu na temat konfiguracji i kwestii dotyczących punktów końcowych usługi przed uruchomieniem poniższego przykładowego skryptu lub skonfigurowaniem punktów końcowych usługi. **Virtual Network punktu końcowego usługi:** Punkt [końcowy Virtual Network to](../virtual-network/virtual-network-service-endpoints-overview.md) podsieć, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. Punkty końcowe usług sieci wirtualnej używają nazwy typu usługi **Microsoft.Sql,** która odwołuje się do usługi platformy Azure o nazwie SQL Database. Ten tag usługi dotyczy również usług Azure SQL Database, Azure Database for PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania tagu usługi **Microsoft.Sql** do punktu końcowego usługi sieci wirtualnej konfiguruje ona ruch punktu końcowego usługi dla wszystkich usług Azure Database, w tym serwerów Azure SQL Database, Azure Database for PostgreSQL i Azure Database for MySQL w podsieci. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Przykładowy skrypt do tworzenia bazy Azure Database for MySQL, tworzenia sieci wirtualnej, punktu końcowego usługi sieci wirtualnej i zabezpieczania serwera do podsieci za pomocą reguły sieci wirtualnej
W tym przykładowym skrypcie zmień wyróżnione wiersze w celu dostosowania nazwy użytkownika i hasła administratora. Zastąp identyfikator Subskrypcji używany w `az account set --subscription` poleceniu własnym identyfikatorem subskrypcji.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
