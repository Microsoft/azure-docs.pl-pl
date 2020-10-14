---
title: Źródła danych obsługiwane w Azure Analysis Services | Microsoft Docs
description: Opisuje źródła danych i łączniki obsługiwane w przypadku tabelarycznych 1200 i wyższych modeli danych w Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 174ad4692d043390e6773a98e31f0985d75c8e2e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018818"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Źródła danych obsługiwane w usługach Azure Analysis Services.

Źródła danych i łączniki wyświetlane w Kreatorze pobieranie danych lub Importowanie tabel w programie Visual Studio z projektami Analysis Services są wyświetlane dla Azure Analysis Services i SQL Server Analysis Services. Nie wszystkie wyświetlane źródła danych i łączniki są jednak obsługiwane w Azure Analysis Services. Typy źródeł danych, z którymi można nawiązać połączenie, są zależne od wielu czynników, takich jak poziom zgodności modelu, dostępne łączniki danych, typ uwierzytelniania i obsługa lokalnej bramy danych. W poniższych tabelach opisano obsługiwane źródła danych dla Azure Analysis Services.

## <a name="azure-data-sources"></a>Źródła danych na platformie Azure

|Źródło danych  |W pamięci  |DirectQuery  |Uwagi |
|---------|---------|---------|---------|
|Azure SQL Database      |   Tak      |    Yes      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   Yes      |   Yes       |<sup>[dwóch](#azprovider)</sup>|
|Azure Blob Storage      |   Tak       |    Nie      | <sup>[jedno](#tab1400a)</sup> |
|Azure Table Storage     |   Tak       |    Nie      | <sup>[jedno](#tab1400a)</sup>|
|Azure Cosmos DB     |  Tak        |  Nie        |<sup>[jedno](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Yes       |    Nie      |<sup>[jedno](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Yes       |    Nie      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Usługa Azure HDInsight w systemie plików HDFS    |     Yes     |   Nie       |<sup>[jedno](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Tak       |   Nie       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Uwagi:**

tylko <a name="tab1400a">1</a> -tabelaryczny model 1400 i wyższe.  
<a name="azprovider">2</a> — Jeśli określono jako źródło danych *dostawcy* w tabelarycznym 1200 i wyższych modelach, zarówno modele w pamięci, jak i zapytania bezpośredniego wymagają sterownika OLE DB firmy Microsoft dla SQL Server MSOLEDBSQL (zalecane), SQL Server Native Client 11,0 lub .NET Framework dostawca danych dla SQL Server.  
<a name="azsqlmanaged">3</a> — obsługiwane jest wystąpienie zarządzane Azure SQL. Ponieważ wystąpienie zarządzane SQL działa w sieci wirtualnej platformy Azure z prywatnym adresem IP, w tym wystąpieniu musi być włączony publiczny punkt końcowy. Jeśli ta funkcja nie jest włączona, wymagana jest [lokalna Brama danych](analysis-services-gateway.md) .  
<a name="databricks">4</a> Azure Databricks używanie łącznika Spark nie jest obecnie obsługiwane.  
Program <a name="gen2">5</a> ADLS Gen2 łącznik nie jest obecnie obsługiwany, jednak łącznik usługi Azure Blob Storage może być używany ze źródłem danych ADLS Gen2.

## <a name="other-data-sources"></a>Inne źródła danych

|Źródło danych | W pamięci | DirectQuery |Uwagi   |
|  --- | --- | --- | --- |
|Baza danych programu Access     |  Tak | Nie |  |
|Active Directory     |  Tak | Nie | <sup>[ust](#tab1400b)</sup>  |
|Analysis Services     |  Yes | Nie |  |
|System platformy analizy     |  Yes | Nie |  |
|Plik CSV  |Yes | Nie |  |
|Dynamics 365     |  Yes | Nie | <sup>[ust](#tab1400b)</sup> |
|Skoroszyt programu Excel     |  Yes | Nie |  |
|Exchange      |  Yes | Nie | <sup>[ust](#tab1400b)</sup> |
|Folder      |Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|IBM Informix  |Yes | Nie |  |
|Dokument JSON      |  Yes | Nie | <sup>[ust](#tab1400b)</sup> |
|Wiersze z pliku binarnego      | Yes | Nie | <sup>[ust](#tab1400b)</sup> |
|Baza danych MySQL     | Tak | Nie |  |
|Źródło danych OData      |  Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Zapytanie ODBC     | Yes | Nie |  |
|OLE DB     |   Tak | Nie |  |
|Oracle  | Tak  |Yes  | <sup>[9](#oracle)</sup> |
|Baza danych PostgreSQL   | Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Obiekty Salesforce|  Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Raporty usługi Salesforce |Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|SAP HANA     |  Tak | Nie |  |
|SAP Business Warehouse    |  Yes | Nie | <sup>[ust](#tab1400b)</sup> |
|Listy programu SharePoint      |   Tak | Nie | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Tak   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Magazyn danych SQL Server |Yes   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Baza danych Sybase     |  Tak | Nie |  |
|Teradata | Tak  | Yes  | <sup>[dziesięć](#teradata)</sup> |
|Plik TXT  |Yes | Nie |  |
|Tabela XML    |  Yes | Nie | <sup>[ust](#tab1400b)</sup> |
| | | |

**Uwagi:**  
<a name="tab1400b">6</a> -tabelaryczne 1400 i wyższe modele.  
<a name="sqlim">7</a> — Jeśli określono jako źródło danych *dostawcy* w tabelarycznym 1200 i wyższych modelach, określ Sterownik OLE DB firmy Microsoft dla SQL Server MSOLEDBSQL (zalecane), SQL Server Native Client 11,0 lub .NET Framework dostawca danych dla SQL Server.  
<a name="instgw">8</a> — Jeśli określono MSOLEDBSQL jako dostawcę danych, może być konieczne pobranie i zainstalowanie [sterownika OLE DB firmy Microsoft w celu SQL Server](/sql/connect/oledb/oledb-driver-for-sql-server) na tym samym komputerze, na którym znajduje się lokalna Brama danych.  
<a name="oracle">9</a> — w przypadku modeli tabelarycznych 1200 lub jako źródła danych *dostawcy* w tabelarycznych modelach modeli, określ dostawca danych Oracle dla platformy .NET. Jeśli określono jako źródło danych ze strukturą, należy [włączyć dostawcę zarządzanego](#enable-oracle-managed-provider)przez program Oracle.   
<a name="teradata">10</a> — w przypadku modeli tabelarycznych 1200 lub jako źródła danych *dostawcy* w tabelarycznych modelach modeli, określ dostawca danych programu Teradata dla platformy .NET.  
<a name="filesSP">11</a> — pliki w lokalnym programie SharePoint nie są obsługiwane.

Połączenie z lokalnymi źródłami danych z serwera Azure Analysis Services wymaga [bramy lokalnej](analysis-services-gateway.md). W przypadku korzystania z bramy wymagane są 64-bitowe dostawcy.

## <a name="understanding-providers"></a>Informacje o dostawcach

Podczas tworzenia tabelarycznych projektów 1400 i wyższych modeli w programie Visual Studio domyślnie nie jest określany dostawca danych podczas nawiązywania połączenia ze źródłem danych przy użyciu polecenia **Pobierz dane**. Tabelaryczne 1400 i wyższe modele używają [Power Query](/power-query/power-query-what-is-power-query) łączników do zarządzania połączeniami, kwerendami danych i mashupy między źródłem danych i Analysis Services. Są one czasami określane jako połączenia ze źródłem danych ze *strukturą* w ramach tych ustawień właściwości połączenia. Można jednak włączyć starsze źródła danych dla projektu modelu w programie Visual Studio. Po włączeniu programu można użyć **Kreatora importu tabeli** do łączenia się z niektórymi źródłami danych tradycyjnie obsługiwanymi w tabelarycznych 1200 i niższych modelach jako *starszych*lub źródeł danych *dostawcy* . W przypadku określenia źródła danych dostawcy można określić określonego dostawcę danych i inne zaawansowane właściwości połączenia. Można na przykład połączyć się z wystąpieniem magazynu danych SQL Server, a nawet Azure SQL Database jako starszym źródłem danych. Następnie można wybrać sterownik OLE DB dla dostawcy danych programu SQL Server MSOLEDBSQL. W takim przypadku wybranie dostawcy danych OLE DB może zapewnić lepszą wydajność w przypadku łącznika Power Query. 

W przypadku korzystania z Kreatora importu tabeli w programie Visual Studio połączenia z dowolnym źródłem danych wymagają dostawcy danych. Wybrano domyślnego dostawcę danych. W razie konieczności można zmienić dostawcę danych. Wybrany typ dostawcy może zależeć od wydajności, bez względu na to, czy model używa magazynu w pamięci lub zapytania bezpośredniego, a który Analysis Services platformę, do której zostanie wdrożony model.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Określ źródła danych dostawcy w tabelarycznych projektach modeli 1400 i wyższych

Aby włączyć źródła danych dostawcy, w programie Visual Studio kliknij pozycję **Narzędzia**  >  **Opcje**  >  **Analysis Services**  >  **Importuj dane**tabelaryczne i wybierz pozycję **Włącz starsze źródła danych**.

![Włącz starsze źródła danych](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Ze włączonymi starszymi źródłami danych w **Eksploratorze modeli tabelarycznych**kliknij prawym przyciskiem myszy pozycję **źródła danych**  >  **Importuj ze źródła danych (starsza wersja)**.

![Starsze źródła danych w Eksploratorze modeli tabelarycznych](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Podobnie jak w przypadku tabelarycznych projektów modelu 1200, użyj **Kreatora importu tabeli** , aby nawiązać połączenie ze źródłem danych. Na stronie Połącz kliknij pozycję **Zaawansowane**. Określ dostawcę danych i inne ustawienia połączenia w **ustawieniu zaawansowane właściwości**.

![Właściwości zaawansowane ze starszymi źródłami danych](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Personifikacja
W niektórych przypadkach może być konieczne określenie innego konta personifikacji. Konto personifikacji można określić w programie Visual Studio lub SQL Server Management Studio (SSMS).

Dla lokalnych źródeł danych:

* W przypadku korzystania z uwierzytelniania SQL, personifikacja powinna być kontem usługi.
* Jeśli używasz uwierzytelniania systemu Windows, ustaw użytkownika/hasło systemu Windows. W przypadku SQL Server Uwierzytelnianie systemu Windows z określonym kontem personifikacji jest obsługiwane tylko dla modeli danych w pamięci.

W przypadku źródeł danych w chmurze:

* W przypadku korzystania z uwierzytelniania SQL, personifikacja powinna być kontem usługi.

## <a name="oauth-credentials"></a>Poświadczenia uwierzytelniania OAuth

Dla modeli tabelarycznych na poziomie zgodności 1400 i wyższych przy użyciu trybu w pamięci, Azure SQL Database, Azure Synapse (dawniej SQL Data Warehouse), Dynamics 365 i lista programu SharePoint obsługują poświadczenia uwierzytelniania OAuth. Azure Analysis Services zarządza odświeżanie tokenów dla źródeł danych OAuth w celu uniknięcia przekroczeń limitu czasu dla długotrwałych operacji odświeżania. Aby wygenerować prawidłowe tokeny, Ustaw poświadczenia przy użyciu Power Query.

Tryb zapytania bezpośredniego nie jest obsługiwany z poświadczeniami uwierzytelniania OAuth.

## <a name="enable-oracle-managed-provider"></a>Włącz dostawcę zarządzanego przez program Oracle

W niektórych przypadkach zapytania języka DAX do źródła danych Oracle mogą zwracać nieoczekiwane wyniki. Przyczyną może być dostawca używany do połączenia ze źródłem danych.

Zgodnie z opisem w sekcji [Informacje o dostawcach](#understanding-providers) modele tabelaryczne łączą się ze źródłami danych jako *strukturalnym* źródłem danych lub źródłem danych *dostawcy* . W przypadku modeli ze źródłem danych Oracle określonych jako źródło danych dostawcy upewnij się, że określony dostawca to Oracle Dostawca danych dla platformy .NET (Oracle. DataAccess. Client). 

Jeśli źródło danych Oracle jest określone jako strukturalne źródło danych, należy włączyć właściwość serwera **MDataEngine\UseManagedOracleProvider** . Ustawienie tej właściwości zapewnia, że model nawiązuje połączenie ze źródłem danych Oracle przy użyciu zalecanej Dostawca danych Oracle dla dostawcy zarządzanego przez platformę .NET.
 
Aby włączyć dostawcę zarządzanego przez program Oracle:

1. W SQL Server Management Studio Połącz się z serwerem.
2. Utwórz zapytanie XMLA z następującym skryptem. Zastąp wartość **servername** pełną nazwą serwera, a następnie wykonaj zapytanie.

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. Uruchom ponownie serwer.


## <a name="next-steps"></a>Następne kroki

* [Brama lokalna](analysis-services-gateway.md)
* [Zarządzanie serwerem](analysis-services-manage.md)