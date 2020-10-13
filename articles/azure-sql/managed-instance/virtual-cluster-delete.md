---
title: Usuń podsieć po usunięciu zarządzanego wystąpienia wystąpienia zarządzanego SQL
description: Dowiedz się, jak usunąć usługę Azure Virtual Network po usunięciu wystąpienia zarządzanego wystąpienia zarządzanego Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 0b8ceee1260e4209420778d7f327d771b858a899
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617727"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>Usuń podsieć po usunięciu zarządzanego wystąpienia wystąpienia zarządzanego SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera wskazówki dotyczące ręcznego usuwania podsieci po usunięciu ostatniego wystąpienia zarządzanego wystąpienia zarządzanego Azure SQL.

Wystąpienia zarządzane są wdrażane w [klastrach wirtualnych](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Każdy klaster wirtualny jest skojarzony z podsiecią. Klaster wirtualny utrzymuje się przez 12 godzin po usunięciu ostatniego wystąpienia, aby umożliwić szybsze tworzenie wystąpień zarządzanych w tej samej podsieci. Nie jest naliczana opłata za utrzymywanie pustego klastra wirtualnego. W tym okresie nie można usunąć podsieci skojarzonej z klastrem wirtualnym.

Jeśli nie chcesz czekać 12 godzin i wolisz usunąć klaster wirtualny i jego podsieć, możesz to zrobić ręcznie. Ręcznie usuń klaster wirtualny przy użyciu Azure Portal lub interfejsu API klastrów wirtualnych.

> [!IMPORTANT]
> - Klaster wirtualny nie powinien zawierać żadnych wystąpień zarządzanych, aby usuwanie powiodło się. 
> - Usuwanie klastra wirtualnego jest długotrwałą operacją długotrwałą przez około 1,5 godzin (zobacz [operacje zarządzania wystąpieniami zarządzanymi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) dla aktualnego czasu usuwania klastra wirtualnego). Klaster wirtualny będzie nadal widoczny w portalu do momentu zakończenia tego procesu.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Usuwanie klastra wirtualnego z Azure Portal

Aby usunąć klaster wirtualny przy użyciu Azure Portal, wyszukaj zasoby klastra wirtualnego.

![Zrzut ekranu przedstawiający Azure Portal z wyróżnionym polem wyszukiwania](./media/virtual-cluster-delete/virtual-clusters-search.png)

Po znalezieniu klastra wirtualnego, który chcesz usunąć, wybierz pozycję ten zasób, a następnie wybierz pozycję **Usuń**. Zostanie wyświetlony monit o potwierdzenie usunięcia klastra wirtualnego.

![Zrzut ekranu pulpitu nawigacyjnego Azure Portal klastrów wirtualnych z wyróżnioną opcją usuwania](./media/virtual-cluster-delete/virtual-clusters-delete.png)

W przypadku powiadomień Azure Portal zostanie wyświetlone potwierdzenie, że żądanie usunięcia klastra wirtualnego zostało pomyślnie przesłane. Sama operacja usuwania będzie trwać około 1,5 godzin, podczas którego klaster wirtualny będzie nadal widoczny w portalu. Po zakończeniu procesu klaster wirtualny nie będzie już widoczny, a skojarzona z nim podsieć zostanie wydana do ponownego użycia.

> [!TIP]
> Jeśli w klastrze wirtualnym nie są wyświetlane żadne wystąpienia zarządzane i nie można usunąć klastra wirtualnego, upewnij się, że nie ma trwającego wdrożenia w toku. Obejmuje to uruchomione i anulowane wdrożenia, które nadal są w toku. Wynika to z faktu, że te operacje nadal będą używać klastra wirtualnego, blokując go przed usunięciem. Przeglądanie karty **wdrożenia** w grupie zasobów, w której wdrożono wystąpienie, będzie wskazywać wszystkie wdrożenia w toku. W takim przypadku poczekaj na zakończenie wdrożenia, Usuń wystąpienie zarządzane, a następnie usuń klaster wirtualny.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Usuwanie klastra wirtualnego za pomocą interfejsu API

Aby usunąć klaster wirtualny za pomocą interfejsu API, użyj parametrów identyfikatora URI określonych w [metodzie Delete klastrów wirtualnych](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).
- Dowiedz się więcej o [architekturze łączności w wystąpieniu zarządzanym SQL](connectivity-architecture-overview.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego SQL](vnet-existing-add-subnet.md).
- Samouczek pokazujący sposób tworzenia sieci wirtualnej, tworzenia wystąpienia zarządzanego i przywracania bazy danych z kopii zapasowej bazy danych znajduje się w temacie [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Problemy związane z usługą DNS można znaleźć w temacie [Configure a Custom DNS](custom-dns-configure.md).
