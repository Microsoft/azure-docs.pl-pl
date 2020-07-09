---
title: Podwójne szyfrowanie infrastruktury — Azure Database for MySQL Azure Portal
description: Dowiedz się, jak skonfigurować podwójne szyfrowanie infrastruktury dla Azure Database for MySQL i zarządzać nimi.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: d3076f2591718931bdab4dba9510d25fe07b2d02
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118764"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Podwójne szyfrowanie infrastruktury dla Azure Database for MySQL

Dowiedz się, jak skonfigurować podwójne szyfrowanie infrastruktury dla Azure Database for MySQL i zarządzać nimi.

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Tworzenie serwera Azure Database for MySQL z podwójnym szyfrowaniem infrastruktury — Portal

Wykonaj następujące kroki, aby utworzyć serwer Azure Database for MySQL z szyfrowaniem podwójnym infrastruktury z poziomu Azure Portal:

1. Wybierz pozycję **Utwórz zasób** (+) w lewym górnym rogu portalu.

2. Wybierz pozycję **bazy danych**  >  **Azure Database for MySQL**. Możesz również wprowadzić **MySQL** w polu wyszukiwania, aby znaleźć usługę.

   ![Opcja Azure Database for MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Podaj podstawowe informacje o serwerze. Wybierz **dodatkowe ustawienia** i Włącz pole wyboru **podwójne szyfrowanie infrastruktury** , aby ustawić parametr.

    ![Azure Database for MySQL wybór](./media/howto-double-encryption/infrastructure-encryption-selected.png)

4. Wybierz pozycję **Przegląd + Utwórz** , aby udostępnić serwer.

    ![Podsumowanie Azure Database for MySQL](./media/howto-double-encryption/infrastructure-encryption-summary.png)

5. Po utworzeniu serwera można sprawdzić poprawność szyfrowania podwójnej infrastruktury, sprawdzając stan w bloku serwer **szyfrowania danych** .

    ![Azure Database for MySQL walidacji](./media/howto-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Tworzenie serwera Azure Database for MySQL z podwójnym szyfrowaniem infrastruktury — interfejs wiersza polecenia

Wykonaj następujące kroki, aby utworzyć serwer Azure Database for MySQL z szyfrowaniem podwójnym infrastruktury z poziomu interfejsu wiersza polecenia:

Ten przykład umożliwia utworzenie grupy zasobów o nazwie `myresourcegroup` w `westus` lokalizacji.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
W poniższym przykładzie serwer MySQL 5.7 o nazwie `mydemoserver` zostanie utworzony w grupie zasobów `myresourcegroup` w regionie Zachodnie stany USA przy użyciu identyfikatora logowania administratora serwera `myadmin`. Jest to serwer **4. generacji** **ogólnego przeznaczenia** z 2 **rdzeniami wirtualnymi**. Spowoduje to również włączenie szyfrowania podwójnego poziomu infrastruktury dla serwera, który został utworzony. Zastąp zmienną `<server_admin_password>` swoją własną wartością.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Następne kroki

 Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Azure Database for MySQL podwójne szyfrowanie infrastruktury danych](concepts-Infrastructure-double-encryption.md).
