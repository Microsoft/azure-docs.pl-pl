---
title: Zarządzanie kontrolerami urządzeń z serii StorSimple 8000 | Microsoft Docs
description: Informacje na temat zatrzymywania, ponownego uruchamiania, zamykania lub resetowania kontrolerów urządzeń StorSimple.
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
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 090797549cc61aa27945114e5ef8b666226b66e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956997"
---
# <a name="manage-your-storsimple-device-controllers"></a>Zarządzanie kontrolerami urządzeń StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano różne operacje, które mogą być wykonywane na kontrolerach urządzeń StorSimple. Kontrolery na urządzeniu StorSimple są nadmiarowe (równorzędne) kontrolery w konfiguracji aktywne-pasywne. W danym momencie tylko jeden kontroler jest aktywny i przetwarza wszystkie operacje na dysku i sieci. Drugi kontroler jest w trybie pasywnym. Jeśli aktywny kontroler ulegnie awarii, kontroler pasywny automatycznie stanie się aktywny.

Ten samouczek zawiera instrukcje krok po kroku dotyczące zarządzania kontrolerami urządzeń przy użyciu:

* Blok **kontrolerów** urządzenia w usłudze StorSimple Device Manager.
* program Windows PowerShell dla usługi StorSimple.

Zalecamy zarządzanie kontrolerami urządzeń za pośrednictwem usługi StorSimple Device Manager. Jeśli akcja może zostać wykonana tylko przy użyciu program Windows PowerShell dla usługi StorSimple, samouczek zanotuje go.

Po przeczytaniu tego samouczka będziesz mieć możliwość:

* Ponowne uruchamianie lub zamykanie kontrolera urządzenia StorSimple
* Zamykanie urządzenia StorSimple
* Zresetuj urządzenie z StorSimple do domyślnych ustawień fabrycznych

## <a name="restart-or-shut-down-a-single-controller"></a>Ponowne uruchamianie lub zamykanie pojedynczego kontrolera
Nie jest wymagane ponowne uruchomienie lub zamknięcie kontrolera w ramach normalnej operacji systemu. Operacje zamykania dla pojedynczego kontrolera urządzeń są wspólne tylko w przypadkach, w których składnik sprzętowy urządzenia nie wymaga zastąpienia. Może być również wymagane ponowne uruchomienie kontrolera w sytuacji, w której wydajność ma wpływ nadmierne użycie pamięci lub niedziałający kontroler. Może być również konieczne ponowne uruchomienie kontrolera po pomyślnym zastępowaniu kontrolera, jeśli chcesz włączyć i przetestować zastąpiony kontroler.

Ponowne uruchomienie urządzenia nie przerywa pracy podłączonych inicjatorów przy założeniu, że kontroler pasywny jest dostępny. Jeśli kontroler pasywny jest niedostępny lub wyłączony, ponowne uruchomienie aktywnego kontrolera może spowodować zakłócenia usługi i przestoje.

