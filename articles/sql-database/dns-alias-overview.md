---
title: Alias DNS
description: Aplikacje mogą łączyć się z aliasem dla nazwy serwera Azure SQL Database. Tymczasem można zmienić SQL Database aliasu wskazuje w dowolnym momencie, aby ułatwić testowanie i tak dalej.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 05fa542a0ad1c72f73148eefd304a9771798598d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73820615"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS dla Azure SQL Database

Azure SQL Database ma serwer systemu nazw domen (DNS). Program PowerShell i interfejsy API REST akceptują [wywołania do tworzenia aliasów DNS i zarządzania nimi](#anchor-powershell-code-62x) dla nazwy serwera SQL Database.

*Alias DNS* może zostać użyty zamiast nazwy serwera Azure SQL Database. Programy klienckie mogą używać aliasu w parametrach połączenia. Alias DNS zawiera warstwę translacji, która umożliwia przekierowywanie programów klienckich do różnych serwerów. Ta warstwa stanowi zapasowe problemy związane z znajdowaniem i edycją wszystkich klientów i ich parametrów połączenia.

Typowym zastosowaniem aliasu DNS są następujące sytuacje:

- Utwórz łatwą do zapamiętania nazwę dla SQL Server platformy Azure.
- Podczas początkowego opracowywania alias może odwoływać się do serwera testowego SQL Database. Gdy aplikacja jest aktywna, można zmodyfikować alias, aby odwołać się do serwera produkcyjnego. Przejście z testu do produkcji nie wymaga żadnych modyfikacji konfiguracji kilku klientów łączących się z serwerem bazy danych.
- Załóżmy, że jedyna baza danych w aplikacji jest przenoszona na inny serwer SQL Database. W tym miejscu możesz zmodyfikować alias bez konieczności modyfikowania konfiguracji kilku klientów.
- W przypadku awarii regionalnej, należy użyć przywracania geograficznego do odzyskania bazy danych na innym serwerze i regionie. Istniejący alias można zmodyfikować tak, aby wskazywał nowy serwer, aby można było ponownie połączyć się z istniejącą aplikacją kliencką. 

## <a name="domain-name-system-dns-of-the-internet"></a>System nazw domen (DNS) Internetu

Internet korzysta z systemu DNS. DNS tłumaczy przyjazne nazwy na nazwę serwera Azure SQL Database.

## <a name="scenarios-with-one-dns-alias"></a>Scenariusze z jednym aliasem DNS

Załóżmy, że musisz przełączyć system na nowy serwer Azure SQL Database. W przeszłości należy znaleźć i zaktualizować wszystkie parametry połączenia w każdym programie klienckim. Ale jeśli teraz parametry połączenia używają aliasu DNS, należy zaktualizować tylko Właściwość alias.

Funkcja aliasu DNS Azure SQL Database może pomóc w następujących scenariuszach:

### <a name="test-to-production"></a>Testuj do środowiska produkcyjnego

Po rozpoczęciu opracowywania programów klienckich należy używać w ich parametrach połączenia aliasu DNS. Właściwości aliasu można wprowadzać do wersji testowej serwera Azure SQL Database.

Później, gdy nowy system działa w środowisku produkcyjnym, można zaktualizować właściwości aliasu, aby wskazywał na serwer produkcyjny SQL Database. Nie trzeba zmieniać programów klienckich.

### <a name="cross-region-support"></a>Obsługa wielu regionów

Odzyskiwanie po awarii może przetworzyć serwer SQL Database w innym regionie geograficznym. W przypadku systemu, który korzysta z aliasu DNS, można uniknąć konieczności znajdowania i aktualizowania wszystkich parametrów połączenia dla wszystkich klientów. Zamiast tego można zaktualizować alias, aby odwołać się do nowego serwera SQL Database, który teraz hostuje bazę danych.

## <a name="properties-of-a-dns-alias"></a>Właściwości aliasu DNS

Następujące właściwości mają zastosowanie do każdego aliasu DNS serwera SQL Database:

- *Unikatowa nazwa:* Każda utworzona Nazwa aliasu jest unikatowa na wszystkich serwerach Azure SQL Database, podobnie jak nazwy serwerów.
- *Serwer jest wymagany:* Nie można utworzyć aliasu DNS, chyba że odwołuje się on dokładnie do jednego serwera, a serwer musi już istnieć. Zaktualizowany alias musi zawsze odwoływać się do dokładnie jednego istniejącego serwera.
  - Gdy porzucasz serwer SQL Database, system Azure porzuca także wszystkie aliasy DNS odwołujące się do serwera.
- *Niepowiązane z żadnym regionem:* Aliasy DNS nie są powiązane z regionem. Wszelkie aliasy DNS można zaktualizować, aby odwoływać się do serwera Azure SQL Database, który znajduje się w dowolnym regionie geograficznym.
  - Jednak podczas aktualizowania aliasu w celu odwoływania się do innego serwera, oba serwery muszą istnieć w tej samej *subskrypcji*platformy Azure.
- *Uprawnienia:* Aby zarządzać aliasem DNS, użytkownik musi mieć uprawnienia *współautora serwera* lub wyższy. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z Access Controlami opartymi na rolach w Azure Portal](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Zarządzanie aliasami DNS

Dostępne są zarówno interfejsy API REST, jak i polecenia cmdlet programu PowerShell umożliwiające programowe Zarządzanie aliasami DNS.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>Interfejsy API REST do zarządzania aliasami DNS

Dokumentacja interfejsów API REST jest dostępna w sąsiedztwie następującej lokalizacji sieci Web:

- [Interfejs API REST Azure SQL Database](https://docs.microsoft.com/rest/api/sql/)

Ponadto interfejsy API REST można zobaczyć w witrynie GitHub w:

- [Serwer Azure SQL Database, interfejsy API REST aliasu DNS](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>Program PowerShell do zarządzania aliasami DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Polecenia cmdlet programu PowerShell są dostępne, które wywołują interfejsy API REST.

Przykład kodu poleceń cmdlet programu PowerShell służących do zarządzania aliasami DNS jest udokumentowany w:

- [Program PowerShell dla aliasu DNS do Azure SQL Database](dns-alias-powershell.md)

Polecenia cmdlet używane w przykładzie kodu są następujące:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): tworzy nowy alias DNS w systemie usługi Azure SQL Database. Alias odnosi się do Azure SQL Database Server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Pobierz i Wyświetl listę wszystkich aliasów DNS przypisanych do serwera SQL dB 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): modyfikuje nazwę serwera, do którego odwołuje się alias, z serwera 1 do SQL DB Server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Usuń alias DNS z serwera SQL DB 2 przy użyciu nazwy aliasu.

## <a name="limitations-during-preview"></a>Ograniczenia w wersji zapoznawczej

Obecnie alias DNS ma następujące ograniczenia:

- *Opóźnienie do 2 minut:* Zaktualizowanie lub usunięcie aliasu DNS może potrwać do 2 minut.
  - Bez względu na krótkie opóźnienie, alias natychmiast przerywa odwołania połączeń klienta do starszego serwera.
- *Wyszukiwanie DNS:* Na razie jedynym autorytatywnym sposobem sprawdzenia, do jakiego serwera odnosi się dany alias DNS, jest wykonanie [wyszukiwania DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- _Inspekcja tabeli nie jest obsługiwana:_ Nie można użyć aliasu DNS na serwerze Azure SQL Database, na którym włączono *inspekcję tabeli* w bazie danych.
  - Inspekcja tabeli jest przestarzała.
  - Zalecamy przechodzenie do [inspekcji obiektów BLOB](sql-database-auditing.md).

## <a name="related-resources"></a>Powiązane zasoby

- [Przegląd ciągłości działania z Azure SQL Database](sql-database-business-continuity.md), w tym odzyskiwania po awarii.

## <a name="next-steps"></a>Następne kroki

- [Program PowerShell dla aliasu DNS do Azure SQL Database](dns-alias-powershell.md)
