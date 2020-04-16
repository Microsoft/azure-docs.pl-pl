---
title: Migrowanie danych z usługi Data Lake i magazynu danych na platformę Azure
description: Użyj usługi Azure Data Factory, aby migrować dane z usługi Data Lake i magazynu danych na platformę Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4408546c892299e5bbbc22b00a4b334c36eda616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416430"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Użyj usługi Azure Data Factory do migracji danych z usługi Data Lake lub magazynu danych na platformę Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Jeśli chcesz przeprowadzić migrację usługi Data Lake lub magazynu danych przedsiębiorstwa (EDW) do platformy Microsoft Azure, rozważ użycie usługi Azure Data Factory. Usługa Azure Data Factory doskonale nadaje się do następujących scenariuszy:

- Migracja obciążenia dużych zbiorów danych z usługi Amazon Simple Storage Service (Amazon S3) lub lokalnego systemu plików rozproszonych (HDFS) firmy Hadoop na platformę Azure
- Migracja EDW z Oracle Exadata, Netezza, Teradata lub Amazon Redshift na platformę Azure

Usługa Azure Data Factory może przenosić petabajty (PB) danych do migracji usługi data lake i dziesiątki terabajtów (TB) danych do migracji magazynu danych.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Dlaczego usługa Azure Data Factory może być używana do migracji danych

- Usługa Azure Data Factory może łatwo skalować ilość mocy obliczeniowej, aby przenosić dane w sposób bezserwerowy o wysokiej wydajności, odporności i skalowalności. I płacisz tylko za to, czego używasz. Należy również zwrócić uwagę na następujące kwestie: 
  - Usługa Azure Data Factory nie ma żadnych ograniczeń dotyczących ilości danych ani liczby plików.
  - Usługa Azure Data Factory może w pełni korzystać z przepustowości sieci i magazynu, aby osiągnąć największą przepustowość przepływu przepływu danych w danym środowisku.
  - Usługa Azure Data Factory używa metody płatności zgodnie z rzeczywistym użyciem, dzięki czemu płacisz tylko za czas, którego faktycznie używasz do uruchomienia migracji danych na platformę Azure.  
- Usługa Azure Data Factory może wykonywać zarówno jednorazowe obciążenie historyczne, jak i zaplanowane obciążenia przyrostowe.
- Usługa Azure Data Factory używa środowiska uruchomieniowego integracji platformy Azure (IR) do przenoszenia danych między publicznie dostępnym punktem końcowym danych a punktami końcowymi magazynu. Można również użyć samodzielnie hostowanego podczerwona do przenoszenia danych dla punktu końcowego usługi Data Lake i magazynu wewnątrz sieci wirtualnej azure (VNet) lub za zaporą.
- Usługa Azure Data Factory ma zabezpieczenia klasy korporacyjnej: można użyć Instalatora Windows (MSI) lub tożsamości usługi do bezpiecznej integracji między usługami lub użyć usługi Azure Key Vault do zarządzania poświadczeniami.
- Usługa Azure Data Factory zapewnia środowisko tworzenia bez kodu i bogaty, wbudowany pulpit nawigacyjny monitorowania.  

## <a name="online-vs-offline-data-migration"></a>Migracja danych online a offline

Usługa Azure Data Factory to standardowe narzędzie do migracji danych online do przesyłania danych za pośrednictwem sieci (internet, ER lub VPN). Podczas gdy w przypadku migracji danych w trybie offline użytkownicy fizycznie wysyłają urządzenia transferu danych z organizacji do centrum danych platformy Azure.  

Istnieją trzy kluczowe zagadnienia dotyczące wyboru między podejściem migracji online i offline:  

- Rozmiar danych do migracji
- Przepustowość sieci
- Okno migracji

Załóżmy na przykład, że planujesz użyć usługi Azure Data Factory, aby zakończyć migrację danych w ciągu dwóch tygodni *(okno migracji).* Zwróć uwagę na różowo-niebieską linię cięcia w poniższej tabeli. Najniższa różowa komórka dla danej kolumny pokazuje parowanie rozmiaru/przepustowości danych/ przepustowości sieci, którego okno migracji jest najbliższe, ale mniej niż dwa tygodnie. (Dowolne parowanie rozmiaru/przepustowości w niebieskiej komórce ma okno migracji online obejmujące więcej niż dwa tygodnie). 

![online vs.](media/data-migration-guidance-overview/online-offline.png) offline Ta tabela pomaga określić, czy można spełnić zamierzone okno migracji za pośrednictwem migracji online (Fabryka danych Azure) na podstawie rozmiaru danych i dostępnej przepustowości sieci. Jeśli okno migracji online trwa dłużej niż dwa tygodnie, warto użyć migracji w trybie offline.

> [!NOTE]
> Korzystając z migracji online, można osiągnąć zarówno wczytywanie danych historycznych, jak i przyrostowe źródła danych end-to-end za pomocą jednego narzędzia.  Dzięki temu podejściu dane mogą być przechowywane synchronizowane między istniejącym magazynem a nowym magazynem podczas całego okna migracji. Oznacza to, że można odbudować logikę ETL w nowym magazynie z odświeżonych danych.


## <a name="next-steps"></a>Następne kroki

- [Migrowanie danych z usług AWS S3 na platformę Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrowanie danych z lokalnego klastra hadoop na platformę Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrowanie danych z lokalnego serwera Netezza na platformę Azure](data-migration-guidance-netezza-azure-sqldw.md)
