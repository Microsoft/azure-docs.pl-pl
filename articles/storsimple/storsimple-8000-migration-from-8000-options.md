---
title: Opcje migracji danych z urządzeń storsimple serii 8000
description: Zawiera omówienie opcji migracji danych z serii StorSimple 8000.
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: 974bcc657b811a10e28b41150439e83d26a208d2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767056"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Opcje migracji danych z serii StorSimple 8000

> [!IMPORTANT]
> 31 grudnia 2022 roku seria StorSimple 8000 osiągnie status końca wsparcia (EOS). Firma Microsoft zaleca, aby klienci serii StorSimple 8000 migrować do jednej z alternatyw opisanych w dokumencie.

Seria StorSimple 8000 kończy [wsparcie](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) w grudniu 2022 roku. Klienci, którzy korzystają z serii StorSimple 8000 mają możliwość uaktualnienia do innych usług hybrydowych pierwszej firmy platformy Azure. W tym artykule opisano opcje hybrydowe platformy Azure dostępne do migracji danych.

## <a name="migration-options"></a>Opcje migracji

Klienci korzystający z serii StorSimple 8000 mają opcje platformy Azure lub innych firm.

### <a name="azure-options"></a>Opcje platformy Azure

#### <a name="migrate-to-azure-file-sync"></a>Migracja do usługi Azure File Sync

Ta zupełnie nowa opcja migracji umożliwia klientom przechowywanie udziałów plików organizacji w plikach azure. Te udziały plików są następnie scentralizowane dla dostępu lokalnego przy użyciu usługi Azure File Sync (AFS). System AFS można wdrożyć na hoście systemu Windows Server. Rzeczywista migracja danych jest następnie wykonywana jako kopia hosta lub narzędzie migracji.

Aby uzyskać więcej informacji na temat migracji danych do usługi Azure File Sync, przejdź do [migracji StorSimple 8100 i 8600 do usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000).

### <a name="third-party-options"></a>Opcje innych firm

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrowanie do serwera Panzura Freedom NAS

Klienci z serii StorSimple 5000-7000 i StorSimple 8000 mogą zdecydować się na migrację na serwer Panzura Freedom NAS, aby zachować swoje dane na platformie Azure. Rozwiązanie Panzura Freedom to rozwiązanie NAS obejmujące centra danych, biura, chmury publiczne i prywatne. Rozwiązanie umożliwia lokalne, hybrydowe i w chmurze przepływy pracy danych dla nfs, SMB i klientów mobilnych.

Ta migracja jest obsługiwana przez Panzurę, a klienci mogą rozpocząć, prosząc o pomoc w migracji ze [strony internetowej Panzura](https://panzura.com/migrate-storsimple-panzura/).

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

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>PYTANIE: Kiedy urządzenia z serii StorSimple 8000 kończą się?

A. StorSimple serii 8000 osiągnąć [koniec wsparcia](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) w grudniu 2022. Koniec pomocy technicznej oznacza, że firma Microsoft nie będzie już mogła zapewnić pomocy technicznej zarówno dla sprzętu, jak i oprogramowania tych urządzeń po grudniu 2022 roku. Zdecydowanie zaleca się, aby rozpocząć formułowanie planu migracji danych z urządzeń teraz.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>PYTANIE: Co się stanie z danymi przechowywanymi na platformie Azure?  

A. Możesz nadal używać danych na platformie Azure po migracji do nowszej usługi.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>PYTANIE: Co się stanie z danymi przechowywanymi lokalnie na urządzeniu StorSimple?

A. Dane, które znajduje się na urządzeniu lokalnym mogą być kopiowane do nowszej usługi, jak opisano w dokumentach migracji.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>PYTANIE: Co się stanie, jeśli chcę zachować urządzenie z serii StorSimple 8000?

A. Chociaż usługi mogą nadal działać, firma Microsoft nie będzie już mogła zapewnić pomocy technicznej dla sprzętu i oprogramowania. Migracja jest zdecydowanie zalecana dla ciągłości biznesowej.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>PYTANIE: Jakie opcje są dostępne do migracji danych z urządzeń storsimple serii 8000?

A. W zależności od ich scenariusza użytkownicy serii StorSimple 8000 mają następujące opcje migracji:

* **Migruj do usługi Azure File Sync:** Użyj tej opcji, jeśli chcesz przełączyć się na format macierzysty platformy Azure. Za pomocą usługi Azure File Sync można używać do scentralizowanego zarządzania udziałami plików.

* **Inne opcje**: Możesz skontaktować się z pomocą techniczną firmy Microsoft, aby omówić opcje migracji, których nie ma w tym miejscu.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>PYTANIE: Czy migracja do innych rozwiązań magazynu jest obsługiwana?

A. Tak. Migracja do innych rozwiązań magazynu przy użyciu kopii hosta danych jest obsługiwana.

### <a name="q-is-migration-supported-by-microsoft"></a>PYTANIE: Czy migracja jest obsługiwana przez firmę Microsoft?

A. Migracja z serii 8000 jest w pełni obsługiwaną operacją. W rzeczywistości firma Microsoft zaleca dotarcie do pomocy technicznej przed rozpoczęciem migracji. Migracja jest obecnie operacją wspomaganą. Jeśli zamierzasz przeprowadzić migrację danych z urządzenia z serii StorSimple 8000, [skontaktuj się z pomocą techniczną storsimple](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>PYTANIE: Jaki jest model cenowy migracji do usługi Azure File Sync?

A. W przypadku korzystania z usługi Azure File Sync mogą obowiązywać opłaty subskrypcyjne za usługę. Klienci będą również musieli pokryć bieżące koszty magazynowania. Zapoznaj się z [cennikiem AFS,]( https://azure.microsoft.com/pricing/details/storage/files/) aby uzyskać wartość szacunkową.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>PYTANIE: Jak długo trwa ukończenie migracji?

A. Czas migracji danych zależy od ilości danych i wybranej opcji uaktualniania.

## <a name="next-steps"></a>Następne kroki

* [Migrowanie danych z serii StorSimple 8000 do usługi Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
