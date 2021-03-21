---
title: Usuń podsieć po usunięciu wystąpienia zarządzanego SQL
description: Dowiedz się, jak usunąć usługę Azure Virtual Network po usunięciu wystąpienia zarządzanego Azure SQL.
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
ms.openlocfilehash: 496ff6c7ec39706a99bb40447b6443ca71f19e5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99093688"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Usuń podsieć po usunięciu wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera wskazówki dotyczące ręcznego usuwania podsieci po usunięciu z niej ostatniego wystąpienia zarządzanego Azure SQL.

Wystąpienia zarządzane SQL są wdrażane w [klastrach wirtualnych](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Każdy klaster wirtualny jest skojarzony z podsiecią. Klaster wirtualny utrzymuje się przez 12 godzin po usunięciu ostatniego wystąpienia, aby umożliwić szybsze tworzenie wystąpień zarządzanych SQL w tej samej podsieci. Nie jest naliczana opłata za utrzymywanie pustego klastra wirtualnego. W tym okresie nie można usunąć podsieci skojarzonej z klastrem wirtualnym.

Jeśli nie chcesz czekać 12 godzin i wolisz usunąć klaster wirtualny i jego podsieć, możesz to zrobić ręcznie. Ręcznie usuń klaster wirtualny przy użyciu Azure Portal lub interfejsu API klastrów wirtualnych.

> [!IMPORTANT]
> - Aby usuwanie powiodło się, klaster wirtualny nie powinien zawierać wystąpień zarządzanych programu SQL. 
> - Usuwanie klastra wirtualnego to długotrwała operacja długotrwała przez około 1,5 godzin (zobacz [operacje zarządzania wystąpieniami zarządzanymi przez program SQL Server](./sql-managed-instance-paas-overview.md#management-operations) , aby uzyskać aktualną wartość czasu usuwania klastra wirtualnego). Klaster wirtualny będzie nadal widoczny w portalu do momentu zakończenia tego procesu.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Usuwanie klastra wirtualnego z Azure Portal

Aby usunąć klaster wirtualny przy użyciu Azure Portal, wyszukaj zasoby klastra wirtualnego.

![Zrzut ekranu przedstawiający Azure Portal z wyróżnionym polem wyszukiwania](./media/virtual-cluster-delete/virtual-clusters-search.png)

Po znalezieniu klastra wirtualnego, który chcesz usunąć, wybierz pozycję ten zasób, a następnie wybierz pozycję **Usuń**. Zostanie wyświetlony monit o potwierdzenie usunięcia klastra wirtualnego.

![Zrzut ekranu pulpitu nawigacyjnego Azure Portal klastrów wirtualnych z wyróżnioną opcją usuwania](./media/virtual-cluster-delete/virtual-clusters-delete.png)

W przypadku powiadomień Azure Portal zostanie wyświetlone potwierdzenie, że żądanie usunięcia klastra wirtualnego zostało pomyślnie przesłane. Sama operacja usuwania będzie trwać około 1,5 godzin, podczas którego klaster wirtualny będzie nadal widoczny w portalu. Po zakończeniu procesu klaster wirtualny nie będzie już widoczny, a skojarzona z nim podsieć zostanie wydana do ponownego użycia.

> [!TIP]
> Jeśli w klastrze wirtualnym nie ma wystąpień zarządzanych przez program SQL i nie można usunąć klastra wirtualnego, upewnij się, że nie ma trwającego wdrożenia w toku. Obejmuje to uruchomione i anulowane wdrożenia, które nadal są w toku. Wynika to z faktu, że te operacje nadal będą używać klastra wirtualnego, blokując go przed usunięciem. Przeglądanie karty **wdrożenia** w grupie zasobów, w której wdrożono wystąpienie, będzie wskazywać wszystkie wdrożenia w toku. W takim przypadku poczekaj na zakończenie wdrożenia, Usuń wystąpienie zarządzane SQL, a następnie usuń klaster wirtualny.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Usuwanie klastra wirtualnego za pomocą interfejsu API

Aby usunąć klaster wirtualny za pomocą interfejsu API, użyj parametrów identyfikatora URI określonych w [metodzie Delete klastrów wirtualnych](/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane Azure SQL?](sql-managed-instance-paas-overview.md).
- Dowiedz się więcej o [architekturze łączności w wystąpieniu zarządzanym SQL](connectivity-architecture-overview.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego SQL](vnet-existing-add-subnet.md).
- Samouczek pokazujący sposób tworzenia sieci wirtualnej, tworzenia wystąpienia zarządzanego usługi Azure SQL i przywracania bazy danych z kopii zapasowej bazy danych znajduje się w temacie [Tworzenie wystąpienia zarządzanego usługi Azure SQL (Portal)](instance-create-quickstart.md).
- Problemy związane z usługą DNS można znaleźć w temacie [Configure a Custom DNS](custom-dns-configure.md).