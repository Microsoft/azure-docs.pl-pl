---
title: Zarządzanie regułami zapory — interfejs wiersza polecenia platformy Azure — Azure Database for MySQL — elastyczny serwer
description: Tworzenie reguł zapory i zarządzanie nimi dla Azure Database for MySQL — elastyczny serwer przy użyciu wiersza polecenia interfejsu wiersza polecenia platformy Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0692e7e7452fef9577414e9aa5340d933f50b30e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776955"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>Tworzenie reguł zapory serwera Azure Database for MySQL zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for MySQL elastyczny serwer jest obecnie w publicznej wersji zapoznawczej

Usługa Azure Database for MySQL — elastyczny serwer obsługuje dwa typy wzajemnie wykluczających się metod łączności sieciowej umożliwiających nawiązanie połączenia z serwerem elastycznym. Te dwie opcje są następujące:

- Dostęp publiczny (dozwolone adresy IP)
- Dostęp prywatny (integracja z siecią wirtualną)

W tym artykule skupimy się na tworzeniu serwera MySQL z dostępem publicznym (dozwolone adresy **IP)** przy użyciu interfejsu wiersza polecenia platformy Azure i udostępnimy przegląd poleceń interfejsu wiersza polecenia platformy Azure, których można użyć do tworzenia, aktualizowania, usuwania, tworzenia listy i pokazywania reguł zapory po utworzeniu serwera. W *przypadku dostępu publicznego (dozwolone adresy IP)* połączenia z serwerem MySQL są ograniczone tylko do dozwolonych adresów IP. Adresy IP klientów muszą być dozwolone w regułach zapory. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [tematem Dostęp publiczny (dozwolone adresy IP).](./concepts-networking.md#public-access-allowed-ip-addresses) Reguły zapory można zdefiniować w czasie tworzenia serwera (zalecane), ale można je również dodać później.

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

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Tworzenie reguły zapory podczas tworzenia serwera elastycznego przy użyciu interfejsu wiersza polecenia platformy Azure

Za pomocą polecenia można utworzyć serwer elastyczny z dostępem publicznym (dozwolone adresy IP) i skonfigurować reguły zapory `az mysql flexible-server --public access` podczas tworzenia serwera elastycznego.  Możesz użyć **przełącznika --public-access,** aby podać dozwolone adresy IP, które będą mogły nawiązać połączenie z serwerem. Możesz podać jeden lub zakres adresów IP, które mają zostać uwzględnione na liście dozwolonych adresów IP. Zakres adresów IP musi być oddzielony kreskami i nie może zawierać spacji. Istnieją różne opcje tworzenia serwera elastycznego przy użyciu interfejsu wiersza polecenia, jak pokazano w poniższym przykładzie.

Pełną listę konfigurowalnych parametrów [interfejsu](/cli/azure/mysql/flexible-server) wiersza polecenia można znaleźć w dokumentacji interfejsu wiersza polecenia platformy Azure. Na przykład w poniższych poleceniach możesz opcjonalnie określić grupę zasobów.

- Tworzenie serwera elastycznego z dostępem publicznym i dodawanie adresu IP klienta w celu uzyskania dostępu do serwera
    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```
- Utwórz serwer elastyczny z dostępem publicznym i dodaj zakres adresów IP, aby mieć dostęp do tego serwera

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Tworzenie serwera elastycznego z dostępem publicznym i zezwalanie aplikacjom z adresów IP platformy Azure na łączenie się z serwerem elastycznym
    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Ta opcja konfiguruje zaporę tak, aby zezwalała na publiczny dostęp z usług i zasobów platformy Azure do tego serwera, w tym połączeń z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
    >
- Tworzenie serwera elastycznego z dostępem publicznym i zezwalanie na wszystkie adresy IP
    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```
    >[!Note]
    > Powyższe polecenie spowoduje utworzenie reguły zapory z adresem IP rozpoczęcia = 0.0.0.0, końcowy adres IP = 255.255.255.255 i nie będą blokowane żadne adresy IP. Każdy host w Internecie może uzyskać dostęp do tego serwera. Zdecydowanie zaleca się używanie tej reguły tylko tymczasowo i tylko na serwerach testowych, które nie zawierają poufnych danych.

- Tworzenie serwera elastycznego z dostępem publicznym bez adresu IP
    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```
    >[!Note]
    > Nie zalecamy tworzenia serwera bez żadnych reguł zapory. Jeśli nie dodasz żadnych reguł zapory, żaden klient nie będzie mógł nawiązać połączenia z serwerem.

## <a name="create-and-manage-firewall-rule-after-server-create"></a>Tworzenie reguły zapory i zarządzanie nimi po utworzeniu serwera
Polecenie **az mysql flexible-server firewall-rule** jest używane z interfejsu wiersza polecenia platformy Azure do tworzenia, usuwania, tworzenia listy, pokazywania i aktualizowania reguł zapory.

Polecenia:
- **create**: utwórz regułę zapory serwera elastycznego.
- **list**: Lista reguł zapory serwera elastycznego.
- **update:** zaktualizuj regułę zapory serwera elastycznego.
- **pokaż**: Pokaż szczegóły reguły zapory serwera elastycznego.
- **delete**: usuń regułę zapory serwera elastycznego.

Pełną listę konfigurowalnych parametrów [interfejsu](/cli/azure/mysql/flexible-server) wiersza polecenia można znaleźć w dokumentacji interfejsu wiersza polecenia platformy Azure. Na przykład w poniższych poleceniach możesz opcjonalnie określić grupę zasobów.

### <a name="create-a-firewall-rule"></a>Tworzenie reguły zapory
Użyj polecenia `az mysql flexible-server firewall-rule create` , aby utworzyć nową regułę zapory na serwerze.
Aby zezwolić na dostęp do zakresu adresów IP, podaj adres IP jako startowy adres IP i końcowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Aby zezwolić na dostęp dla jednego adresu IP, wystarczy podać pojedynczy adres IP, jak w tym przykładzie.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Aby umożliwić aplikacjom z adresów IP platformy Azure łączenie się z serwerem elastycznym, podaj adres IP 0.0.0.0 jako startowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę tak, aby zezwalała na publiczny dostęp z usług i zasobów platformy Azure do tego serwera, w tym połączeń z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

Po sukcesie każde polecenie tworzenia wyświetla szczegóły utworzonej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi awaria, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

### <a name="list-firewall-rules"></a>Lista reguł zapory 
Użyj polecenia `az mysql flexible-server firewall-rule list` , aby wyświetlić listę istniejących reguł zapory serwera na serwerze. Zwróć uwagę, że atrybut nazwy serwera jest określony w **przełączniku --name.**
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```
Dane wyjściowe wyświetla listę reguł(jeśli są) w formacie JSON (domyślnie). Możesz użyć przełącznika --output table**, aby uzyskać wyniki w bardziej czytelnym formacie tabeli.
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Aktualizowanie reguły zapory
Użyj polecenia `az mysql flexible-server firewall-rule update` , aby zaktualizować istniejącą regułę zapory na serwerze. Podaj nazwę istniejącej reguły zapory jako dane wejściowe, a także atrybuty adresu IP i końcowego adresu IP do zaktualizowania.
```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po sukcesie dane wyjściowe polecenia będą zawierały szczegóły zaktualizowanej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi awaria, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

> [!NOTE]
> Jeśli reguła zapory nie istnieje, reguła jest tworzona za pomocą polecenia update.

### <a name="show-firewall-rule-details"></a>Wyświetlanie szczegółów reguły zapory
Użyj polecenia `az mysql flexible-server firewall-rule show` , aby wyświetlić szczegóły istniejącej reguły zapory z serwera. Podaj nazwę istniejącej reguły zapory jako dane wejściowe.
```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Po sukcesie dane wyjściowe polecenia będą zawierały szczegóły określonej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi awaria, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

### <a name="delete-a-firewall-rule"></a>Usuwanie reguły zapory
Użyj polecenia `az mysql flexible-server firewall-rule delete` , aby usunąć istniejącą regułę zapory z serwera. Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Po sukcesie nie ma żadnych danych wyjściowych. Po niepowodzeniu zostanie wyświetlony tekst komunikatu o błędzie.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sieci w Azure Database for MySQL elastycznym](./concepts-networking.md)
- Dowiedz się więcej na [temat Azure Database for MySQL zapory serwera elastycznego](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Tworzenie reguł zapory Azure Database for MySQL serwera elastycznego i](./how-to-manage-firewall-portal.md)zarządzanie nimi przy użyciu Azure Portal .