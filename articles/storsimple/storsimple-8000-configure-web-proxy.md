---
title: Konfigurowanie serwera proxy sieci Web dla urządzenia z serii StorSimple 8000 | Microsoft Docs
description: Informacje dotyczące konfigurowania ustawień serwera proxy sieci Web dla urządzenia StorSimple za pomocą program Windows PowerShell dla usługi StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 48671e7558ea1bd613d33372c96fa3c563407e81
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017223"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurowanie serwera proxy sieci Web dla urządzenia StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku opisano, jak używać program Windows PowerShell dla usługi StorSimple do konfigurowania i wyświetlania ustawień serwera proxy sieci Web dla urządzenia StorSimple. Ustawienia serwera proxy sieci Web są używane przez urządzenie StorSimple podczas komunikowania się z chmurą. Serwer proxy sieci Web jest używany do dodawania kolejnej warstwy zabezpieczeń, filtrowania zawartości, pamięci podręcznej, co ułatwia wymagania dotyczące przepustowości, a także do analizy.

Wskazówki zawarte w tym samouczku dotyczą tylko urządzeń fizycznych z serii StorSimple 8000. Konfiguracja serwera proxy sieci Web nie jest obsługiwana w urządzeniu w chmurze StorSimple (8010 i 8020).

Serwer proxy sieci Web jest _opcjonalną_ konfiguracją urządzenia StorSimple. Serwer proxy sieci Web można skonfigurować tylko za pośrednictwem program Windows PowerShell dla usługi StorSimple. Konfiguracja jest procesem dwuetapowym w następujący sposób:

1. Najpierw skonfiguruj ustawienia serwera proxy sieci Web za pomocą Kreatora instalacji lub poleceń cmdlet program Windows PowerShell dla usługi StorSimple.
2. Następnie należy włączyć skonfigurowane ustawienia serwera proxy sieci Web za pomocą poleceń cmdlet program Windows PowerShell dla usługi StorSimple.

Po zakończeniu konfiguracji serwera proxy sieci Web można wyświetlić skonfigurowane ustawienia serwera proxy sieci Web w usłudze Microsoft Azure StorSimple Menedżer urządzeń i program Windows PowerShell dla usługi StorSimple.

Po przeczytaniu tego samouczka będziesz mieć możliwość:

* Skonfiguruj serwer proxy sieci Web przy użyciu Kreatora instalacji i poleceń cmdlet.
* Włącz serwer proxy sieci Web za pomocą poleceń cmdlet.
* Wyświetl ustawienia serwera proxy sieci Web w Azure Portal.
* Rozwiązywanie problemów podczas konfiguracji serwera proxy sieci Web.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurowanie serwera proxy sieci Web za pośrednictwem program Windows PowerShell dla usługi StorSimple

Aby skonfigurować ustawienia serwera proxy sieci Web, należy użyć jednej z następujących czynności:

* Kreator instalacji, który przeprowadzi Cię przez kroki konfiguracji.
* Polecenia cmdlet w program Windows PowerShell dla usługi StorSimple.

Każda z tych metod została omówiona w poniższych sekcjach.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurowanie serwera proxy sieci Web za pomocą Kreatora instalacji

Kreator instalacji zawiera instrukcje dotyczące konfiguracji serwera proxy sieci Web. Wykonaj następujące kroki, aby skonfigurować serwer proxy sieci Web na urządzeniu.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Aby skonfigurować serwer proxy sieci Web za pomocą Kreatora instalacji

1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi uprawnieniami dostępu** i podaj **hasło administratora urządzenia**. Wpisz następujące polecenie, aby rozpocząć sesję Kreatora instalacji:
   
    `Invoke-HcsSetupWizard`
