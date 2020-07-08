---
title: Podwójne szyfrowanie infrastruktury — Azure Database for PostgreSQL Azure Portal
description: Dowiedz się, jak skonfigurować podwójne szyfrowanie infrastruktury dla Azure Database for PostgreSQL i zarządzać nimi.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 2eb1c196e73644eeefa77d66562dc2a55d6f221a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034947"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-postgresql"></a>Podwójne szyfrowanie infrastruktury dla Azure Database for PostgreSQL

Dowiedz się, jak skonfigurować podwójne szyfrowanie infrastruktury dla Azure Database for PostgreSQL i zarządzać nimi.

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć subskrypcję platformy Azure i być administratorem tej subskrypcji.

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---portal"></a>Tworzenie serwera Azure Database for PostgreSQL z podwójnym szyfrowaniem infrastruktury — Portal

Wykonaj następujące kroki, aby utworzyć serwer Azure Database for MySQL z szyfrowaniem podwójnym infrastruktury z poziomu Azure Portal:

1. Wybierz pozycję **Utwórz zasób** (+) w lewym górnym rogu portalu.

2. Wybierz pozycję **bazy danych**  >  **Azure Database for PostgreSQL**. Możesz również wprowadzić PostgreSQL w polu wyszukiwania, aby znaleźć usługę. Włączono opcję wdrożenia **pojedynczego serwera** .

   !["Azure Database for PostgreSQL" w menu](./media/quickstart-create-database-portal/1-create-database.png)

3. Podaj podstawowe informacje o serwerze. Wybierz **dodatkowe ustawienia** i Włącz pole wyboru **podwójne szyfrowanie infrastruktury** , aby ustawić parametr.

    ![Azure Database for PostgreSQL wybór](./media/howto-infrastructure-double-encryption/infrastructure-encryption-selected.png)

4. Wybierz pozycję **Przegląd + Utwórz** , aby udostępnić serwer.

    ![Podsumowanie Azure Database for PostgreSQL](./media/howto-infrastructure-double-encryption/infrastructure-encryption-summary.png)

5. Po utworzeniu serwera można sprawdzić poprawność szyfrowania podwójnej infrastruktury, sprawdzając stan w bloku serwer **szyfrowania danych** .

    ![Azure Database for MySQL walidacji](./media/howto-infrastructure-double-encryption/infrastructure-encryption-validation.png)

## <a name="create-an-azure-database-for-postgresql-server-with-infrastructure-double-encryption---cli"></a>Tworzenie serwera Azure Database for PostgreSQL z podwójnym szyfrowaniem infrastruktury — interfejs wiersza polecenia

Wykonaj następujące kroki, aby utworzyć serwer Azure Database for MySQL z szyfrowaniem podwójnym infrastruktury z poziomu interfejsu wiersza polecenia:

Ten przykład umożliwia utworzenie grupy zasobów o nazwie `myresourcegroup` w `westus` lokalizacji.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
W poniższym przykładzie jest tworzony serwer PostgreSQL 11 z regionu zachodnie stany USA o nazwie `mydemoserver` w grupie zasobów `myresourcegroup` z identyfikatorem logowania administratora serwera `myadmin` . Jest to serwer **4. generacji** **ogólnego przeznaczenia** z 2 **rdzeniami wirtualnymi**. Spowoduje to również włączenie szyfrowania podwójnego poziomu infrastruktury dla serwera, który został utworzony. Zastąp zmienną `<server_admin_password>` swoją własną wartością.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 11 --infrastructure-encryption >Enabled/Disabled>
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat szyfrowania danych, zobacz [Azure Database for PostgreSQL podwójne szyfrowanie infrastruktury danych](concepts-Infrastructure-double-encryption.md).

