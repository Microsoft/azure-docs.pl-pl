---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 74a9764187b15bddf1dc48fa2b7988217d31abce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183431"
---
## <a name="troubleshooting-update-failures"></a>Rozwiązywanie problemów dotyczących niepowodzenia aktualizacji
**Co się stanie, jeśli zostanie wyświetlone powiadomienie o niepowodzeniu testów przed uaktualnieniem?**

Jeśli test wstępny nie powiedzie się, przeczytaj informacje z paska powiadomień szczegółowych w dolnej części strony. Znajdują się tam wskazówki dotyczące rodzaju testu wstępnego, który się nie powiódł. Na poniższej ilustracji przedstawiono wystąpienie, w którym pojawia się takie powiadomienie. W tym przypadku nie powiódł się test kondycji kontrolera i test kondycji składnika sprzętowego. W sekcji **Stan sprzętu** widać, że zarówno składnik **Controller 0**, jak i składnik **Controller 1** wymagają uwagi.

  ![Niepowodzenie testu wstępnego](./media/storsimple-install-troubleshooting/HCS_PreUpdateCheckFailed-include.png)

Należy się upewnić, że oba kontrolery są w dobrej kondycji i są w trybie online. Należy się także upewnić, że wszystkie składniki sprzętowe w urządzeniu StorSimple są wyświetlane na stronie Obsługa jako będące w dobrej kondycji. Po spełnieniu tych warunków można spróbować zainstalować aktualizacje. Jeśli nie jesteś w stanie rozwiązać problemów związanych ze składnikami sprzętowymi, konieczne będzie skontaktowanie się z pomocą techniczną firmy Microsoft w celu uzyskania dalszych instrukcji.

**Co zrobić, jeśli pojawi się komunikat o błędzie „Nie można zainstalować aktualizacji”, a zaleceniem jest odwołanie się do przewodnika rozwiązywania problemów dotyczących aktualizacji w celu określenia przyczyny awarii?**

Jedną z prawdopodobnych przyczyn może być brak połączenia z serwerami usługi Microsoft Update. Poniżej opisano sprawdzanie ręczne, które należy wykonać. Jeśli utracisz łączność z serwerem aktualizacji, zadanie aktualizacji zakończy się niepowodzeniem. Łączność możesz sprawdzić, uruchamiając następujące polecenie cmdlet z interfejsu programu Windows PowerShell urządzenia StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Uruchom to polecenie cmdlet na obu kontrolerach.

Jeśli sprawdzono, że połączenie istnieje, ale problem nadal występuje, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania dalszych instrukcji.

**Co zrobić, jeśli podczas aktualizowania urządzenia do aktualizacji Update 4 jest wyświetlany komunikat o niepowodzeniu aktualizacji, a na obu kontrolerach działa aktualizacja Update 4?**

Począwszy od aktualizacji Update 4, jeśli na obu kontrolerach działa ta sama wersja oprogramowania i wystąpi niepowodzenie aktualizacji, kontrolery nie przejdą w tryb odzyskiwania. Ta sytuacja może wystąpić, jeśli poprawka oprogramowania urządzenia (aktualizacja 1-rzędna) zostanie zastosowana pomyślnie na obu kontrolerach, ale inne poprawki (2-rzędna i 3-rzędna) mają dopiero zostać zastosowane. Począwszy od aktualizacji Update 4, kontrolery przejdą w tryb odzyskiwania tylko wtedy, gdy na obu kontrolerach działają różne wersje oprogramowania. 

Jeśli użytkownik zobaczy niepowodzenie aktualizacji po uruchomieniu aktualizacji Update 4 na obu kontrolerach, zalecane jest odczekanie kilku minut i wykonanie ponownej próby przeprowadzenia aktualizacji. Jeśli ponowna próba się nie powiedzie, należy skontaktować się z pomocą techniczną firmy Microsoft.
