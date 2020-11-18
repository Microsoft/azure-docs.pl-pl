---
title: Korzystanie z reguł sieci wirtualnej — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób tworzenia i zarządzania punktami końcowymi usługi sieci wirtualnej oraz regułami dotyczącymi Azure Database for PostgreSQL przy użyciu wiersza polecenia platformy Azure.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07e0f7cf2834b3984d9207fa18f3b0e32340e216
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660886"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tworzenie punktów końcowych usługi sieci wirtualnej i zarządzanie nimi dla Azure Database for PostgreSQL-pojedynczego serwera przy użyciu interfejsu wiersza polecenia platformy Azure
Punkty końcowe usług Virtual Network (VNet) i reguły zwiększają prywatną przestrzeń adresową Virtual Network do serwera Azure Database for PostgreSQL. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, można tworzyć, aktualizować, usuwać, wyświetlać i przedstawiać punkty końcowe usługi sieci wirtualnej oraz zasady zarządzania serwerem. Omówienie punktów końcowych usługi sieci wirtualnej Azure Database for PostgreSQL, w tym ograniczeń, znajduje się w temacie [punkty końcowe usługi sieci wirtualnej Azure Database for PostgreSQL Server](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach dla Azure Database for PostgreSQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść do przewodnika:
- Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub użyj Azure Cloud Shell w przeglądarce.
- Utwórz [serwer Azure Database for PostgreSQL i bazę danych](quickstart-create-server-database-azure-cli.md).

    > [!NOTE]
    > Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
    > W przypadku komunikacji równorzędnej sieci wirtualnych, jeśli ruch odbywa się za pomocą wspólnej bramy sieci wirtualnej z punktami końcowymi usługi i powinien przepływać do elementu równorzędnego, Utwórz regułę listy ACL/sieci wirtualnej, aby umożliwić usłudze Azure Virtual Machines w sieci wirtualnej bramy dostęp do serwera Azure Database for PostgreSQL.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Skonfiguruj punkty końcowe usługi sieci wirtualnej dla Azure Database for PostgreSQL
Polecenia [AZ Network VNET](/cli/azure/network/vnet) służą do konfigurowania sieci wirtualnych.

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az-account-set). Zastąp właściwość **id** z danych wyjściowych polecenia **az login** dla subskrypcji symbolem zastępczym identyfikatora subskrypcji.

- Konto musi mieć niezbędne uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

Punkty końcowe usługi można niezależnie konfigurować w sieciach wirtualnych, przez użytkownika z dostępem do zapisu do sieci wirtualnej.

Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia do "Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md) i przypisywaniu określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny znajdować się w tej samej dzierżawie Active Directory (AD). Upewnij się, że obie subskrypcje mają zarejestrowany dostawca zasobów **Microsoft. SQL** . Aby uzyskać więcej informacji, zobacz zapoznaj się z tematem [Menedżer zasobów-Rejestracja][resource-manager-portal].

> [!IMPORTANT]
> Zdecydowanie zaleca się zapoznanie się z tym artykułem dotyczącym konfiguracji i zagadnień punktu końcowego usługi przed uruchomieniem przykładowego skryptu poniżej lub skonfigurowaniem punktów końcowych usługi. **Virtual Network punkt końcowy usługi:** [Punkt końcowy usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. Punkty końcowe usług wirtualnych używają nazwy typu usługi **Microsoft. SQL**, która odnosi się do usługi platformy Azure o nazwie SQL Database. Ten tag usługi dotyczy także usług Azure SQL Database, Azure Database for PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania znacznika usługi **Microsoft. SQL** do punktu końcowego usługi sieci wirtualnej konfigurowany jest ruch punktu końcowego usługi dla wszystkich usług Azure Database, w tym Azure SQL Database, Azure Database for PostgreSQL i Azure Database for MySQL serwerów w podsieci. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Przykładowy skrypt służący do tworzenia bazy danych Azure Database for PostgreSQL, tworzenia punktu końcowego usługi sieci wirtualnej i zabezpieczenia serwera w podsieci przy użyciu reguły sieci wirtualnej
W tym przykładowym skrypcie zmień wyróżnione wiersze w celu dostosowania nazwy użytkownika i hasła administratora. Zastąp Identyfikator subskrypcji używany w `az account set --subscription` poleceniu podanym identyfikatora subskrypcji.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
