---
title: Raportowanie rozszerzeń plików na danych w usłudze Azure kontrolą za pomocą usługi kontrolą Insights
description: W tym przewodniku opisano sposób wyświetlania i używania funkcji raportowania rozszerzeń plików kontrolą na danych.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: 5cbfb41d50e055f745864e4d5f8bc15a55d925e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668573"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Informacje o rozszerzeniu plików dotyczące danych z usługi Azure kontrolą 

W tym przewodniku opisano sposób uzyskiwania dostępu, wyświetlania i filtrowania szczegółowych informacji o rozszerzeniach plików lub typach plików, które znajdują się w danych.

Obsługiwane źródła danych obejmują: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, zasobniki Amazon S3

W tym przewodniku krok po kroku dowiesz się, jak:
> [!div class="checklist"]
> * Uruchamianie konta kontrolą z poziomu platformy Azure 
> - Wyświetlanie szczegółowych informacji o rozszerzeniu plików na danych
> - Przejdź do szczegółów, aby uzyskać szczegółowe informacje o rozszerzeniu pliku dla danych

## <a name="prerequisites"></a>Wymagania wstępne 

Przed rozpoczęciem pracy z usługą kontrolą Insights upewnij się, że zostały wykonane następujące czynności:

- Skonfiguruj zasoby platformy Azure i wypełnianie odpowiednich kont danymi testowymi

- Skonfiguruj i Ukończ skanowanie danych testowych w każdym źródle danych. Aby uzyskać więcej informacji, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)](manage-data-sources.md) i [Tworzenie zestawu reguł skanowania](create-a-scan-rule-set.md).

- Zalogowano się do kontrolą przy użyciu konta z [czytnikiem danych lub rolą Curator danych](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).


Aby uzyskać więcej informacji, zobacz [Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Korzystanie ze szczegółowych informacji o rozszerzeniu kontrolą

Podczas skanowania zasobów usługa Azure kontrolą jest w stanie wykryć typy plików znajdujące się w obszarze danych i zapewnić więcej szczegółowych informacji na temat każdego typu plików. Szczegóły obejmują liczbę plików każdego typu, w których znajdują się te pliki, oraz tego, czy są one scannable do poufnych danych.

> [!NOTE]
> Po przeskanowaniu typów źródłowych nadaj **plikowi** informacje o kilku godzinach, aby odzwierciedlić nowe zasoby.

**Aby wyświetlić szczegółowe informacje o rozszerzeniu pliku:**

1. Przejdź do ekranu wystąpienia **usługi Azure kontrolą** [w Azure Portal](https://aka.ms/purviewportal) i wybierz swoje konto kontrolą.

1. Na stronie **Przegląd** w sekcji **wprowadzenie** wybierz kafelek **Uruchom konto kontrolą** .

1. W kontrolą wybierz element menu usługi **Insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: po lewej stronie, aby uzyskać dostęp do obszaru **wglądu** w dane.
    
1. W usłudze **Insights** wybierz kartę **rozszerzenia plików** .

    W raporcie jest wyświetlane podsumowanie liczby znalezionych unikatowych rozszerzeń plików, a także grafu 10 najważniejszych rozszerzeń w wybranym przedziale czasu (wartość domyślna: 30 dni).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Raport o rozszerzeniu plików — omówienie" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Aby dowiedzieć się więcej, wykonaj dowolną z następujących czynności:

    - Wybierz selektor **czas** u góry raportu, aby zmienić przedział czasu, dla którego znaleziono rozszerzenia plików.
    
    - Wybierz pozycję **Wyświetl więcej** poniżej wykresu, aby wyświetlić pełną listę znalezionych rozszerzeń plików. Aby uzyskać więcej informacji, zobacz Przechodzenie do [szczegółów rozszerzenia pliku Insights](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Szczegółowe informacje o rozszerzeniu rozszerzenia pliku

Po przejrzeniu ogólnych informacji o typach plików znalezionych w obszarze danych, przechodzenie do szczegółów, aby uzyskać więcej informacji na temat ich lokalizacji i czy można je przeskanować w poszukiwaniu poufnych danych.

Na przykład:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Raport o rozszerzeniu plików — przechodzenie do szczegółów" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

Siatka pokazuje szczegółowe informacje o znalezionych rozszerzeniach plików, w tym:

- **Liczba plików**. Liczba plików z określonym rozszerzeniem.
- **Skanowanie zawartości**. Czy rozszerzenie pliku jest obsługiwane do skanowania w poszukiwaniu poufnych danych.
- **Subskrypcje**. Liczba subskrypcji, w których znaleziono określone rozszerzenie.
- **Źródła**. Liczba źródeł znalezionych dla określonego rozszerzenia pliku.



Użyj filtrów powyżej siatki, aby odfiltrować wyświetlane dane:

|Opcja  |Opis  |
|---------|---------|
|**Filtruj według słowa kluczowego**     |    Wprowadź tekst w polu **Filtruj według słowa kluczowego**  , aby wyświetlić Filtrowanie typów plików według nazwy. Na przykład, aby wyświetlić tylko pliki PDF, wprowadź `PDF` .     |
|**Godzina**        | Wybierz, aby przefiltrować według określonego przedziału czasu dla momentu utworzenia danych. <br>**Wartość domyślna:** 30 dni  |
|**Rozszerzenie pliku**     |Wybierz, aby odfiltrować siatkę według jednego lub większej liczby typów plików.        |
|**Źródła**    |Wybierz, aby odfiltrować siatkę według określonych źródeł danych. |
|**Skanowanie zawartości**     |Wybierz opcję **obsługiwane** lub **nieobsługiwane**, aby wyświetlić tylko typy plików, które można przeskanować w poszukiwaniu poufnych danych lub dane, których nie można przeskanować, takie jak pliki **CERT** lub **jpg** . |
| | |

Powyżej filtrów wybierz opcję **Edytuj kolumny** , :::image type="icon" source="media/insights/ico-columns.png" border="false"::: Aby wyświetlić więcej lub mniej kolumn w siatce, lub zmienić kolejność. 

Aby posortować siatkę, wybierz nagłówek kolumny, aby posortować według tej kolumny.
## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o raportach usługi Azure kontrolą Insights
> [!div class="nextstepaction"]
> [Szczegółowe informacje na temat słownika](glossary-insights.md)

> [!div class="nextstepaction"]
> [Skanuj szczegółowe dane](scan-insights.md)

> [!div class="nextstepaction"]
> [Szczegółowe informacje o klasyfikacji](./classification-insights.md)

> [!div class="nextstepaction"]
> [Wgląd w szczegółowe informacje etykiet](sensitivity-insights.md)
