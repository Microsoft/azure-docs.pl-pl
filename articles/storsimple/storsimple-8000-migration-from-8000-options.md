---
title: Opcje migracji danych z urządzeń z serii StorSimple 8000
description: Zawiera omówienie opcji migracji danych z serii StorSimple 8000.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: alkohli
ms.openlocfilehash: 4839f8211e678f5fc2fb3572c7eaa545fbee6c6c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961196"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Opcje migracji danych z serii StorSimple 8000

> [!IMPORTANT]
> 31 grudnia 2022 seria StorSimple 8000 osiągnie status końca wsparcia (EOS). Firma Microsoft zaleca, aby klienci z serii StorSimple 8000 migrowani do jednej z wariantów opisanych w dokumencie.

Seria StorSimple 8000 zbliża się do [końca wsparcia](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) w grudniu 2022. Klienci, którzy korzystają z serii StorSimple 8000, mają możliwość uaktualnienia do innych usług hybrydowych platformy Azure w pierwszej kolejności. W tym artykule opisano opcje hybrydowe platformy Azure dostępne do migrowania danych.

## <a name="migration-options"></a>Opcje migracji

Klienci korzystający z serii StorSimple 8000 mają opcje platformy Azure lub innych firm.

### <a name="azure-options"></a>Opcje platformy Azure

#### <a name="migrate-to-azure-file-sync"></a>Migracja do usługi Azure File Sync

Ta nowa opcja migracji umożliwia klientom przechowywanie udziałów plików w organizacji w Azure Files. Te udziały plików są następnie scentralizowane dla dostępu lokalnego przy użyciu Azure File Sync (AFS). Serwer AFS można wdrożyć na hoście z systemem Windows Server. Rzeczywista migracja danych jest następnie wykonywana jako kopia hosta lub narzędzie do migracji.

Aby uzyskać więcej informacji na temat migrowania danych do Azure File Sync, przejdź do [StorSimple 8100 i 8600 migracji do Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Opcje innych firm

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrowanie do Panzura wolności NAS

Klienci serii StorSimple 5000-7000 i StorSimple 8000 mogą zdecydować się na migrację do Panzura swobody NAS, aby zachować swoje dane na platformie Azure. Panzura Wolnościing to rozwiązanie NAS, które obejmuje centra danych, biura, chmurę publiczną i prywatną. Rozwiązanie umożliwia wykonywanie lokalnych, hybrydowych i w chmurze przepływów danych dla klientów systemu plików NFS, SMB i urządzeń przenośnych.

Ta migracja jest obsługiwana przez Panzura i klienci mogą rozpocząć pracę, żądając obsługi migracji z [witryny sieci Web Panzura](https://panzura.com/migrate-storsimple-panzura/).

#### <a name="migrate-to-nasuni"></a>Migrowanie do nasuni

Przeniesienie całego środowiska StorSimple na stabilną i bezpieczną platformę usług plików o wysokiej wydajności jest proste z nasuni. Nasuni oferuje zabezpieczenia i wydajność lokalnego magazynu plików podczas łączenia go z skalowalnością i trwałością platformy Azure.  Jako wiodący dostawca niezależnego oprogramowania platformy Azure nasuni udostępnia wszystkie narzędzia niezbędne do przenoszenia danych StorSimple do nowoczesnej platformy umożliwiającej udostępnianie plików i współpracę z nimi w wielu lokalizacjach.

Zacznij już dzisiaj: [nasuni witrynę sieci Web](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migracja — często zadawane pytania

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>PYTANIE: Kiedy urządzenia z serii StorSimple 8000 osiągnęły koniec usługi?

A. Seria StorSimple 8000 osiągnie [koniec wsparcia](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) w grudniu 2022. Koniec pomocy technicznej oznacza, że firma Microsoft nie będzie już mogła zapewniać pomocy technicznej dotyczącej sprzętu i oprogramowania tych urządzeń po 2022 grudnia. Zdecydowanie zalecamy rozpoczęcie opracowywania planu, aby przeprowadzić migrację danych z urządzeń teraz.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>PYTANIE: Co się dzieje z danymi przechowywanymi na platformie Azure?  

A. Po przeprowadzeniu migracji do nowszej usługi można nadal korzystać z danych na platformie Azure.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>PYTANIE: Co się dzieje z danymi przechowywanymi lokalnie na moim urządzeniu StorSimple?

A. Dane znajdujące się na urządzeniu lokalnym można skopiować do nowszej usługi zgodnie z opisem w dokumentach migracji.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>PYTANIE: Co się stanie, jeśli chcę utrzymać urządzenie z serii StorSimple 8000?

A. Chociaż usługi mogą nadal działać, firma Microsoft nie będzie już w stanie zapewnić obsługi sprzętu i oprogramowania. Migracja jest silnie zalecana dla ciągłości działania.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>PYTANIE: Jakie opcje są dostępne w przypadku migrowania danych z urządzeń z serii StorSimple 8000?

A. W zależności od ich scenariusza użytkownicy z serii StorSimple 8000 mają następujące opcje migracji:

* **Migruj do Azure File Sync**: Użyj tej opcji, jeśli chcesz przełączyć do formatu natywnego platformy Azure. Azure File Sync służy do scentralizowanego zarządzania udziałami plików.

* **Inne opcje**: można skontaktować się z pomoc techniczna firmy Microsoft, aby omówić opcje migracji, które nie są wyświetlane w tym miejscu.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>PYTANIE: Czy migracja do innych rozwiązań magazynu jest obsługiwana?

A. Tak. Obsługiwana jest migracja do innych rozwiązań magazynu przy użyciu kopii danych hosta.

### <a name="q-is-migration-supported-by-microsoft"></a>PYTANIE: Czy migracja jest obsługiwana przez firmę Microsoft?

A. Migrowanie z serii 8000 jest w pełni obsługiwaną operacją. W rzeczywistości firma Microsoft zaleca, aby uzyskać pomoc techniczną przed rozpoczęciem migracji. Migracja jest obecnie asystowaną operacją. Jeśli planujesz migrację danych z urządzenia z serii StorSimple 8000, [skontaktuj się z pomocą techniczną StorSimple](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>PYTANIE: Jaki jest model cen migracji do Azure File Sync?

A. W przypadku korzystania z Azure File Sync opłaty za subskrypcję usługi mogą być stosowane. Klienci będą również musieli płacić bieżące koszty magazynowania. Zapoznaj się z [cennikiem platformy AFS]( https://azure.microsoft.com/pricing/details/storage/files/) , aby oszacować.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>PYTANIE: Jak długo trwa przeprowadzenie migracji?

A. Czas migracji danych zależy od ilości danych i wybranej opcji uaktualniania.

## <a name="next-steps"></a>Następne kroki

* [Migrowanie danych z serii StorSimple 8000 do Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md)