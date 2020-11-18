---
title: Platforma Data Lake Storage i WANdisco LiveData dla platformy Azure (wersja zapoznawcza)
description: Migruj lokalne dane usługi Hadoop do Azure Data Lake Storage Gen2 przy użyciu platformy WANdisco LiveData dla platformy Azure.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/06/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 9da6ea7abf57ffecc900a6dbef065a8c6b123e61
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94810999"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Zapoznaj się z wymaganymi wymaganiami dotyczącymi migracji przy użyciu platformy WANdisco LiveData dla platformy Azure

Migruj lokalne dane usługi Hadoop do Azure Data Lake Storage Gen2 przy użyciu [platformy WANdisco LiveData dla platformy Azure](https://docs.wandisco.com/live-data-platform/docs/landing/). Ta platforma eliminuje konieczność przestoju aplikacji, pozwala wyeliminować utratę danych i zapewnić spójność danych, nawet w przypadku kontynuowania operacji w środowisku lokalnym.  

> [!NOTE]
> Platforma WANdisco LiveData dla platformy Azure jest w publicznej wersji zapoznawczej. Aby uzyskać dostęp do regionu, zobacz [Obsługiwane regiony](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions).

Platforma składa się z dwóch usług: [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) do migrowania aktywnie używanych danych ze środowisk lokalnych do usługi Azure Storage i [płaszczyzny LiveData dla platformy Azure](https://www.wandisco.com/products/livedata-plane-for-azure) , która zapewnia spójną replikację wszystkich modyfikacji danych lub pozyskiwania danych. 

> [!div class="mx-imgBorder"]
> ![Ilustracja przedstawiająca Omówienie platformy danych na żywo](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Tymi usługami można zarządzać przy użyciu Azure Portal i interfejsu wiersza polecenia platformy Azure, a oba te modele są zgodne z modelem rozliczeń płatności zgodnie z rzeczywistym użyciem, co w przypadku wszystkich innych usług platformy Azure. Platforma LiveData dla korzystania z platformy Azure zostanie wyświetlona na tym samym miesięcznym rachunku na korzystanie z platformy Azure i będzie zapewnić spójny i wygodny sposób śledzenia i monitorowania użycia.

W przeciwieństwie do migracji danych w _trybie offline_ przez [Kopiowanie informacji statycznych do Azure Data Box](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster)lub przy użyciu narzędzi Hadoop, takich jak [pomocą distcp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), można zachować pełną eksploatację systemów firmy podczas migracji w _trybie online_ z WANdisco LiveData dla platformy Azure. Przechowuj swoje środowiska danych Big Data nawet podczas przenoszenia ich na platformę Azure.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Najważniejsze funkcje platformy WANdisco LiveData dla platformy Azure

[Platforma WANdisco LiveData dla platformy Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) korzysta z unikatowego, wielowarstwowego aparatu umożliwiającego osiągnięcie spójności danych oraz przeprowadzania replikacji danych na dużą skalę, gdy aplikacje mogą nadal modyfikować dane w ramach replikacji.  

Najważniejsze funkcje platformy obejmują następujące elementy:

- **Spójność danych**: Rozwiązywanie problemów z migracją dużych ilości danych między środowiskami i utrzymywaniem spójnych danych w ramach migracji przepływności systemów magazynowania, nawet w przypadku ciągłej zmiany. Korzystaj z unikatowego, wielowarstwowego aparatu z systemem WANdisco, który bezpośrednio na platformie Azure zapewnia spójność danych i migruje dane z gwarancją spójności w ramach zmian danych.

- **Obsługa operacji**: ponieważ aplikacje mogą nadal tworzyć, modyfikować, odczytywać i usuwać dane podczas migracji, nie ma potrzeby zakłócania operacji działania biznesowego ani przeprowadzenia okna przestoju tylko w celu migrowania danych Big Data na platformę Azure. Kontynuuj działanie aplikacji, infrastruktury analitycznej, zadań pozyskiwania i innych procesów przetwarzania.

- **Weryfikuj wyniki**: kompleksowe sprawdzanie poprawności, aby dane mogły być używane efektywnie po migracji na platformę Azure, wymagają uruchomienia dla nich obciążeń aplikacji produkcyjnych. Tylko usługa LiveData zapewnia to bez konieczności ryzyka rozbieżności danych, zapewniając spójność danych bez względu na to, czy zmiany są wykonywane w lokalizacji źródłowej, czy docelowej migracji. Testowanie i weryfikowanie zachowania aplikacji bez ryzyka lub zmiany w procesach i systemach.

- **Zmniejsz złożoność**: eliminuje konieczność tworzenia zaplanowanych zadań i zarządzania nimi w celu kopiowania danych przez Migrowanie danych za pomocą automatyzacji. Głębokiej integracji z platformą Azure można używać jako płaszczyzny kontroli w celu zarządzania postępem migracji oraz monitorowania go, w tym selektywnej replikacji danych, metadanych programu Hive, bezpieczeństwa danych i poufności.

- **Wydajność**: Zachowaj wysoką przepływność i wydajność oraz łatwo Skaluj do woluminów danych Big Data. Przy kontroli zużycia przepustowości można zagwarantować, że można osiągnąć cele migracji bez wpływu na inne operacje systemu.

## <a name="migrate-big-data-faster-without-risk"></a>Szybsze Migrowanie danych Big Data bez ryzyka

Pierwsza usługa platformy WANdisco LiveData dla platformy Azure to [LiveData Migrator dla platformy Azure](https://www.wandisco.com/products/livedata-migrator-for-azure). rozwiązanie do migrowania aktywnie używanych danych ze środowisk lokalnych do usługi Azure Storage. LiveData Migrator for Azure jest inicjowany i zarządzany całkowicie z Azure Portal lub interfejsu wiersza polecenia platformy Azure i działa razem z lokalnym klastrem usługi Hadoop bez żadnej zmiany konfiguracji, modyfikacji aplikacji ani ponownego uruchamiania usługi, aby rozpocząć migrację danych od razu.

> [!div class="mx-imgBorder"]
> ![LiveData Migrator dla architektury platformy Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

Migracje danych Big Data mogą być złożone i trudne. Nie jest możliwe przechodzenie petabajtów informacji bez zakłócania operacji działania biznesowego za pomocą technologii kopiowania danych w trybie offline. [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) oferuje proste wdrożenie i może nawiązywać usługę LiveData z ciągłą migracją danych i replikacją, podczas gdy aplikacje odczytują, zapisują i modyfikują migrowane dane.

Migracja jest tak prosta, jak te trzy kroki:

1. Zainicjuj obsługę wystąpienia Migrator LiveData z Azure Portal w lokalnym klastrze usługi Hadoop. Nie trzeba zmieniać ani przestoju klastra, a aplikacje mogą nadal działać.

   > [!div class="mx-imgBorder"]
   >![Tworzenie wystąpienia Migrator LiveData](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Zdefiniuj docelowe konto magazynu z włączonym Azure Data Lake Storage Gen2.

   > [!div class="mx-imgBorder"]
   >![Utwórz element docelowy Migrator LiveData](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Zdefiniuj lokalizację danych, które chcesz zmigrować, na przykład: `/user/hive/warehouse` , i Rozpocznij migrację.

   > [!div class="mx-imgBorder"]
   > ![Tworzenie migracji LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Monitoruj postęp migracji za pomocą standardowych narzędzi platformy Azure, w tym interfejsu wiersza polecenia platformy Azure i Azure Portal i Kontynuuj używanie środowiska lokalnego w programie. Przed rozpoczęciem należy przejrzeć te [wymagania wstępne](https://docs.wandisco.com/live-data-platform/docs/prereq/).

## <a name="replicate-data-under-active-change"></a>Replikowanie danych w ramach aktywnej zmiany

Migracje na dużą skalę lokalnych jezior danych do platformy Azure wymagają testowania i weryfikacji aplikacji. Można to zrobić bez ryzyka wprowadzenia zmian danych, które spowodują utworzenie wielu źródeł prawdy, które nie mogą być łatwo uzgodnione, mają kluczowe znaczenie dla wyeliminowania ryzyka i zminimalizowania kosztów przejścia na platformę Azure. [Płaszczyzna LiveData dla platformy Azure](https://www.wandisco.com/products/livedata-plane-for-azure) używa technologii aparatu koordynacji WANdisco w celu pokonania tych problemów.

> [!div class="mx-imgBorder"]
> ![Płaszczyzna LiveData dla architektury platformy Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Zachowaj spójność danych w lokalnych klastrach Hadoop i usłudze Azure Storage z płaszczyzną LiveData dla platformy Azure po migracji początkowej:

1. Udostępniaj płaszczyznę LiveData dla platformy Azure lokalnie i na platformie Azure, rozpoczynając od Azure Portal. Nie są wymagane żadne zmiany aplikacji.
2. Skonfiguruj reguły replikacji obejmujące te lokalizacje danych, które chcesz zachować spójność, na przykład: `/user/contoso/sales/region/WA` .
3. Uruchamiaj aplikacje, które uzyskują dostęp do danych w dowolnej lokalizacji jako system plików zgodny z usługą Hadoop i modyfikuje je.

Płaszczyzna LiveData na platformie Azure zapewnia spójność danych bez nakładania znaczących obciążeń na operacje klastra lub wydajność aplikacji. Modyfikuj lub Pozyskaj dane, podczas gdy wszystkie zmiany są replikowane spójnie.

## <a name="next-steps"></a>Następne kroki

- [Platforma LiveData dla](https://docs.wandisco.com/live-data-platform/docs/landing/) platformy Azure dla platformy Azure jest używana jak każdy inny zasób platformy Azure i jest teraz dostępna w wersji zapoznawczej. 

- Zapoznaj się z [wymaganiami wstępnymi](https://docs.wandisco.com/live-data-platform/docs/prereq/), zaplanuj migrację i przeprowadź szybką migrację na dużą skalę dzięki LiveData Migrator dla platformy Azure.

- Wypróbuj Migrator LiveData bez konieczności posiadania lokalnego klastra Hadoop przy użyciu [piaskownicy](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/)systemu plików HDFS.

## <a name="see-also"></a>Zobacz także

- [LiveData Migrator for Azure w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Płaszczyzna LiveData dla platformy Azure w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData Migrator dla planów i cen platformy Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [Płaszczyzna LiveData dla planów i cen platformy Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [Platforma LiveData dla platformy Azure — często zadawane pytania](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Znane problemy z platformą LiveData dla platformy Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Wprowadzenie do usługi Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)