---
title: Przypadek użycia — profilowanie klientów
description: Dowiedz się, jak Azure Data Factory jest używany do tworzenia przepływu pracy opartego na danych (potok) do profilowania klientów gier.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b8193039edd21db5a52eabca37c1e61880e49cfe
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779753"
---
# <a name="use-case---customer-profiling"></a>Przypadek użycia — profilowanie klientów
Azure Data Factory jest jedną z wielu usług służących do implementowania Cortana Intelligence Suite akceleratorów rozwiązań.  Aby uzyskać więcej informacji na temat Cortana Intelligence, odwiedź [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). W tym dokumencie opisano prosty przypadek użycia, który pomoże Ci rozpocząć zrozumienie, jak Azure Data Factory może rozwiązać typowe problemy z analizą.

## <a name="scenario"></a>Scenariusz
Contoso to firma firmy, która tworzy gry dla wielu platform: konsole gier, urządzenia z ręczną i komputery osobiste (komputery). Ponieważ gracze odgrywają te gry, tworzone są duże ilości danych dziennika, które śledzą wzorce użycia, styl gier i preferencje użytkownika.  W połączeniu z danymi demograficznymi, regionalnymi i towarowymi firma Contoso może przeprowadzić analizę, aby ułatwić im udoskonalenie środowiska graczy i kierowanie ich do uaktualnień i zakupów w grach. 

Celem firmy Contoso jest zidentyfikowanie możliwości sprzedaży/sprzedaży krzyżowej w oparciu o historię gier swoich graczy i dodanie atrakcyjnych funkcji w celu zwiększenia rozwoju działalności biznesowej i zapewnienia klientom lepszego środowiska. W tym przypadku użycia firma Microsoft używa firmy do gier jako przykładu firmy. Firma chce zoptymalizować swoje gry w oparciu o zachowanie graczy. Te zasady mają zastosowanie do wszystkich firm, które chcą angażować swoich klientów w swoje rzeczy i usługi, a także zwiększyć komfort pracy klientów.

W tym rozwiązaniu firma Contoso chce oszacować efektywność kampanii marketingowej, która została ostatnio uruchomiona. Rozpoczynamy od nieprzetworzonych dzienników gier, przetwarzają i wzbogacają je za pomocą danych geolokalizacyjnych, dołączą do nich dane referencyjne, a następnie kopiują je do Azure SQL Database, aby przeanalizować wpływ kampanii.

## <a name="deploy-solution"></a>Wdróż rozwiązanie
Wystarczy uzyskać dostęp do tego prostego przypadku użycia i wypróbować go w [ramach subskrypcji platformy Azure](https://azure.microsoft.com/pricing/free-trial/), [konta usługi Azure Blob Storage](../../storage/common/storage-account-create.md)i [Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md). Potok profilowania klienta jest wdrażany na kafelku **potoki przykładowe** na stronie głównej fabryki danych.

1. Utwórz fabrykę danych lub Otwórz istniejącą fabrykę danych. Aby utworzyć fabrykę danych, zobacz [Kopiowanie danych z BLOB Storage do SQL Database przy użyciu Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
2. W bloku **Fabryka danych** dla fabryki danych kliknij kafelek **przykładowe potoki** .

    ![Kafelek przykładowe potoki](./media/data-factory-samples/SamplePipelinesTile.png)
3. W bloku **przykładowe potoki** kliknij **profilowanie klienta** , który chcesz wdrożyć.

    ![Blok przykładowych potoków](./media/data-factory-samples/SampleTile.png)
4. Określ ustawienia konfiguracji dla przykładu. Na przykład nazwa i klucz konta usługi Azure Storage, nazwa logiczna serwera SQL, baza danych, identyfikator użytkownika i hasło.

    ![Przykładowy blok](./media/data-factory-samples/SampleBlade.png)
5. Po określeniu ustawień konfiguracji kliknij pozycję **Utwórz** , aby utworzyć/wdrożyć przykładowe potoki i połączone usługi/tabele używane przez potoki.
6. Zobaczysz stan wdrożenia na kafelku przykładowym, który został kliknięty wcześniej w bloku **przykładowe potoki** .

    ![Stan wdrożenia](./media/data-factory-samples/DeploymentStatus.png)
7. Gdy na kafelku przykładu zostanie wyświetlony komunikat **wdrażanie zakończyło się pomyślnie** , Zamknij blok **przykładowe potoki** .  
8. W bloku **Fabryka danych** zobaczysz, że połączone usługi, zestawy danych i potoki są dodawane do fabryki danych.  

    ![Blok Fabryka danych](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Omówienie rozwiązania
Tego prostego przypadku użycia można użyć jako przykładowego sposobu użycia Azure Data Factory do pozyskiwania, przygotowywania, przekształcania, analizowania i publikowania danych.

![Kompletny przepływ pracy](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Ten rysunek przedstawia sposób, w jaki potoki danych pojawiają się w Azure Portal po ich wdrożeniu.

1. **PartitionGameLogsPipeline** odczytuje nieprzetworzone zdarzenia gier z magazynu obiektów blob i tworzy partycje na podstawie roku, miesiąca i dnia.
2. **EnrichGameLogsPipeline** sprzęga podzielone na partycje zdarzenia z kodem geograficznym i wzbogaca dane przez mapowanie adresów IP do odpowiednich lokalizacji geograficznych.
3. Potok **AnalyzeMarketingCampaignPipeline** korzysta z wzbogaconych danych i przetwarza je za pomocą danych reklamowych, aby utworzyć końcowe dane wyjściowe zawierające efektywność kampanii marketingowej.

W tym przykładzie Data Factory jest używany do organizowania działań, które kopiują dane wejściowe, przekształcają i przetwarzają dane, a następnie wyprowadzają końcowe dane do Azure SQL Database.  Możesz również wizualizować sieć potoków danych, zarządzać nimi i monitorować ich stan za pomocą interfejsu użytkownika.

## <a name="benefits"></a>Korzyści
Przez optymalizację analizy profilu użytkownika i dostosowanie jej do celów firmy, firma gier może szybko zbierać wzorce użycia i analizować efektywność kampanii marketingowych.