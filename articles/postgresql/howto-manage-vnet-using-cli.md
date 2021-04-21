---
title: Korzystanie z reguł sieci wirtualnej — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób tworzenia punktów końcowych i reguł usługi sieci wirtualnej oraz zarządzania nimi Azure Database for PostgreSQL wiersza polecenia interfejsu wiersza polecenia platformy Azure.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0de1f00823bd34e18a727b8e2929f64a8769c93a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789901"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tworzenie punktów końcowych usługi sieci wirtualnej i zarządzanie nimi dla Azure Database for PostgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure
Virtual Network (VNet) usługi i reguły rozszerzają prywatną przestrzeń adresową Virtual Network na serwer Azure Database for PostgreSQL sieci wirtualnej. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, można tworzyć, aktualizować, usuwać, wyświetlać i wyświetlać punkty końcowe i reguły usługi sieci wirtualnej w celu zarządzania serwerem. Aby uzyskać omówienie punktów końcowych Azure Database for PostgreSQL sieci wirtualnej, w tym ograniczenia, zobacz [Azure Database for PostgreSQL server VNet service endpoints](concepts-data-access-and-security-vnet.md)(Punkty końcowe usługi sieci wirtualnej serwera). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach dla Azure Database for PostgreSQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik:
- Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub użyj Azure Cloud Shell w przeglądarce.
- Utwórz serwer [Azure Database for PostgreSQL bazy danych](quickstart-create-server-database-azure-cli.md).

    > [!NOTE]
    > Obsługa punktów końcowych usługi sieci wirtualnej jest dostępna tylko dla serwerów Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.
    > W przypadku komunikacji równorzędnej sieci wirtualnych, jeśli ruch przepływa przez wspólną sieć VNet Gateway z punktami końcowymi usługi i ma przepływać do elementu równorzędnego, utwórz regułę listy ACL/sieci wirtualnej, aby umożliwić usłudze Azure Virtual Machines w sieci wirtualnej bramy dostęp do serwera Azure Database for PostgreSQL.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Konfigurowanie punktów końcowych usługi sieci wirtualnej dla Azure Database for PostgreSQL
Polecenia [az network vnet](/cli/azure/network/vnet) służą do konfigurowania sieci wirtualnych.

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az_account_set). Zastąp właściwość **id** z danych wyjściowych polecenia **az login** dla subskrypcji symbolem zastępczym identyfikatora subskrypcji.

- Konto musi mieć niezbędne uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

Punkty końcowe usługi mogą być konfigurowane w sieciach wirtualnych niezależnie przez użytkownika z dostępem do zapisu w sieci wirtualnej.

Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md) i przypisywaniu określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny być w tej samej dzierżawie usługi Active Directory (AD). Upewnij się, że w obu subskrypcjach zarejestrowano dostawcę zasobów **Microsoft.Sql.** Aby uzyskać więcej informacji, [zobacz resource-manager-registration][resource-manager-portal]( Rejestracja menedżera zasobów).

> [!IMPORTANT]
> Zdecydowanie zaleca się przeczytanie tego artykułu o konfiguracjach i zagadnieniach dotyczących punktów końcowych usługi przed uruchomieniem poniższego przykładowego skryptu lub skonfigurowaniem punktów końcowych usługi. **Virtual Network punktu końcowego usługi:** Punkt [Virtual Network usługi to](../virtual-network/virtual-network-service-endpoints-overview.md) podsieć, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. Punkty końcowe usług sieci wirtualnej używają nazwy typu usługi **Microsoft.Sql,** która odwołuje się do usługi platformy Azure o nazwie SQL Database. Ten tag usługi dotyczy również usług Azure SQL Database, Azure Database for PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania tagu usługi **Microsoft.Sql** do punktu końcowego usługi sieci wirtualnej konfiguruje ona ruch punktu końcowego usługi dla wszystkich usług Azure Database, w tym serwerów Azure SQL Database, Azure Database for PostgreSQL i Azure Database for MySQL w podsieci. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Przykładowy skrypt do tworzenia bazy Azure Database for PostgreSQL, tworzenia sieci wirtualnej i punktu końcowego usługi sieci wirtualnej oraz zabezpieczania serwera w podsieci za pomocą reguły sieci wirtualnej
W tym przykładowym skrypcie zmień wyróżnione wiersze w celu dostosowania nazwy użytkownika i hasła administratora. Zastąp identyfikator Subskrypcji używany w `az account set --subscription` poleceniu własnym identyfikatorem subskrypcji.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
