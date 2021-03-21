---
title: Migrowanie kont magazynu StorSimple Device Manager, subskrypcje
description: Dowiedz się, jak migrować subskrypcje, konta magazynu dla usługi StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 523788dc727fc5fa32b2f57250fb738352ff6d31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491524"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrowanie subskrypcji i kont magazynu skojarzonych z usługą StorSimple Device Manager

Może być konieczne przeniesienie usługi StorSimple na nową rejestrację lub nową subskrypcję. Te scenariusze migracji to zmiany konta lub zmiany centrum danych. Skorzystaj z poniższej tabeli, aby zrozumieć, które z tych scenariuszy są obsługiwane, w tym szczegółowe kroki do przeniesienia.

## <a name="account-changes"></a>Zmiany konta

| Czy można przenieść...| Obsługiwane| Downtime (Przestoje)| Proces pomocy technicznej platformy Azure| Podejście|
|-----|-----|-----|-----|-----|
| Cała subskrypcja (obejmuje konta usług StorSimple i magazynu) do innej rejestracji? | Tak       | Nie       | **Transfer rejestracji**<br>Używanych<li>Zakup nowego zobowiązania platformy Azure w ramach nowej umowy.</li><li>Chcesz migrować wszystkie konta i subskrypcje ze starej rejestracji do nowej. Obejmuje to wszystkie usługi platformy Azure w ramach starej subskrypcji.</li> | **Krok 1. Otwieranie biletu pomocy technicznej platformy Azure Enterprise.**<li>Przejdź do witryny [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li> Wybierz pozycję **Administracja rejestracji** , a następnie wybierz pozycję **transfer z jednej rejestracji do nowej rejestracji**.<br>**Krok 2. podanie żądanych informacji**<br>Być<li>Numer rejestracji źródła</li><li> Numer rejestracji docelowej</li><li>Data wprowadzenia przeniesienia|
| StorSimple usługę z istniejącego konta do nowej rejestracji?    | Tak       | Nie       | **Transfer konta**<br>Używanych<li>Jeśli nie chcesz, aby przetransferować pełną rejestrację.</li><li>Chcesz przenieść określone konta tylko do nowej rejestracji.</li>| **Krok 1. Otwieranie biletu pomocy technicznej platformy Azure Enterprise.**<li>Przejdź do witryny [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li>Wybierz pozycję **Administracja rejestracji** , a następnie wybierz pozycję **Przenieś konto EA do nowej rejestracji**.<br>**Krok 2. podanie żądanych informacji**<br>Być<li>Numer rejestracji źródła</li><li> Numer rejestracji docelowej</li><li>Data wprowadzenia przeniesienia|
| StorSimple usługę z jednej subskrypcji do innej subskrypcji?      | Nie        |    Tak         | Brak, proces ręczny|<li>Migruj dane z urządzenia StorSimple.</li><li>Przeprowadzenie resetowania urządzenia do ustawień fabrycznych spowoduje usunięcie wszystkich danych lokalnych na urządzeniu.</li><li>Zarejestruj urządzenie w nowej subskrypcji w usłudze StorSimple Device Manager.</li><li>Przeprowadź migrację danych z powrotem do urządzenia.|
|Czy mogę przenieść własność subskrypcji platformy Azure do innego katalogu? | Tak       | Nie       | Kojarzenie istniejącej subskrypcji z katalogiem usługi Azure AD | Zapoznaj się z tematem [kojarzenie istniejącej subskrypcji z katalogiem usługi Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Może upłynąć do 10 minut, zanim wszystko zostanie poprawnie wyświetlone.|
| StorSimple urządzenie z jednej usługi StorSimple Device Manager do innej usługi w innym regionie?      | Nie        | Tak            | Brak, proces ręczny |Jak wyżej.|
| Czy konto magazynu ma nową subskrypcję lub grupę zasobów?     | Tak        | Nie             |Przenieś konto magazynu do innej subskrypcji lub grupy zasobów |Po przeniesieniu, jeśli klucze dostępu do konta magazynu zostaną zaktualizowane, użytkownik będzie musiał ręcznie skonfigurować klucze dostępu dla zmigrowanego konta magazynu za pomocą usługi StorSimple Device Manager.|
| Klasyczne konto magazynu na konto magazynu Azure Resource Manager      | Tak        | Nie             |Migrowanie z wersji klasycznej do usługi Azure Resource Manager |<li>Aby uzyskać szczegółowe instrukcje dotyczące migracji konta magazynu z klasycznego do Azure Resource Manager, przejdź do [obszaru Migrowanie klasycznego konta magazynu](../virtual-machines/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account).</li><li> Jeśli klucze dostępu do konta magazynu zostaną zaktualizowane po migracji, użytkownik będzie musiał zsynchronizować klucze dostępu dla zmigrowanego konta magazynu za pomocą usługi StorSimple Device Manager. Ma to na celu zapewnienie, że urządzenia StorSimple nadal działają normalnie i umożliwiają warstwy podstawowe/zapasowe danych na platformie Azure. Aby uzyskać szczegółowe instrukcje dotyczące synchronizowania kluczy dostępu, przejdź do obszaru [rotacja przepływu pracy](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> W przypadku urządzenia w chmurze StorSimple, jeśli klasyczne konto magazynu zostanie zmigrowane, ale źródłowa maszyna wirtualna nadal pozostaje w wersji klasycznej, urządzenie powinno działać prawidłowo. W przypadku migrowania podstawowej maszyny wirtualnej dla urządzenia w chmurze funkcja Dezaktywuj i Usuń nie będzie działać.</li><li> W Azure Portal należy utworzyć nowe urządzenia w chmurze StorSimple, a następnie przełączyć je w tryb failover ze starszych urządzeń w chmurze. Nie można utworzyć urządzenia w chmurze StorSimple w nowym Azure Portal przy użyciu klasycznego konta magazynu, które muszą mieć konto usługi Azure Resource Manager Storage. Aby uzyskać więcej informacji, przejdź do [wdrażania urządzenia w chmurze StorSimple i zarządzania nim](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Zmiany centrum danych

| Czy można przenieść...| Obsługiwane|Downtime (Przestoje)| Proces pomocy technicznej platformy Azure| Podejście|
|-----|-----|-----|-----|-----|
| Usługa StorSimple z jednego centrum danych platformy Azure na inną? | Nie | Tak |Brak, proces ręczny  |<li>Migruj dane z urządzenia StorSimple.</li><li>Przeprowadzenie resetowania urządzenia do ustawień fabrycznych spowoduje usunięcie wszystkich danych lokalnych na urządzeniu.</li><li>Zarejestruj urządzenie z nową subskrypcją w nowej subskrypcji usługi StorSimple Device Manager.</li><li>Przeprowadź migrację danych z powrotem do urządzenia.|
| Konto magazynu z jednego centrum danych platformy Azure na inne? | Nie |Tak  |Brak, proces ręczny  | Jak wyżej.|

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie usługi StorSimple Device Manager](storsimple-8000-manage-service.md)
* [Wdrażanie urządzenia z serii StorSimple 8000 w Azure Portal](storsimple-8000-deployment-walkthrough-u2.md)