---
title: Źródła danych obsługiwane w Azure Analysis Services | Microsoft Docs
description: Opisuje źródła danych i łączniki obsługiwane w przypadku tabelarycznych 1200 i wyższych modeli danych w Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77461661"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Źródła danych obsługiwane w usługach Azure Analysis Services.

Źródła danych i łączniki wyświetlane w Kreatorze pobieranie danych lub Importowanie tabel w programie Visual Studio z projektami Analysis Services są wyświetlane dla Azure Analysis Services i SQL Server Analysis Services. Nie wszystkie wyświetlane źródła danych i łączniki są jednak obsługiwane w Azure Analysis Services. Typy źródeł danych, z którymi można nawiązać połączenie, są zależne od wielu czynników, takich jak poziom zgodności modelu, dostępne łączniki danych, typ uwierzytelniania i obsługa lokalnej bramy danych. W poniższych tabelach opisano obsługiwane źródła danych dla Azure Analysis Services.

## <a name="azure-data-sources"></a>Źródła danych na platformie Azure

|Źródło danych  |W pamięci  |Tryb DirectQuery  |Uwagi |
|---------|---------|---------|---------|
|Azure SQL Database      |   Tak      |    Tak      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Analiza usługi Azure Synapse (SQL Data Warehouse)      |   Tak      |   Tak       |<sup>[dwóch](#azprovider)</sup>|
|Azure Blob Storage      |   Tak       |    Nie      | <sup>[jedno](#tab1400a)</sup> |
|Azure Table Storage     |   Tak       |    Nie      | <sup>[jedno](#tab1400a)</sup>|
|Azure Cosmos DB     |  Tak        |  Nie        |<sup>[jedno](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Tak       |    Nie      |<sup>[jedno](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Tak       |    Nie      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Usługa Azure HDInsight w systemie plików HDFS    |     Tak     |   Nie       |<sup>[jedno](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Tak       |   Nie       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**O**   
tylko <a name="tab1400a">1</a> -tabelaryczny model 1400 i wyższe.  
<a name="azprovider">2</a> — Jeśli określono jako źródło danych *dostawcy* w tabelarycznym 1200 i wyższych modelach, zarówno modele w pamięci, jak i zapytania bezpośredniego wymagają sterownika OLE DB firmy Microsoft dla SQL Server MSOLEDBSQL (zalecane), SQL Server Native Client 11,0 lub .NET Framework dostawca danych dla SQL Server.    
<a name="azsqlmanaged">3</a> -Azure SQL Database wystąpienie zarządzane jest obsługiwane. Ponieważ wystąpienie zarządzane działa w sieci wirtualnej platformy Azure z prywatnym adresem IP, w tym wystąpieniu musi być włączony publiczny punkt końcowy. Jeśli ta funkcja nie jest włączona, wymagana jest [lokalna Brama danych](analysis-services-gateway.md) .    
<a name="databricks">4</a> Azure Databricks używanie łącznika Spark nie jest obecnie obsługiwane.   
Program <a name="gen2">5</a> ADLS Gen2 łącznik nie jest obecnie obsługiwany, jednak łącznik usługi Azure Blob Storage może być używany ze źródłem danych ADLS Gen2.   

## <a name="other-data-sources"></a>Inne źródła danych

|Źródło danych | W pamięci | Tryb DirectQuery |Uwagi   |
|  --- | --- | --- | --- |
|Baza danych programu Access     |  Tak | Nie |  |
|Usługa Active Directory     |  Tak | Nie | <sup>[ust](#tab1400b)</sup>  |
|Analysis Services     |  Tak | Nie |  |
|System platformy analizy     |  Tak | Nie |  |
|Plik CSV  |Tak | Nie |  |
|Dynamics 365     |  Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Skoroszyt programu Excel     |  Tak | Nie |  |
|Exchange      |  Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Folder      |Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|IBM Informix  |Tak | Nie |  |
|Dokument JSON      |  Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Wiersze z pliku binarnego      | Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Baza danych MySQL     | Tak | Nie |  |
|Źródło danych OData      |  Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Zapytanie ODBC     | Tak | Nie |  |
|OLE DB     |   Tak | Nie |  |
|Oracle  | Tak  |Tak  | <sup>[9](#oracle)</sup> |
|Baza danych PostgreSQL   | Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Obiekty Salesforce|  Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Raporty usługi Salesforce |Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|SAP HANA     |  Tak | Nie |  |
|SAP Business Warehouse    |  Tak | Nie | <sup>[ust](#tab1400b)</sup> |
|Listy programu SharePoint      |   Tak | Nie | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Tak   | Tak  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> | 
|Magazyn danych SQL Server |Tak   | Tak  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Baza danych Sybase     |  Tak | Nie |  |
|Teradata | Tak  | Tak  | <sup>[dziesięć](#teradata)</sup> |
|Plik TXT  |Tak | Nie |  |
|Tabela XML    |  Tak | Nie | <sup>[ust](#tab1400b)</sup> |
| | | |

**O**   
<a name="tab1400b">6</a> -tabelaryczne 1400 i wyższe modele.  
<a name="sqlim">7</a> — Jeśli określono jako źródło danych *dostawcy* w tabelarycznym 1200 i wyższych modelach, określ Sterownik OLE DB firmy Microsoft dla SQL Server MSOLEDBSQL (zalecane), SQL Server Native Client 11,0 lub .NET Framework dostawca danych dla SQL Server.  
<a name="instgw">8</a> — Jeśli określono MSOLEDBSQL jako dostawcę danych, może być konieczne pobranie i zainstalowanie [sterownika OLE DB firmy Microsoft w celu SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) na tym samym komputerze, na którym znajduje się lokalna Brama danych.  
<a name="oracle">9</a> — w przypadku modeli tabelarycznych 1200 lub jako źródła danych *dostawcy* w tabelarycznych modelach modeli, określ dostawca danych Oracle dla platformy .NET.  
<a name="teradata">10</a> — w przypadku modeli tabelarycznych 1200 lub jako źródła danych *dostawcy* w tabelarycznych modelach modeli, określ dostawca danych programu Teradata dla platformy .NET.   
<a name="filesSP">11</a> — pliki w lokalnym programie SharePoint nie są obsługiwane.

Połączenie z lokalnymi źródłami danych z serwera Azure Analysis Services wymaga [bramy lokalnej](analysis-services-gateway.md). W przypadku korzystania z bramy wymagane są 64-bitowe dostawcy. 

## <a name="understanding-providers"></a>Informacje o dostawcach

Podczas tworzenia tabelarycznych projektów 1400 i wyższych modeli w programie Visual Studio domyślnie nie jest określany dostawca danych podczas nawiązywania połączenia ze źródłem danych przy użyciu polecenia **Pobierz dane**. Tabelaryczne 1400 i wyższe modele używają [Power Query](/power-query/power-query-what-is-power-query) łączników do zarządzania połączeniami, kwerendami danych i mashupy między źródłem danych i Analysis Services. Są one czasami określane jako połączenia ze źródłem danych ze *strukturą* w ramach tych ustawień właściwości połączenia. Można jednak włączyć starsze źródła danych. Po włączeniu programu można użyć **Kreatora importu tabeli** do łączenia się z niektórymi źródłami danych tradycyjnie obsługiwanymi w tabelarycznych 1200 i niższych modelach jako *starszych*lub źródeł danych *dostawcy* . W przypadku określenia źródła danych dostawcy można określić określonego dostawcę danych i inne zaawansowane właściwości połączenia. Można na przykład połączyć się z lokalnym magazynem danych SQL Server, a nawet Azure SQL Database jako starszym źródłem danych. Następnie można wybrać sterownik OLE DB dla dostawcy danych programu SQL Server MSOLEDBSQL. W takim przypadku wybranie dostawcy danych OLE DB może zapewnić lepszą wydajność w przypadku łącznika Power Query. 

W przypadku korzystania z Kreatora importu tabeli w programie Visual Studio połączenia z dowolnym źródłem danych wymagają dostawcy danych. Wybrano domyślnego dostawcę danych. W razie konieczności można zmienić dostawcę danych. Wybrany typ dostawcy może zależeć od wydajności, bez względu na to, czy model używa magazynu w pamięci lub zapytania bezpośredniego, a który Analysis Services platformę, do której zostanie wdrożony model.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Określ źródła danych dostawcy w tabelarycznych projektach modeli 1400 i wyższych

Aby włączyć źródła danych dostawcy, w programie Visual Studio kliknij pozycję **Narzędzia** > **Opcje** > **Analysis Services** > **Importuj dane**tabelaryczne i wybierz pozycję **Włącz starsze źródła danych**.

![Włącz starsze źródła danych](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Ze włączonymi starszymi źródłami danych w **Eksploratorze modeli tabelarycznych**kliknij prawym przyciskiem myszy pozycję **źródła** > danych**Importuj ze źródła danych (starsza wersja)**.

![Starsze źródła danych w Eksploratorze modeli tabelarycznych](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Podobnie jak w przypadku tabelarycznych projektów modelu 1200, użyj **Kreatora importu tabeli** , aby nawiązać połączenie ze źródłem danych. Na stronie Połącz kliknij pozycję **Zaawansowane**. Określ dostawcę danych i inne ustawienia połączenia w **ustawieniu zaawansowane właściwości**.

![Właściwości zaawansowane ze starszymi źródłami danych](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Personifikacja
W niektórych przypadkach może być konieczne określenie innego konta personifikacji. Konto personifikacji można określić w programie Visual Studio lub SSMS.

Dla lokalnych źródeł danych:

* W przypadku korzystania z uwierzytelniania SQL, personifikacja powinna być kontem usługi.
* Jeśli używasz uwierzytelniania systemu Windows, ustaw użytkownika/hasło systemu Windows. W przypadku SQL Server Uwierzytelnianie systemu Windows z określonym kontem personifikacji jest obsługiwane tylko dla modeli danych w pamięci.

W przypadku źródeł danych w chmurze:

* W przypadku korzystania z uwierzytelniania SQL, personifikacja powinna być kontem usługi.

## <a name="oauth-credentials"></a>Poświadczenia uwierzytelniania OAuth

Dla modeli tabelarycznych na poziomie zgodności 1400 i wyższych przy użyciu trybu w pamięci Azure SQL Database, usługa Azure Synapse Analytics (SQL Data Warehouse), Dynamics 365 i lista programu SharePoint obsługują poświadczenia uwierzytelniania OAuth. Azure Analysis Services zarządza odświeżanie tokenów dla źródeł danych OAuth w celu uniknięcia przekroczeń limitu czasu dla długotrwałych operacji odświeżania. Aby wygenerować prawidłowe tokeny, Ustaw poświadczenia za pomocą programu SSMS.

Tryb zapytania bezpośredniego nie jest obsługiwany z poświadczeniami uwierzytelniania OAuth.

## <a name="next-steps"></a>Następne kroki
[Brama lokalna](analysis-services-gateway.md)   
[Zarządzanie serwerem](analysis-services-manage.md)   

