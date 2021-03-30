---
title: Wskazówki dotyczące odzyskiwania po awarii dla Azure Data Lake Analytics
description: Dowiedz się, jak zaplanować odzyskiwanie po awarii dla kont Azure Data Lake Analytics.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ce5e96bf464eebb9b91e3ffac9312e89df9cbabe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220979"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Wskazówki dotyczące odzyskiwania po awarii dla Azure Data Lake Analytics

Usługa Azure Data Lake Analytics jest usługą zadań analizy na żądanie, która pozwala uprościć analizowanie danych big data. Zamiast wdrażać, konfigurować i dostosowywać sprzęt, możesz pisać zapytania umożliwiające przekształcanie danych i wyodrębniać wartościowe informacje. Usługa analizy może od razu rozpocząć obsługę zadań w dowolnej skali — należy wybrać ustawienie wskazujące potrzebną moc. Płacisz tylko za uruchomione zadanie, dzięki czemu oszczędzasz pieniądze. Ten artykuł zawiera wskazówki dotyczące ochrony zadań przed rzadkimi przypadkami wypadków w całym regionie.

## <a name="disaster-recovery-guidance"></a>Wskazówki dotyczące odzyskiwania po awarii

W przypadku korzystania z Azure Data Lake Analytics najważniejsze jest przygotowanie własnego planu odzyskiwania po awarii. Ten artykuł ułatwia tworzenie planu odzyskiwania po awarii. Istnieją dodatkowe zasoby, które mogą pomóc w tworzeniu własnego planu:
- [Failure and disaster recovery for Azure applications (Awarie i odzyskiwanie po awariach w aplikacjach platformy Azure)](/azure/architecture/reliability/disaster-recovery)
- [Odporność platformy Azure — wskazówki techniczne](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Najlepsze rozwiązania i wskazówki dotyczące scenariusza

Można uruchomić cykliczne zadanie U-SQL na koncie ADLA w regionie, który odczytuje i zapisuje tabele U-SQL, a także dane bez struktury.  Przygotuj się na awarię, wykonując następujące czynności:

1. Utwórz konta ADLA i ADLS w regionie pomocniczym, które będą używane podczas przestoju.

   > [!NOTE]
   > Ponieważ nazwy kont są globalnie unikatowe, użyj spójnego schematu nazewnictwa, który wskazuje, które konto jest pomocnicze.

2. W przypadku danych bez struktury, [wskazówki dotyczące odzyskiwania po awarii dotyczące danych w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. W przypadku danych strukturalnych przechowywanych w tabelach i bazach danych ADLA można tworzyć kopie artefaktów metadanych, takich jak bazy danych, tabele, funkcje z wartościami przechowywanymi w tabeli i zestawy. Należy okresowo ponownie synchronizować te artefakty, gdy nastąpią zmiany w środowisku produkcyjnym. Na przykład nowo wstawione dane są replikowane do regionu pomocniczego przez skopiowanie danych i wstawienie ich do tabeli pomocniczej.

   > [!NOTE]
   > Te nazwy obiektów są objęte zakresem konta pomocniczego i nie są globalnie unikatowe, dlatego mogą mieć takie same nazwy jak na podstawowym koncie produkcyjnym.

Podczas przestoju należy zaktualizować swoje skrypty, aby ścieżki wejściowe wskazywały pomocniczy punkt końcowy. Następnie użytkownicy przesyłają swoje zadania do konta ADLA w regionie pomocniczym. Dane wyjściowe zadania zostaną następnie zazapisywane na koncie ADLA i ADLS w regionie pomocniczym.

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące odzyskiwania po awarii dla danych w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
