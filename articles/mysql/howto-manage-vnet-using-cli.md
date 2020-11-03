---
title: Zarządzanie punktami końcowymi sieci wirtualnej — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL
description: W tym artykule opisano sposób tworzenia punktów końcowych i reguł usługi sieci wirtualnej Azure Database for MySQL oraz zarządzania nimi przy użyciu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cdc17f84ed7d91d130faf0049b8f2730a13ce35b
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242164"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Tworzenie punktów końcowych usługi Azure Database for MySQL sieci wirtualnej i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure
Punkty końcowe i reguły usługi sieci wirtualnej rozszerzają prywatną przestrzeń adresową sieci wirtualnej na serwer usługi Azure Database for MySQL. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, można tworzyć, aktualizować, usuwać, wyświetlać i przedstawiać punkty końcowe usługi sieci wirtualnej oraz zasady zarządzania serwerem. Omówienie punktów końcowych usługi sieci wirtualnej Azure Database for MySQL, w tym ograniczeń, znajduje się w temacie [punkty końcowe usługi sieci wirtualnej Azure Database for MySQL Server](concepts-data-access-and-security-vnet.md). Punkty końcowe usługi sieci wirtualnej są dostępne we wszystkich obsługiwanych regionach dla Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub użyj Azure Cloud Shell w przeglądarce.
- [Serwer Azure Database for MySQL i baza danych](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.
> W przypadku komunikacji równorzędnej sieci wirtualnych, jeśli ruch odbywa się za pomocą wspólnej bramy sieci wirtualnej z punktami końcowymi usługi i powinien przepływać do elementu równorzędnego, Utwórz regułę listy ACL/sieci wirtualnej, aby umożliwić usłudze Azure Virtual Machines w sieci wirtualnej bramy dostęp do serwera Azure Database for MySQL.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Skonfiguruj punkty końcowe usługi sieci wirtualnej dla Azure Database for MySQL
Polecenia [AZ Network VNET](/cli/azure/network/vnet) służą do konfigurowania sieci wirtualnych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz zalogować się na swoje konto za pomocą polecenia [az login](/cli/azure/authenticate-azure-cli). Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.
```azurecli-interactive
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account#az-account-set). Zastąp właściwość **id** z danych wyjściowych polecenia **az login** dla subskrypcji symbolem zastępczym identyfikatora subskrypcji.

- Konto musi mieć niezbędne uprawnienia do tworzenia sieci wirtualnej i punktu końcowego usługi.

Punkty końcowe usługi można niezależnie konfigurować w sieciach wirtualnych, przez użytkownika z dostępem do zapisu do sieci wirtualnej.

Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia do "Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/" dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględniane we wbudowanych rolach administratora usługi, domyślnie i może być modyfikowane przez tworzenie ról niestandardowych.

Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md) i przypisywaniu określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure znajdują się w różnych subskrypcjach, zasoby powinny znajdować się w tej samej dzierżawie Active Directory (AD). Upewnij się, że obie subskrypcje mają zarejestrowany dostawca zasobów **Microsoft. SQL** . Aby uzyskać więcej informacji, zobacz temat [Resource-Manager-Registration][resource-manager-portal]

> [!IMPORTANT]
> Zdecydowanie zaleca się zapoznanie się z tym artykułem dotyczącym konfiguracji i zagadnień punktu końcowego usługi przed uruchomieniem przykładowego skryptu poniżej lub skonfigurowaniem punktów końcowych usługi. **Virtual Network punkt końcowy usługi:** [Punkt końcowy usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. Punkty końcowe usług wirtualnych używają nazwy typu usługi **Microsoft. SQL** , która odnosi się do usługi platformy Azure o nazwie SQL Database. Ten tag usługi dotyczy także usług Azure SQL Database, Azure Database for PostgreSQL i MySQL. Należy pamiętać, że podczas stosowania znacznika usługi **Microsoft. SQL** do punktu końcowego usługi sieci wirtualnej konfigurowany jest ruch punktu końcowego usługi dla wszystkich usług Azure Database, w tym Azure SQL Database, Azure Database for PostgreSQL i Azure Database for MySQL serwerów w podsieci. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Przykładowy skrypt służący do tworzenia bazy danych Azure Database for MySQL, tworzenia punktu końcowego usługi sieci wirtualnej i zabezpieczenia serwera w podsieci przy użyciu reguły sieci wirtualnej
W tym przykładowym skrypcie zmień wyróżnione wiersze w celu dostosowania nazwy użytkownika i hasła administratora. Zastąp Identyfikator subskrypcji używany w `az account set --subscription` poleceniu podanym identyfikatora subskrypcji.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md