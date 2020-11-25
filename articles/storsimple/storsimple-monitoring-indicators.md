---
title: StorSimple wskaźniki monitorowania | Microsoft Docs
description: Opisuje diody emitujące światło (diody LED) i dźwiękowe alarmy używane do monitorowania stanu urządzenia StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 97209dca7d30de037dbd21f5cc145b2941060e70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015404"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Używanie wskaźników monitorowania StorSimple do zarządzania urządzeniem


## <a name="overview"></a>Omówienie
Urządzenie StorSimple obejmuje diodowe nadajniki (diody LED) i alarmy, których można użyć do monitorowania modułów i ogólnego stanu urządzenia StorSimple. Wskaźniki monitorowania znajdują się na składnikach sprzętowych podstawowej obudowy urządzenia i w obudowie EBOD. Wskaźniki monitorowania mogą być diodami LED lub alarmami dźwiękowymi.

Istnieją trzy stany LED używane do wskazywania stanu modułu: zielony, migający zielony do czerwonego żółtego lub czerwonego bursztynu.  

* Zielone diody LED przedstawiają stan działania w dobrej kondycji.  
* Lampka błyskowa w kolorze zielonym do czerwonego bursztynu przedstawia obecność niekrytycznych warunków, które mogą wymagać interwencji użytkownika.  
* Czerwone żółte diody LED wskazują, że występuje błąd krytyczny występujący w module.  

W pozostałej części tego artykułu opisano różne diody LED wskaźnika monitorowania, ich lokalizacje na urządzeniu StorSimple, stan urządzenia oparty na Stanach LED i wszelkie związane z nimi alarmy dźwiękowe.

## <a name="front-panel-indicator-leds"></a>Diody LED wskaźnika panelu przedniego
Panel przedni, nazywany również *panelem operacje* lub *panelem Ops*, wyświetla zagregowany stan wszystkich modułów w systemie. Panel przedni jest identyczny z StorSimpleą i obudowy EBOD i przedstawiono poniżej.  

   ![Panel przedni urządzenia][1]

Panel przedni zawiera następujące wskaźniki:  

1. Przycisk Wycisz
2. Dioda LED wskaźnika napięcia (kolor zielony/czerwony — bursztynowo)
3. Wskaźnik błędu modułu (na czerwono — bursztyn/wył.)
4. Wskaźnik błędu logicznego diody LED (czerwony-bursztynowy/wyłączony)
5. Wyświetlanie identyfikatora jednostki  

Główna różnica między diodami LED panelu przedniego a urządzeniem dla obudowy EBOD jest **numer identyfikacyjny jednostki systemowej** wyświetlany na ekranie diody LED. Domyślnym IDENTYFIKATORem jednostki wyświetlanym na urządzeniu jest **00**, podczas gdy domyślny identyfikator jednostki wyświetlany w obudowie EBOD jest **01**. Pozwala to na szybkie rozróżnienie urządzenia i obudowy EBOD, gdy urządzenie jest włączone. Jeśli urządzenie jest wyłączone, Skorzystaj z informacji podanych w temacie [Włączanie nowego urządzenia](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) w celu odróżnienia urządzenia od obudowy EBOD.  

## <a name="front-panel-led-status"></a>Stan diody LED panelu przedniego
Poniższa tabela służy do identyfikowania stanu wskazywanego przez diody LED na panelu przednim urządzenia lub obudowy EBOD.  

