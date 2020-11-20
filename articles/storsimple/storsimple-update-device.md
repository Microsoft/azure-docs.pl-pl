---
title: Aktualizowanie urządzenia StorSimple | Microsoft Docs
description: Wyjaśnia, jak używać funkcji aktualizacji StorSimple do instalowania aktualizacji i poprawek w trybie regularnym.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: faf06775b78c5a7c90cea000ac0a1eb768107ef4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956844"
---
# <a name="update-your-storsimple-8000-series-device"></a>Aktualizowanie urządzenia z serii StorSimple 8000
> [!NOTE]
> Klasyczny portal dla urządzeń StorSimple jest przestarzały. Menedżerowie urządzeń StorSimple dokonają automatycznego przeniesienia do nowej witryny Azure Portal zgodnie z ustalonym harmonogramem wycofywania przestarzałych produktów. Powiadomienie o przeniesieniu otrzymasz pocztą e-mail i za pośrednictwem portalu. Ten dokument zostanie wkrótce usunięty. W razie jakichkolwiek pytań dotyczących przeniesienia, zobacz [FAQ: Move to Azure portal (Często zadawane pytania — przeniesienie do witryny Azure Portal)](./index.yml).

## <a name="overview"></a>Omówienie
Funkcje aktualizacji StorSimple umożliwiają łatwe utrzymywanie Aktualności urządzenia StorSimple. W zależności od typu aktualizacji można zastosować aktualizacje do urządzenia za pośrednictwem klasycznego portalu Azure lub za pomocą interfejsu programu Windows PowerShell. W tym samouczku opisano typy aktualizacji i sposób instalowania poszczególnych z nich.

Można stosować dwa typy aktualizacji urządzeń: 

* Aktualizacje regularne (lub normalne)
* Aktualizacje trybu konserwacji

Regularne aktualizacje można instalować za pośrednictwem klasycznego portalu Azure lub programu Windows PowerShell. Aby zainstalować aktualizacje trybu konserwacji, należy jednak użyć środowiska Windows PowerShell. 

Każdy typ aktualizacji jest opisany osobno poniżej.

### <a name="regular-updates"></a>Regularne aktualizacje
Regularne aktualizacje to niezakłócone aktualizacje, które można zainstalować, gdy urządzenie jest w trybie normalnym. Te aktualizacje są stosowane za pomocą witryny sieci Web Microsoft Update do każdego kontrolera urządzenia. 

> [!IMPORTANT]
> Tryb failover kontrolera może wystąpić w trakcie procesu aktualizacji. Nie ma to jednak wpływu na dostępność systemu ani działanie.
> 
> 

