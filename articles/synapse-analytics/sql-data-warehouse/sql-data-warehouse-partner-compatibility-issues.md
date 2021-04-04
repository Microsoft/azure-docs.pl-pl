---
title: Problemy ze zgodnością z aplikacjami innych firm i analizą usługi Azure Synapse
description: Opisuje znane problemy, które mogą znajdować się w aplikacjach innych firm przy użyciu usługi Azure Synapse
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: a1031656eaa5125d07ae078773379270b26625e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98121383"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>Problemy ze zgodnością z aplikacjami innych firm i analizą usługi Azure Synapse

Aplikacje utworzone dla SQL Server będą bezproblemowo współpracować z dedykowanymi pulami SQL platformy Azure Synapse. Jednak w niektórych przypadkach funkcje i elementy języka, które są powszechnie używane w SQL Server mogą nie być dostępne w usłudze Azure Synapse lub mogą zachowywać się inaczej.

W tym artykule wymieniono znane problemy, które można napotkać podczas korzystania z aplikacji innych firm za pomocą usługi Azure Synapse Analytics. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Błąd Tableau: "próba ukończenia transakcji nie powiodła się. Nie znaleziono odpowiadających transakcji "

Począwszy od dedykowanej puli SQL Synapse platformy Azure w wersji 10.0.11038.0, niektóre zapytania Tableau wykonujące wywołania procedury składowanej mogą zakończyć się niepowodzeniem z następującym komunikatem o błędzie: "**[Microsoft] [ODBC Driver 17 for SQL Server] [SQL Server] 111214; Próba ukończenia transakcji nie powiodła się. Nie znaleziono odpowiadających transakcji.**"

### <a name="cause"></a>Przyczyna

Jest to problem z dedykowaną pulą SQL usługi Azure Synapse spowodowanym wprowadzeniem nowych systemowych procedur składowanych, które są wywoływane automatycznie przez sterowniki ODBC i JDBC. Jedna z tych procedur składowanych w systemie może spowodować przerwanie otwartych transakcji w przypadku niepowodzenia wykonania. Ten problem może wystąpić w zależności od logiki aplikacji klienta.

### <a name="solution"></a>Rozwiązanie
Klienci widzą ten konkretny problem w przypadku korzystania z usługi Tableau połączonej z dedykowanymi pulami SQL Synapse, należy ustawić wartość tak w połączeniu SQL. W przypadku komputerów z systemem Tableau i Tableau należy użyć pliku dostosowania źródła danych Tableau (TDC), aby upewnić się, że Tableau przekaże ten parametr do sterownika.  

> [!NOTE] 
> Firma Microsoft nie zapewnia pomocy technicznej dla narzędzi innych firm. Po przetestowaniu, że to rozwiązanie współpracuje z Tableau Desktop 2020.3.2, należy użyć tego obejścia na własną pojemność.
>

* [Aby dowiedzieć się, jak dokonać globalnych dostosowań przy użyciu pliku TDC na pulpicie Tableau, zapoznaj się z dokumentacją programu Tableau Desktop.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Aby dowiedzieć się, jak dokonać niestandardowych dostosowań przy użyciu pliku TDC na serwerze Tableau, zobacz. Plik TDC z serwerem Tableau.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

W poniższym przykładzie przedstawiono plik Tableau TDC, który przekazuje parametr FMTONLY = YES do parametrów połączenia SQL:

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
Aby uzyskać więcej informacji o używaniu plików TDC, skontaktuj się z pomocą techniczną Tableau. 

## <a name="see-also"></a>Zobacz też

* [Elementy języka T-SQL dla dedykowanej puli SQL w usłudze Azure Synapse Analytics.](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [W przypadku dedykowanej puli SQL w usłudze Azure Synapse Analytics obsługiwane są instrukcje języka T-SQL.](./sql-data-warehouse-reference-tsql-statements.md)