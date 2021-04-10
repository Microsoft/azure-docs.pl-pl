---
title: Zarządzanie regułami zapory — interfejs wiersza polecenia platformy Azure — Azure Database for PostgreSQL-elastyczny serwer
description: Utwórz reguły zapory dla Azure Database for PostgreSQL-elastyczny serwer przy użyciu wiersza polecenia platformy Azure i zarządzaj nimi.
author: rothja
ms.author: jroth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 843ad39d1c09d7c5a2fb7e9b536e300edf58d8e5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551125"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Tworzenie reguł zapory serwera Azure Database for PostgreSQL elastycznych i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Serwer elastyczny Azure Database for PostgreSQL obsługuje dwa typy wzajemnie wykluczających się metod łączności sieciowej w celu nawiązania połączenia z serwerem elastycznym. Te dwie opcje są następujące:

* Dostęp publiczny (dozwolone adresy IP)
* Dostęp prywatny (integracja z siecią wirtualną)

W tym artykule skupmy się na tworzeniu serwera PostgreSQL z **dostępem publicznym (dozwolone adresy IP)** za pomocą interfejsu wiersza polecenia platformy Azure i udostępniamy przegląd poleceń interfejsu wiersza polecenia platformy Azure, których można użyć do tworzenia, aktualizowania, usuwania, wyświetlania i wyświetlania reguł zapory po utworzeniu serwera. W przypadku *dostępu publicznego (dozwolone adresy IP)* połączenia z serwerem PostgreSQL są ograniczone tylko do dozwolonych adresów IP. Adresy IP klienta muszą być dozwolone w regułach zapory. Aby dowiedzieć się więcej na ten temat, zobacz [dostęp publiczny (dozwolone adresy IP)](./concepts-networking.md#public-access-allowed-ip-addresses). Reguły zapory można definiować w momencie tworzenia serwera (zalecane), ale można je również dodać później.

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

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Tworzenie reguły zapory podczas tworzenia elastycznego serwera przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz użyć polecenia, `az postgres flexible-server --public access` Aby utworzyć elastyczny serwer z *dostępem publicznym (dozwolone adresy IP)* i skonfigurować reguły zapory podczas tworzenia elastycznego serwera. Możesz użyć przełącznika **--Public-Access** , aby podać dozwolone adresy IP, które będą mogły nawiązywać połączenia z serwerem. Można podać jeden lub zakres adresów IP, które mają być uwzględnione na liście dozwolonych wartości IP. Zakres adresów IP musi być rozdzielony kreską i nie może zawierać spacji. Istnieją różne opcje tworzenia elastycznego serwera przy użyciu interfejsu wiersza polecenia, jak pokazano w poniższym przykładzie.

Zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure <!--FIXME --> Aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. Na przykład w poniższych poleceniach można opcjonalnie określić grupę zasobów.

- Tworzenie elastycznego serwera z dostępem publicznym i Dodawanie adresu IP klienta w celu uzyskania dostępu do serwera
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- Utwórz elastyczny serwer z dostępem publicznym i Dodaj zakres adresów IP, aby uzyskać dostęp do tego serwera

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Tworzenie elastycznego serwera z dostępem publicznym i Zezwalanie aplikacjom z adresów IP platformy Azure na łączenie się z elastycznym serwerem
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Ta opcja umożliwia skonfigurowanie zapory w taki sposób, aby zezwalała na publiczny dostęp z usług i zasobów platformy Azure na platformie Azure do tego serwera, łącznie z połączeniami z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
    >
- - Tworzenie elastycznego serwera z dostępem publicznym i Zezwalanie na cały adres IP
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > Powyższe polecenie spowoduje utworzenie reguły zapory z początkowym adresem IP = 0.0.0.0, końcowym adresem IP = 255.255.255.255, a żadne adresy IP nie będą blokowane. Dowolny host w Internecie może uzyskać dostęp do tego serwera. Zdecydowanie zaleca się używanie tej reguły tylko tymczasowo i tylko na serwerach testowych, które nie zawierają poufnych danych.
- Tworzenie elastycznego serwera z dostępem publicznym i bez adresu IP
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > nie zaleca się tworzenia serwera bez żadnych reguł zapory. Jeśli nie dodasz żadnych reguł zapory, żaden klient nie będzie mógł nawiązać połączenia z serwerem.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>Utwórz regułę zapory po utworzeniu serwera i zarządzaj nią
Polecenie **AZ Postgres elastyczna-Server firewall-Rule** jest używane w interfejsie wiersza polecenia platformy Azure do tworzenia, usuwania, wyświetlania i aktualizowania reguł zapory.

Polecenia:
- **Tworzenie**: Tworzenie elastycznej reguły zapory serwera.
- **Lista**: Lista elastycznych reguł zapory serwera.
- **Aktualizacja**: aktualizowanie elastycznej reguły zapory serwera.
- **Pokaż**: pokazuje szczegóły elastycznej reguły zapory serwera.
- **usuwanie**: usuwanie elastycznej reguły zapory serwera.

Zapoznaj się z dokumentacją interfejsu wiersza polecenia platformy Azure <!--FIXME --> Aby uzyskać pełną listę konfigurowalnych parametrów interfejsu wiersza polecenia. Na przykład w poniższych poleceniach można opcjonalnie określić grupę zasobów.

### <a name="create-a-firewall-rule"></a>Tworzenie reguły zapory
Użyj `az postgres flexible-server firewall-rule create` polecenia, aby utworzyć nową regułę zapory na serwerze.
Aby zezwolić na dostęp do zakresu adresów IP, podaj adres IP jako początkowy adres IP i końcowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Aby zezwolić na dostęp dla pojedynczego adresu IP, po prostu podaj pojedynczy adres IP, jak w tym przykładzie.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Aby umożliwić aplikacjom z adresów IP platformy Azure Łączenie się z serwerem elastycznym, podaj adres IP 0.0.0.0 jako początkowy adres IP, jak w tym przykładzie.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ta opcja umożliwia skonfigurowanie zapory w taki sposób, aby zezwalała na publiczny dostęp z usług i zasobów platformy Azure na platformie Azure do tego serwera, łącznie z połączeniami z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 
Po pomyślnym wykonaniu poszczególnych poleceń tworzenia danych wyjściowych polecenia są wyświetlane szczegóły utworzonej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi błąd, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

### <a name="list-firewall-rules"></a>Wyświetlanie listy reguł zapory 
Użyj `az postgres flexible-server firewall-rule list` polecenia, aby wyświetlić listę istniejących reguł zapory serwera na serwerze. Należy zauważyć, że atrybut nazwy serwera jest określony w przełączniku **--name** . 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
W danych wyjściowych są wyświetlane reguły, jeśli istnieją, w formacie JSON (domyślnie). Aby wyprowadzić wyniki w bardziej czytelnym formacie tabeli, można użyć przełącznika--Output tabeli * *.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Aktualizowanie reguły zapory
Użyj `az postgres flexible-server firewall-rule update` polecenia, aby zaktualizować istniejącą regułę zapory na serwerze. Podaj nazwę istniejącej reguły zapory jako dane wejściowe, a także atrybuty początkowy adres IP i końcowy adres IP do zaktualizowania.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Po pomyślnym wykonaniu polecenia wyświetlane są szczegóły zaktualizowanej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi błąd, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

> [!NOTE]
> Jeśli reguła zapory nie istnieje, reguła zostanie utworzona za pomocą polecenia Update.
### <a name="show-firewall-rule-details"></a>Pokaż szczegóły reguły zapory
Użyj `az postgres flexible-server firewall-rule show` polecenia, aby wyświetlić szczegóły istniejącej reguły zapory z serwera. Podaj nazwę istniejącej reguły zapory jako dane wejściowe.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Po pomyślnym wykonaniu polecenia wyświetlane są szczegóły zdefiniowanej reguły zapory w formacie JSON (domyślnie). Jeśli wystąpi błąd, w danych wyjściowych zostanie wyświetlony tekst komunikatu o błędzie.

### <a name="delete-a-firewall-rule"></a>Usuwanie reguły zapory
Użyj `az postgres flexible-server firewall-rule delete` polecenia, aby usunąć istniejącą regułę zapory z serwera. Podaj nazwę istniejącej reguły zapory.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Po pomyślnym zakończeniu nie ma żadnych danych wyjściowych. W przypadku niepowodzenia zostanie wyświetlony tekst komunikatu o błędzie.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sieci w Azure Database for PostgreSQL-elastycznym serwerze](./concepts-networking.md)
- Dowiedz się więcej o [regułach zapory serwera elastycznych Azure Database for PostgreSQL](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Tworzenie reguł zapory serwera Azure Database for PostgreSQL elastycznych i zarządzanie nimi przy użyciu Azure Portal](./how-to-manage-firewall-portal.md).
