---
title: Spójne z aplikacjami kopie zapasowe maszyn wirtualnych z systemem Linux
description: Twórz spójne z aplikacjami kopie zapasowe maszyn wirtualnych z systemem Linux na platformie Azure. W tym artykule wyjaśniono, jak skonfigurować strukturę skryptów do tworzenia kopii zapasowych maszyn wirtualnych z systemem Linux wdrożonych na platformie Azure. Ten artykuł zawiera również informacje dotyczące rozwiązywania problemów.
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 22053004026a2dd8976027359f11d50a5663b334
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88999244"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Kopia zapasowa maszyn wirtualnych platformy Azure z systemem Linux spójna na poziomie aplikacji

Podczas tworzenia migawek kopii zapasowych maszyn wirtualnych, spójność aplikacji oznacza, że aplikacje są uruchamiane po rozruchu maszyn wirtualnych po ich przywróceniu. Jak można wyobrazić, spójność aplikacji jest niezwykle ważna. Aby zapewnić spójność maszyn wirtualnych z systemem Linux, można użyć skryptów przedskryptowych i skryptów po skrypcie dla systemu Linux do tworzenia kopii zapasowych spójnych z aplikacjami. Środowisko poprzedzające skrypt i skrypt po skrypcie obsługują maszyny wirtualne z systemem Linux wdrożone Azure Resource Manager. Skrypty spójności aplikacji nie obsługują maszyn wirtualnych wdrożonych w Service Manager ani maszyn wirtualnych z systemem Windows.

## <a name="how-the-framework-works"></a>Jak działa struktura

Platforma udostępnia opcję uruchamiania niestandardowych skryptów przed i skryptów po utworzeniu migawek maszyn wirtualnych. Przed wykonaniem migawki maszyny wirtualnej i po wykonaniu migawki maszyny wirtualnej skrypty wykonywane przed uruchomieniem skryptów są uruchamiane bezpośrednio. Wstępne skrypty i skrypty po skrypcie zapewniają elastyczność umożliwiającą sterowanie aplikacją i środowiskiem, podczas gdy tworzysz migawki maszyn wirtualnych.

Przed skryptami wywoływane są natywne interfejsy API aplikacji, które w trybie spoczynku i opróżniają zawartość pamięci na dysk. Te akcje zapewniają, że migawka jest spójna z aplikacją. Skrypty po stronie używają natywnych interfejsów API aplikacji do odblokowania systemu IOs, co umożliwia aplikacji wznowienie normalnych operacji po migawce maszyny wirtualnej.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Kroki konfigurowania skryptu wstępnego i skryptu

1. Zaloguj się jako użytkownik główny do maszyny wirtualnej z systemem Linux, dla której chcesz utworzyć kopię zapasową.

2. W witrynie [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)Pobierz **VMSnapshotScriptPluginConfig.jsna** i skopiuj go do folderu **/etc/Azure** dla wszystkich maszyn wirtualnych, dla których chcesz utworzyć kopię zapasową. Jeśli folder **/etc/Azure** nie istnieje, utwórz go.

3. Skopiuj skrypt poprzedzający i skrypt dla aplikacji na wszystkich maszynach wirtualnych, dla których planujesz utworzyć kopię zapasową. Skrypty można skopiować do dowolnej lokalizacji na maszynie wirtualnej. Pamiętaj, aby zaktualizować pełną ścieżkę plików skryptów w **VMSnapshotScriptPluginConfig.js** pliku.

4. Upewnij się, że następujące uprawnienia dotyczą tych plików:

   - **VMSnapshotScriptPluginConfig.js**: uprawnienie "600". Na przykład tylko użytkownik "root" powinien mieć uprawnienia "read" i "Write" do tego pliku, a użytkownik nie powinien mieć uprawnień "Execute".

   - **Plik skryptu wstępnego**: uprawnienie "700".  Na przykład tylko użytkownik "root" powinien mieć uprawnienia "read", "Write" i "Execute" dla tego pliku.

   - **Po skrypcie** Uprawnienie "700". Na przykład tylko użytkownik "root" powinien mieć uprawnienia "read", "Write" i "Execute" dla tego pliku.

   > [!IMPORTANT]
   > Platforma zapewnia użytkownikom dużą moc. Zabezpiecz platformę i upewnij się, że tylko użytkownik "root" ma dostęp do krytycznych plików JSON i skryptów.
   > Jeśli wymagania nie są spełnione, skrypt nie zostanie uruchomiony, co spowoduje awarię systemu plików i niespójną kopię zapasową.
   >

