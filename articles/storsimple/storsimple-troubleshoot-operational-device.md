---
title: Rozwiązywanie problemów ze wdrożonym urządzeniem StorSimple | Microsoft Docs
description: Opisuje, jak diagnozować i naprawiać błędy występujące na urządzeniu StorSimple, które jest obecnie wdrożone i działa.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 6cd1d981737db1e7c852931ecc2449e0afc03530
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956793"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Rozwiązywanie problemów z działającym urządzeniem StorSimple
> [!NOTE]
> Klasyczny portal dla urządzeń StorSimple jest przestarzały. Menedżerowie urządzeń StorSimple dokonają automatycznego przeniesienia do nowej witryny Azure Portal zgodnie z ustalonym harmonogramem wycofywania przestarzałych produktów. Powiadomienie o przeniesieniu otrzymasz pocztą e-mail i za pośrednictwem portalu. Ten dokument zostanie wkrótce usunięty. W razie jakichkolwiek pytań dotyczących przeniesienia, zobacz [FAQ: Move to Azure portal (Często zadawane pytania — przeniesienie do witryny Azure Portal)](./index.yml).

## <a name="overview"></a>Omówienie
Ten artykuł zawiera pomocne wskazówki dotyczące rozwiązywania problemów z konfiguracją, które mogą wystąpić po wdrożeniu i uruchomieniu urządzenia StorSimple. Opisano w nim typowe problemy, możliwe przyczyny i zalecane kroki ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas uruchamiania Microsoft Azure StorSimple. Te informacje dotyczą zarówno StorSimple lokalnego urządzenia fizycznego, jak i urządzenia wirtualnego StorSimple.

Na końcu tego artykułu można znaleźć listę kodów błędów, które mogą wystąpić podczas Microsoft Azure StorSimple operacji, a także kroki, które należy wykonać, aby rozwiązać te błędy. 

## <a name="setup-wizard-process-for-operational-devices"></a>Proces Kreatora instalacji dla urządzeń operacyjnych
Aby sprawdzić konfigurację urządzenia i podjąć działania naprawcze w razie potrzeby, należy użyć Kreatora instalacji programu ([Invoke-HcsSetupWizard][1]).

Po uruchomieniu Kreatora instalacji na wcześniej skonfigurowanym i działającym urządzeniu, przepływ procesu jest inny. Można zmienić tylko następujące wpisy:

* Adres IP, maska podsieci i Brama
* Podstawowy serwer DNS
* Podstawowy serwer NTP
* Opcjonalna konfiguracja serwera proxy sieci Web

Kreator instalacji nie wykonuje operacji związanych z zbieraniem haseł i rejestracją urządzenia.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Błędy występujące podczas kolejnych uruchomień Kreatora instalacji
W poniższej tabeli opisano błędy, które można napotkać podczas uruchamiania Kreatora instalacji na urządzeniu operacyjnym, możliwe przyczyny błędów oraz zalecane działania umożliwiające ich rozwiązanie. 

