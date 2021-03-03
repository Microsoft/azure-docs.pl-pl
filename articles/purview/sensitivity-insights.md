---
title: Etykieta czułości raportowanie danych na platformie Azure kontrolą za pomocą usługi kontrolą Insights
description: W tym przewodniku opisano sposób wyświetlania i używania funkcji raportowania etykiet czułości kontrolą na danych.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: bb8ac82b2e59ec86db89c7eba0ce607fcfc0ac2d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676569"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Informacje o czułości dotyczące danych w usłudze Azure kontrolą

W tym przewodniku opisano sposób uzyskiwania dostępu, wyświetlania i filtrowania szczegółowych informacji o zabezpieczeniach zapewnianych przez etykiety czułości stosowane do danych.

Obsługiwane źródła danych obejmują: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, wystąpienie zarządzane usługi Azure SQL, pakiet Amazon S3

W tym przewodniku krok po kroku dowiesz się, jak:

> [!div class="checklist"]
> - Uruchom konto kontrolą na platformie Azure.
> - Wyświetlanie informacji o czułości etykiet dotyczących danych
> - Przechodzenie do szczegółów w celu uzyskania większej dokładności etykietowania danych

> [!NOTE]
> Etykiety czułości znalezione na [Power BIch elementów zawartości](register-scan-power-bi-tenant.md) , które są skanowane przez kontrolą, nie są obecnie wyświetlane w raporcie o czułości z etykietami. 
>
> Aby wyświetlić etykiety czułości dla Power BI elementów zawartości, Wyświetl element zawartości w [Data Catalog kontrolą](how-to-search-catalog.md).
> 
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem pracy z usługą kontrolą Insights upewnij się, że zostały wykonane następujące czynności:

- Skonfiguruj zasoby platformy Azure i wypełnianie odpowiednich kont danymi testowymi

- [Rozszerzona Microsoft 365 etykiety czułości do elementów zawartości w usłudze Azure kontrolą](create-sensitivity-label.md)i utworzonych lub wybranych etykiet, które mają być stosowane do danych.

- Skonfiguruj i Ukończ skanowanie danych testowych w każdym źródle danych. Aby uzyskać więcej informacji, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)](manage-data-sources.md) i [Tworzenie zestawu reguł skanowania](create-a-scan-rule-set.md).