5. Skonfiguruj **VMSnapshotScriptPluginConfig.jsw** sposób opisany tutaj:
    - **wtyczkaname**: pozostaw to pole jako is lub skrypty mogą nie funkcjonować zgodnie z oczekiwaniami.

    - **preScriptLocation**: Podaj pełną ścieżkę skryptu wstępnego na maszynie wirtualnej, dla której ma zostać utworzona kopia zapasowa.

    - **postScriptLocation**: Podaj pełną ścieżkę do skryptu na maszynie wirtualnej, dla której ma zostać utworzona kopia zapasowa.

    - **preScriptParams**: podaj parametry opcjonalne, które muszą zostać przesłane do skryptu poprzedzającego. Wszystkie parametry powinny znajdować się w cudzysłowach. Jeśli używasz wielu parametrów, oddziel parametry przecinkami.

    - **postScriptParams**: podaj parametry opcjonalne, które muszą zostać przesłane do skryptu po stronie. Wszystkie parametry powinny znajdować się w cudzysłowach. Jeśli używasz wielu parametrów, oddziel parametry przecinkami.

    - **preScriptNoOfRetries**: Ustaw liczbę powtórzeń poprzedzających skrypt, jeśli wystąpił błąd przed zakończeniem. Zero oznacza tylko jedną próbę i nie ponawiania próby w przypadku wystąpienia błędu.

    - **postScriptNoOfRetries**: Ustaw liczbę ponownych prób wykonania skryptu po skrypcie, jeśli wystąpił błąd przed zakończeniem. Zero oznacza tylko jedną próbę i nie ponawiania próby w przypadku wystąpienia błędu.

    - **timeoutInSeconds**: Określ indywidualne limity czasu dla skryptu wstępnego i po skrypcie (wartość maksymalna może być równa 1800).

    - **continueBackupOnFailure**: Ustaw tę wartość na **true (prawda** ), jeśli chcesz, aby Azure Backup powracać do systemu plików w spójny sposób lub kopia zapasowa spójna na poziomie awarii w przypadku niepowodzenia skryptów poprzedzających skrypt lub po awarii. Ustawienie **wartości false** powoduje niepowodzenie tworzenia kopii zapasowej w przypadku niepowodzenia skryptu (z wyjątkiem sytuacji, gdy istnieje maszyna wirtualna z jednym dyskiem, która powraca do kopii zapasowej spójnej na poziomie awarii, niezależnie od tego ustawienia). Gdy wartość **continueBackupOnFailure** jest ustawiona na false, jeśli kopia zapasowa nie powiedzie się, zostanie podjęta ponowna próba wykonania kopii zapasowej na podstawie logiki ponawiania w usłudze (dla określonej liczby prób).

    - **fsFreezeEnabled**: Określ, czy system Linux fsfreeze powinien być wywoływany podczas tworzenia migawki maszyny wirtualnej w celu zapewnienia spójności systemu plików. Zalecamy pozostawienie tego ustawienia na **wartość true** , chyba że aplikacja ma zależność od wyłączenia fsfreeze.

    - **ScriptsExecutionPollTimeSeconds**: Ustaw czas przejścia rozszerzenia do trybu uśpienia między każdą sondowaniem do wykonania skryptu. Na przykład jeśli wartość jest równa 2, rozszerzenie sprawdza, czy wykonanie skryptu przedniego jest zakończone co 2 sekundy. Minimalna i maksymalna wartość, którą może przyjmować odpowiednio 1 i 5. Wartość powinna być ściśle liczbą całkowitą.

