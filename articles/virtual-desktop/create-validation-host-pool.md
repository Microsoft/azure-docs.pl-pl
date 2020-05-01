---
title: Aktualizacje usługi puli hostów usług pulpitu wirtualnego systemu Windows — Azure
description: Jak utworzyć pulę hostów weryfikacji do monitorowania aktualizacji usługi przed przeprowadzeniem aktualizacji do środowiska produkcyjnego.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b74b7f0b79ad4064d7133a19316d6aec6bd5ba3a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611572"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Samouczek: Tworzenie puli hostów w celu sprawdzenia poprawności aktualizacji usługi

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pule hostów są kolekcją co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy usług pulpitu wirtualnego systemu Windows. Przed wdrożeniem pul hostów w środowisku produkcyjnym zdecydowanie zalecamy utworzenie puli hostów weryfikacji. Aktualizacje są stosowane najpierw w przypadku pul hostów weryfikacji, umożliwiając monitorowanie aktualizacji usługi przed ich wycofaniem do środowiska produkcyjnego. Bez puli hostów weryfikacji nie można wykryć zmian wprowadzających błędy, co może spowodować przestoje dla użytkowników w środowisku produkcyjnym.

Aby zapewnić, że aplikacje będą działać z najnowszymi aktualizacjami, Pula hostów weryfikacji powinna być podobna do pul hostów w środowisku produkcyjnym, jak to możliwe. Użytkownicy powinni często łączyć się z pulą hostów weryfikacji, tak jak w przypadku puli hostów produkcyjnych. W przypadku automatycznego testowania w puli hostów należy uwzględnić automatyczne testowanie w puli hostów weryfikacji.

Problemy w puli hostów weryfikacji można debugować za pomocą [funkcji diagnostyki](diagnostics-role-service.md) lub [artykułów do rozwiązywania problemów z pulpitem wirtualnym systemu Windows](troubleshoot-set-up-overview.md).

>[!NOTE]
> Zaleca się pozostawienie puli hostów weryfikacji w celu przetestowania wszystkich przyszłych aktualizacji.

>[!IMPORTANT]
>Wersja 2020 systemu Windows Virtual Desktop wiosną aktualnie ma problemy z włączeniem i wyłączeniem środowiska sprawdzania poprawności. Po rozwiązaniu problemu będziemy aktualizować ten artykuł.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem postępuj zgodnie z instrukcjami w temacie [Konfigurowanie modułu PowerShell pulpitu wirtualnego systemu Windows](powershell-module.md) w celu skonfigurowania modułu programu PowerShell i zalogowania się na platformie Azure.

## <a name="create-your-host-pool"></a>Tworzenie puli hostów

Pulę hostów można utworzyć, postępując zgodnie z instrukcjami zawartymi w poniższych artykułach:
- [Samouczek: Tworzenie puli hostów za pomocą witryny Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definiowanie puli hostów jako puli hostów weryfikacji

Uruchom następujące polecenia cmdlet programu PowerShell, aby zdefiniować nową pulę hostów jako pulę hostów weryfikacji. Zastąp wartości w nawiasach wartościami istotnymi dla danej sesji:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Uruchom następujące polecenie cmdlet programu PowerShell, aby upewnić się, że właściwość walidacji została ustawiona. Zastąp wartości w nawiasach wartościami istotnymi dla danej sesji.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Wyniki polecenia cmdlet powinny wyglądać podobnie do tych danych wyjściowych:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>Harmonogram aktualizacji

Aktualizacje usługi są wykonywane co miesiąc. W przypadku wystąpienia poważnych problemów aktualizacje krytyczne będą udostępniane w krótszym tempie.

Jeśli istnieją jakieś aktualizacje usługi, upewnij się, że masz co najmniej małą grupę użytkowników, którzy logują się codziennie, aby sprawdzić poprawność środowiska. Zalecamy regularne zapoznanie się z naszą [witryną usługi TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) i korzystanie z dowolnych wpisów z WVDUPdate, aby otrzymywać informacje o aktualizacjach usług.

## <a name="next-steps"></a>Następne kroki

Teraz, po utworzeniu puli hostów weryfikacji, można dowiedzieć się, jak używać Azure Service Health do monitorowania wdrożenia pulpitu wirtualnego systemu Windows. 

> [!div class="nextstepaction"]
> [Konfigurowanie alertów usługi](./set-up-service-alerts.md)
