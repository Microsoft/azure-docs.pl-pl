---
title: Administrowanie usługą w portalu
titleSuffix: Azure Cognitive Search
description: Zarządzanie usługą Azure Wyszukiwanie poznawcze, hostowaną usługą wyszukiwania w chmurze na Microsoft Azure przy użyciu Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 98e0137c8e48696737cd5d8d1fd4d3de925b9f7f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579792"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Administracja usługi dla platformy Azure Wyszukiwanie poznawcze w Azure Portal

> [!div class="op_single_selector"]
>
> * [Program PowerShell](search-manage-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](search-manage-azure-cli.md)
> * [Interfejs API REST](/rest/api/searchmanagement/)
> * [Zestaw SDK platformy .NET](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Wyszukiwanie poznawcze to w pełni zarządzana usługa wyszukiwania oparta na chmurze służąca do tworzenia bogatego środowiska wyszukiwania w aplikacjach niestandardowych. W tym artykule opisano zadania administracyjne, które można wykonać w [Azure Portal](https://portal.azure.com) dla usługi wyszukiwania, która została już utworzona. Portal umożliwia wykonywanie wszystkich [zadań zarządzania](#management-tasks) związanych z usługą, a także zarządzanie zawartością i eksplorację. W związku z tym Portal zapewnia szeroki dostęp do wszystkich aspektów operacji usługi wyszukiwania.

Każda usługa wyszukiwania jest zarządzana jako zasób autonomiczny. Na poniższej ilustracji przedstawiono strony portalu dla pojedynczej bezpłatnej usługi wyszukiwania o nazwie "Demonstracja-Search-svc". Mimo że można korzystać z usługi Azure PowerShell lub interfejsu wiersza polecenia platformy Azure do zarządzania usługami, warto zapoznać się z narzędziami i funkcjami udostępnianymi przez strony portalu. Niektóre zadania są znacznie łatwiejsze i szybsze do wykonania w portalu niż za pomocą kodu. 

## <a name="overview-home-page"></a>Strona przeglądu (Strona główna)

Strona przegląd jest stroną "Strona główna" każdej usługi. Poniżej znajdują się obszary na ekranie ujęte w czerwone pola wskazują zadania, narzędzia i kafelki, które mogą być często używane, zwłaszcza jeśli dopiero zaczynasz korzystać z usługi.

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="Strony portalu dla usługi wyszukiwania" border="true":::

| Obszar | Opis |
|------|-------------|
| 1  | Sekcja **podstawowe** informacje zawiera właściwości usługi, w tym punkt końcowy używany podczas konfigurowania połączeń. Pokazuje także, jak również warstwy, repliki i liczby partycji. |
| 2 | W górnej części strony znajdują się serie poleceń służących do wywoływania interaktywnych narzędzi lub zarządzania usługą. Zarówno [Importowanie danych](search-get-started-portal.md) , jak i [Eksplorator wyszukiwania](search-explorer.md) są często używane do tworzenia prototypów i eksploracji. |
| 3 | Poniżej sekcji **podstawowe** jest seria podstron z kartami umożliwiająca szybki dostęp do statystyk użycia, metryki kondycji usług oraz dostęp do wszystkich istniejących indeksów, indeksatorów, źródeł danych i umiejętności w usłudze. W przypadku wybrania indeksu lub innego obiektu stają się dostępne dodatkowe strony umożliwiające wyświetlenie kompozycji, ustawień i stanu obiektu (jeśli dotyczy). |
| 4 | Po lewej stronie możesz uzyskać dostęp do linków umożliwiających otwieranie dodatkowych stron na potrzeby uzyskiwania dostępu do kluczy interfejsu API używanych w połączeniach, konfigurowania usługi, konfigurowania zabezpieczeń, operacji monitorowania, automatyzowania zadań i uzyskiwania pomocy technicznej. |

### <a name="read-only-service-properties"></a>Właściwości usługi tylko do odczytu

Niektóre aspekty usługi wyszukiwania są określane podczas aprowizacji usługi i nie można jej zmienić:

* Nazwa usługi (nie można zmienić nazwy usługi wyszukiwania)
* Lokalizacja usługi (nie można łatwo przenieść nienaruszonej usługi wyszukiwania do innego regionu. Chociaż istnieje szablon, przeniesienie zawartości jest procesem ręcznym)
* Warstwa usług (nie można przełączyć z S1 na S2, na przykład bez tworzenia nowej usługi)

## <a name="management-tasks"></a>Zadania zarządzania

Administracja usługi jest lekkim projektem i często jest definiowana przez zadania, które można wykonywać względem samej usługi:

* [Dostosuj pojemność](search-capacity-planning.md) przez dodanie lub usunięcie replik i partycji
* [Zarządzanie kluczami interfejsu API](search-security-api-keys.md) używanymi na potrzeby operacji administratorów i zapytań
* [Kontrola dostępu do operacji administracyjnych](search-security-rbac.md) za pośrednictwem zabezpieczeń opartych na rolach
* [Konfigurowanie reguł zapory adresów IP](service-configure-firewall.md) w celu ograniczenia dostępu przy użyciu adresu IP
* [Skonfiguruj prywatny punkt końcowy](service-create-private-endpoint.md) przy użyciu prywatnego linku platformy Azure i prywatnej sieci wirtualnej
* [Monitorowanie kondycji usługi](search-monitor-usage.md): Magazyn, woluminy zapytań i czas oczekiwania

Można również wyliczyć wszystkie obiekty utworzone w usłudze: indeksy, indeksatory, źródła danych, umiejętności i tak dalej. Na stronie Przegląd w portalu jest wyświetlana cała zawartość, która istnieje w usłudze. Większość operacji w usłudze wyszukiwania jest związanych z zawartością.

Te same zadania zarządzania wykonywane w portalu mogą być również obsługiwane programowo za pomocą [interfejsów API REST zarządzania](/rest/api/searchmanagement/), [AZ. Search module PowerShell](search-manage-powershell.md), [AZ Search module interfejsu wiersza polecenia platformy Azure](search-manage-azure-cli.md)i zestawów Azure SDK dla platformy .NET, Python, Java i JavaScript. Zadania administracyjne są w pełni reprezentowane przez portal i wszystkie interfejsy programistyczne. Nie ma żadnych określonych zadań administracyjnych, które są dostępne tylko w jednym elemencie modalnym.

Wyszukiwanie poznawcze wykorzystuje inne usługi platformy Azure w celu lepszego monitorowania i zarządzania. Same dane przechowywane w usłudze wyszukiwania to zawartość obiektów (indeksy, indeksator i definicje źródeł danych oraz inne obiekty). Metryki zgłoszone do stron portalu są pobierane z dzienników wewnętrznych w ramach 30-dniowego cyklu. W przypadku przechowywania dzienników sterowanych przez użytkownika i zdarzeń dodatkowych konieczne będzie [Azure monitor](../azure-monitor/index.yml). Aby uzyskać więcej informacji o konfigurowaniu rejestrowania diagnostycznego dla usługi wyszukiwania, zobacz [zbieranie i analizowanie danych dziennika](search-monitor-logs.md).

## <a name="administrator-permissions"></a>Uprawnienia administratora

Po otwarciu strony Przegląd usługi wyszukiwania uprawnienia przypisane do konta określają, które strony są dostępne dla użytkownika. Na stronie Przegląd na początku artykułu są wyświetlane strony portalu, które będą widoczne dla administratora lub współautora.

W obszarze zasób platformy Azure prawa administracyjne są udzielane za pomocą przypisań ról. W kontekście usługi Azure Wyszukiwanie poznawcze [przypisania ról](search-security-rbac.md) określają, kto może przydzielić repliki i partycje lub zarządzać kluczami interfejsu API, niezależnie od tego, czy korzystają z portalu, [programu PowerShell](search-manage-powershell.md), [interfejsu wiersza polecenia platformy Azure](search-manage-azure-cli.md), czy też [interfejsów API REST zarządzania](/rest/api/searchmanagement/search-howto-management-rest-api):

> [!TIP]
> Inicjowanie obsługi administracyjnej lub likwidowanie samej usługi może odbywać się przez administratora subskrypcji platformy Azure lub współadministratora. Korzystając z mechanizmów opartych na platformie Azure, można zablokować subskrypcję lub zasób, aby zapobiec przypadkowemu lub nieautoryzowanemu usunięciu usługi wyszukiwania przez użytkowników z uprawnieniami administratora. Aby uzyskać więcej informacji, zobacz [Zablokuj zasoby, aby zapobiec nieoczekiwanemu usunięciu](../azure-resource-manager/management/lock-resources.md).

## <a name="next-steps"></a>Następne kroki

* Przegląd [możliwości monitorowania](search-monitor-usage.md) dostępnych w portalu
* Automatyzowanie przy użyciu [programu PowerShell](search-manage-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](search-manage-azure-cli.md)
* Przejrzyj [funkcje zabezpieczeń](search-security-overview.md) , aby chronić zawartość i operacje
* Włączanie [rejestrowania diagnostycznego](search-monitor-logs.md) w celu monitorowania obciążeń zapytań i indeksowania