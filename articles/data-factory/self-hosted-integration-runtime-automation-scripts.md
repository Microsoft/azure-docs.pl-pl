---
title: Automatyzacja instalacji samoobsługowego środowiska Integration Runtime za pomocą lokalnych skryptów programu PowerShell
description: W celu zautomatyzowania instalacji Integration Runtime samoobsługowego na maszynach lokalnych.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 8cbe54a23cb1c8b55afd86a18b51c0e392c3f78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376211"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Automatyzacja instalacji samoobsługowego środowiska Integration Runtime za pomocą lokalnych skryptów programu PowerShell
Aby zautomatyzować instalację Integration Runtime samoobsługowego na maszynach lokalnych (innych niż maszyny wirtualne platformy Azure, w których możemy wykorzystać szablon Menedżer zasobów), możesz użyć lokalnych skryptów programu PowerShell. W tym artykule wprowadzono dwa skrypty, których można użyć.

## <a name="prerequisites"></a>Wymagania wstępne

* Uruchom program PowerShell na komputerze lokalnym. Aby uruchomić skrypty, należy wybrać **Uruchom jako administrator**.
* [Pobierz](https://www.microsoft.com/download/details.aspx?id=39717) własne oprogramowanie Integration Runtime. Skopiuj ścieżkę, w której znajduje się pobrany plik. 
* Do zarejestrowania własnego środowiska Integration Runtime jest również wymagany **klucz uwierzytelniania** .
* Aby zautomatyzować aktualizacje ręczne, musisz dysponować wstępnie skonfigurowanym środowiskiem Integration Runtime.

## <a name="scripts-introduction"></a>Wprowadzenie do skryptów 

> [!NOTE]
> Skrypty te są tworzone przy użyciu [udokumentowanego narzędzia wiersza polecenia](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell) w ramach własnego środowiska Integration Runtime. Jeśli jest to konieczne, można dostosować te skrypty odpowiednio do potrzeb automatyzacji.
> Skrypty należy zastosować na węzeł, więc upewnij się, że jest on uruchamiany we wszystkich węzłach w przypadku instalacji wysokiej dostępności (co najmniej 2 węzły).

* W celu zautomatyzowania instalacji: Zainstaluj i Zarejestruj nowy węzeł środowisko Integration Runtime (własny) przy użyciu **[InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** — skrypt może służyć do instalowania węzła środowiska Integration Runtime (własny) i rejestrowania go przy użyciu klucza uwierzytelniania. Skrypt akceptuje dwa argumenty, **najpierw** określając lokalizację [własnego środowiska Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) na dysku lokalnym, **drugi** określa **klucz uwierzytelniania** (na potrzeby rejestrowania samodzielnego węzła IR).

* Aby zautomatyzować aktualizacje ręczne: należy zaktualizować samodzielny węzeł IR przy użyciu określonej wersji lub do najnowszej wersji **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** — jest to również obsługiwane na wypadek wyłączenia automatycznej aktualizacji lub chcesz mieć większą kontrolę nad aktualizacjami. Skrypt ten może służyć do aktualizowania węzła samodzielnego środowiska Integration Runtime do najnowszej wersji lub do określonej wyższej wersji (obniżenie poziomu nie działa). Akceptuje on argument służący do określania numeru wersji (przykład:-Version 3.13.6942.1). Jeśli żadna wersja nie zostanie określona, program zawsze aktualizuje środowisko IR obsługiwane przez funkcję samodzielnego udostępniania do najnowszej [wersji.](https://www.microsoft.com/download/details.aspx?id=39717)
    > [!NOTE]
    > Można określić tylko ostatnie 3 wersje. W idealnym przypadku służy do aktualizowania istniejącego węzła do najnowszej wersji. **przyjęto założenie, że masz zarejestrowane własne środowisko IR**. 

## <a name="usage-examples"></a>Przykłady użycia

### <a name="for-automating-setup"></a>Do automatyzowania instalacji
1. Pobierz własne środowisko IR z tego [miejsca](https://www.microsoft.com/download/details.aspx?id=39717). 
1. Określ ścieżkę do powyższego pobranego pliku MSI SHIR (plik instalacyjny). Na przykład jeśli ścieżka jest *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi*, można użyć poniżej przykładowego wiersza polecenia programu PowerShell dla tego zadania:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > Zastąp klucz [Key] kluczem uwierzytelniania, aby zarejestrować swoje środowisko IR.
    > Zastąp ciąg "username" nazwą użytkownika.
    > Określ lokalizację pliku "InstallGatewayOnLocalMachine.ps1" podczas uruchamiania skryptu. W tym przykładzie Zapisano go na pulpicie.

1. Jeśli na maszynie jest zainstalowane wstępnie hostowane środowisko IR, skrypt automatycznie odinstaluje go, a następnie skonfiguruje nowy. Zobaczysz następujące okno zdjęte: ![ Konfigurowanie środowiska Integration Runtime](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. Po zakończeniu instalacji i rejestracji klucza zobaczysz *pomyślne zainstalowanie bramy* i *pomyślne zarejestrowanie wyników bramy* w lokalnym programie PowerShell.
        [![wynik uruchomienia skryptu 1](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Aby zautomatyzować aktualizacje ręczne
Ten skrypt służy do aktualizowania/instalowania + rejestrowania najnowszego środowiska Integration Runtime. Uruchomienie skryptu wykonuje następujące czynności:
1. Sprawdź bieżącą wersję środowiska IR samoobsługowego
2. Pobierz najnowszą wersję lub określoną wersję z argumentu
3. Jeśli wersja jest nowsza niż bieżąca wersja:
    * Pobieranie samoobsługowego pliku MSI środowiska IR
    * Uaktualnij go

Poniżej przedstawiono przykładowy wiersz polecenia, aby użyć tego skryptu:
* Pobierz i zainstaluj najnowszą bramę:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Pobierz i zainstaluj bramę z określonej wersji:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Jeśli bieżąca wersja jest już Najnowsza, zobaczysz następujący wynik, sugerując, że żadna aktualizacja nie jest wymagana.   
    [![wynik uruchomienia skryptu 2](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)