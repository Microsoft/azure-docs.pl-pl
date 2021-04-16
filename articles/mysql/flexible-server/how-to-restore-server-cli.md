---
title: Przywracanie Azure Database for MySQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano sposób wykonywania operacji przywracania w Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 61a8439b770d8909e04d1b80a5219810dc72aa34
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509103"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>Przywracanie do punktu w czasie dla maszyny wirtualnej Azure Database for MySQL — elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure


> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest obecnie dostępny w publicznej wersji zapoznawczej.

Ten artykuł zawiera procedurę krok po kroku w celu wykonania odzyskiwania do punktu w czasie na serwerze elastycznym przy użyciu kopii zapasowych.

## <a name="prerequisites"></a>Wymagania wstępne
- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
- Zainstaluj lub uaktualnij interfejs wiersza polecenia platformy Azure do najnowszej wersji. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
-  Zaloguj się do konta platformy Azure przy użyciu [polecenia az login.](/cli/azure/reference-index#az-login) **Zanotuj właściwość id,** która odwołuje się do **identyfikatora subskrypcji** dla twojego konta platformy Azure.

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

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Przywracanie serwera z kopii zapasowej na nowy serwer

Możesz uruchomić następujące polecenie, aby przywrócić serwer do najwcześniejszej istniejącej kopii zapasowej.

**Użycie**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Przykład:** Przywróć serwer z tej ```2021-03-03T13:10:00Z``` migawki kopii zapasowej.

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
Czas przywracania zależy od rozmiaru danych przechowywanych na serwerze.

## <a name="perform-post-restore-tasks"></a>Wykonywanie zadań po przywróceniu
Po zakończeniu przywracania należy wykonać następujące zadania, aby tworzyć i uruchamiać użytkowników i aplikacje:

- Jeśli nowy serwer ma zastąpić oryginalny serwer, przekieruj klientów i aplikacje klienckie na nowy serwer
- Upewnij się, że istnieją odpowiednie reguły sieci wirtualnej, aby użytkownicy nawiązyli połączenie. Te reguły nie są kopiowane z oryginalnego serwera.
- Upewnij się, że są dostępne odpowiednie identyfikatory logowania i uprawnienia na poziomie bazy danych
- Skonfiguruj alerty odpowiednio dla nowo przywróconego serwera

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [ciągłości działania](concepts-business-continuity.md)

