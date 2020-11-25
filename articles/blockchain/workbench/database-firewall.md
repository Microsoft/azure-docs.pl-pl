---
title: Konfigurowanie zapory usługi Azure łańcucha bloków Workbench Database
description: Dowiedz się, jak skonfigurować zaporę bazy danych usługi Azure łańcucha bloków Workbench w wersji zapoznawczej, aby umożliwić łączenie się z klientami zewnętrznymi i aplikacjami
ms.date: 09/09/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: dc7a1dc7e4a083ef8f5f7650f29ed1430b32e88b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009453"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Konfigurowanie zapory bazy danych usługi Azure Blockchain Workbench

W tym artykule przedstawiono sposób konfigurowania reguły zapory przy użyciu witryny Azure Portal. Reguły zapory pozwalają klientom lub aplikacjom zewnętrznym na łączenie się z bazą danych usługi Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Nawiązywanie połączenia z bazą danych usługi Workbench Blockchain

Aby połączyć się z bazą danych, w której chcesz skonfigurować regułę:

1. Zaloguj się do Azure Portal przy użyciu konta z uprawnieniami **właściciela**     dla zasobów usługi Azure łańcucha bloków Workbench.
2. W lewym okienku nawigacji wybierz pozycję **Grupy zasobów**.
3. Wybierz nazwę grupy zasobów dla danego wdrożenia usługi Blockchain Workbench.
4. Wybierz **Typ**, aby posortować listę zasobów, a następnie wybierz **program SQL Server**.
5. Przykładowa lista zasobów na poniższym zrzucie ekranu przedstawia dwie bazy danych: *master* i *lsgn-sdk*. Skonfigurujesz regułę zapory dla bazy danych *lsgn-sdk*.

![Lista zasobów usługi Blockchain Workbench](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Tworzenie reguły zapory bazy danych

Aby utworzyć regułę zapory:

1. Wybierz link do bazy danych „lsgn-sdk”.
2. Na pasku menu wybierz pozycję **Ustaw zaporę serwera**.

   ![Ustaw zaporę serwera](./media/database-firewall/configure-server-firewall.png)

3. Aby utworzyć regułę dla swojej organizacji:

   * Wprowadź nazwę w polu **NAZWA REGUŁY**
   * Wprowadź adres IP w polu **POCZĄTKOWY ADRES IP** zakresu adresów
   * Wprowadź adres IP w polu **KOŃCOWY ADRES IP** zakresu adresów

   ![Tworzenie reguły zapory](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Jeśli chcesz tylko dodać adres IP swojego komputera, wybierz pozycję **+ Dodaj adres IP klienta**.
        
1. Aby zapisać konfigurację zapory, wybierz pozycję **Zapisz**.
2. Przetestuj zakres adresów IP skonfigurowany dla bazy danych, nawiązując połączenie z poziomu aplikacji lub narzędzia, na przykład programu SQL Server Management Studio.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](database-views.md)