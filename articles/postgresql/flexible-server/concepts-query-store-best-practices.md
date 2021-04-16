---
title: Najlepsze rozwiązania dotyczące magazynu zapytań w Azure Database for PostgreSQL — Flex Server
description: W tym artykule opisano najlepsze rozwiązania dotyczące magazynu zapytań w Azure Database for PostgreSQL — Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559114"
---
# <a name="best-practices-for-query-store---flexible-server"></a>Najlepsze rozwiązania dotyczące magazynu zapytań — serwer elastyczny

**Dotyczy:** Azure Database for PostgreSQL — Flex Server w wersji 11, 12

W tym artykule przedstawiono najlepsze rozwiązania dotyczące używania magazynu zapytań w Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Ustawianie optymalnego trybu przechwytywania zapytania
Pozwól magazynowi zapytań przechwytywać ważne dane. 

|**pg_qs.query_capture_mode** | **Scenariusz**|
|---|---|
|_Wszystko_  |Dokładnie przeanalizuj obciążenie pod kątem wszystkich zapytań, ich częstotliwości wykonywania i innych statystyk. Identyfikowanie nowych zapytań w obciążeniu. Wykrywanie, czy zapytania ad hoc są używane do identyfikowania możliwości użytkownika lub automatycznej parametryzacji. _Wszystkie_ te zasoby mają zwiększony koszt użycia zasobów. |
|_Do góry_  |Skoncentruj swoją uwagę na najlepszych zapytaniach — tych wystawionych przez klientów.
|_Brak_ |Jeśli zostanie ustawiona wartość Brak, magazyn zapytań nie przechwyci żadnych nowych zapytań. Przechwycono już zestaw zapytań i okno czasu, które chcesz zbadać, i chcesz wyeliminować rozpraszające uwagę, które mogą wprowadzać inne zapytania. _Brak_ jest odpowiedni do testowania i oznaczania środowiska. _Nie_ należy używać ich ostrożnie, ponieważ możesz pominąć możliwość śledzenia i optymalizowania ważnych nowych zapytań. |


> [!NOTE] 
> **pg_qs.query_capture_mode** **pgms_wait_sampling.query_capture_mode**. Jeśli pg_qs.query_capture_mode _nie_ ma wartości , ustawienie pgms_wait_sampling.query_capture_mode nie ma żadnego efektu. 


## <a name="keep-the-data-you-need"></a>Zachowaj potrzebne dane
Parametr **pg_qs.retention_period_in_days** określa w dniach okres przechowywania danych dla magazynu zapytań. Starsze dane zapytań i statystyk są usuwane. Domyślnie magazyn zapytań jest skonfigurowany do przechowywania danych przez 7 dni. Unikaj przechowywania danych historycznych, których nie planujesz używać. Zwiększ wartość, jeśli chcesz przechowywać dane dłużej.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak pobrać lub ustawić parametry przy użyciu [interfejsu Azure Portal](howto-configure-server-parameters-using-portal.md) lub interfejsu wiersza [polecenia platformy Azure.](howto-configure-server-parameters-using-cli.md)
