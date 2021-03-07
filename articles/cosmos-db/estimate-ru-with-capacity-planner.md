---
title: Oszacuj koszty przy użyciu Azure Cosmos DB planista pojemności
description: Planista wydajności Azure Cosmos DB pozwala oszacować przepływność (RU/s) wymagany i koszt dla obciążenia. W tym artykule opisano, jak używać nowej wersji planisty wydajności do oszacowania przepływności i wymaganego kosztu.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: 829fb28577fbb89c031a3b972d3ee5e04c935e9f
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435005"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Szacowanie RU/s przy użyciu Azure Cosmos DB planista pojemności
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Konfigurowanie baz danych i kontenerów platformy Azure Cosmos z właściwą ilością przepływności lub [jednostek żądań (ru/s)](request-units.md)dla obciążenia jest niezbędne do optymalizacji kosztów i wydajności. W tym artykule opisano, jak używać [planisty wydajności](https://cosmos.azure.com/capacitycalculator/) Azure Cosmos DB, aby uzyskać szacunkowe wymagania ru/s i koszty związane z obciążeniem. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Jak oszacować przepływność i koszty dzięki Azure Cosmos DB planisty wydajności

Planista pojemności może być używana w dwóch trybach.

|**Tryb**  |**Opis**  |
|---------|---------|
|Podstawowa|Zapewnia szybkie, wysokiego poziomu RU/s i oszacowanie kosztów. W tym trybie założono domyślne ustawienia Azure Cosmos DB dla zasad indeksowania, spójności i innych parametrów. <br/><br/>Użyj trybu podstawowego, aby szybko oszacować wysoki poziom oszacowania podczas oceniania potencjalnych obciążeń do uruchomienia na Azure Cosmos DB.|
|Zaawansowany|Zapewnia bardziej szczegółowy zakres RU/s i oszacowanie kosztów, z możliwością dostrajania dodatkowych ustawień — zasad indeksowania, poziomu spójności i innych parametrów, które wpływają na koszt i przepływność. <br/><br/>Użyj trybu zaawansowanego, gdy szacujesz RU/s dla nowego projektu lub chcesz bardziej szczegółowo oszacować. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Szacowanie alokowanej przepływności i kosztów przy użyciu trybu podstawowego
Aby szybko oszacować obciążenie dla obciążenia przy użyciu trybu Basic, przejdź do [planisty wydajności](https://cosmos.azure.com/capacitycalculator/). Wprowadź następujące parametry w zależności od obciążenia: 

|**Dane wejściowe**  |**Opis**  |
|---------|---------|
|Liczba regionów|Azure Cosmos DB jest dostępna we wszystkich regionach świadczenia usługi Azure. Wybierz liczbę regionów wymaganych do obciążania. Możesz skojarzyć dowolną liczbę regionów z kontem Cosmos. Aby uzyskać więcej informacji, zobacz [globalna dystrybucja](distribute-data-globally.md) w Azure Cosmos DB.|
|Zapisy w wielu regionach|W przypadku włączenia [zapisu wieloregionowego](distribute-data-globally.md#key-benefits-of-global-distribution)aplikacja będzie mogła odczytywać i zapisywać dane w dowolnym regionie świadczenia usługi Azure. Jeśli wyłączysz zapis wieloregionowy, aplikacja będzie mogła zapisywać dane w jednym regionie. <br/><br/> Włącz zapisywanie w wielu regionach, Jeśli spodziewasz się, że istnieje aktywne-aktywne obciążenie, które wymaga zapisów o małych opóźnieniach w różnych regionach. Na przykład obciążenie IOT, które zapisuje dane w bazie danych w dużych ilościach w różnych regionach. <br/><br/> Zapisy wieloregionowe gwarantują dostępność na 99,999% odczytu i zapisu. Zapisy w wielu regionach wymagają większej przepływności w porównaniu z pojedynczymi regionami zapisu. Aby dowiedzieć się więcej, zobacz [jak jednostek ru różnią się w artykule dotyczącym jednego i wielu regionów](optimize-cost-regions.md) .|
|Łączna ilość przechowywanych danych (na region)|Łączna Szacowana ilość danych przechowywanych w GB w jednym regionie.|
|Rozmiar elementu|Szacowany rozmiar elementu danych (np. dokumentu), od 1 KB do 2 MB. |
|Odczyty/s na region|Oczekiwana liczba odczytów na sekundę. |
|Zapisy/s na region|Oczekiwana Liczba zapisów na sekundę. |

Po wypełnieniu wymaganych szczegółów wybierz pozycję **Oblicz**. Na karcie **szacowanie kosztów** jest wyświetlany łączny koszt magazynu i przepływność aprowizacji. Można rozwinąć link **Pokaż szczegóły** na tej karcie, aby uzyskać informacje o przepływności wymaganej do odczytu i zapisu żądania. Za każdym razem, gdy zmieniasz wartość dowolnego pola, wybierz pozycję **Oblicz** , aby ponownie obliczyć szacowany koszt. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="Planista wydajności — Tryb podstawowy":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Szacowanie alokowanej przepływności i kosztów przy użyciu trybu zaawansowanego

Tryb zaawansowany umożliwia podanie większej liczby ustawień, które mają wpływ na oszacowanie RU/s. Aby użyć tej opcji, przejdź do [planisty wydajności](https://cosmos.azure.com/capacitycalculator/) i zaloguj się do narzędzia przy użyciu konta używanego na platformie Azure. Opcja logowania jest dostępna w prawym górnym rogu. 

Po zalogowaniu można zobaczyć dodatkowe pola w porównaniu do pól w trybie podstawowym. Wprowadź dodatkowe parametry w zależności od obciążenia. 

|**Dane wejściowe**  |**Opis**  |
|---------|---------|
|Interfejs API|Azure Cosmos DB to usługa wielomodelowa i wiele interfejsów API. W przypadku nowych obciążeń wybierz opcję interfejs API języka SQL (rdzeń). |
|Liczba regionów|Azure Cosmos DB jest dostępna we wszystkich regionach świadczenia usługi Azure. Wybierz liczbę regionów wymaganych do obciążania. Możesz skojarzyć dowolną liczbę regionów z kontem Cosmos. Aby uzyskać więcej informacji, zobacz [globalna dystrybucja](distribute-data-globally.md) w Azure Cosmos DB.|
|Zapisy w wielu regionach|W przypadku włączenia [zapisu wieloregionowego](distribute-data-globally.md#key-benefits-of-global-distribution)aplikacja będzie mogła odczytywać i zapisywać dane w dowolnym regionie świadczenia usługi Azure. Jeśli wyłączysz zapis wieloregionowy, aplikacja będzie mogła zapisywać dane w jednym regionie. <br/><br/> Włącz zapisywanie w wielu regionach, Jeśli spodziewasz się, że istnieje aktywne-aktywne obciążenie, które wymaga zapisów o małych opóźnieniach w różnych regionach. Na przykład obciążenie IOT, które zapisuje dane w bazie danych w dużych ilościach w różnych regionach. <br/><br/> Zapisy wieloregionowe gwarantują dostępność na 99,999% odczytu i zapisu. Zapisy w wielu regionach wymagają większej przepływności w porównaniu z pojedynczymi regionami zapisu. Aby dowiedzieć się więcej, zobacz [jak jednostek ru różnią się w artykule dotyczącym jednego i wielu regionów](optimize-cost-regions.md) .|
|Domyślna spójność|Azure Cosmos DB obsługuje 5 poziomów spójności, dzięki czemu deweloperzy mogą zrównoważyć kompromis między kompromisami spójności, dostępności i opóźnień. Aby dowiedzieć się więcej, zobacz artykuł [poziomy spójności](consistency-levels.md) . <br/><br/> Domyślnie Azure Cosmos DB używa spójności sesji, co gwarantuje możliwość odczytywania własnych zapisów w sesji. <br/><br/> Wybranie silnych lub ograniczonych niezgodności będzie wymagało podwójnego wymagania RU/s do odczytu w porównaniu do sesji, spójnego prefiksu i spójności ostatecznej. Silna spójność przy użyciu zapisu wieloregionowego nie jest obsługiwana i automatycznie będzie domyślnie zapisywać dane jednoregionowe z silną spójnością. |
|Zasady indeksowania|Domyślnie Azure Cosmos DB [indeksuje wszystkie właściwości](index-policy.md) we wszystkich elementach dla elastycznych i wydajnych zapytań (mapuje do **automatycznych** zasad indeksowania). <br/><br/> W przypadku wybrania opcji **wyłączone** żadna z właściwości nie jest indeksowana. Powoduje to najniższą opłatę za usługę RU dla operacji zapisu. Wybierz opcję **Wyłącz** zasady, jeśli oczekujesz tylko na [odczyty punktów](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) (wyszukiwania wartości klucza) i/lub zapis, a nie zapytania. <br/><br/> Niestandardowe zasady indeksowania umożliwiają uwzględnienie lub wykluczenie określonych właściwości z indeksu dla mniejszej przepustowości zapisu i magazynu. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [zasad indeksowania](index-overview.md) i [przykładowych zasad indeksowania](how-to-manage-indexing-policy.md#indexing-policy-examples) .|
|Łączna ilość przechowywanych danych (na region)|Łączna Szacowana ilość danych przechowywanych w GB w jednym regionie.|
|Tryb obciążenia|Wybierz pozycję **stałe** , jeśli ilość obciążenia jest stała. <br/><br/> Wybierz pozycję **zmienna** , jeśli rozmiar obciążenia zmienia się w miarę upływu czasu.  Na przykład w ciągu określonego dnia lub miesiąca. <br/><br/> Następujące ustawienia są dostępne w przypadku wybrania opcji zmienne obciążenie:<ul><li>Procent czasu o wartości szczytowej: procent czasu w miesiącu, w którym obciążenie wymaga szczytowej (najwyższej) przepływności. <br/><br/> Na przykład jeśli masz obciążenie, które ma wysoką aktywność w czasie pracy w 9:00 – 18:00 dzień tygodnia, wówczas procent czasu w szczycie wynosi: 45 godzin w godzinach szczytu/730 godz./miesiąc = ~ 6%.<br/><br/></li><li>Liczba odczytów/s na region w szczycie w wartości szczytowej jest oczekiwana na sekundę.</li><li>Liczba zapisów/s na region w szczycie — oczekiwano liczby zapisów na sekundę.</li><li>Odczyty/s na region poza szczytem — liczba operacji odczytu oczekiwanych na sekundę poza szczytem.</li><li>Liczba zapisów/s na region poza szczytem — oczekiwano liczby zapisów na sekundę w czasie szczytu.</li></ul>W przypadku interwału szczytowego i przestoju można zoptymalizować koszt przez programowe [skalowanie przepływności](set-throughput.md#update-throughput-on-a-database-or-a-container) w górę i w dół.|
|Rozmiar elementu|Rozmiar elementu danych (np. dokumentu), od 1 KB do 2 MB. <br/><br/>Możesz również **przekazać dokument przykładowy (JSON)** , aby uzyskać dokładniejszy szacunek.<br/><br/>Jeśli obciążenie zawiera wiele typów elementów (z inną zawartością JSON) w tym samym kontenerze, można przekazać wiele dokumentów JSON i uzyskać oszacowanie. Aby dodać wiele przykładowych dokumentów JSON, użyj przycisku **Dodaj nowy element** .|

Możesz również użyć przycisku **Zapisz oszacowanie** , aby pobrać plik CSV zawierający bieżące oszacowanie. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="Zaawansowany tryb planowania wydajności":::

Ceny przedstawione w terminarzu wydajności Azure Cosmos DB są oszacowaniami opartymi na publicznych stawkach cenowych dotyczących przepływności i magazynu. Wszystkie ceny są podane w dolarach amerykańskich. Zapoznaj się ze [stroną cennika Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) , aby wyświetlić wszystkie stawki według regionów.  

## <a name="estimating-throughput-for-queries"></a>Szacowanie przepływności dla zapytań

Kalkulator pojemności usługi Azure Cosmos zakłada odczyty punktów (odczyt pojedynczego elementu, np. dokument, według identyfikatora i wartość klucza partycji) i zapisuje dla obciążenia. Aby oszacować przepustowość potrzebną dla zapytań, należy uruchomić zapytanie na reprezentatywnym zestawie danych w kontenerze Cosmos i [uzyskać opłatę ru](find-request-unit-charge.md). Pomnóż opłatę RU przez liczbę zapytań, które zamierzasz uruchomić na sekundę w celu uzyskania łącznej liczby wymaganych jednostek RU/s. 

Na przykład, jeśli obciążenie wymaga zapytania, ``SELECT * FROM c WHERE c.id = 'Alice'`` który jest uruchamiany 100 razy na sekundę, a opłata za obiekt ru jest równa 10 jednostek ru, wymagana jest wartość 100 zapytania/s * 10 ru/Query = 1000 ru/s łącznie, aby obsłużyć te żądania. Należy dodać te RU/s do jednostek RU/s wymaganych do dowolnych operacji odczytu lub zapisu w obciążeniu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [modelu cen Azure Cosmos DB](how-pricing-works.md).
* Utwórz nowe [konto Cosmos, bazę danych i kontener](create-cosmosdb-resources-portal.md).
* Dowiedz się, jak [zoptymalizować koszt przepływności z obsługą administracyjną](optimize-cost-throughput.md).
* Dowiedz się, jak [zoptymalizować koszty dzięki zarezerwowanej pojemności](cosmos-db-reserved-capacity.md).