---
title: Uaktualnianie do Azure Search .NET Management SDK
titleSuffix: Azure Cognitive Search
description: Uaktualnij do Azure Search .NET Management SDK z poprzednich wersji. Dowiedz się więcej o nowych funkcjach i zmianach w kodzie koniecznych do migracji.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 8648347eb48081389cf360fa949b31bbd0b8c71e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936711"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Uaktualnianie wersji Azure Search .NET Management SDK

W tym artykule wyjaśniono, jak przeprowadzić migrację do kolejnych wersji Azure Search .NET Management SDK, służących do aprowizacji lub anulowania aprowizacji usług wyszukiwania, dostosowywania pojemności i zarządzania kluczami interfejsu API.

Zestawy SDK zarządzania są przeznaczone dla określonej wersji interfejsu API REST zarządzania. Aby uzyskać więcej informacji na temat pojęć i operacji, zobacz [zarządzanie wyszukiwaniem (REST)](/rest/api/searchmanagement/).

## <a name="versions"></a>Wersje

| Wersja zestawu SDK | Odpowiadająca wersja interfejsu API REST | Dodawanie funkcji lub zmiana zachowania |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | API-Version = 2020-30-20 | Dodaje zabezpieczenia punktu końcowego (zapory IP i integrację z [prywatnym łączem platformy Azure](../private-link/private-endpoint-overview.md)) |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | API-Version = 2019-10-01 | Usprawnienia w zakresie użyteczności. Nieprzerwana zmiana [kluczy zapytania listy](/rest/api/searchmanagement/querykeys/listbysearchservice) (Get jest wycofana). |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | API-Version = 2015-08-19  | Pierwsza wersja |

## <a name="how-to-upgrade"></a>Jak przeprowadzić uaktualnianie

1. Zaktualizuj odwołanie do programu NuGet za `Microsoft.Azure.Management.Search` pomocą konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając pozycję "Zarządzaj pakietami NuGet..." w programie Visual Studio.

1. Po pobraniu przez program NuGet nowych pakietów i ich zależności, należy ponownie skompilować projekt. W zależności od tego, jak kod jest strukturalny, może zostać pomyślnie ponownie odbudowany, w tym przypadku.

1. Jeśli kompilacja nie powiedzie się, może to oznaczać, że wdrożono niektóre interfejsy zestawu SDK (na przykład na potrzeby testowania jednostek), które uległy zmianie. Aby rozwiązać ten problem, należy zaimplementować nowsze metody, takie jak `BeginCreateOrUpdateWithHttpMessagesAsync` .

1. Po naprawieniu błędów kompilacji można wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji. 

## <a name="upgrade-to-30"></a>Uaktualnij do 3,0

Wersja 3,0 dodaje prywatną ochronę punktu końcowego przez ograniczenie dostępu do zakresów adresów IP i opcjonalnie integrację z usługą Azure Private link dla usług wyszukiwania, które nie powinny być widoczne w publicznym Internecie.

### <a name="new-apis"></a>Nowe interfejsy API

| Interfejs API | Kategoria| Szczegóły |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/services/createorupdate#networkruleset) | Zapora IP | Ogranicz dostęp do punktu końcowego usługi do listy dozwolonych adresów IP. Zobacz [Konfigurowanie zapory IP](service-configure-firewall.md) dla koncepcji i instrukcje dotyczące portalu. |
| [Zasób udostępnionego linku prywatnego](/rest/api/searchmanagement/sharedprivatelinkresources) | Private Link | Utwórz współużytkowany zasób linku prywatnego, który będzie używany przez usługę wyszukiwania.  |
| [Połączenia prywatnego punktu końcowego](/rest/api/searchmanagement/privateendpointconnections) | Private Link | Ustanów połączenia z usługą wyszukiwania i zarządzaj nimi za pomocą prywatnego punktu końcowego. Zobacz [Tworzenie prywatnego punktu końcowego](service-create-private-endpoint.md) dla pojęć i instrukcji dotyczących portalu.|
| [Prywatne zasoby linku](/rest/api/searchmanagement/privatelinkresources/) | Private Link | W przypadku usługi wyszukiwania z połączeniem prywatnego punktu końcowego Uzyskaj listę wszystkich usług używanych w tej samej sieci wirtualnej. Jeśli rozwiązanie wyszukiwania obejmuje indeksatory ściągające ze źródeł danych platformy Azure (Azure Storage, Cosmos DB, Azure SQL) lub używają Cognitive Services lub Key Vault, wszystkie te zasoby powinny mieć punkty końcowe w sieci wirtualnej, a ten interfejs API powinien zwrócić listę. |
| [PublicNetworkAccess](/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Private Link | Jest to właściwość podczas tworzenia lub aktualizacji żądań obsługi. Po wyłączeniu link prywatny jest jedynym modalnym dostępem. |

### <a name="breaking-changes"></a>Fundamentalne zmiany

Nie można już używać funkcji GET na [liście kluczy zapytań](/rest/api/searchmanagement/querykeys/listbysearchservice) . W poprzednich wersjach można było użyć opcji GET lub POST, w tej wersji i we wszystkich wersjach przenoszonych do przodu, obsługiwane są tylko pozycje POST. 

## <a name="upgrade-to-20"></a>Uaktualnij do 2,0

Wersja 2 programu Azure Search .NET Management SDK jest drobnym uaktualnieniem, dlatego zmiana kodu powinna wymagać tylko minimalnego nakładu pracy. Zmiany w zestawie SDK są ścisłymi zmianami po stronie klienta, aby poprawić użyteczność samego zestawu SDK. Są to m.in. następujące zmiany:

* `Services.CreateOrUpdate` i jego wersje asynchroniczne teraz automatycznie sonduje Inicjowanie obsługi `SearchService` i nie zwracają do momentu ukończenia aprowizacji usługi. Dzięki temu możesz samodzielnie napisać ten kod sondowania.

* Jeśli nadal chcesz przeprowadzić sondowanie aprowizacji usługi ręcznie, możesz użyć nowej `Services.BeginCreateOrUpdate` metody lub jednej z jej wersji asynchronicznych.

* `Services.Update`Do zestawu SDK dodano nowe metody i jego wersje asynchroniczne. Metody te używają poprawki HTTP do obsługi przyrostowej aktualizacji usługi. Można na przykład skalować usługę przez przekazanie `SearchService` wystąpienia do tych metod, które zawierają tylko żądane `partitionCount` i `replicaCount` właściwości. Stary sposób wywoływania `Services.Get` , modyfikowania zwracanych `SearchService` i przekazywania go do `Services.CreateOrUpdate` jest nadal obsługiwany, ale nie jest już potrzebny. 

## <a name="next-steps"></a>Następne kroki

Jeśli wystąpią problemy, najlepszym forum do ogłaszania pytań jest [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub zestawu SDK platformy Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest oznaczony przy użyciu "[Search]".