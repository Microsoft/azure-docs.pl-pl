---
title: Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)
description: Dowiedz się, jak rejestrować nowe źródła danych, zarządzać kolekcjami źródeł danych i wyświetlać źródła w usłudze Azure kontrolą (wersja zapoznawcza).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 90a873b7de6ccc1ba21a05bf4c0e288ed668cac0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694465"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)

W tym artykule dowiesz się, jak zarejestrować nowe źródła danych, zarządzać kolekcjami źródeł danych i wyświetlać źródła w usłudze Azure kontrolą (wersja zapoznawcza)

## <a name="register-a-new-source"></a>Rejestrowanie nowego źródła

Wykonaj poniższe kroki, aby zarejestrować nowe źródło.

1. Otwórz kontrolą Studio i wybierz kafelek **Rejestruj źródła** .

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure kontrolą Studio":::

1. Wybierz pozycję **zarejestruj**, a następnie wybierz typ źródła. Ten przykład używa platformy Azure Blob Storage. Wybierz opcję **Kontynuuj**.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Wybierz typ źródła danych na stronie Rejestrowanie źródeł":::

1. Wypełnij formularz na stronie **Rejestrowanie źródeł** . Wybierz nazwę źródła i wprowadź odpowiednie informacje. W przypadku wybrania **z subskrypcji platformy Azure** jako metody wyboru konta źródła w subskrypcji zostaną wyświetlone na liście rozwijanej. Alternatywnie możesz ręcznie wprowadzić swoje informacje źródłowe.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Formularz informacji o źródle danych":::

1. Wybierz pozycję **Zakończ**.

## <a name="view-sources"></a>Wyświetl źródła

Wszystkie zarejestrowane źródła można wyświetlić na karcie **źródła** w usłudze Azure kontrolą Studio. Istnieją dwa typy widoków: widok mapy i widok listy.

### <a name="map-view"></a>Widok mapy

W widoku mapy można zobaczyć wszystkie źródła i kolekcje. Na poniższej ilustracji znajduje się jedno źródło Blob Storage platformy Azure. Z każdego kafelka źródłowego można edytować źródło, rozpocząć nowe skanowanie lub wyświetlić szczegóły źródła.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Widok mapy źródła danych usługi Azure kontrolą":::

### <a name="list-view"></a>Widok listy

W widoku listy można zobaczyć listę źródeł do sortowania. Umieść kursor nad źródłem opcji do edycji, Rozpocznij nowe skanowanie lub Usuń.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Widok listy źródeł danych usługi Azure kontrolą":::

## <a name="manage-collections"></a>Zarządzanie kolekcjami

Źródła danych można grupować w kolekcje. Aby utworzyć nową kolekcję, wybierz pozycję **+ Nowa kolekcja** na stronie *źródła* w programie Azure kontrolą Studio. Nadaj kolekcji nazwę i wybierz pozycję *Brak* jako element nadrzędny. Nowa kolekcja zostanie wyświetlona w widoku mapy.

Aby dodać źródła do kolekcji, wybierz pozycję **Edytuj** ołówek na źródle i wybierz kolekcję z menu rozwijanego **Wybierz kolekcję** .

Aby utworzyć hierarchię kolekcji, przypisz kolekcje wyższego poziomu jako element nadrzędny do kolekcji niższego poziomu. Na poniższej ilustracji *firma Fabrikam* jest elementem nadrzędnym kolekcji *finanse* , która zawiera źródło danych BLOB Storage platformy Azure. Kolekcje można zwijać i rozwijać przez kliknięcie okręgu dołączonego do strzałki między poziomami.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Hierarchia kolekcji w usłudze Azure kontrolą Studio":::

Źródła można usunąć z hierarchii, wybierając pozycję *Brak* dla elementu nadrzędnego. Nienadrzędne źródła są pogrupowane w kropkowanym polu w widoku mapy bez strzałek łączących je z elementami nadrzędnymi.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak rejestrować i skanować różne źródła danych:

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Dzierżawa Power BI](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
