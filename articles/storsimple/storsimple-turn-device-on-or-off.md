---
title: Włącz lub Wyłącz urządzenie z serii StorSimple 8000
description: Wyjaśnia, jak włączyć nowe urządzenie StorSimple, włączyć urządzenie, które zostało wyłączone lub zgubione, i wyłączyć uruchomione urządzenie.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8f4269235c494ff9dd8d1bf8e0ef940562f8927
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85515267"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Włączanie lub wyłączanie urządzenia z serii StorSimple 8000

## <a name="overview"></a>Omówienie
Zamykanie urządzenia Microsoft Azure StorSimple nie jest wymagane jako część normalnej operacji systemowej. Może być jednak konieczne włączenie nowego urządzenia lub urządzenia, które musiały zostać zamknięte. Ogólnie rzecz biorąc, w przypadkach, w których należy zastąpić sprzęt zakończony niepowodzeniem, należy fizycznie przenieść jednostkę lub przełączyć urządzenie poza usługę. W tym samouczku opisano wymaganą procedurę włączania i wyłączania urządzenia z systemem StorSimple w różnych scenariuszach.

## <a name="turn-on-a-new-device"></a>Włącz nowe urządzenie
Kroki związane z włączaniem urządzenia StorSimple po raz pierwszy różnią się w zależności od tego, czy urządzenie jest modelem 8100 czy 8600. 8100 ma jedną obudowę podstawową, a 8600 to urządzenie z dwoma obudowami z obudową podstawową i obudową EBOD. Szczegółowe kroki dla obu modeli zostały omówione w poniższych sekcjach.

