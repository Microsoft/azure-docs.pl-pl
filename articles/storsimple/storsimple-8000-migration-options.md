---
title: Opcje migracji danych z urządzeń z serii StorSimple 5000-7000
description: Zawiera omówienie opcji migracji danych z serii StorSimple 5000-7000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2386a7f03508be2a3e6c57111ae206807efaf3ed
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567869"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opcje migracji danych z urządzeń StorSimple serii 5000-7000 

> [!IMPORTANT]
> 9 lipca 2019 seria StorSimple 5000/7000 osiągnie status końca wsparcia (EOS). Firma Microsoft zaleca, aby klienci z serii StorSimple 5000/7000 migrowani do jednej z wariantów opisanych w dokumencie.

Seria StorSimple 5000-7000 zbliża się do [końca wsparcia](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) w lipcu 2019. Klienci, którzy korzystają z serii StorSimple 5000-7000, mają możliwość uaktualnienia do innych usług hybrydowych platformy Azure w pierwszej kolejności. W tym artykule opisano opcje hybrydowe platformy Azure dostępne do migrowania danych. 

## <a name="migration-options"></a>Opcje migracji

Klienci korzystający z serii StorSimple 5000-7000 mają opcje platformy Azure lub innych firm.

### <a name="azure-options"></a>Opcje platformy Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Uaktualnianie do serii StorSimple 8000

Uaktualnij do serii StorSimple 8000 i w ten sposób Kontynuuj na platformie StorSimple.  Ta ścieżka uaktualnienia wymaga od klientów zastępowania urządzeń z serii 5000-7000 z serii 8000. Dane są migrowane z urządzenia z serii 5000-7000 przy użyciu narzędzia migracji. Po pomyślnym zakończeniu migracji urządzenia z serii StorSimple 8000 będą kontynuowały korzystanie z danych warstwy na platformie Azure Blob Storage. 

