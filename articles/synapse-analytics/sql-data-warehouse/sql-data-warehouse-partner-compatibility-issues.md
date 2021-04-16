---
title: Problemy ze zgodnością aplikacji i aplikacji innych Azure Synapse Analytics
description: Opisuje znane problemy, które aplikacje innych firm mogą znaleźć w Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: sql
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 3bad9d6464b41ff1b7ad03147d3a48c50c787cb0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568118"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Problemy ze zgodnością aplikacji i aplikacji innych Azure Synapse Analytics

Aplikacje sbudowaną SQL Server bezproblemowo współpracować z Azure Synapse pulami SQL. Jednak w niektórych przypadkach funkcje i elementy języka, które są często używane w programie SQL Server, mogą nie być dostępne Azure Synapse lub mogą zachowywać się inaczej.

W tym artykule wymieniono znane problemy, które mogą wystąpić podczas korzystania z aplikacji innych firm z Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Błąd Tableau: "Próba ukończenia transakcji nie powiodła się. Nie znaleziono odpowiedniej transakcji"

Począwszy od Azure Synapse dedykowanej puli SQL w wersji 10.0.11038.0, niektóre zapytania Tableau, które wysyłają wywołania procedur składowanych, mogą się nie powieść z następującym komunikatem o błędzie:**"Microsoft][ODBC Driver 17 for SQL Server][SQL Server]111214; Próba ukończenia transakcji nie powiodła się. Nie znaleziono odpowiadającej mu transakcji.**"

### <a name="cause"></a>Przyczyna

Jest to problem w dedykowanej Azure Synapse SQL spowodowany wprowadzeniem nowych systemowych procedur składowanych, które są wywoływane automatycznie przez sterowniki ODBC i JDBC. Jedna z tych procedur składowanych systemu może spowodować przerwanie otwartych transakcji, jeśli ich wykonanie nie powiedzie się. Ten problem może wystąpić w zależności od logiki aplikacji klienckiej.

### <a name="solution"></a>Rozwiązanie
Klienci, którzy widzą ten konkretny problem podczas korzystania z usługi Tableau połączonej z Azure Synapse dedykowanymi pulami SQL, powinni ustawić wartość FMTONLY na wartość YES (Tak) w połączeniu SQL. W przypadku programu Tableau Desktop i serwera Tableau należy użyć pliku dostosowywania źródła danych Tableau (TDC), aby upewnić się, że tableau przekazuje ten parametr do sterownika.  

> [!NOTE] 
> Firma Microsoft nie zapewnia pomocy technicznej dotyczącej narzędzi innych firm. Przetestowaliśmy, że to rozwiązanie działa z rozwiązaniem Tableau Desktop 2020.3.2, jednak należy użyć tego obejścia we własnej pojemności.
>

* [Aby dowiedzieć się, jak dostosowań globalnych za pomocą pliku TDC w programie Tableau Desktop, zapoznaj się z dokumentacją programu Tableau Desktop.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Aby dowiedzieć się, jak dostosowań globalnych za pomocą pliku TDC na serwerze Tableau, zobacz Using a . Plik TDC z serwerem Tableau.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

W poniższym przykładzie pokazano plik TDC Tableau, który przekazuje parametr FMTONLY=YES do parametrów połączenia SQL:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Aby uzyskać więcej informacji na temat korzystania z plików TDC, skontaktuj się z pomocą techniczną tableau. 

## <a name="see-also"></a>Zobacz też

* [Elementy języka T-SQL dla dedykowanej puli SQL w Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [Instrukcje języka T-SQL obsługiwane dla dedykowanej puli SQL w Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-statements.md)