> [!IMPORTANT]
> * **Uruchomiony Kontroler nigdy nie powinien być fizycznie usunięty, ponieważ spowoduje to utratę nadmiarowości i zwiększone ryzyko przestoju.**
> * Poniższa procedura dotyczy tylko urządzenia fizycznego StorSimple. Aby uzyskać informacje o sposobach uruchamiania, zatrzymywania i ponownego uruchamiania urządzenia w chmurze StorSimple, zobacz [Working with the Cloud urządzenie](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

Można ponownie uruchomić lub zamknąć pojedynczy kontroler urządzenia za pomocą Azure Portal usługi StorSimple Device Manager lub program Windows PowerShell dla usługi StorSimple.

Aby zarządzać kontrolerami urządzeń z poziomu Azure Portal, wykonaj następujące czynności.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Aby ponownie uruchomić lub zamknąć kontroler w Azure Portal
1. W usłudze StorSimple Device Manager przejdź do pozycji **urządzenia**. Wybierz urządzenie z listy urządzeń. 

    ![Wybierz urządzenie](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Przejdź do pozycji **ustawienia > kontrolery**.
   
    ![Sprawdź, czy kontrolery urządzeń StorSimple są w dobrej kondycji](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. W bloku **Kontrolery** Sprawdź, czy stan obu kontrolerów na urządzeniu jest w **dobrej kondycji**. Wybierz kontroler, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Uruchom ponownie** lub **Zamknij**.

    ![Wybierz pozycję Uruchom ponownie lub Zamknij StorSimple kontrolery urządzeń](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Zadanie jest tworzone w celu ponownego uruchomienia lub zamknięcia kontrolera i wyświetlenie odpowiednich ostrzeżeń, jeśli istnieją. Aby monitorować ponowne uruchomienie lub zamknięcie, przejdź do pozycji **usługa > dzienniki aktywności** , a następnie Przefiltruj według parametrów specyficznych dla usługi. Jeśli kontroler został zamknięty, należy wypchnąć przycisk zasilacza, aby włączyć go.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Aby ponownie uruchomić lub zamknąć kontroler w program Windows PowerShell dla usługi StorSimple
Wykonaj następujące kroki, aby zamknąć lub ponownie uruchomić jeden kontroler na urządzeniu StorSimple z poziomu program Windows PowerShell dla usługi StorSimple.

1. Dostęp do urządzenia za pośrednictwem konsoli szeregowej lub sesji Telnet z komputera zdalnego. Aby połączyć się z kontrolerem 0 lub kontrolerem 1, wykonaj kroki opisane w temacie [Korzystanie z konsoli szeregowej urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**.
3. W komunikacie transparentu Zanotuj kontroler, z którym nawiązano połączenie (kontroler 0 lub kontroler 1) i czy jest to kontroler aktywny lub pasywny (Standby).
   
   * Aby zamknąć pojedynczy kontroler, w wierszu polecenia wpisz:
     
       `Stop-HcsController`
     
       Spowoduje to zamknięcie kontrolera, z którym nawiązano połączenie. Jeśli zatrzymasz aktywny kontroler, urządzenie przejdzie w tryb failover na kontrolerze pasywnym.

   * Aby ponownie uruchomić kontroler, w wierszu polecenia wpisz:
     
       `Restart-HcsController`
     
       Spowoduje to ponowne uruchomienie kontrolera, z którym nawiązano połączenie. Jeśli ponownie uruchomisz kontroler Active, przejdzie on w tryb failover na kontrolerze pasywnym przed ponownym uruchomieniem.

## <a name="shut-down-a-storsimple-device"></a>Zamykanie urządzenia StorSimple

W tej sekcji wyjaśniono, jak zamknąć uruchomione lub uszkodzone Urządzenie StorSimple z komputera zdalnego. Urządzenie jest wyłączone po wyłączeniu obu kontrolerów urządzeń. Urządzenie jest zamykane, gdy urządzenie jest fizycznie przenoszone lub jest wyłączane z usługi.

> [!IMPORTANT]
> Przed zamknięciem urządzenia Sprawdź kondycję składników urządzenia. Przejdź do urządzenia, a następnie kliknij pozycję **ustawienia > kondycja sprzętu**. W bloku **kondycja stanu i sprzętu** Sprawdź, czy stan diody LED wszystkich składników jest zielony. Tylko urządzenie o dobrej kondycji ma zielony stan. Jeśli urządzenie jest zamykane w celu zastąpienia nieprawidłowego składnika, zobaczysz błąd (czerwony) lub obniżony stan (żółty) dla odpowiednich składników.


#### <a name="to-shut-down-a-storsimple-device"></a>Aby zamknąć Urządzenie StorSimple

1. Użyj procedury [restart lub Zamknij kontroler](#restart-or-shut-down-a-single-controller) , aby zidentyfikować i zamknąć kontroler pasywny na urządzeniu. Tę operację można wykonać w Azure Portal lub program Windows PowerShell dla usługi StorSimple.
2. Powtórz powyższy krok, aby zamknąć kontroler aktywny.
3. Należy teraz sprawdzić płaszczyznę tyłu urządzenia. Po wyłączeniu obu kontrolerów, diody LED stanu na kontrolerach powinny migać czerwoną. Jeśli zachodzi potrzeba wyłączenia całkowicie urządzenia w tym momencie, należy przerzucić przełączniki zasilające w modułach zasilanie i chłodzące (PCMs). Powinno to spowodować wyłączenie urządzenia.

## <a name="reset-the-device-to-factory-default-settings"></a>Resetowanie urządzenia do domyślnych ustawień fabrycznych

> [!IMPORTANT]
> Jeśli musisz zresetować urządzenie do domyślnych ustawień fabrycznych, skontaktuj się z pomoc techniczna firmy Microsoft. Opisana poniżej procedura powinna być używana tylko w połączeniu z pomoc techniczna firmy Microsoft.

Ta procedura zawiera opis sposobu resetowania urządzenia Microsoft Azure StorSimple do domyślnych ustawień fabrycznych przy użyciu program Windows PowerShell dla usługi StorSimple.
Zresetowanie urządzenia spowoduje domyślnie usunięcie wszystkich danych i ustawień z całego klastra.

Wykonaj następujące kroki, aby przywrócić domyślne ustawienia fabryczne urządzenia Microsoft Azure StorSimple:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Aby zresetować ustawienia domyślne urządzenia do program Windows PowerShell dla usługi StorSimple
1. Uzyskaj dostęp do urządzenia za pomocą jego konsoli szeregowej. Sprawdź wiadomość banerową, aby upewnić się, że nawiązano połączenie z **aktywnym** kontrolerem.
2. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**.
3. W wierszu polecenia wpisz następujące polecenie, aby zresetować cały klaster, usuwając wszystkie dane, metadane i ustawienia kontrolera:
   
    `Reset-HcsFactoryDefault`
   
    Aby zamiast tego zresetować pojedynczy kontroler, należy użyć polecenia cmdlet  [Reset-HcsFactoryDefault](/previous-versions/windows/powershell-scripting/dn688132(v=wps.630)) z `-scope` parametrem.)
   
    System zostanie wielokrotnie uruchomiony ponownie. Użytkownik zostanie powiadomiony o pomyślnym zakończeniu resetowania. W zależności od modelu systemu może upłynąć 45-60 minut na urządzenie 8100 i 60-90 min, aby w ciągu tego procesu wystąpił komunikat 8600.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Pytania i odpowiedzi dotyczące zarządzania kontrolerami urządzeń
Ta sekcja zawiera podsumowanie niektórych często zadawanych pytań dotyczących zarządzania kontrolerami urządzeń StorSimple.

**Pytania.** Co się stanie, jeśli kontrolery na urządzeniu są w dobrej kondycji i są włączone i ponownie uruchamiają lub wyłączają aktywny kontroler?

**Z.** Jeśli kontrolery na urządzeniu są w dobrej kondycji i są włączone, zostanie wyświetlony monit o potwierdzenie. Możesz wybrać następujące opcje:

* **Uruchom ponownie aktywny kontroler** — zostanie wyświetlony komunikat z powiadomieniem, że ponowne uruchomienie aktywnego kontrolera spowodowało przełączenie urządzenia w tryb failover na kontrolerze pasywnym. Kontroler zostanie uruchomiony ponownie.
* **Zamknij aktywny kontroler** — zostanie wyświetlony komunikat z powiadomieniem, że Zamknięcie aktywnego kontrolera skutkuje przestojem. Aby włączyć kontroler, należy również wypchnąć przycisk energia na urządzeniu.

**Pytania.** Co się stanie, Jeśli kontroler pasywny na urządzeniu jest niedostępny lub wyłączony i ponownie uruchomisz lub wyłączysz aktywny kontroler?

**Z.** Jeśli kontroler pasywny na urządzeniu jest niedostępny lub wyłączony i użytkownik zdecyduje się na:

* **Uruchom ponownie aktywny kontroler** — zostanie wyświetlony komunikat informujący o tym, że kontynuowanie operacji spowoduje chwilowe zakłócenie działania usługi i wyświetli monit o potwierdzenie.
* **Zamknij aktywny kontroler** — otrzymasz powiadomienie, że kontynuowanie operacji powoduje przestoje. Aby włączyć urządzenie, należy również wypchnąć przycisk energia na jednym lub obu kontrolerach. Zostanie wyświetlony monit o potwierdzenie.

**Pytania.** Kiedy proces ponownego uruchamiania lub zamykania kontrolera kończy się niepowodzeniem?

**Z.** Ponowne uruchomienie lub zamknięcie kontrolera może zakończyć się niepowodzeniem, jeśli:

* Aktualizacja urządzenia jest w toku.
* Ponowne uruchomienie kontrolera jest już w toku.
* Zamykanie kontrolera jest już w toku.

**Pytania.** Jak ustalić, czy kontroler został ponownie uruchomiony lub zamknięty?

**Z.** Stan kontrolera można sprawdzić w bloku kontroler. Stan kontrolera wskazuje, czy kontroler jest w trakcie ponownego uruchamiania lub zamykania. Ponadto blok **alerty** zawiera alert informacyjny, Jeśli kontroler jest ponownie uruchamiany lub wyłączony. Operacje ponownego uruchamiania i zamykania kontrolera są również rejestrowane w dziennikach aktywności. Aby uzyskać więcej informacji na temat dzienników aktywności, przejdź do [wyświetlania dzienników aktywności](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**Pytania.** Czy istnieje jakikolwiek wpływ na we/wy w wyniku przełączenia kontrolera do trybu failover?

**Z.** Połączenia TCP między inicjatorami i aktywnym kontrolerem będą resetowane w wyniku przełączenia kontrolera do trybu failover, ale zostaną ponownie nawiązane, gdy zostanie przyjęty kontroler pasywny. W przypadku działania we/wy między inicjatorami a urządzeniem w trakcie tej operacji może istnieć tymczasowy (mniej niż 30 sekund).

**Pytania.** Jak mogę zwrócić kontroler do usługi po jej zamknięciu i usunięciu?

**Z.** Aby przywrócić kontroler do usługi, należy go wstawić do obudowy zgodnie z opisem w artykule [Zastąp moduł kontrolera na urządzeniu StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Następne kroki
* Jeśli wystąpią problemy z kontrolerami urządzeń StorSimple, których nie można rozwiązać za pomocą procedur wymienionych w tym samouczku, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
* Aby dowiedzieć się więcej o korzystaniu z usługi StorSimple Device Manager, przejdź do [korzystania z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).