2. Jeśli używasz Kreatora instalacji do rejestracji urządzenia po raz pierwszy, musisz skonfigurować wszystkie wymagane ustawienia sieciowe do momentu uzyskania dostępu do konfiguracji serwera proxy sieci Web. Jeśli urządzenie jest już zarejestrowane, Zaakceptuj wszystkie skonfigurowane ustawienia sieciowe, dopóki nie zostanie osiągnięta konfiguracja serwera proxy sieci Web. Po wyświetleniu monitu o skonfigurowanie ustawień serwera proxy sieci Web w Kreatorze instalacji wpisz **tak**.
3. W polu **adres URL serwera proxy sieci Web** Określ adres IP lub w pełni kwalifikowaną nazwę domeny (FQDN) serwera proxy sieci Web i numer portu TCP, który ma być używany przez urządzenie podczas komunikacji z chmurą. Użyj następującego formatu:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Domyślnie jest określony numer portu TCP 8080.
4. Wybierz typ uwierzytelniania jako **NTLM**, **podstawowy** lub **Brak**. Podstawowa jest najmniej bezpiecznym uwierzytelnianiem dla konfiguracji serwera proxy. NT LAN Manager (NTLM) to wysoce bezpieczny i skomplikowany protokół uwierzytelniania, który korzysta z trójstanowego systemu obsługi komunikatów (czasami cztery Jeśli wymagana jest dodatkowa integralność) w celu uwierzytelnienia użytkownika. Domyślnym uwierzytelnianiem jest NTLM. Aby uzyskać więcej informacji, zobacz Uwierzytelnianie [podstawowe](https://hc.apache.org/httpclient-3.x/authentication.html) i [NTLM](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **W usłudze StorSimple Menedżer urządzeń wykresy monitorowania urządzeń nie działają w przypadku włączenia uwierzytelniania podstawowego lub NTLM w konfiguracji serwera proxy dla urządzenia. Aby wykresy monitorowania działały, należy się upewnić, że uwierzytelnianie jest ustawione na NONE.**
  
5. W przypadku włączenia uwierzytelniania należy podać **nazwę użytkownika serwera proxy sieci Web** i **hasło serwera proxy sieci Web**. Należy również potwierdzić hasło.
   
    ![Konfigurowanie serwera proxy sieci Web na StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Jeśli rejestrujesz urządzenie po raz pierwszy, Kontynuuj rejestrację. Jeśli urządzenie zostało już zarejestrowane, kreator zakończy pracę. Skonfigurowane ustawienia są zapisywane.

Serwer proxy sieci Web jest teraz włączony. Możesz pominąć krok [Włącz serwer proxy sieci Web](#enable-web-proxy) i przejść bezpośrednio do [widoku ustawienia serwera proxy sieci Web w Azure Portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurowanie serwera proxy sieci Web za pomocą poleceń cmdlet program Windows PowerShell dla usługi StorSimple

Alternatywną metodą konfigurowania ustawień serwera proxy sieci Web jest za pośrednictwem poleceń cmdlet program Windows PowerShell dla usługi StorSimple. Wykonaj następujące kroki, aby skonfigurować serwer proxy sieci Web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Aby skonfigurować serwer proxy sieci Web za pomocą poleceń cmdlet
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło to `Password1` .
2. W wierszu polecenia wpisz polecenie:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Podaj i Potwierdź hasło po wyświetleniu monitu.
   
    ![Konfigurowanie serwera proxy sieci Web na StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Serwer proxy sieci Web jest teraz skonfigurowany i musi być włączony.

## <a name="enable-web-proxy"></a>Włącz serwer proxy sieci Web

Serwer proxy sieci Web jest domyślnie wyłączony. Po skonfigurowaniu ustawień serwera proxy sieci Web na urządzeniu StorSimple Użyj program Windows PowerShell dla usługi StorSimple, aby włączyć ustawienia serwera proxy sieci Web.

> [!NOTE]
> **Ten krok nie jest wymagany w przypadku konfigurowania serwera proxy sieci Web przy użyciu Kreatora instalacji. Serwer proxy sieci Web jest domyślnie automatycznie włączany po zakończeniu sesji Kreatora instalacji.**


Wykonaj następujące kroki w program Windows PowerShell dla usługi StorSimple, aby włączyć serwer proxy sieci Web na urządzeniu:

#### <a name="to-enable-web-proxy"></a>Aby włączyć serwer proxy sieci Web
1. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**. Po wyświetleniu monitu podaj **hasło administratora urządzenia**. Domyślne hasło to `Password1` .
2. W wierszu polecenia wpisz polecenie:
   
    `Enable-HcsWebProxy`
   
    Konfiguracja serwera proxy sieci Web została teraz włączona na urządzeniu StorSimple.
   
    ![Konfigurowanie serwera proxy sieci Web na StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Wyświetl ustawienia serwera proxy sieci Web w Azure Portal

Ustawienia serwera proxy sieci Web są konfigurowane za pomocą interfejsu programu Windows PowerShell i nie można ich zmienić z poziomu portalu. Można jednak wyświetlić te skonfigurowane ustawienia w portalu. Wykonaj następujące kroki, aby wyświetlić serwer proxy sieci Web.

#### <a name="to-view-web-proxy-settings"></a>Aby wyświetlić ustawienia serwera proxy sieci Web
1. Przejdź do **usługi StorSimple Menedżer urządzeń > urządzeń**. Wybierz i kliknij urządzenie, a następnie przejdź do pozycji **Ustawienia urządzenia > sieć**.

    ![Kliknij pozycję Sieć](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. W bloku **Ustawienia sieci** kliknij kafelek **serwer proxy sieci Web** .

    ![Kliknij pozycję Serwer proxy sieci Web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. W bloku **serwer proxy sieci Web** Przejrzyj skonfigurowane ustawienia serwera proxy sieci Web na urządzeniu StorSimple.
   
    ![Wyświetl ustawienia serwera proxy sieci Web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Błędy podczas konfiguracji serwera proxy sieci Web

Jeśli ustawienia serwera proxy sieci Web są nieprawidłowo skonfigurowane, komunikaty o błędach są wyświetlane użytkownikowi w program Windows PowerShell dla usługi StorSimple. W poniższej tabeli objaśniono niektóre z tych komunikatów o błędach, ich prawdopodobne przyczyny i zalecane akcje.

| Numer seryjny | Kod błędu HRESULT | Możliwa przyczyna główna | Zalecana akcja |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Polecenie jest uruchamiane z kontrolera pasywnego i nie może komunikować się z aktywnym kontrolerem. |Uruchom polecenie na aktywnym kontrolerze. Aby uruchomić polecenie z kontrolera pasywnego, należy nawiązać połączenie z pasywnym kontrolerem aktywnym. Jeśli ta łączność zostanie przerwana, należy nawiązać pomoc techniczna firmy Microsoft. |
| 2. |0x800710dd — Identyfikator operacji jest nieprawidłowy |Ustawienia serwera proxy nie są obsługiwane na urządzeniu w chmurze StorSimple. |Ustawienia serwera proxy nie są obsługiwane na urządzeniu w chmurze StorSimple. Można je skonfigurować tylko na urządzeniu fizycznym StorSimple. |
| 3. |0x80070057 — nieprawidłowy parametr |Jeden z parametrów podanych dla ustawień serwera proxy jest nieprawidłowy. |Identyfikator URI nie ma poprawnego formatu. Użyj następującego formatu: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba — serwer RPC niedostępny |Główna przyczyna jest jedną z następujących czynności:</br></br>Klaster nie działa. </br></br>Usługa datapath nie jest uruchomiona.</br></br>Polecenie jest uruchamiane z kontrolera pasywnego i nie może komunikować się z aktywnym kontrolerem. |Zaangażuj pomoc techniczna firmy Microsoft, aby upewnić się, że klaster działa i usługa datapath jest uruchomiona.</br></br>Uruchom polecenie z poziomu aktywnego kontrolera. Aby uruchomić polecenie z kontrolera pasywnego, należy się upewnić, że kontroler pasywny może komunikować się z aktywnym kontrolerem. Jeśli ta łączność zostanie przerwana, należy nawiązać pomoc techniczna firmy Microsoft. |
| 5. |0X800706be — wywołanie RPC nie powiodło się |Klaster nie działa. |Zaangażuj pomoc techniczna firmy Microsoft, aby upewnić się, że klaster działa. |
| 6. |0x8007138f — nie znaleziono zasobu klastra |Nie znaleziono zasobu klastra usługi platformy. Taka sytuacja może wystąpić, jeśli instalacja nie była właściwa. |Może być konieczne przeprowadzenie resetowania do ustawień fabrycznych na urządzeniu. Może być konieczne utworzenie zasobu platformy. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| 7. |0x8007138c — zasób klastra nie jest w trybie online |Zasoby platformy lub klastra usługi datapath nie są w trybie online. |Skontaktuj się pomoc techniczna firmy Microsoft, aby upewnić się, że zasób datapath i usługa platformy są w trybie online. |

> [!NOTE]
> * Powyższa lista komunikatów o błędach nie jest wyczerpująca.
> * Błędy związane z ustawieniami serwera proxy sieci Web nie będą wyświetlane w Azure Portal w usłudze Menedżer urządzeń usługi StorSimple. Jeśli wystąpi problem z serwerem proxy sieci Web po zakończeniu konfiguracji, stan urządzenia zmieni się na **offline** w portalu klasycznym. |

## <a name="next-steps"></a>Następne kroki
* W przypadku napotkania problemów podczas wdrażania urządzenia lub konfigurowania ustawień serwera proxy sieci Web zapoznaj się z tematem [Rozwiązywanie problemów z wdrożeniem urządzenia StorSimple](./storsimple-8000-troubleshoot-deployment.md).
* Aby dowiedzieć się, jak korzystać z usługi StorSimple Menedżer urządzeń, przejdź do [korzystania z usługi StorSimple Menedżer urządzeń w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).