| Moc systemu | Błąd modułu | Błąd logiczny | Uruchomienia | Stan |
| --- | --- | --- | --- | --- |
| Czerwona — bursztynowa |WYŁ. |WYŁ. |Nie dotyczy |Utrata zasilania, działanie z mocą tworzenia kopii zapasowej lub zasilanie i moduły kontrolera zostały usunięte. |
| Green (Zielony) |ON |ON |Nie dotyczy |Stan testu włączania napięcia (5 s) w panelu Ops |
| Green (Zielony) |WYŁ. |WYŁ. |Nie dotyczy |Włącz, wszystkie funkcje są dobre |
| Green (Zielony) |ON |Nie dotyczy |Diody błędów modułu PCM, diody LED błędów wentylatorów |Wszelkie błędy modułu PCM, awaria wentylatorów, ponad lub poniżej temperatury |
| Green (Zielony) |ON |Nie dotyczy |Diody LED modułu we/wy |Dowolny błąd modułu kontrolera |
| Green (Zielony) |ON |NIE DOTYCZY |NIE DOTYCZY |Błąd logiki obudowy |
| Green (Zielony) |Flash |Nie dotyczy |Stan modułu został przeprowadzony w module kontrolera. Diody błędów modułu PCM, diody LED błędów wentylatorów |Zainstalowano nieznany typ modułu kontrolera, awaria magistrali I2C, nieważna konfiguracja danych produktu (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Diody LED wskaźnika modułu chłodzenia (PCM)
Diody LED wskaźnika modułu chłodzenia (PCM) można znaleźć na tylnej stronie obudowy podstawowej lub obudowy EBOD na każdym module PCM. W tym temacie omówiono sposób używania następujących diod LED do monitorowania stanu urządzenia StorSimple.  

* Diody LED modułu PCM dla obudowy podstawowej
* Diody LED modułu PCM dla obudowy EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>Diody LED modułu PCM dla obudowy podstawowej
Urządzenie StorSimple ma moduł 764W PCM z dodatkową baterią. Na poniższej ilustracji przedstawiono panel LED dla urządzenia.  

   ![Diody LED PCM na obudowie podstawowej][2]

Legenda diody LED:

1. Awaria zasilania AC
2. Awaria wentylatora
3. Awaria baterii
4. MODUŁ PCM JEST PRAWIDŁOWY
5. Awaria kontrolera domeny
6. Bateria jest dobra  

Stan modułu PCM jest wskazany w panelu LED. Panel LED urządzenia PCM ma sześć diod LED. Cztery z tych diod LED wyświetlają stan zasilacza i wentylatoru. Pozostałe dwa diody LED wskazują stan modułu baterii kopii zapasowej w module PCM. Aby określić stan modułu PCM, można użyć poniższych tabel.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Diody LED wskaźnika PCM dla zasilacza i wentylatoru
| Stan | Moduł PCM OK (zielony) | Awaria AC (bursztynowa) | Awaria wentylatoru (bursztynowa) | Awaria kontrolera domeny (bursztynowa) |
| --- | --- | --- | --- | --- |
| Bez zasilania sieciowego (do obudowy) |WYŁ. |WYŁ. |WYŁ. |WYŁ. |
| Bez zasilania AC (tylko ten moduł PCM) |WYŁ. |ON |WYŁ. |ON |
| Zaprezentowanie modułu PCM na OK |ON |WYŁ. |WYŁ. |WYŁ. |
| Niepowodzenie PCM (wentylator nie powiodło się) |WYŁ. |WYŁ. |ON |Nie dotyczy |
| Błąd PCM (over amp, ponad napięciem, over) |WYŁ. |ON |ON |ON |
| PCM (brak tolerancji) |ON |WYŁ. |WYŁ. |ON |
| Tryb wstrzymania |Braku |WYŁ. |WYŁ. |WYŁ. |
| Pobieranie oprogramowania układowego PCM |WYŁ. |Braku |Braku |Braku |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Diody LED wskaźnika PCM dla baterii kopii zapasowej
| Stan | Baterie dobry (zielony) | Błąd baterii (bursztynowo) |
| --- | --- | --- |
| Bateria nieobecna |WYŁ. |WYŁ. |
| Baterie obecne i naliczone |ON |WYŁ. |
| Ładowanie baterii lub zakończenie konserwacji |Braku |WYŁ. |
| Błąd baterii "miękki" (możliwy do odzyskania) |WYŁ. |Braku |
| Błąd baterii "twarda" (bez możliwości odzyskania) |WYŁ. |ON |
| Nieuzbrojony akumulator |Braku |WYŁ. |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>Diody LED modułu PCM dla obudowy EBOD
Obudowa EBOD ma 580W PCM i nie ma dodatkowej baterii. Panel PCM dla obudowy EBOD ma diody LED wskaźnika tylko dla zasilaczy i wentylatorów. Na poniższej ilustracji przedstawiono te diody LED.

   ![Diody LED modułu PCM w obudowie EBOD][3] 

Aby określić stan modułu PCM, można użyć poniższej tabeli.  

| Stan | Moduł PCM OK (zielony) | Awaria AC (bursztynowa) | Awaria wentylatoru (bursztynowa) | Awaria kontrolera domeny (bursztynowa) |
| --- | --- | --- | --- | --- |
| Bez zasilania sieciowego (do obudowy) |WYŁ. |WYŁ. |WYŁ. |WYŁ. |
| Bez zasilania AC (tylko ten moduł PCM) |WYŁ. |ON |WYŁ. |ON |
| Zaprezentowanie modułu PCM w — OK |ON |WYŁ. |WYŁ. |WYŁ. |
| Niepowodzenie PCM (wentylator nie powiodło się) |WYŁ. |WYŁ. |ON |X |
| Błąd modułu PCM (over amp, ponad napięciem, nad prądem |WYŁ. |ON |ON |ON |
| PCM (brak tolerancji) |ON |WYŁ. |WYŁ. |ON |
| Model rezerwy |Braku |WYŁ. |WYŁ. |WYŁ. |
| Pobieranie oprogramowania układowego PCM |WYŁ. |Braku |Braku |Braku |

## <a name="controller-module-indicator-leds"></a>Diody LED wskaźnika modułu kontrolera
Urządzenie StorSimple zawiera diody LED dla kontrolera podstawowego i modułów kontrolera EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitorowanie diod LED dla kontrolera podstawowego
Poniższa ilustracja pomaga zidentyfikować diody LED na kontrolerze podstawowym. (Wszystkie składniki są wymienione na liście w celu uzyskania pomocy w orientacji).  

   ![Diody LED monitorowania — kontroler podstawowy][4]

Skorzystaj z poniższej tabeli, aby określić, czy moduł kontrolera działa prawidłowo.  

### <a name="controller-indicator-leds"></a>Diody LED wskaźnika kontrolera
| BRANŻ | Opis |
| --- | --- |
| Identyfikator LED (niebieski) |Wskazuje, że moduł jest identyfikowany. Jeśli niebieska dioda LED miga na uruchomionym kontrolerze, kontroler jest aktywnym kontrolerem, a drugi jest kontrolerem gotowości. Aby uzyskać więcej informacji, zobacz [Identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Dioda LED błędu (bursztynowo) |Wskazuje błąd w kontrolerze. |
| OK (zielony) |Stałe zielony wskazuje, że kontroler jest prawidłowy. Migający zielony wskazuje na błąd konfiguracji VPD kontrolera. |
| Diody LED działania SAS (zielony) |Stałe zielony wskazuje połączenie bez bieżącego działania. Migająca zielona wskazuje, że połączenie ma trwającą aktywność. |
| Diody LED stanu sieci Ethernet |Po prawej stronie wskazuje, że działanie link/sieć: (stałe zielone) aktywne łącze (migające) aktywność sieciową. Po lewej stronie wskazuje szybkość sieci: (żółty) 1000 MB/s, (zielony) 100 MB/s i (wyłączone) 10 MB/s. W zależności od modelu składnika sygnalizator ten może migać nawet wtedy, gdy interfejs sieciowy nie jest włączony. |
| Opublikuj diody LED |Wskazuje postęp rozruchu, gdy kontroler jest włączony. Jeśli rozruch urządzenia StorSimple nie powiedzie się, ta dioda LED pomoże pomoc techniczna firmy Microsoft zidentyfikować punkt w procesie rozruchu, w którym wystąpił błąd. |

> [!IMPORTANT]
> W przypadku awarii, wystąpił problem z modułem kontrolera, który można rozwiązać przez ponowne uruchomienie kontrolera. Skontaktuj się z pomoc techniczna firmy Microsoft, jeśli ponowne uruchomienie kontrolera nie rozwiąże tego problemu.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Diody LED monitorowania dla EBOD (obudowa EBOD)
Każdy kontroler EBOD SAS 6 GB/s ma diody LED wskazujące jego stan, jak pokazano na poniższej ilustracji.  

  ![Diody LED monitorowania — obudowa EBOD][5]

Skorzystaj z poniższej tabeli, aby określić, czy moduł kontrolera EBOD działa normalnie.  

### <a name="ebod-controller-module-indicator-leds"></a>Diody LED wskaźnika modułu EBOD Controller
| Stan | Moduł we/wy jest prawidłowy (zielony) | Błąd modułu we/wy (bursztynowo) | Aktywność portów hosta (zielona) |
| --- | --- | --- | --- |
| Moduł kontrolera jest prawidłowy |ON |WYŁ. |- |
| Błąd modułu kontrolera |WYŁ. |ON |- |
| Brak połączenia z portem zewnętrznym hosta |- |- |WYŁ. |
| Połączenie z portem zewnętrznym hosta — brak działania |- |- |ON |
| Połączenie z portem zewnętrznym hosta — aktywność |- |- |Braku |
| Błąd metadanych modułu kontrolera |Braku |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Diody LED wskaźnika stacji dysków dla obudowy podstawowej i obudowy EBOD
Urządzenie StorSimple ma stacje dysków znajdujące się zarówno w obudowie podstawowej, jak i w obudowie EBOD. Każdy dysk zawiera diody LED wskaźnika monitorowania, zgodnie z opisem w tej sekcji. 

W przypadku dysków dyskowych stan stacji jest wskazywany przez zieloną diodę LED, a dioda LED Red-bursztynowa została zainstalowana z przodu każdego modułu nośnej stacji. Na poniższej ilustracji przedstawiono te diody LED.

  ![Diody LED dysku][6]

Skorzystaj z poniższej tabeli, aby określić stan każdego dysku, który z kolei ma wpływ na ogólny stan diody LED panelu przedniego.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Diody LED wskaźnika stacji dysków dla obudowy EBOD
| Stan | Działanie OK (zielony) | Dioda LED błędu (czerwony — bursztynowo) | DIODa skojarzona z panelem Ops |
| --- | --- | --- | --- |
| Nie zainstalowano żadnego dysku |WYŁ. |WYŁ. |Brak |
| Stacja dysków zainstalowana i operacyjna |Miganie przy działaniu |X |Brak |
| Zestaw tożsamości urządzeń usługi obudów SCSI (SES) |ON |Migająca 1 sekunda na/1 sekundę |Brak |
| Zestaw bitów błędów urządzenia SES |ON |ON |Błąd logiczny (czerwony) |
| Awaria obwodu sterowania mocą |WYŁ. |ON |Błąd modułu (czerwony) |

## <a name="audible-alarms"></a>Alarmy dźwiękowe
Urządzenie StorSimple zawiera dźwiękowe alarmy skojarzone zarówno z obudową podstawową, jak i obudową EBOD. Alarm dźwiękowy znajduje się na panelu przednim (nazywanym również panelem Ops) obu obudów. Alarm dźwiękowy wskazuje, kiedy występuje warunek błędu. Następujące warunki spowodują aktywowanie alarmu:  

* Błąd wentylatoru lub niepowodzenie
* Napięcie poza zakresem
* W warunkach temperatury lub poniżej
* Przepełnienie termiczne
* Błąd systemu
* Błąd logiczny
* Awaria zasilacza
* Usuwanie modułu chłodzenia prądem (PCM)  

W poniższej tabeli opisano różne stany alarmu.  

### <a name="alarm-states"></a>Stany alarmu
| Stan alarmu | Akcja | Naciśnięto przycisk akcji z wyciszeniem |
| --- | --- | --- |
| S0 |Tryb normalny: dyskretny |Sygnalizuj dźwiękiem dwa razy |
| S1 |Tryb błędu: 1 sekundę na/1 sekundę |Przejście do S2 lub S3 (Zobacz uwagi) |
| S2 |Tryb przypomnień: sporadyczny sygnał dźwiękowy |Brak |
| S3 |Tryb wyciszony: dyskretny |Brak |
| S4 |Tryb błędu krytycznego: alarm ciągły |Niedostępne: wyciszenie nieaktywne |

> [!NOTE]
> * W stanie alarmu S1, jeśli nie naciśniesz przycisku Wycisz w ciągu 2 minut, stan automatycznie przechodzi do S2 lub S3.  
> * Stany alarmu S1 z S4 Wróć do S0 po wyczyszczeniu warunku błędu.  
> * Stan błędu krytycznego S4 można wprowadzić z dowolnego innego stanu.  


Alarm dźwiękowy można wyciszyć, naciskając przycisk Wycisz w panelu Ops. Automatyczne wyciszenie nastąpi po upływie dwóch minut, jeśli przełącznik wyciszenia nie będzie obsługiwany ręcznie. Gdy alarm jest wyciszony, będzie kontynuował dźwięk z krótkimi sporadycznymi sygnałami dźwiękowymi, aby wskazać, że problem nadal istnieje. Alarm będzie cichy, gdy wszystkie problemy zostaną wyczyszczone.

W poniższej tabeli opisano różne warunki alarmu.

### <a name="alarm-conditions"></a>Warunki alarmu
| Stan | Ważność | Uruchomienia | Dioda LED panelu Ops |
| --- | --- | --- | --- |
| Alert PCM — utrata zasilania kontrolera domeny z pojedynczego modułu PCM |Błąd — brak utraty nadmiarowości |S1 |Błąd modułu |
| Alert PCM — utrata zasilania kontrolera domeny z pojedynczego modułu PCM |Błąd — utrata nadmiarowości |S1 |Błąd modułu |
| Wentylator modułu PCM nie powiodło się |Błąd — utrata nadmiarowości |S1 |Błąd modułu |
| Moduł SBB wykrył błąd PCM |Powodu |S1 |Błąd modułu |
| Usunięto moduł PCM |Błąd konfiguracji |Brak |Błąd modułu |
| Błąd konfiguracji obudowy |Błąd — krytyczny |S1 |Błąd modułu |
| Alert dotyczący niskich temperatur ostrzeżenia |Ostrzeżenie |S1 |Błąd modułu |
| Alert dotyczący temperatury wysokiego ostrzeżenia |Ostrzeżenie |S1 |Błąd modułu |
| Alarm z ponad temperaturą |Błąd — krytyczny |S1 |Błąd modułu |
| Awaria magistrali I2C |Błąd — utrata nadmiarowości |S1 |Błąd modułu |
| Błąd komunikacji z panelem Ops (I2C) |Błąd — krytyczny |S1 |Błąd modułu |
| Błąd kontrolera |Błąd — krytyczny |S1 |Błąd modułu |
| Błąd modułu interfejsu SBB |Błąd — krytyczny |S1 |Błąd modułu |
| Błąd modułu interfejsu SBB — pozostałe moduły działające |Błąd — krytyczny |S4 |Błąd modułu |
| Moduł interfejsu SBB został usunięty |Ostrzeżenie |Brak |Błąd modułu |
| Awaria sterowania mocą |Ostrzeżenie — brak możliwości dysku |S1 |Błąd modułu |
| Awaria sterowania mocą |Błąd — krytyczny; Utrata mocy dysku |S1 |Błąd modułu |
| Usunięto dysk |Ostrzeżenie |Brak |Błąd modułu |
| Za mało dostępnej mocy |Ostrzeżenie |brak |Błąd modułu |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [składnikach i stanie sprzętu StorSimple](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