* Aby uzyskać szczegółowe informacje na temat sposobu instalowania zwykłych aktualizacji za pośrednictwem klasycznego portalu Azure, zobacz [Instalowanie zwykłych aktualizacji za pośrednictwem klasycznego portalu Azure](#install-regular-updates-via-the-azure-classic-portal).
* Możesz również zainstalować regularne aktualizacje za pośrednictwem program Windows PowerShell dla usługi StorSimple. Aby uzyskać szczegółowe informacje, zobacz [Instalowanie zwykłych aktualizacji za pośrednictwem program Windows PowerShell dla usługi StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Aktualizacje trybu konserwacji
Aktualizacje trybu konserwacji to zakłócenia aktualizacji, takie jak uaktualnienia oprogramowania układowego dysku. Te aktualizacje wymagają przełączenia urządzenia w tryb konserwacji. Aby uzyskać szczegółowe informacje, zobacz [krok 2: wprowadzanie trybu konserwacji](#step2). Nie można użyć klasycznej witryny Azure Portal, aby zainstalować aktualizacje trybu konserwacji. Zamiast tego należy użyć program Windows PowerShell dla usługi StorSimple. 

Aby uzyskać szczegółowe informacje na temat instalowania aktualizacji trybu konserwacji, zobacz [Instalowanie aktualizacji trybu konserwacji za pośrednictwem program Windows PowerShell dla usługi StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Aktualizacje trybu konserwacji należy zastosować osobno dla każdego kontrolera. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Instalowanie zwykłych aktualizacji za pośrednictwem klasycznego portalu Azure
Możesz użyć klasycznej witryny Azure Portal, aby zastosować aktualizacje urządzenia StorSimple.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Instaluj regularne aktualizacje za pośrednictwem program Windows PowerShell dla usługi StorSimple
Alternatywnie można użyć program Windows PowerShell dla usługi StorSimple, aby zastosować aktualizacje regularne (normalne).

> [!IMPORTANT]
> Chociaż można instalować regularne aktualizacje przy użyciu program Windows PowerShell dla usługi StorSimple, zdecydowanie zalecamy zainstalowanie zwykłych aktualizacji za pośrednictwem klasycznego portalu Azure. Począwszy od wersji Update 1, sprawdzanie wstępne zostanie wykonane przed zainstalowaniem aktualizacji z portalu. Te wstępne sprawdzenia zastępują błędy i zapewniają płynne działanie. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Zainstaluj aktualizacje trybu konserwacji za pośrednictwem program Windows PowerShell dla usługi StorSimple
Użyj program Windows PowerShell dla usługi StorSimple, aby zastosować aktualizacje trybu konserwacji na urządzeniu StorSimple. Wszystkie żądania we/wy są wstrzymane w tym trybie. Usługi, takie jak nietrwały dostęp losowy (NVRAM) lub usługa klastrowania, również są zatrzymane. Oba kontrolery są ponownie uruchamiane po wprowadzeniu lub wyjściu z tego trybu. Po zamknięciu tego trybu wszystkie usługi zostaną wznowione i powinny być w dobrej kondycji. (Może to potrwać kilka minut).

Jeśli musisz zastosować aktualizacje trybu konserwacji, otrzymasz alert w klasycznym portalu Azure, że masz aktualizacje, które muszą zostać zainstalowane. Ten alert zawiera instrukcje dotyczące korzystania z program Windows PowerShell dla usługi StorSimple w celu zainstalowania aktualizacji. Po zaktualizowaniu urządzenia Użyj tej samej procedury, aby zmienić tryb na normalny. Aby uzyskać instrukcje krok po kroku, zobacz [krok 4: wyjście z trybu konserwacji](#step4).

> [!IMPORTANT]
> * Przed wprowadzeniem trybu konserwacji Sprawdź, czy oba kontrolery urządzeń są w dobrej kondycji, sprawdzając **stan sprzętu** na stronie **Obsługa** w klasycznym portalu Azure. Jeśli kontroler nie jest w dobrej kondycji, skontaktuj się z firmą pomoc techniczna firmy Microsoft w celu wykonania następnych kroków. Aby uzyskać więcej informacji, przejdź do pomoc techniczna firmy Microsoft kontaktu. 
> * W trybie konserwacji należy najpierw zastosować aktualizację na jednym kontrolerze, a następnie na drugim kontrolerze.
> 
> 

### <a name="step-1-connect-to-the-serial-console"></a>Krok 1. Nawiązywanie połączenia z konsolą szeregową <a name="step1"></a>
Najpierw użyj aplikacji, takiej jak "polecenie", aby uzyskać dostęp do konsoli szeregowej. Poniższa procedura wyjaśnia, jak używać metody instrukcje do nawiązywania połączenia z konsolą szeregową.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode"></a>Krok 2. wprowadzanie trybu konserwacji <a name="step2"></a>
Po nawiązaniu połączenia z konsolą programu Ustal, czy są dostępne aktualizacje do zainstalowania, a następnie przejdź do trybu konserwacji, aby je zainstalować.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates"></a>Krok 3. Instalacja aktualizacji <a name="step3"></a>
Następnie zainstaluj aktualizacje.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode"></a>Krok 4. wyjście z trybu konserwacji <a name="step4"></a>
Na koniec Wyjdź z trybu konserwacji.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Instalowanie poprawek za pomocą program Windows PowerShell dla usługi StorSimple
W przeciwieństwie do aktualizacji Microsoft Azure StorSimple, poprawki są instalowane z folderu udostępnionego. Podobnie jak w przypadku aktualizacji, istnieją dwa typy poprawek: 

* Regularne poprawki 
* Poprawki trybu konserwacji  

Poniższe procedury wyjaśniają, jak używać program Windows PowerShell dla usługi StorSimple do instalowania poprawek zwykłych i w trybie konserwacji.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Co się stanie z aktualizacjami w przypadku przeprowadzenia resetowania urządzenia do ustawień fabrycznych?
Jeśli urządzenie zostanie zresetowane do ustawień fabrycznych, wszystkie aktualizacje zostaną utracone. Po zarejestrowaniu i skonfigurowaniu urządzenia resetowania do ustawień fabrycznych należy ręcznie zainstalować aktualizacje za pomocą klasycznego portalu Azure i/lub program Windows PowerShell dla usługi StorSimple. Aby uzyskać więcej informacji na temat resetowania do ustawień fabrycznych, zobacz [Resetowanie urządzenia na domyślne ustawienia fabryczne](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [korzystaniu z program Windows PowerShell dla usługi StorSimple do administrowania urządzeniem StorSimple](./storsimple-8000-windows-powershell-administration.md).
* Dowiedz się więcej [na temat używania usługi StorSimple Manager do administrowania urządzeniem StorSimple](./storsimple-8000-manager-service-administration.md).