| Nie. | Komunikat lub warunek błędu | Możliwe przyczyny | Zalecana akcja |
|:--- |:--- |:--- |:--- |
| 1 |Błąd 350032: to urządzenie zostało już zdezaktywowane. |Ten błąd zostanie wyświetlony w przypadku uruchomienia Kreatora instalacji programu na urządzeniu, które zostało zdezaktywowane. |[Skontaktuj się pomoc techniczna firmy Microsoft](./storsimple-8000-contact-microsoft-support.md) w celu wykonania następnych kroków. Zdezaktywowane urządzenie nie może zostać wprowadzone do usługi. Przed ponownym aktywowaniem urządzenia może być konieczne zresetowanie do ustawień fabrycznych. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (wyjątek z HRESULT: 0x80070001) |Aktualizacja serwera DNS kończy się niepowodzeniem. Ustawienia DNS są ustawieniami globalnymi i są stosowane do wszystkich włączonych interfejsów sieciowych. |Włącz interfejs i ponownie Zastosuj ustawienia DNS. Może to zakłócać działanie sieci dla innych włączonych interfejsów, ponieważ te ustawienia są globalne. |
| 3 |Urządzenie jest w trybie online w portalu usługi StorSimple Manager, ale podczas próby przeprowadzenia minimalnej instalacji i zapisania konfiguracji nie powiedzie się. |Podczas początkowej konfiguracji serwer proxy sieci Web nie został skonfigurowany, nawet jeśli w miejscu istnieje rzeczywisty serwer proxy. |Aby zlokalizować ten błąd, użyj [polecenia cmdlet Test-HcsmConnection][2] . [Skontaktuj się z firmą pomoc techniczna firmy Microsoft](./storsimple-8000-contact-microsoft-support.md) , jeśli nie możesz rozwiązać problemu. |
| 4 |Invoke-HcsSetupWizard: wartość nie należy do oczekiwanego zakresu. |Nieprawidłowa maska podsieci powoduje wystąpienie tego błędu. Możliwe przyczyny są następujące: <ul><li> Brak maski podsieci lub jest ona pusta.</li><li>Format prefiksu IPv6 jest nieprawidłowy.</li><li>Interfejs jest włączony w chmurze, ale brak bramy lub jest ona nieprawidłowa.</li></ul>Należy pamiętać, że w przypadku konfigurowania za pomocą Kreatora instalacji usługa DATA 0 jest automatycznie włączona w chmurze. |Aby określić problem, użyj podsieci 0.0.0.0 lub 256.256.256.256, a następnie poszukaj danych wyjściowych. Wprowadź poprawne wartości w polu Maska podsieci, Brama i prefiks IPv6, zgodnie z wymaganiami. |

## <a name="error-codes"></a>Kody błędów
Błędy są wymienione w kolejności liczbowej.

| Numer błędu | Tekst lub opis błędu | Zalecana akcja użytkownika |
|:--- |:--- |:--- |
| 10502 |Wystąpił błąd podczas uzyskiwania dostępu do konta magazynu. |Poczekaj kilka minut i spróbuj ponownie. Jeśli błąd będzie się powtarzać, skontaktuj się z pomoc techniczna firmy Microsoft w celu wykonania następnych kroków. |
| 40017 |Operacja tworzenia kopii zapasowej nie powiodła się, ponieważ na urządzeniu nie znaleziono woluminu określonego w zasadach tworzenia kopii zapasowych. |Spróbuj ponownie wykonać operację tworzenia kopii zapasowej, jeśli błąd będzie nadal występować, skontaktuj się z pomoc techniczna firmy Microsoft. na potrzeby następnych kroków. |
| 40018 |Operacja tworzenia kopii zapasowej nie powiodła się, ponieważ nie znaleziono żadnych woluminów określonych w zasadach tworzenia kopii zapasowych na urządzeniu. |Spróbuj ponownie wykonać operację tworzenia kopii zapasowej, jeśli błąd będzie nadal występować, skontaktuj się z pomoc techniczna firmy Microsoft. na potrzeby następnych kroków. |
| 390061 |System jest zajęty lub niedostępny. |Poczekaj kilka minut i spróbuj ponownie. Jeśli błąd będzie się powtarzać, skontaktuj się z pomoc techniczna firmy Microsoft w celu wykonania następnych kroków. |
| 390143 |Wystąpił błąd z kodem błędu 390143. (Nieznany błąd). |Jeśli błąd będzie się powtarzać, skontaktuj się z pomoc techniczna firmy Microsoft w celu wykonania następnych kroków. |

## <a name="next-steps"></a>Następne kroki
Jeśli nie możesz rozwiązać tego problemu, [skontaktuj się z pomoc techniczna firmy Microsoft](./storsimple-8000-contact-microsoft-support.md) w celu uzyskania pomocy. 

[1]: /previous-versions/windows/powershell-scripting/dn688135(v=wps.630)
[2]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)