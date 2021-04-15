---
title: Alias DNS
description: Aplikacje mogą łączyć się z aliasem nazwy serwera dla Azure SQL Database. W międzyczasie możesz zmienić punkt SQL Database alias na w dowolnym momencie, aby ułatwić testowanie i tak dalej.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 6ef268b349d5a21cdbadd55ffd2199a35f650e5b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376294"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS dla usługi Azure SQL Database
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL Database ma serwer systemu nazw domen (DNS). Program PowerShell i interfejsy API REST [akceptują wywołania](#anchor-powershell-code-62x) tworzenia aliasów DNS dla nazwy serwera logicznego [SQL i zarządzania](logical-servers.md) nimi.

Alias *DNS* może być używany w miejsce nazwy serwera. Programy klienckie mogą używać aliasu w swoich ciągach połączenia. Alias DNS zapewnia warstwę translacji, która może przekierowywać programy klienckie do różnych serwerów. Ta warstwa nie pozwala na trudności z wyszukiwaniem i edytowaniem wszystkich klientów i ich ciągów połączenia.

Typowe zastosowania aliasu DNS obejmują następujące przypadki:

- Utwórz łatwą do zapamiętania nazwę serwera.
- Podczas początkowego tworzenia alias może odwoływać się do serwera testowego. Gdy aplikacja stanie się żywa, możesz zmodyfikować alias, aby odwoływować się do serwera produkcyjnego. Przejście z testowania do produkcji nie wymaga żadnych modyfikacji konfiguracji kilku klientów, którzy łączą się z serwerem.
- Załóżmy, że jedyna baza danych w aplikacji została przeniesiona na inny serwer. Alias można zmodyfikować bez konieczności modyfikowania konfiguracji kilku klientów.
- Podczas regionalnej outage użyj przywracania geograficznego, aby odzyskać bazę danych na innym serwerze i w innym regionie. Możesz zmodyfikować istniejący alias, aby wskazać nowy serwer, aby istniejąca aplikacja klienca mogła ponownie nawiązać z nim połączenie.

## <a name="domain-name-system-dns-of-the-internet"></a>System nazw domen (DNS) Internetu

Internet opiera się na systemie DNS. System DNS tłumaczy przyjazne nazwy na nazwę serwera.

## <a name="scenarios-with-one-dns-alias"></a>Scenariusze z jednym aliasem DNS

Załóżmy, że musisz przełączyć system na nowy serwer. W przeszłości konieczne było znalezienie i zaktualizowanie wszystkich parametrów połączenia w każdym programie klienckim. Jednak teraz, jeśli parametry połączenia używają aliasu DNS, należy zaktualizować tylko właściwość aliasu.

Funkcja aliasu DNS usługi Azure SQL Database może pomóc w następujących scenariuszach:

### <a name="test-to-production"></a>Testowanie do produkcji

Po rozpoczęciu tworzenia programów klienckich należy użyć aliasu DNS w swoich ciągach połączenia. Właściwości aliasu wskazują testową wersję serwera.

Później, gdy nowy system przejdzie w życie w środowisku produkcyjnym, możesz zaktualizować właściwości aliasu, aby wskazać serwer produkcyjny. Nie jest konieczna żadna zmiana programów klienckich.

### <a name="cross-region-support"></a>Obsługa między regionami

Odzyskiwanie po awarii może spowodować przeniesienie serwera do innego regionu geograficznego. W przypadku systemu, który używa aliasu DNS, można uniknąć konieczności znalezienia i zaktualizowania wszystkich ciągów połączenia dla wszystkich klientów. Zamiast tego można zaktualizować alias, aby odwoływać się do nowego serwera, który teraz hostuje Azure SQL Database.

## <a name="properties-of-a-dns-alias"></a>Właściwości aliasu DNS

Następujące właściwości mają zastosowanie do każdego aliasu DNS dla serwera:

- *Unikatowa nazwa:* Każda nazwa aliasu, który tworzysz, jest unikatowa na wszystkich serwerach, podobnie jak nazwy serwerów.
- *Serwer jest wymagany:* Nie można utworzyć aliasu DNS, chyba że odwołuje się on dokładnie do jednego serwera i serwer musi już istnieć. Zaktualizowany alias musi zawsze odwoływać się do dokładnie jednego istniejącego serwera.
  - Po porzuceniu serwera system platformy Azure również porzuca wszystkie aliasy DNS odwołujące się do serwera.
- *Nie są powiązane z żadnym regionem:* Aliasy DNS nie są powiązane z regionem. Wszelkie aliasy DNS można zaktualizować, aby odwołyły się do serwera, który znajduje się w dowolnym regionie geograficznym.
  - Jednak podczas aktualizowania aliasu w celu odwołania się do innego serwera oba serwery muszą istnieć w tej samej subskrypcji *platformy* Azure.
- *Uprawnienia:* Aby zarządzać aliasem DNS, użytkownik musi mieć uprawnienia *Współautor* serwera lub wyższe. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z kontrolą](../../role-based-access-control/overview.md)dostępu opartą na rolach na platformie Azure w Azure Portal .

