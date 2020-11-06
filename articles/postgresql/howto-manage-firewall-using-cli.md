---
title: Zarządzanie regułami zapory — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano sposób tworzenia reguł zapory i zarządzania nimi w Azure Database for PostgreSQL-pojedynczym serwerze przy użyciu wiersza polecenia platformy Azure.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a559b8c65ab57b0144b807a3b4cc1faa912d430
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422743"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tworzenie reguł zapory i zarządzanie nimi w Azure Database for PostgreSQL-pojedynczym serwerze przy użyciu interfejsu wiersza polecenia platformy Azure
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do serwera Azure Database for PostgreSQL z określonego adresu IP lub zakresu adresów IP. Korzystając z wygodnych poleceń interfejsu wiersza polecenia platformy Azure, można tworzyć, aktualizować, usuwać i wyświetlać reguły zapory w celu zarządzania serwerem. Aby zapoznać się z omówieniem reguł zapory Azure Database for PostgreSQL, zobacz [reguły zapory Azure Database for PostgreSQL Server](concepts-firewall-rules.md).

Reguły Virtual Network (VNet) mogą również służyć do zabezpieczania dostępu do serwera. Dowiedz się więcej o [tworzeniu i zarządzaniu Virtual Network punktów końcowych usługi i reguł przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne
Aby krokowo poprowadzić ten przewodnik, musisz:
- Zainstaluj narzędzie wiersza polecenia [platformy Azure](/cli/azure/install-azure-cli) lub użyj Azure Cloud Shell w przeglądarce.
- [Serwer Azure Database for PostgreSQL i baza danych](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurowanie reguł zapory dla Azure Database for PostgreSQL
Polecenia [AZ Postgres Server firewall-Rule](/cli/azure/postgres/server/firewall-rule) są używane do konfigurowania reguł zapory.

## <a name="list-firewall-rules"></a>Wyświetlanie listy reguł zapory 
Aby wyświetlić listę istniejących reguł zapory serwera, uruchom polecenie [AZ Postgres Server firewall-Rule list](/cli/azure/postgres/server/firewall-rule) .
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
W danych wyjściowych są wyświetlane reguły zapory (jeśli istnieją), domyślnie w formacie JSON. Możesz użyć przełącznika, `--output table` Aby uzyskać bardziej czytelny Format tabeli jako dane wyjściowe.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Tworzenie reguły zapory
Aby utworzyć nową regułę zapory na serwerze, uruchom polecenie [AZ Postgres Server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) . 


Aby zezwolić na dostęp do pojedynczego adresu IP, podaj ten sam adres w `--start-ip-address` i `--end-ip-address` , tak jak w tym przykładzie, zastępując IP widoczny w tym miejscu przy użyciu określonego adresu IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Aby umożliwić aplikacjom z adresów IP platformy Azure Łączenie się z serwerem Azure Database for PostgreSQL, podaj adres IP 0.0.0.0 jako początkowy adres IP i końcowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

Po pomyślnym wykonaniu polecenia wyświetla szczegóły utworzonej reguły zapory, domyślnie w formacie JSON. Jeśli wystąpi błąd, w danych wyjściowych zostanie wyświetlony komunikat o błędzie.

## <a name="update-firewall-rule"></a>Aktualizowanie reguły zapory 
Zaktualizuj istniejącą regułę zapory na serwerze za pomocą polecenia [AZ Postgres Server firewall-Rule Update](/cli/azure/postgres/server/firewall-rule) . Podaj nazwę istniejącej reguły zapory jako dane wejściowe i początkowy adres IP i końcowe atrybuty adresu IP do zaktualizowania.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Po pomyślnym wykonaniu polecenia wyświetlane są szczegóły zaktualizowanej reguły zapory, domyślnie w formacie JSON. Jeśli wystąpi błąd, w danych wyjściowych zostanie wyświetlony komunikat o błędzie.
> [!NOTE]
> Jeśli reguła zapory nie istnieje, zostanie utworzona za pomocą polecenia Update.

## <a name="show-firewall-rule-details"></a>Pokaż szczegóły reguły zapory
Możesz również wyświetlić szczegóły istniejącej reguły zapory na poziomie serwera, uruchamiając polecenie [AZ Postgres Server firewall-Rule show](/cli/azure/postgres/server/firewall-rule) .
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po pomyślnym wykonaniu polecenia wyświetlane są szczegóły zdefiniowanej reguły zapory, domyślnie w formacie JSON. Jeśli wystąpi błąd, w danych wyjściowych zostanie wyświetlony komunikat o błędzie.

## <a name="delete-firewall-rule"></a>Usuń regułę zapory
Aby odwołać dostęp do zakresu adresów IP na serwerze, Usuń istniejącą regułę zapory, wykonując polecenie [AZ Postgres Server firewall-Rule Delete](/cli/azure/postgres/server/firewall-rule) . Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Po pomyślnym zakończeniu nie ma żadnych danych wyjściowych. W przypadku niepowodzenia zwracany jest tekst komunikatu o błędzie.

## <a name="next-steps"></a>Następne kroki
- Podobnie można użyć przeglądarki sieci Web do [tworzenia reguł zapory Azure Database for PostgreSQL i zarządzania nimi przy użyciu Azure Portal](howto-manage-firewall-using-portal.md).
- Dowiedz się więcej o [regułach zapory serwera Azure Database for PostgreSQL](concepts-firewall-rules.md).
- Aby zapewnić bardziej bezpieczny dostęp do serwera, można [utworzyć i zarządzać Virtual Network punktami końcowymi usługi i regułami przy użyciu interfejsu wiersza polecenia platformy Azure](howto-manage-vnet-using-cli.md).
- Aby uzyskać pomoc w połączeniu z serwerem Azure Database for PostgreSQL, zobacz [biblioteki połączeń dla Azure Database for PostgreSQL](concepts-connection-libraries.md).
