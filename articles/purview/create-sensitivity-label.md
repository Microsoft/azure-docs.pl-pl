---
title: Automatycznie stosuj etykiety czułości do danych
description: Dowiedz się, jak tworzyć etykiety czułości i automatycznie stosować je do danych podczas skanowania.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 195ddfcc3c781c1866a7751f989c9460463edbb9
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558168"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Automatyczne etykietowanie danych w usłudze Azure kontrolą

W tym artykule opisano sposób tworzenia etykiet czułości dla programu Microsoft Information Protection (MCI) i automatycznego stosowania ich do zasobów platformy Azure w usłudze Azure kontrolą.

## <a name="what-are-sensitivity-labels"></a>Co to są etykiety czułości? 

Aby rozpocząć pracę, osoby w Twojej organizacji współpracują z innymi osobami w organizacji i poza nią. Dane nie zawsze znajdują się w chmurze i często przechodzą wszędzie, między urządzeniami, aplikacjami i usługami. 

Gdy dane są przekazywane, należy to zrobić w bezpieczny, chroniony sposób, który spełnia zasady biznesowe i dotyczące zgodności w organizacji.

Zastosowanie etykiet czułości pozwala określić, jak poufne są pewne dane w organizacji. Na przykład określona nazwa projektu może być wysoce poufna w organizacji, podczas gdy ten sam termin nie jest poufny dla innych organizacji. 

### <a name="sensitivity-labels-in-azure-purview"></a>Etykiety czułości na platformie Azure kontrolą

W kontrolą klasyfikacje są podobne do tagów podmiotu i służą do oznaczania i identyfikowania danych określonego typu, które znajdują się w danych podczas skanowania.

Kontrolą używa tych samych klasyfikacji, znanych również jako typy informacji poufnych, jako Microsoft 365.  Etykiety dotyczące czułości MCI są tworzone w centrum zabezpieczeń i zgodności Microsoft 365 (SCC). Dzięki temu można zwiększyć istniejące etykiety czułości dla zasobów usługi Azure kontrolą.

> [!NOTE]
> Klasyfikacje są dopasowywane bezpośrednio (numer ubezpieczenia społecznego ma klasyfikację numeru ubezpieczenia **społecznego**), natomiast etykiety czułości są stosowane w przypadku, gdy co najmniej jedna Klasyfikacja i scenariusze są jednocześnie dostępne. 
> 

Etykiety czułości w usłudze Azure kontrolą mogą służyć do automatycznego stosowania etykiet do plików i kolumn baz danych.

Aby uzyskać więcej informacji, zobacz:

- [Dowiedz się więcej na temat etykiet czułości](/microsoft-365/compliance/sensitivity-labels) w dokumentacji Microsoft 365
- [Co to są reguły autoetykietowania?](#what-are-autolabeling-rules)
- [Obsługiwane typy danych dla etykiet czułości na platformie Azure kontrolą](#supported-data-types-for-sensitivity-labels-in-azure-purview)

#### <a name="what-are-autolabeling-rules"></a>Co to są reguły autoetykietowania?

Twoje dane ciągle rośnie i zmieniają się. Śledzenie danych, które są obecnie bez etykiet, i podejmowanie akcji ręcznego stosowania etykiet nie jest tylko skomplikowane, ale jest również niezbędną kłopotliwej. 

Reguły autoetykietowania są określonymi warunkami, co oznacza, że należy zastosować konkretną etykietę. Gdy te warunki są spełnione, etykieta zostanie automatycznie przypisana do danych, zachowując spójne etykiety czułości danych na dużą skalę.

Podczas tworzenia etykiet upewnij się, że definiują reguły autoetykietowania zarówno dla [plików](#define-autolabeling-rules-for-files) , jak i [kolumn baz danych](#define-autolabeling-rules-for-database-columns) , aby automatycznie stosować etykiety przy każdym skanowaniu danych. 

Po przeskanowaniu danych w programie kontrolą można wyświetlić etykiety, które są automatycznie stosowane w wykazie kontrolą i raportach szczegółowych.

#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Obsługiwane typy danych dla etykiet czułości na platformie Azure kontrolą

Etykiety czułości są obsługiwane w usłudze Azure kontrolą dla następujących typów danych:

|Typ danych  |Źródła  |
|---------|---------|
|Automatyczne etykietowanie plików     |     — Blob Storage platformy Azure  </br>-Azure Data Lake Storage Gen 1 i Gen 2  |
|Automatyczne etykietowanie kolumn bazy danych     |  — SQL Server </br>— Azure SQL Database </br>-Azure SQL Database wystąpienia zarządzanego   <br> — Azure Synapse  <br>-Azure Cosmos DB   |
| | |

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Jak utworzyć etykiety czułości w Microsoft 365

Jeśli nie masz jeszcze etykiet czułości, musisz je utworzyć i udostępnić je dla usługi Azure kontrolą. Istniejące etykiety czułości można także zmodyfikować, aby były dostępne dla usługi Azure kontrolą.

Aby uzyskać więcej informacji, zobacz:

- [Wymagania dotyczące licencjonowania](#licensing-requirements)
- [Rozszerzanie etykiet czułości na platformę Azure kontrolą](#extending-sensitivity-labels-to-azure-purview)
- [Tworzenie nowych etykiet o czułości lub modyfikowanie istniejących etykiet](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania

Etykiety dotyczące czułości MCI są tworzone i zarządzane w centrum zabezpieczeń i zgodności Microsoft 365. Aby utworzyć etykiety czułości do użycia w usłudze Azure kontrolą, musisz mieć aktywną licencję Microsoft 365 E5.

Jeśli nie masz jeszcze wymaganej licencji, możesz zarejestrować się w celu uzyskania wersji próbnej [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion).

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Rozszerzanie etykiet czułości na platformę Azure kontrolą

Domyślnie etykiety czułości MCI są dostępne tylko dla zasobów w Microsoft 365, w których można je stosować do plików i wiadomości e-mail.

Aby zastosować etykiety czułości MCI do zasobów platformy Azure w usłudze Azure kontrolą, musisz jawnie wyrazić zgodę na rozszerzenie etykiet i wybrać określone etykiety, które mają być dostępne w kontrolą.

Dzięki rozszerzeniu etykiet czułości MCI przy użyciu usługi Azure kontrolą organizacje mogą teraz odkrywać, klasyfikować i uzyskiwać wgląd w informacje o czułości w szerszym zakresie źródeł danych, minimalizując ryzyko zgodności.

> [!NOTE]
> Ponieważ Microsoft 365 i Azure kontrolą są oddzielnymi usługami, istnieje możliwość, że zostaną one wdrożone w różnych regionach. Nazwy etykiet i niestandardowe typy informacji poufnych są uważane za dane klienta i są przechowywane w tej samej lokalizacji geograficznej domyślnie, aby chronić czułość danych i uniknąć Rodo obowiązujących przepisów.
>
> Z tego powodu etykiety i niestandardowe typy informacji poufnych nie są domyślnie udostępniane do usługi Azure kontrolą i wymagają zgody użytkownika na korzystanie z nich w usłudze Azure kontrolą.

> [!IMPORTANT]
> Dzięki temu firma Microsoft może udostępniać nazwę etykiety i informacje o niestandardowym typie informacji *poufnych na platformie* Azure kontrolą i Azure Security Center (ASC). Firma Microsoft używa informacji o etykiecie z platformy Azure kontrolą do wzbogacania zaleceń i alertów w usłudze ASC. 
>
> Wyrażanie zgody w Microsoft 365 Centrum zgodności ma zastosowanie do udostępniania tych danych w obu usługach. Obecnie nie ma możliwości udostępniania informacji o etykietach tylko za pomocą usługi Azure kontrolą.

**Aby zwiększyć czułość etykiet do kontrolą:**

W Microsoft 365 przejdź do strony **Information Protection** . W oknie dialogowym **Rozwiń etykietowanie do elementów zawartości w usłudze Azure kontrolą** wybierz przycisk **Włącz** , a następnie wybierz pozycję **tak** w wyświetlone okno dialogowe potwierdzenia.

Na przykład:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="SELECT * * Włącz * *, aby zwiększyć etykiety czułości do kontrolą" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Po dodaniu etykietowania do elementów zawartości w usłudze Azure kontrolą można wybrać etykiety, które mają być dostępne w kontrolą. Aby uzyskać więcej informacji, zobacz [Tworzenie nowych etykiet o czułości lub modyfikowanie istniejących etykiet](#creating-new-sensitivity-labels-or-modifying-existing-labels).
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>Tworzenie nowych etykiet o czułości lub modyfikowanie istniejących etykiet

1. Otwórz [Centrum zabezpieczeń i zgodności Microsoft 365](https://protection.office.com/homepage). 

1. W obszarze **rozwiązania** wybierz pozycję **Ochrona informacji**, a następnie wybierz pozycję **Utwórz etykietę**. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Tworzenie etykiet czułości w centrum zabezpieczeń i zgodności Microsoft 365" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. Nadaj etykietę nazwę. Następnie w obszarze **Zdefiniuj zakres dla tej etykiety** wybierz pozycję **pliki i wiadomości E-mail** i **zasoby platformy Azure kontrolą**.
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Utwórz etykietę w centrum zabezpieczeń i zgodności Microsoft 365" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. Postępuj zgodnie z pozostałymi instrukcjami wyświetlanymi w kreatorze dla ustawień etykiet. 

    Zdefiniuj reguły autoetykietowania dla plików i kolumn bazy danych:

    - [Definiuj reguły autoetykietowania dla plików](#define-autolabeling-rules-for-files)
    - [Definiowanie reguł autoetykietowania dla kolumn bazy danych](#define-autolabeling-rules-for-database-columns)

    Więcej informacji o opcjach kreatora [można](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) znaleźć w dokumentacji Microsoft 365.

1. Powtórz powyższe kroki, aby utworzyć dodatkowe etykiety. 

    Aby utworzyć podetykietę, wybierz etykietę nadrzędną > **...**  >  **Więcej akcji**  >  **Dodaj etykietę podrzędną**.

1. Aby zmodyfikować istniejące etykiety, przejdź do **Information Protection**  >  **etykiet** i wybierz etykietę. 

    Następnie wybierz pozycję **edytuj etykietę** , aby ponownie otworzyć kreatora **edycji etykiet czułości** ze wszystkimi ustawieniami zdefiniowanymi podczas tworzenia etykiety.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="Edytuj istniejącą etykietę czułości" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. Po zakończeniu tworzenia wszystkich etykiet upewnij się, że jest wyświetlana kolejność etykiet i zmień ich kolejność zgodnie z potrzebami. 

    Aby zmienić kolejność etykiet, wybierz pozycję **...** **> więcej akcji**  >  **Przenieś w górę** lub **Przenieś w dół.** 

    Aby uzyskać więcej informacji, zobacz [priorytet etykiety (kwestie w porządku)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) w dokumentacji Microsoft 365.

> [!IMPORTANT]
> Nie usuwaj etykiety, chyba że rozumiesz wpływ użytkowników. 
>
> Aby uzyskać więcej informacji, zobacz [usuwanie i usuwanie etykiet](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) w dokumentacji Microsoft 365.

Kontynuuj [skanowanie danych, aby automatycznie zastosować etykiety](#scan-your-data-to-apply-labels-automatically), a następnie:

- [Wyświetl etykiety zasobów](#view-labels-on-assets)
- [Wyświetl raporty szczegółowe dotyczące klasyfikacji i etykiet informacji o czułości](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-autolabeling-rules-for-files"></a>Definiuj reguły autoetykietowania dla plików

Zdefiniuj reguły autoetykietowania dla plików w Kreatorze podczas tworzenia lub edytowania etykiety. 

Na stronie **automatyczne etykietowanie aplikacji pakietu Office** Włącz **automatyczne etykietowanie aplikacji pakietu Office,** a następnie zdefiniuj warunki, w których etykieta ma być automatycznie stosowana do danych.

Na przykład:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Zdefiniuj reguły autoetykietowania dla plików w centrum zabezpieczeń i zgodności Microsoft 365" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
Aby uzyskać więcej informacji, zobacz [Automatyczne stosowanie etykiety czułości do danych](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) w dokumentacji Microsoft 365. 

#### <a name="define-autolabeling-rules-for-database-columns"></a>Definiowanie reguł autoetykietowania dla kolumn bazy danych

Zdefiniuj reguły autoetykietowania dla kolumn bazy danych w Kreatorze podczas tworzenia lub edytowania etykiety. 

W obszarze **zasoby usługi Azure kontrolą (wersja zapoznawcza)** :

1. Wybierz suwak **autoetykietowanie kolumn bazy danych** .

1. Wybierz pozycję **Sprawdź typy informacji poufnych** , aby wybrać typy informacji poufnych, które chcesz zastosować do etykiety.

Na przykład:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Definiowanie reguł autoetykietowania dla kolumn SQL w centrum zabezpieczeń i zgodności Microsoft 365" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>Skanuj dane, aby automatycznie stosować etykiety

Skanuj dane w usłudze Azure kontrolą, aby automatycznie zastosować utworzone etykiety na podstawie zdefiniowanych reguł autoetykietowania. 

Aby uzyskać więcej informacji na temat sposobu konfigurowania skanowania dla różnych zasobów w usłudze Azure kontrolą, zobacz:

|Element źródłowy  |Dokumentacja  |
|---------|---------|
|**Azure Blob Storage**     |[Rejestrowanie i skanowanie Blob Storage platformy Azure](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake Storage**     |[Rejestruj i Skanuj Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[Rejestruj i Skanuj Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)         |
|**Bazy danych Azure SQL Database**|[Rejestrowanie i skanowanie Azure SQL Database](register-scan-azure-sql-database.md) </br>[Rejestrowanie i skanowanie Azure SQL Database wystąpienia zarządzanego](register-scan-azure-sql-database-managed-instance.md)|
|**Konta magazynu za zaporami**     |[Skanowanie kont magazynu za zaporą w usłudze Azure kontrolą](scan-sqlresource-firewall.md)         |
| | |

## <a name="view-labels-on-assets"></a>Wyświetl etykiety zasobów

Po zdefiniowaniu reguł autoetykietowania dla etykiet w Microsoft 365 i przeskanowaniu danych w usłudze Azure kontrolą etykiety są automatycznie stosowane do zasobów. 

**Aby wyświetlić etykiety zastosowane do zasobów w katalogu usługi Azure kontrolą:**

W katalogu usługi Azure kontrolą Użyj opcji filtrowania **etykiet** , aby wyświetlić tylko pliki z określonymi etykietami. Na przykład: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="Wyszukaj zasoby według etykiety" lightbox="media/create-sensitivity-label/filter-search-results.png":::

Na przykład:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Wyświetlanie etykiety czułości pliku na Blob Storage platformy Azure" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Wyświetl raporty szczegółowe dotyczące klasyfikacji i etykiet informacji o czułości

Poznaj szczegółowe informacje o sklasyfikowanych i etykietowanych danych na platformie Azure kontrolą przy użyciu raportów **klasyfikacji** i **czułości** .

> [!div class="nextstepaction"]
> [Szczegółowe informacje o klasyfikacji](./classification-insights.md)

> [!div class="nextstepaction"]
> [Wgląd w szczegółowe informacje etykiet](sensitivity-insights.md)