6. Struktura skryptów jest teraz skonfigurowana. Jeśli kopia zapasowa maszyny wirtualnej jest już skonfigurowana, kolejna kopia zapasowa wywołuje skrypty i wyzwala kopię zapasową spójną na poziomie aplikacji. Jeśli nie skonfigurowano kopii zapasowej maszyny wirtualnej, skonfiguruj ją przy użyciu [kopii zapasowych maszyn wirtualnych platformy Azure do magazynów Recovery Services.](./backup-azure-vms-first-look-arm.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Pamiętaj o dodaniu odpowiedniego rejestrowania podczas pisania skryptu wstępnego i po skrypcie oraz Przejrzyj dzienniki skryptów, aby rozwiązać wszelkie problemy ze skryptami. Jeśli nadal występują problemy z uruchamianiem skryptów, zapoznaj się z poniższą tabelą, aby uzyskać więcej informacji.

| Błąd | Komunikat o błędzie | Zalecana akcja |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Skrypt przed wystąpieniem zwrócił błąd, dlatego kopia zapasowa może nie być spójna z aplikacją.| Zapoznaj się z dziennikami błędów dla skryptu, aby rozwiązać ten problem.|  
|Po ScriptExecutionFailed |Skrypt po stronie zwrócił błąd, który może mieć wpływ na stan aplikacji. |Zapoznaj się z dziennikami błędów dla skryptu, aby rozwiązać problem, i sprawdź stan aplikacji. |
| Pre-ScriptNotFound |Nie znaleziono skryptu wstępnego w lokalizacji określonej w **VMSnapshotScriptPluginConfig.js** pliku konfiguracyjnym. |Upewnij się, że skrypt jest obecny w ścieżce określonej w pliku konfiguracji, aby zapewnić kopię zapasową spójną na poziomie aplikacji.|
| Po ScriptNotFound |Nie znaleziono skryptu wykonywanego w lokalizacji określonej w **VMSnapshotScriptPluginConfig.js** pliku konfiguracyjnym. |Upewnij się, że skrypt jest obecny w ścieżce określonej w pliku konfiguracji, aby zapewnić kopię zapasową spójną na poziomie aplikacji.|
| IncorrectPluginhostFile |Plik **hosta wtyczki** , który jest dostarczany z rozszerzeniem VmSnapshotLinux, jest uszkodzony, więc nie można uruchomić skryptu wstępnego i skryptu, a kopia zapasowa nie będzie spójna z aplikacją.| Odinstaluj rozszerzenie **VmSnapshotLinux** i zostanie ono automatycznie zainstalowane ponownie przy użyciu następnej kopii zapasowej w celu rozwiązania problemu. |
| IncorrectJSONConfigFile | **VMSnapshotScriptPluginConfig.jsw** pliku jest niepoprawny, więc nie można uruchomić skryptu wstępnego i skryptu, a kopia zapasowa nie będzie spójna z aplikacją. | Pobierz kopię z usługi [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) i skonfiguruj ją ponownie. |
| InsufficientPermissionforPre-Script | W przypadku uruchamiania skryptów użytkownik "root" powinien być właścicielem pliku, a plik powinien mieć uprawnienia "700" (oznacza to, że tylko "właściciel" powinien mieć uprawnienia "Odczyt", "zapis" i "wykonywanie"). | Upewnij się, że użytkownik "root" jest użytkownikiem "Owner" pliku skryptu i że tylko "Owner" ma uprawnienia "read", "Write" i "Execute". |
| InsufficientPermissionforPost-Script | W przypadku uruchamiania skryptów użytkownik główny powinien być właścicielem pliku, a plik powinien mieć uprawnienia "700" (oznacza to, że tylko "właściciel" powinien mieć uprawnienia "Odczyt", "zapis" i "Execute"). | Upewnij się, że użytkownik "root" jest użytkownikiem "Owner" pliku skryptu i że tylko "Owner" ma uprawnienia "read", "Write" i "Execute". |
| Przed-ScriptTimeout | Upłynął limit czasu wykonywania skryptu wstępnego tworzenia kopii zapasowej spójnej na poziomie aplikacji. | Sprawdź skrypt i zwiększ limit czasu w **VMSnapshotScriptPluginConfig.js** pliku, który znajduje się w **/etc/Azure**. |
| Post-ScriptTimeout | Przekroczono limit czasu wykonywania skryptu po wykonaniu kopii zapasowej spójnej na poziomie aplikacji. | Sprawdź skrypt i zwiększ limit czasu w **VMSnapshotScriptPluginConfig.js** pliku, który znajduje się w **/etc/Azure**. |

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie kopii zapasowej maszyny wirtualnej w magazynie Recovery Services](./backup-azure-vms-first-look-arm.md)