Aby uzyskać więcej informacji na temat migrowania danych przy użyciu serii StorSimple 8000, przejdź do obszaru [Migrowanie danych z serii StorSimple 5000-7000 do urządzeń z serii 8000](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migracja do usługi Azure File Sync

Ta nowa opcja migracji umożliwia klientom przechowywanie udziałów plików w organizacji w Azure Files. Te udziały plików są następnie scentralizowane dla dostępu lokalnego przy użyciu Azure File Sync (AFS). Serwer AFS można wdrożyć na hoście z systemem Windows Server. Rzeczywista migracja danych jest następnie wykonywana jako kopia hosta lub narzędzie do migracji.

Aby uzyskać więcej informacji na temat migrowania danych do Azure File Sync, przejdź do obszaru [Migrowanie danych z serii StorSimple 5000-7000 do Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Opcje innych firm

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrowanie do Panzura wolności NAS

Klienci z StorSimple 5000-7000 mogą zdecydować się na migrację do Panzura swobody NAS, aby zachować swoje dane na platformie Azure. Rozwiązanie Panzura wolności udostępnia rozwiązanie NAS, które obejmuje centra danych, biura i chmurę publiczną i prywatną. Rozwiązanie umożliwia wykonywanie lokalnych, hybrydowych i w chmurze przepływów danych dla klientów systemu plików NFS, SMB i urządzeń przenośnych. 

Ta migracja jest obsługiwana przez Panzura i klienci mogą rozpocząć pracę, żądając obsługi migracji z [witryny sieci Web Panzura](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Migrowanie do Cohesity

Cohesity umożliwia Migrowanie danych z bieżącego StorSimple 5000 – 7000 do platformy danych Cohesity na platformie Azure. Cohesity Data Platform to zdefiniowane przez oprogramowanie rozwiązanie sieci Web, które umożliwia konsolidowanie plików, kopii zapasowych, obiektów i maszyn wirtualnych w ramach jednego rozwiązania natywnego w chmurze. Po przeprowadzeniu migracji na platformę danych możesz zarządzać danymi i aplikacjami w chmurze oraz udostępniać je na rdzeńch za pomocą jednego okienka ze szkła. W programie Cohesity Rozpocznij od najmniejszej liczby węzłów. 

Dowiedz się więcej na temat [migracji do platformy danych Cohesity](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrowanie do nasuni

Nasuni ułatwia klientom StorSimple 5000-7000 migrowanie i przechowywanie danych na platformie Azure.  Nasuni to wiodące rozwiązanie magazynu NAS oparte na platformie Azure, dzięki czemu klienci mogą uzyskać wydajność i bezpieczeństwo, które oczekują od rozwiązań Premium, dzięki ekonomiom i skalowaniu w chmurze.  Oprócz magazynu plików o wysokiej wydajności nasuni i Azure obsługują kopie zapasowe i odzyskiwanie po awarii, a jednocześnie umożliwia udostępnianie i współpracę nad danymi na całym świecie przy użyciu scentralizowanego zarządzania magazynem plików. 

Nasuni ma doświadczenie w upraszczaniu migracji — Zacznij już dzisiaj: https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Przeprowadź migrację do Talon FAST

Talon ułatwia klientom StorSimple 5000-7000 korzystanie z korzyści, które są przez nich wyceniane na platformie StorSimple (w małych lokacjach na podstawie nieograniczonego poziomu zasobów w chmurze) i jeszcze większej funkcji.  Dzięki SZYBKIEmu rozwiązaniu Talon klienci mogą migrować i przechowywać swoje dane na platformie Azure, jednocześnie mając nawet mniejsze oprogramowanie — zarówno w miejscu, jak i w ramach globalnego blokowania plików, globalnej przestrzeni nazw i współpracy obejmującej wiele lokacji.  Talon to wiodące rozwiązanie ekosystemu platformy Azure, które współpracuje z klientami globalnymi w celu migrowania lokalnych obciążeń serwera plików do skonsolidowanych zasobów opartych na platformie Azure bez wpływu na przepływy pracy użytkownika lub środowiska.  

Dowiedz się więcej na temat sposobu ewolucji w przedsiębiorstwie skonsolidowanym w chmurze.

## <a name="migration---frequently-asked-questions"></a>Migracja — często zadawane pytania

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>PYTANIE: Kiedy urządzenia z serii StorSimple 5000 i 7000 osiągną koniec usługi? 

A. Seria StorSimple 5000-7000 osiągnie [koniec usługi](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) w lipcu 2019. Koniec usługi oznacza, że firma Microsoft nie będzie już w stanie zapewnić pomocy technicznej dotyczącej sprzętu i oprogramowania tych urządzeń po lipca 2019. Zdecydowanie zalecamy rozpoczęcie opracowywania planu, aby przeprowadzić migrację danych z urządzeń teraz.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>PYTANIE: Co się dzieje z danymi przechowywanymi na platformie Azure?  

A. Po przeprowadzeniu migracji do nowszej usługi można nadal korzystać z danych na platformie Azure. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>PYTANIE: Co się dzieje z danymi przechowywanymi lokalnie na moim urządzeniu StorSimple? 

A. Dane znajdujące się na urządzeniu lokalnym można skopiować do nowszej usługi zgodnie z opisem w dokumentach migracji.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>PYTANIE: Co się stanie, jeśli chcę utrzymać urządzenie z serii StorSimple 5000/7000? 

A. Chociaż usługi mogą nadal działać, firma Microsoft nie będzie już w stanie zapewnić obsługi sprzętu i oprogramowania. Migracja jest silnie zalecana dla ciągłości działania.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>PYTANIE: Jakie opcje są dostępne w przypadku migrowania danych z urządzeń z serii StorSimple 5000-7000? 

A. W zależności od ich scenariusza użytkownicy z serii StorSimple 5000-7000 mają następujące opcje migracji. 

 - **Uaktualnij do serii 8000**: Użyj tej opcji, jeśli chcesz kontynuować na platformie StorSimple. 
 - **Migruj do Azure File Sync**: Użyj tej opcji, jeśli chcesz przełączyć do formatu natywnego platformy Azure. Azure File Sync służy do scentralizowanego zarządzania udziałami plików. 

Możesz skontaktować się z pomoc techniczna firmy Microsoft, aby omówić opcje migracji, które nie są wyświetlane w tym miejscu.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>PYTANIE: Czy migracja do innych rozwiązań magazynu jest obsługiwana?

A. Tak. Obsługiwana jest migracja do innych rozwiązań magazynu przy użyciu kopii danych hosta.

### <a name="q-is-migration-supported-by-microsoft"></a>PYTANIE: Czy migracja jest obsługiwana przez firmę Microsoft? 

A. Migrowanie z serii 5000 lub 7000 jest w pełni obsługiwaną operacją. W rzeczywistości firma Microsoft zaleca, aby uzyskać pomoc techniczną przed rozpoczęciem migracji. Migracja jest obecnie asystowaną operacją. Jeśli planujesz migrację danych z urządzenia z serii StorSimple 5000-7000, [Otwórz bilet pomocy technicznej](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>PYTANIE: Jaki jest model cenowy dla obu opcji migracji?

A. Koszt migracji różni się w zależności od wybranej opcji. Gdy migracja jest bezpłatna, w przypadku podjęcia decyzji o przeprowadzeniu uaktualnienia do serii StorSimple 8000 będzie to koszt urządzenia sprzętowego. 

Podobnie w przypadku korzystania z Azure File Sync opłaty za subskrypcję usługi mogą być stosowane. W każdym przypadku klienci będą musieli również płacić bieżące koszty magazynowania. Zapoznaj się z poniższymi w celu oszacowania: 
- [Cennik StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Cennik AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>PYTANIE:  Jak długo trwa przeprowadzenie migracji?

A. Czas migracji danych zależy od ilości danych i wybranej opcji uaktualniania. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>PYTANIE: Co to jest koniec dnia pomocy technicznej dla serii StorSimple 8000?

A. Data zakończenia obsługi serii StorSimple 8000 została opublikowana w [tym miejscu](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Następne kroki
 - [Migruj dane z serii StorSimple 5000-7000 do urządzenia z serii 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrowanie danych z serii StorSimple 5000-7000 do Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md)