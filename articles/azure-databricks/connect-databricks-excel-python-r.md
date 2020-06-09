---
title: 'Łączenie się z Azure Databricks z programu Excel, Python lub R '
description: Dowiedz się, jak połączyć Azure Databricks z programem Excel, Python lub R przy użyciu sterownika Simba.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mamccrea
ms.custom: tracking-python
ms.openlocfilehash: 77b9bcf0dad8fa11fad4828649cd75d7df7806d5
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561308"
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Łączenie się z Azure Databricks z programu Excel, Python lub R

W tym artykule dowiesz się, jak używać sterownika ODBC datacegłs do łączenia Azure Databricks z programem Microsoft Excel, Python lub językiem R. Po nawiązaniu połączenia można uzyskać dostęp do danych w Azure Databricks z klientów programu Excel, Python lub R. Klientów można także użyć do dalszej analizy danych. 

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć obszar roboczy Azure Databricks, klaster Spark i przykładowe dane skojarzone z klastrem. Jeśli te wymagania wstępne nie zostały jeszcze spełnione, wykonaj czynności opisane w temacie [Uruchamianie zadania Spark na Azure Databricks przy użyciu Azure Portal](quickstart-create-databricks-workspace-portal.md).

* Pobierz sterownik ODBC datacegły ze [strony pobierania sterownika datakostek](https://databricks.com/spark/odbc-driver-download). Zainstaluj wersję 32-bitową lub 64-bitową w zależności od aplikacji, z której chcesz się połączyć Azure Databricks. Na przykład aby połączyć się z programu Excel, zainstaluj 32-bitową wersję sterownika. Aby nawiązać połączenie z języków R i Python, zainstaluj 64-bitową wersję sterownika.

* Konfigurowanie osobistego tokenu dostępu w kostkach. Aby uzyskać instrukcje, zobacz [zarządzanie tokenami](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="set-up-a-dsn"></a>Konfigurowanie nazwy DSN

Nazwa źródła danych (DSN) zawiera informacje o określonym źródle danych. Sterownik ODBC potrzebuje tego DSN, aby połączyć się ze źródłem danych. W tej sekcji skonfigurujesz nazwę DSN, która może być używana z sterownikiem ODBC datatagss do łączenia się z Azure Databricks od klientów, takich jak Microsoft Excel, Python lub R.

1. W obszarze roboczym Azure Databricks przejdź do klastra datakosteks.

    ![Otwórz klaster datakostki](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Otwórz klaster datakostki")

2. Na karcie **Konfiguracja** kliknij kartę **JDBC/ODBC** i skopiuj wartości dla **nazwy hosta serwera** i **ścieżki http**. Te wartości są wymagane do wykonania kroków opisanych w tym artykule.

    ![Pobierz konfigurację datakostki](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Pobierz konfigurację datakostki")

3. Na komputerze Uruchom aplikację **źródła danych ODBC** (32-bit lub 64-bit) w zależności od aplikacji. Aby nawiązać połączenie z programu Excel, użyj wersji 32-bitowej. Aby nawiązać połączenie z języków R i Python, użyj wersji 64-bitowej.

    ![Uruchom ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Uruchom aplikację ODBC")

4. Na karcie **DSN użytkownika** kliknij przycisk **Dodaj**. W oknie dialogowym **Utwórz nowe źródło danych** wybierz **Sterownik Simba Spark ODBC**, a następnie kliknij przycisk **Zakończ**.

    ![Uruchom ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Uruchom aplikację ODBC")

5. W oknie dialogowym **Simba Spark ODBC Driver** podaj następujące wartości:

    ![Konfigurowanie nazwy DSN](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Konfigurowanie nazwy DSN")

    Poniższa tabela zawiera informacje o wartościach, które należy podać w oknie dialogowym.
    
    |Pole  | Wartość  |
    |---------|---------|
    |**Data Source Name (Nazwa źródła danych)**     | Podaj nazwę źródła danych.        |
    |**Hosty**     | Podaj wartość skopiowaną z obszaru roboczego datakosteks dla *nazwy hosta serwera*.        |
    |**Port**     | Wprowadź *443*.        |
    |**Uwierzytelnianie**  >  **Mechanizm**     | Wybierz *nazwę użytkownika i hasło*.        |
    |**Nazwa użytkownika**     | Wprowadź *token*.        |
    |**Hasło**     | Wprowadź wartość tokenu skopiowaną z obszaru roboczego datakostki. |
    
    W oknie dialogowym Konfiguracja DSN wykonaj następujące dodatkowe kroki.
    
    * Kliknij pozycję **Opcje http**. W oknie dialogowym, które się otworzy, wklej wartość *ścieżki http* skopiowanej z obszaru roboczego datakostki. Kliknij przycisk **OK**.
    * Kliknij pozycję **Opcje protokołu SSL**. W otwartym oknie dialogowym zaznacz pole wyboru **Włącz protokół SSL** . Kliknij przycisk **OK**.
    * Kliknij przycisk **Testuj** , aby przetestować połączenie z Azure Databricks. Kliknij przycisk **OK** , aby zapisać konfigurację.
    * W oknie dialogowym **Administrator źródła danych ODBC** kliknij przycisk **OK**.

Masz teraz skonfigurowaną nazwę DSN. W następnych sekcjach użyjesz tego DSN do łączenia się z Azure Databricks z programu Excel, Python lub R.

## <a name="connect-from-microsoft-excel"></a>Łączenie z programu Microsoft Excel

W tej sekcji pobierasz dane z Azure Databricks do programu Microsoft Excel przy użyciu utworzonej wcześniej nazwy DSN. Przed rozpoczęciem upewnij się, że na komputerze jest zainstalowany program Microsoft Excel. Możesz użyć wersji próbnej programu Excel z [linku do wersji próbnej programu Microsoft Excel](https://products.office.com/excel).

1. Otwórz pusty skoroszyt w programie Microsoft Excel. Na Wstążce **dane** kliknij pozycję **Pobierz dane**. Kliknij **z innych źródeł** , a następnie kliknij pozycję **z ODBC**.

    ![Uruchamianie ODBC z programu Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Uruchamianie ODBC z programu Excel")

2. W oknie dialogowym **z ODBC** Wybierz utworzoną wcześniej nazwę DSN, a następnie kliknij przycisk **OK**.

    ![Wybierz nazwę DSN](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Wybierz nazwę DSN")

3. Jeśli zostanie wyświetlony monit o podanie poświadczeń, w obszarze Nazwa użytkownika wprowadź **token**. W polu Hasło podaj wartość tokenu, która została pobrana z obszaru roboczego datakostki.

    ![Podaj poświadczenia dla datakostki](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Wybierz nazwę DSN")

4. W oknie Nawigator wybierz tabelę w kostkach, które chcesz załadować do programu Excel, a następnie kliknij przycisk **Załaduj**. 

    ![Załaduj DTA do programu Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Załaduj DTA do programu Excel")

Po umieszczeniu danych w skoroszycie programu Excel można wykonywać na niej operacje analityczne.

## <a name="connect-from-r"></a>Połącz z R

> [!NOTE]
> Ta sekcja zawiera informacje na temat integrowania klienta programu R Studio uruchomionego na pulpicie przy użyciu Azure Databricks. Aby uzyskać instrukcje dotyczące korzystania z programu R Studio na samym klastrze Azure Databricks, zobacz [R Studio on Azure Databricks](/azure/databricks/spark/latest/sparkr/rstudio).

W tej sekcji użyjesz środowiska IDE języka R do odwoływania się do danych dostępnych w Azure Databricks. Przed rozpoczęciem należy zainstalować na komputerze następujące elementy.

* Środowisko IDE dla języka R. W tym artykule jest stosowany program RStudio dla komputerów stacjonarnych. Można go zainstalować z poziomu programu [R Studio Download](https://www.rstudio.com/products/rstudio/download/).
* Jeśli używasz programu RStudio for Desktop jako środowiska IDE, Zainstaluj również Microsoft R Client z programu [https://aka.ms/rclient/](https://aka.ms/rclient/) . 

Otwórz RStudio i wykonaj następujące czynności:

- Odwołuje się do `RODBC` pakietu. Umożliwia to nawiązanie połączenia z Azure Databricks przy użyciu utworzonej wcześniej nazwy DSN.
- Nawiąż połączenie przy użyciu nazwy DSN.
- Uruchom zapytanie SQL dotyczące danych w Azure Databricks. W poniższym fragmencie kodu *radio_sample_data* to tabela, która już istnieje w Azure Databricks.
- Wykonaj pewne operacje dotyczące zapytania, aby sprawdzić dane wyjściowe. 

Poniższy fragment kodu wykonuje następujące zadania:

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Łączenie z języka Python

W tej sekcji użyjesz środowiska IDE języka Python (na przykład bezczynności) do danych referencyjnych dostępnych w Azure Databricks. Przed rozpoczęciem należy spełnić następujące wymagania wstępne:

* Zainstaluj środowisko Python w [tym miejscu](https://www.python.org/downloads/). Zainstalowanie języka Python z tego linku powoduje także instalację bezczynną.

* W wierszu polecenia na komputerze zainstaluj `pyodbc` pakiet. Uruchom następujące polecenie:

      pip install pyodbc

Otwórz bezczynny i wykonaj następujące czynności:

- Zaimportuj `pyodbc` pakiet. Umożliwia to nawiązanie połączenia z Azure Databricks przy użyciu utworzonej wcześniej nazwy DSN.
- Nawiąż połączenie przy użyciu utworzonej wcześniej nazwy DSN.
-  Uruchom zapytanie SQL przy użyciu utworzonego połączenia. W poniższym fragmencie kodu *radio_sample_data* to tabela, która już istnieje w Azure Databricks.
- Wykonaj operacje dotyczące zapytania, aby sprawdzić dane wyjściowe.

Poniższy fragment kodu wykonuje następujące zadania:

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit=True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o źródłach, z których można importować dane do Azure Databricks, zobacz [źródła danych dla Azure Databricks](/azure/databricks/data/data-sources/index)


