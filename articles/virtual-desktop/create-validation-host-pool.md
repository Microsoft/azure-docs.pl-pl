---
title: Aktualizacje usługi puli hostów usług pulpitu wirtualnego systemu Windows — Azure
description: Jak utworzyć pulę hostów weryfikacji do monitorowania aktualizacji usługi przed przeprowadzeniem aktualizacji do środowiska produkcyjnego.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8eac40ad958a10b8c853304ee2be8b2dc27af1a2
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008716"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Samouczek: Tworzenie puli hostów w celu sprawdzenia poprawności aktualizacji usługi

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

Pule hostów są kolekcją co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy usług pulpitu wirtualnego systemu Windows. Przed wdrożeniem pul hostów w środowisku produkcyjnym zdecydowanie zalecamy utworzenie puli hostów weryfikacji. Aktualizacje są stosowane najpierw w przypadku pul hostów weryfikacji, umożliwiając monitorowanie aktualizacji usługi przed ich wycofaniem do środowiska produkcyjnego. Bez puli hostów weryfikacji nie można wykryć zmian wprowadzających błędy, co może spowodować przestoje dla użytkowników w środowisku produkcyjnym.

Aby zapewnić, że aplikacje będą działać z najnowszymi aktualizacjami, Pula hostów weryfikacji powinna być podobna do pul hostów w środowisku produkcyjnym, jak to możliwe. Użytkownicy powinni często łączyć się z pulą hostów weryfikacji, tak jak w przypadku puli hostów produkcyjnych. W przypadku automatycznego testowania w puli hostów należy uwzględnić automatyczne testowanie w puli hostów weryfikacji.

Problemy w puli hostów weryfikacji można debugować za pomocą [funkcji diagnostyki](diagnostics-role-service.md) lub [artykułów do rozwiązywania problemów z pulpitem wirtualnym systemu Windows](troubleshoot-set-up-overview.md).

>[!NOTE]
> Zaleca się pozostawienie puli hostów weryfikacji w celu przetestowania wszystkich przyszłych aktualizacji.

>[!IMPORTANT]
>Integracja z systemem Windows Virtual Desktop z integracją usługi Azure Resource Management aktualnie ma problemy z włączaniem i wyłączaniem środowisk walidacji. Po rozwiązaniu problemu będziemy aktualizować ten artykuł.

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