- Zalogowano się do kontrolą przy użyciu konta z [czytnikiem danych lub rolą Curator danych](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Aby uzyskać więcej informacji, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)](manage-data-sources.md) i [automatyczne etykietowanie danych na platformie Azure kontrolą](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>Korzystanie z Kontroląi z etykietami

W kontrolą klasyfikacje są podobne do tagów podmiotu i służą do oznaczania i identyfikowania danych określonego typu, które znajdują się w danych podczas skanowania.

Etykiety czułości umożliwiają przeznaczenie, jak poufne są pewne dane w organizacji. Na przykład określona nazwa projektu może być wysoce poufna w organizacji, podczas gdy ten sam termin nie jest poufny dla innych organizacji. 

Klasyfikacje są dopasowywane bezpośrednio, takie jak numer ubezpieczenia społecznego, który ma klasyfikację **numeru ubezpieczenia społecznego**. 

Natomiast etykiety czułości są stosowane w przypadku, gdy co najmniej jedna Klasyfikacja i warunki są dostępne razem. W tym kontekście [warunki](/microsoft-365/compliance/apply-sensitivity-label-automatically) odnoszą się do wszystkich parametrów, które można zdefiniować dla danych bez struktury, takich jak **bliskość do innej klasyfikacji** i **% pewnością**. 

Kontrolą używa tych samych klasyfikacji, znanych również jako [typy informacji poufnych](/microsoft-365/compliance/sensitive-information-type-entity-definitions), jako Microsoft 365. Dzięki temu można zwiększyć istniejące etykiety czułości dla zasobów usługi Azure kontrolą.

> [!NOTE]
> Po przeskanowaniu typów źródłowych nadaj **literom** informacje o kilku godzinach, aby odzwierciedlić nowe zasoby.

**Aby wyświetlić szczegółowe informacje o etykietach:**

1. Przejdź do strony głównej **usługi Azure kontrolą** .

1. Na stronie **Przegląd** w sekcji **wprowadzenie** wybierz kafelek **Uruchom konto kontrolą** .

1. W kontrolą wybierz element menu usługi **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: po lewej stronie, aby uzyskać dostęp do obszaru **wglądu** w dane.

1. W obszarze **szczegółowe** dane :::image type="icon" source="media/insights/ico-insights.png" border="false"::: Wybierz pozycję **etykiety czułości** , aby wyświetlić raport o czułości kontrolą z **etykietami** .

    > [!NOTE]
    > Jeśli ten raport jest pusty, nie można rozszerzyć etykiet czułości na platformę Azure kontrolą. Aby uzyskać więcej informacji, zobacz [automatyczne etykietowanie danych w usłudze Azure kontrolą](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Szczegółowe informacje o etykietowaniu" lightbox="media/insights/sensitivity-labeling-insights.png":::

   Podstawowe **Informacje o etykietowaniu etykiet** na stronie dane są wyświetlane w następujących obszarach:

   |Obszar  |Opis  |
   |---------|---------|
   |**Przegląd źródeł z etykietami czułości**     |Wyświetla kafelki, które udostępniają: <br>-Liczba subskrypcji znalezionych w danych. <br>— Liczba unikatowych etykiet czułości zastosowanych do danych <br>-Liczba źródeł z zastosowanymi etykietami czułości <br>-Znaleziono liczbę plików i tabel z zastosowanymi etykietami czułości|
   |**Najpopularniejsze źródła z etykietami danych (ostatnie 30 dni)**     | Pokazuje trend w ciągu ostatnich 30 dni od liczby źródeł z zastosowanymi etykietami czułości.       |
   |**Górne etykiety stosowane między źródłami**     |Pokazuje górne etykiety stosowane dla wszystkich zasobów danych kontrolą. |
   |**Górne etykiety zastosowane do plików**     |Pokazuje etykiety o górnej czułości stosowane do plików w danych.          |
   |**Górne etykiety zastosowane w tabelach**     | Pokazuje etykiety o górnej czułości stosowane do tabel bazy danych w danych. |   
   |  **Działanie etykietowania**  |  Wyświetla osobne wykresy dla plików i tabel, z których każda pokazuje liczbę plików lub tabel oznaczonych w wybranym przedziale czasu. <br>**Wartość domyślna**: 30 dni<br>Wybierz filtr **czasu** powyżej wykresów, aby wybrać inny przedział czasu do wyświetlenia.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Rozróżnianie szczegółów szczegółowych informacji

W dowolnym z następujących informacji o **czułości etykiet szczegółowych** wykresów wybierz link **Wyświetl więcej** , aby przejść do szczegółów, aby uzyskać więcej szczegółów:

- **Górne etykiety stosowane między źródłami**
- **Górne etykiety zastosowane do plików**
- **Górne etykiety zastosowane w tabelach**
- **Działanie etykietowania > danych z etykietą**

Na przykład:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Oddrążenie etykiety czułości" lightbox="media/insights/sensitivity-label-drilldown.png":::

Aby dowiedzieć się więcej, wykonaj jedną z następujących czynności:

|Opcja  |Opis  |
|---------|---------|
|**Filtrowanie danych**     |  Użyj filtrów powyżej siatki, aby odfiltrować wyświetlane dane, łącznie z nazwą etykiety, nazwą subskrypcji lub typem źródła. <br><br>Jeśli nie masz pewności co do dokładnej nazwy etykiety, możesz wprowadzić część lub całą nazwę w polu **Filtruj według słowa kluczowego** .       |
|**Sortuj siatkę** |Wybierz nagłówek kolumny, aby posortować siatkę według tej kolumny. | 
|**Edytuj kolumny**     |  Aby wyświetlić więcej lub mniej kolumn w siatce, wybierz opcję **Edytuj kolumny** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: , a następnie wybierz kolumny, które chcesz wyświetlić lub zmienić kolejność.    <br><br>Wybierz nagłówek kolumny, aby posortować siatkę według tej kolumny.   |
|**Przechodzenie do szczegółów**     | Aby przejść do szczegółów określonej etykiety, wybierz nazwę w kolumnie **etykieta czułości** , aby wyświetlić raport **etykieta według źródła** . <br><br>Ten raport wyświetla dane dla wybranej etykiety, w tym nazwę źródła, typ źródła, Identyfikator subskrypcji oraz liczbę sklasyfikowanych plików i tabel.      |
|**Przeglądaj zasoby**     |  Aby przeglądać zasoby znalezione z określoną etykietą lub źródłem, wybierz jedną lub więcej etykiet lub źródeł, w zależności od wyświetlanego raportu, a następnie wybierz pozycję **Przeglądaj zasoby** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: powyżej filtrów. <br><br>W wynikach wyszukiwania są wyświetlane wszystkie oznaczone elementy zawartości dla wybranego filtru.  Aby uzyskać więcej informacji, zobacz [Wyszukiwanie w usłudze Azure kontrolą Data Catalog](how-to-search-catalog.md).       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Integracja etykiet czułości z Microsoft 365 zgodności

Bliska integracja z [firmą Microsoft Information Protection](/microsoft-365/compliance/information-protection) oferowana w Microsoft 365 oznacza, że kontrolą umożliwiają bezpośrednie sposoby zwiększania widoczności do własnych potrzeb, a następnie klasyfikowanie i etykietowanie danych.

Aby można było rozszerzyć etykiety czułości Microsoft 365 na elementy zawartości w usłudze Azure kontrolą, należy aktywnie włączyć Information Protection dla usługi Azure kontrolą w centrum zgodności Microsoft 365.

Aby uzyskać więcej informacji, zobacz [automatyczne etykietowanie danych w usłudze Azure kontrolą](create-sensitivity-label.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat tych raportów usługi Azure kontrolą Insights:

- [Szczegółowe informacje na temat słownika](glossary-insights.md)
- [Skanuj szczegółowe dane](scan-insights.md)
- [Szczegółowe informacje o klasyfikacji](./classification-insights.md)
- [Szczegółowe informacje o rozszerzeniu pliku](file-extension-insights.md)