* [Nowe urządzenie z tylko podstawową obudową](#new-device-with-primary-enclosure-only)
* [Nowe urządzenie z obudową EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nowe urządzenie z tylko podstawową obudową
Model StorSimple 8100 to pojedyncze urządzenie obudowy. Urządzenie obejmuje nadmiarowe moduły mocy i chłodzenia (PCMs). Aby zapewnić wysoką dostępność, muszą być zainstalowane i połączone z różnymi źródłami zasilanymi przez PCMs.

Wykonaj następujące kroki, aby podłączyć urządzenie do urządzeń.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Aby uzyskać pełne instrukcje dotyczące konfiguracji i okablowania urządzenia, przejdź do pozycji [Instalowanie urządzenia z usługą StorSimple 8100](storsimple-8100-hardware-installation.md). Upewnij się, że dokładnie postępuj zgodnie z instrukcjami.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nowe urządzenie z obudową EBOD
Model StorSimple 8600 ma zarówno obudowę podstawowy, jak i obudowę EBOD. Wymaga to okablowania połączonego z łącznością SAS (Serial Attached SCSI) i mocą.

Konfigurując to urządzenie po raz pierwszy, najpierw wykonaj kroki dla okablowania SAS, a następnie wykonaj kroki dla okablowania.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Aby uzyskać pełne instrukcje dotyczące konfiguracji i okablowania urządzenia, przejdź do pozycji [Instalowanie urządzenia z usługą StorSimple 8600](storsimple-8600-hardware-installation.md). Upewnij się, że dokładnie postępuj zgodnie z instrukcjami.

## <a name="turn-on-a-device-after-shutdown"></a>Włącz urządzenie po zamknięciu
Kroki związane z włączaniem urządzenia StorSimple po jego zamknięciu są różne w zależności od tego, czy urządzenie jest modelem 8100 czy 8600. 8100 ma jedną obudowę podstawową, a 8600 to urządzenie z dwoma obudowami z obudową podstawową i obudową EBOD.

* [Tylko urządzenie z podstawową obudową](#device-with-primary-enclosure-only)
* [Urządzenie z obudową EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Tylko urządzenie z podstawową obudową
Po zamknięciu programu wykonaj poniższą procedurę, aby włączyć urządzenie StorSimple z obudową podstawową i bez obudowy EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Aby włączyć urządzenie tylko przy użyciu podstawowej obudowy
1. Upewnij się, że przełączniki zasilające w modułach zasilanie i chłodzące (PCMs) są wyłączone. Jeśli przełączniki nie znajdują się poza pozycją, przewróć je do pozycji wyłączone i poczekaj na wyłączenie świateł.
2. Włącz urządzenie, przerzucając przełączniki w obu PCMs na pozycji włączone. Urządzenie powinno włączyć.
3. Sprawdź następujące, aby sprawdzić, czy urządzenie jest w pełni włączone:
   
   1. Poprawne diody LED dla obu modułów PCM są zielone.
   2. Diody LED stanu na obu kontrolerach są pełnymi kolorami zielonymi.
   3. Niebieskie diody LED na jednym z kontrolerów migają, co oznacza, że kontroler jest aktywny.
      
      Jeśli którykolwiek z tych warunków nie zostanie spełniony, urządzenie nie jest w dobrej kondycji. [Skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Urządzenie z obudową EBOD
Po zamknięciu programu wykonaj poniższą procedurę, aby włączyć urządzenie z systemem StorSimple przy użyciu obudowy podstawowej i obudowy EBOD. Wykonaj każdy krok w sekwencji dokładnie zgodnie z opisem. Niewykonanie tej czynności może spowodować utratę danych.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Aby włączyć urządzenie przy użyciu podstawowej i EBOD obudowy
1. Upewnij się, że obudowa EBOD jest połączona z obudową podstawową. Aby uzyskać więcej informacji, zobacz [Instalowanie urządzenia z StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Upewnij się, że moduły energetyczne i chłodzące (PCMs) zarówno dla obudowy EBOD, jak i podstawowych znajdują się na pozycji OFF. Jeśli przełączniki nie znajdują się poza pozycją, przewróć je do pozycji wyłączone i poczekaj na wyłączenie świateł.
3. Najpierw włącz obudowę EBOD, przełączając przełączniki w obu PCMsach do pozycji włączone. Diody LED PCM powinny mieć kolor zielony. W tej jednostce zostanie przekierowany zielony kontroler EBOD wskazujący, że obudowa EBOD jest włączona.
4. Włącz obudowę podstawową, przełączając przełączniki w obu PCMs do pozycji włączone. Cały system powinien być teraz włączony.
5. Sprawdź, czy diody LED sygnatury dostępu współdzielonego są zielone, co gwarantuje, że połączenie między obudową EBOD a podstawową obudową jest dobre.

## <a name="turn-on-a-device-after-a-power-loss"></a>Włącz urządzenie po utracie mocy
Awaria lub przerwy w zasilaniu mogą wyłączyć urządzenie StorSimple. Awaria w zasilaniu może wystąpić na jednym z zasilaczy lub w obu zasilaczach. Kroki odzyskiwania różnią się w zależności od tego, czy urządzenie jest modelem 8100 czy 8600. 8100 ma jedną obudowę podstawową, a 8600 to urządzenie z dwoma obudowami z obudową podstawową i obudową EBOD. W tej sekcji opisano procedurę odzyskiwania dla każdego scenariusza.

* [Tylko urządzenie z podstawową obudową](#8100)
* [Urządzenie z obudową EBOD](#8600)

### <a name="device-with-primary-enclosure-only"></a>Tylko urządzenie z podstawową obudową <a name="8100"></a>
System może kontynuować swoją normalną operację, jeśli nastąpi utrata mocy do jednego z jego zasilaczy. Aby jednak zapewnić wysoką dostępność urządzenia, należy przywrócić zasilanie do zasilacza najszybciej, jak to możliwe.

W przypadku awarii lub przerwy w zasilaniu obu zasilaczach system zostanie zamknięty w sposób uporządkowany i kontrolowany. Po przywróceniu napięcia system zostanie automatycznie włączony.

### <a name="device-with-ebod-enclosure"></a>Urządzenie z obudową EBOD <a name="8600"></a>
#### <a name="power-loss-on-one-power-supply"></a>Utrata mocy w jednym zasilaczu
System może kontynuować swoją normalną operację, jeśli nastąpi utrata mocy do jednej z jej zasilaczy podstawowego lub obudowy EBOD. Aby zapewnić wysoką dostępność urządzenia, Przywróć zasilanie do zasilacza tak szybko, jak to możliwe.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Utrata mocy na zasilaczach podstawowych i EBOD
Jeśli nastąpi awaria lub przerwa w zasilaniu obu zasilacza, Obudowa EBOD zostanie natychmiast ZAMKNIĘTA, a Podstawowa obudowa zostanie zamknięta w sposób uporządkowany i kontrolowany. Gdy przywrócona jest energia, urządzenie zostanie uruchomione automatycznie.

Jeśli zasilanie jest wyłączone ręcznie, wykonaj następujące kroki, aby przywrócić zasilanie do systemu.

1. Włącz obudowę EBOD.
2. Po włączeniu obudowy EBOD Włącz obudowę obudowy podstawowej.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Utrata mocy dla obu zasilaczy w obudowie EBOD
Podczas konfigurowania kabli należy upewnić się, że EBOD nigdy nie są połączone z osobną PDU. Jeśli EBOD i podstawowa obudowa nie powiedzie się w tym samym czasie, system zostanie odzyskany.

Jeśli tylko obudowa EBOD nie powiedzie się w obu zasilaczach, system nie odzyska automatycznie sprawności. Wykonaj następujące kroki, aby włączyć system i przywrócić go do stanu dobrej kondycji:

1. Jeśli obudowa podstawowa jest włączona, wyłącz zarówno Moduły zasilające, jak i chłodzenia (PCMs).
2. Zaczekaj kilka minut, aż system zostanie zamknięty.
3. Włącz obudowę EBOD.
4. Po włączeniu obudowy EBOD Włącz obudowę obudowy podstawowej.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Włącz urządzenie po utracie połączenia obudowy podstawowe i EBOD
Jeśli połączenie zostanie utracone między kontrolerem gotowości i odpowiednim kontrolerem EBOD, urządzenie nadal działa. Jeśli połączenie między aktywnym kontrolerem systemu i odpowiednim kontrolerem EBOD zostanie utracone, tryb failover powinien wystąpić, a urządzenie powinno nadal działać normalnie.

Gdy dyski SAS (Serial Attached SCSI) są usuwane lub połączenie między obudową EBOD a podstawową obudową jest poważniejsze, urządzenie przestanie działać. W tym momencie wykonaj następujące czynności.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Aby włączyć urządzenie po utracie połączenia
1. Dostęp do tyłu urządzenia.
2. Jeśli połączenie kablowe SYGNATURy dostępu współdzielonego między obudową EBOD a podstawową obudowa jest przerwane, wszystkie diody LED ścieżki SAS w obudowie EBOD będą wyłączone.
3. Zamknij zarówno moduły energetyczne, jak i chłodzące (PCMs) w obudowie EBOD i podstawowej obudowie.
4. Poczekaj, aż wszystkie sygnalizatory z tyłu obu obudów wyłączają się.
5. Włóż ponownie kable SAS i upewnij się, że istnieje dobre połączenie między obudową EBOD a podstawową obudową.
6. Najpierw włącz obudowę EBOD, przerzucając oba przełączniki PCM do pozycji włączone.
7. Upewnij się, że obudowa EBOD jest włączona, sprawdzając, czy zielona dioda LED jest włączona.
8. Włącz obudowę obudowy podstawowej.
9. Upewnij się, że podstawowy załącznik jest włączony, sprawdzając, czy jest włączona zielona dioda LED kontrolera.
10. Sprawdź, czy połączenie obudowy EBOD z obudową podstawową jest dobre, sprawdzając, czy wszystkie diody LED ścieżki sygnatury dostępu współdzielonego (cztery na kontroler EBOD) są włączone.

> [!IMPORTANT]
> Jeśli kable SAS są uszkodzone lub połączenie między obudową EBOD a podstawową obudową nie jest dobrym warunkiem, po włączeniu systemu zostanie ono przełączone w tryb odzyskiwania. Jeśli tak się stanie, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) .


## <a name="turn-off-a-running-device"></a>Wyłączanie uruchomionego urządzenia
Uruchomienie urządzenia StorSimple może być konieczne w przypadku jego przeniesienia, wypróbowania usługi lub nieprawidłowego składnika, który musi zostać zastąpiony. Kroki różnią się w zależności od tego, czy urządzenie StorSimple jest modelem 8100 czy 8600. 8100 ma jedną obudowę podstawową, a 8600 to urządzenie z dwoma obudowami z obudową podstawową i obudową EBOD. Ta sekcja zawiera szczegółowe instrukcje dotyczące zamykania uruchomionego urządzenia.

* [Urządzenie z obudową podstawową](#8100a)
* [Urządzenie z obudową EBOD](#8600a)

### <a name="device-with-primary-enclosure"></a>Urządzenie z obudową podstawową <a name="8100a"></a>
Aby wyłączyć urządzenie w sposób uporządkowany i kontrolowany, można to zrobić za pośrednictwem Azure Portal lub za pośrednictwem program Windows PowerShell dla usługi StorSimple. 

> [!IMPORTANT]
> Nie zamykaj uruchomionego urządzenia przy użyciu przycisku energia z tyłu urządzenia.
> 
> Przed zamknięciem urządzenia upewnij się, że wszystkie składniki urządzenia są w dobrej kondycji. W Azure Portal przejdź do **urządzeń**  >  **Monitoruj**  >  **kondycję sprzętu** i sprawdź, czy stan wszystkich składników jest zielony. Jest to prawdziwe tylko w przypadku systemu w dobrej kondycji. Jeśli system jest zamykany w celu zastąpienia nieprawidłowego składnika, w **stanie sprzętu** zostanie wyświetlony stan "Niepowodzenie" (czerwony) lub zdegradowany (żółty) dla odpowiedniego składnika.
> 
> 

Po uzyskaniu dostępu do program Windows PowerShell dla usługi StorSimple lub Azure Portal, wykonaj kroki opisane w [sekcji wyłączanie urządzenia StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure"></a>Urządzenie z obudową EBOD <a name="8600a"></a>
> [!IMPORTANT]
> Przed zamknięciem obudowy podstawowej i obudowy EBOD upewnij się, że wszystkie składniki urządzenia są w dobrej kondycji. W Azure Portal przejdź do **urządzeń**  >  **Monitoruj**  >  **kondycję sprzętu** i sprawdź, czy wszystkie składniki są w dobrej kondycji.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Aby zamknąć uruchomione urządzenie przy użyciu obudowy EBOD
1. Wykonaj wszystkie czynności opisane w [sekcji wyłączanie urządzenia StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) dla obudowy podstawowej.
2. Po wyłączeniu obudowy podstawowej należy wyłączyć EBOD, przełączając oba przełączniki zasilanie i chłodzenie (PCM).
3. Aby sprawdzić, czy EBOD został zamknięty, sprawdź, czy wszystkie sygnalizatory z tyłu obudowy EBOD są wyłączone.

> [!NOTE]
> Kable sygnatury dostępu współdzielonego, które są używane do łączenia obudowy EBOD z obudową podstawową nie należy usuwać do momentu wyłączenia systemu.

## <a name="next-steps"></a>Następne kroki
[Skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , jeśli wystąpią problemy podczas włączania lub wyłączania urządzenia StorSimple.

