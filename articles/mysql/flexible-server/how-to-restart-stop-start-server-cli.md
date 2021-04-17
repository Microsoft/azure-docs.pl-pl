---
title: Ponowne uruchamianie/zatrzymywanie/uruchamianie — Azure Portal — Azure Database for MySQL serwer elastyczny
description: W tym artykule opisano sposób ponownego uruchamiania/zatrzymania/uruchamiania operacji w usłudze Azure Database for MySQL za pośrednictwem interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: c9e646ad40c669e51f052ac9888cdd7c6883a848
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509108"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Ponowne uruchamianie/zatrzymywanie/uruchamianie Azure Database for MySQL — serwer elastyczny (wersja zapoznawcza)

> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest obecnie w publicznej wersji zapoznawczej.

W tym artykule pokazano, jak uruchomić ponownie, uruchomić i zatrzymać serwer elastyczny przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
- Zainstaluj interfejs wiersza polecenia platformy Azure lub uaktualnij go do najnowszej wersji. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
-  Zaloguj się do konta platformy Azure przy użyciu [polecenia az login.](/cli/azure/reference-index#az-login) **Zanotuj** właściwość id, która odwołuje się **do identyfikatora subskrypcji** konta platformy Azure.

    ```azurecli-interactive
    az login
    ````

- Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w której chcesz utworzyć serwer, używając ```az account set``` polecenia .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Utwórz serwer elastyczny MySQL, jeśli jeszcze go nie utworzono za pomocą ```az mysql flexible-server create``` polecenia .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>Zatrzymywanie uruchomionego serwera
Aby zatrzymać serwer, uruchom  ```az mysql flexible-server stop``` polecenie . Jeśli używasz kontekstu [lokalnego](/cli/azure/config/param-persist), nie musisz po podaniem żadnych argumentów.

**Użycia:**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**Przykład bez kontekstu lokalnego:**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**Przykład z kontekstem lokalnym:**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>Uruchamianie zatrzymanych serwerów
Aby uruchomić serwer, uruchom  ```az mysql flexible-server start``` polecenie . Jeśli używasz kontekstu [lokalnego](/cli/azure/config/param-persist), nie musisz po podaniem żadnych argumentów.

**Użycia:**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**Przykład bez kontekstu lokalnego:**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**Przykład z kontekstem lokalnym:**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
> Po pomyślnym ponownym uruchomieniu serwera wszystkie operacje zarządzania są teraz dostępne dla serwera elastycznego.

## <a name="restart-a-server"></a>Ponowne uruchamianie serwera
Aby ponownie uruchomić serwer, uruchom  ```az mysql flexible-server restart``` polecenie . Jeśli używasz kontekstu [lokalnego](/cli/azure/config/param-persist), nie musisz po podaniem żadnych argumentów.

**Użycia:**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Przykład bez kontekstu lokalnego:**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**Przykład z kontekstem lokalnym:**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
> Po pomyślnym ponownym uruchomieniu serwera wszystkie operacje zarządzania są teraz dostępne dla serwera elastycznego.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [sieci w Azure Database for MySQL serwera elastycznego](./concepts-networking.md)
- [Tworzenie sieci wirtualnej Azure Database for MySQL serwera elastycznego i](./how-to-manage-virtual-network-portal.md)zarządzanie nimi przy użyciu Azure Portal .