## <a name="manage-your-dns-aliases"></a>Zarządzanie aliasami DNS

Dostępne są zarówno interfejsy API REST, jak i polecenia cmdlet programu PowerShell umożliwiające programowe zarządzanie aliasami DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>Interfejsy API REST do zarządzania aliasami DNS

Dokumentacja interfejsów API REST jest dostępna w następującej lokalizacji internetowej:

- [Azure SQL Database REST API](/rest/api/sql/)

Ponadto interfejsy API REST są widoczne w usłudze GitHub w witrynie:

- [Azure SQL Database API REST aliasu DNS](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>Program PowerShell do zarządzania aliasami DNS

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager PowerShell jest nadal obsługiwany, ale cały przyszły rozwój jest dla modułu Az.Sql. Aby uzyskać informacje o tych poleceniach cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenty poleceń w module Az i modułach AzureRm są zasadniczo identyczne.

Dostępne są polecenia cmdlet programu PowerShell, które wywołują interfejsy API REST.

Przykład kodu poleceń cmdlet programu PowerShell używanych do zarządzania aliasami DNS jest udokumentowany w:

- [Program PowerShell dla aliasu DNS do Azure SQL Database](dns-alias-powershell-create.md)

Polecenia cmdlet użyte w przykładzie kodu są następujące:

- [New-AzSqlServerDnsAlias:](/powershell/module/az.Sql/New-azSqlServerDnsAlias)tworzy nowy alias DNS w Azure SQL Database usługi. Alias odwołuje się do serwera 1.
- [Get-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Get-azSqlServerDnsAlias)pobierz i wydziel wszystkie aliasy DNS przypisane do serwera 1.
- [Set-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Set-azSqlServerDnsAlias)modyfikuje nazwę serwera, do których odnosi się alias, z serwera 1 na serwer 2.
- [Remove-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)usuń alias DNS z serwera 2 przy użyciu nazwy aliasu.

## <a name="limitations"></a>Ograniczenia

Obecnie alias DNS ma następujące ograniczenia:

- *Opóźnienie do 2 minut:* Zaktualizowanie lub usunięcie aliasu DNS może potrwać do 2 minut.
  - Niezależnie od dowolnego krótkiego opóźnienia alias natychmiast zatrzymuje odwoływanie połączeń klienckich do starszego serwera.
- *Odnośnik DNS:* Na razie jedynym autorytatywnym sposobem sprawdzenia, do jakiego serwera odwołuje się dany alias DNS, jest wykonanie [wyszukiwania DNS](/windows-server/administration/windows-commands/nslookup).
- _Inspekcja tabel nie jest obsługiwana:_ Nie można użyć aliasu DNS na serwerze z *włączoną inspekcją tabel* w bazie danych.
  - Inspekcja tabel jest przestarzała.
  - Zalecamy przejście na [inspekcję obiektów blob.](../../azure-sql/database/auditing-overview.md)

## <a name="related-resources"></a>Powiązane zasoby

- [Omówienie ciągłości działalności biznesowej z Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md), w tym odzyskiwania po awarii.
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
- [Interfejs API aliasów DNS serwera](/rest/api/sql/2020-11-01-preview/serverdnsaliases)

## <a name="next-steps"></a>Następne kroki

- [Program PowerShell dla aliasu DNS do Azure SQL Database](dns-alias-powershell-create.md)
