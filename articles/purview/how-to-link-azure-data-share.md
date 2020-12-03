---
title: Nawiązywanie połączenia z udziałem danych platformy Azure
description: W tym artykule opisano sposób łączenia konta udziału danych platformy Azure z usługą Azure kontrolą w celu wyszukiwania zasobów i śledzenia pochodzenia danych.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555360"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Jak połączyć udziały danych platformy Azure i usługę Azure kontrolą

W tym artykule omówiono sposób łączenia konta [udziału danych platformy Azure](../data-share/overview.md) z usługą Azure kontrolą oraz zarządzania udostępnionymi zestawami danych (zarówno wychodzącymi, jak i przychodzącymi). Różne osóbi do zarządzania danymi mogą odkrywać i śledzić zakres danych między granicami, takimi jak organizacje, działy, a nawet centra danych.

## <a name="common-scenarios"></a>Typowe scenariusze

Elementy powiązane z udziałami danych mają na celu dostarczenie szczegółowych informacji dotyczących analizy głównych przyczyn i analizy wpływu.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>Scenariusz 1:360 widok zestawów danych udostępnianych we/zewnątrz dla organizacji partnerskiej lub wewnętrznego działu

Funkcjonariusze danych mogą wyświetlić listę wszystkich zestawów danych, które są dwukierunkowe udostępniane organizacjom partnerskim. Mogą wyszukiwać i odnajdować zestawy danych według nazwy organizacji i wyświetlać pełny widok wszystkich udziałów wychodzących i przychodzących.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>Scenariusz 2: Analiza głównej przyczyny — zależność nadrzędna w zestawach danych, które są w organizacji (widok konsumenta udziałów przychodzących)

Raport zawiera nieprawidłowe informacje z powodu nadrzędnych problemów z danymi z zewnętrznego konta udziału danych. Inżynierowie danych mogą zrozumieć błędy nadrzędnego, uzyskać informacje o przyczynach, a następnie skontaktować się z właścicielem udziału, aby rozwiązać problemy powodujące rozbieżność danych.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>Scenariusz 3: Analiza wpływu na zestawy danych, które przechodzą poza organizację (widok dostawcy udziałów wychodzących)

Producenci danych chcą wiedzieć, kto będzie miał wpływ na wprowadzanie zmian w zestawie danych. Korzystając z elementów zewnętrznych, producent danych może łatwo zrozumieć wpływ wewnętrznych lub zewnętrznych partnerów, którzy zużywają dane za pomocą udziału danych platformy Azure.

## <a name="azure-data-share-and-purview-connected-experience"></a>Udostępnianie danych platformy Azure i środowisko połączone kontrolą

Aby połączyć udział danych platformy Azure i konto usługi Azure kontrolą, wykonaj następujące czynności:

1. Utwórz konto kontrolą. Wszystkie informacje o pozostałym udziale danych zostaną zebrane przy użyciu konta kontrolą. Możesz użyć istniejącego konta lub utworzyć nowe konto kontrolą.

1. Połącz udział danych platformy Azure z kontem kontrolą.

    1. W portalu kontrolą możesz przejść do **centrum zarządzania** i połączyć udział danych platformy Azure w sekcji **połączenia zewnętrzne** .
    1. Wybierz pozycję **+ Nowy** na górnym pasku, Znajdź udział danych platformy Azure na pasku bocznym podręcznym i Dodaj konto udostępniania danych. Uruchom zadanie migawki po połączeniu udziału danych z kontem kontrolą, tak aby zasoby udziału danych i informacje o powiązaniu były widoczne w kontrolą.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Centrum zarządzania do łączenia udziału danych platformy Azure":::

1. Wykonaj migawkę w udziale danych platformy Azure.

    - Po ustanowieniu połączenia udziału danych platformy Azure z usługą Azure kontrolą można wykonać migawkę istniejących udziałów. 
    - Jeśli nie masz żadnych istniejących udziałów, przejdź do portalu udostępniania danych platformy Azure, aby [udostępnić dane](../data-share/share-your-data.md) [i subskrybować udział danych](../data-share/subscribe-to-data-share.md).
    - Po zakończeniu tworzenia migawki udziału można wyświetlić skojarzone zasoby udziałów danych i elementy powiązane w kontrolą.

1. Odnajdywanie kont udziałów danych i udostępnianie informacji na koncie kontrolą.

    - Na stronie głównej konta kontrolą wybierz pozycję **Przeglądaj według typu zasobu** i wybierz kafelek **udział danych platformy Azure** . Można wyszukać nazwę konta, nazwę udziału, migawkę udziału lub organizację partnera. W przeciwnym razie Zastosuj filtry na stronie wyników wyszukiwania dla nazwy konta, typu udziału (wysłane i odebrane udziały).

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Udział danych platformy Azure na stronie wyników wyszukiwania":::

    >[!Important]
    >Aby zasoby udostępniania danych były wyświetlane w kontrolą, należy uruchomić zadanie migawki po połączeniu udziału danych z kontrolą.

1. Śledź elementy powiązane z zestawami danych udostępnionymi przy użyciu usługi Azure Data Share.

    - Na stronie wynik wyszukiwania kontrolą wybierz migawkę udziału danych (odebrane/wysłane) i **Wybierz kartę elementy** nadrzędne, aby wyświetlić wykres oddzielny z zależnościami nadrzędnymi i podrzędnymi.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Elementy powiązane z zestawami danych udostępnionymi przy użyciu usługi Azure Data Share":::

## <a name="next-steps"></a>Następne kroki

- [Podręcznik użytkownika z wykazem](catalog-lineage-user-guide.md)
- [Link do Azure Data Factory dla elementu powiązanego](how-to-link-azure-data-factory.md)
