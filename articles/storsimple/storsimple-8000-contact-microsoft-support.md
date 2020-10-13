---
title: Utwórz bilet pomocy technicznej lub przypadek dla serii StorSimple 8000
description: Dowiedz się, jak rejestrować żądania obsługi i uruchamiać sesję pomocy technicznej na urządzeniu z serii StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: daf54b64e33b83b909fe91455cee2f5c1ec27d3d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960324"
---
# <a name="contact-microsoft-support"></a>Kontakt z pomocą techniczną firmy Microsoft

StorSimple Menedżer urządzeń zapewnia możliwość **rejestrowania nowego żądania obsługi** w bloku podsumowania usługi. Jeśli wystąpią problemy z rozwiązaniem StorSimple, możesz utworzyć żądanie obsługi dla pomocy technicznej. W sesji online z inżynierem pomocy technicznej może być również konieczne rozpoczęcie sesji obsługi na urządzeniu StorSimple. W tym artykule przedstawiono następujące instrukcje:

* Jak utworzyć żądanie obsługi.
* Jak zarządzać cyklem życia żądania pomocy technicznej z poziomu portalu.
* Jak uruchomić sesję obsługi w interfejsie programu Windows PowerShell urządzenia StorSimple.

Przed utworzeniem Support request należy zapoznać się z [tematem umowy SLA i informacjami dotyczącymi serii StorSimple 8000](https://msdn.microsoft.com/library/mt433077.aspx) .

## <a name="create-a-support-request"></a>Tworzenie żądania obsługi

W zależności od Twojego [planu pomocy](https://azure.microsoft.com/support/plans/)technicznej możesz utworzyć bilety dla problemu na urządzeniu z systemem StorSimple bezpośrednio z bloku podsumowania usługi StorSimple Menedżer urządzeń. Wykonaj następujące kroki, aby utworzyć żądanie obsługi:

#### <a name="to-create-a-support-request"></a>Aby utworzyć żądanie pomocy technicznej

1. Przejdź do usługi Menedżer urządzeń StorSimple. W ustawieniach bloku podsumowanie usługi przejdź do sekcji **Obsługa i rozwiązywanie problemów** , a następnie kliknij pozycję **nowe żądanie obsługi**.
     
    ![Skontaktuj się z pomocą techniczną firmy Microsoft za pośrednictwem nowego portalu](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. W bloku **nowe żądanie obsługi** wybierz pozycję **podstawowe**. W bloku **podstawowe** wykonaj następujące czynności:
   1. Z listy rozwijanej **typ problemu** wybierz pozycję **techniczne**.
   2. Automatycznie wybierana jest bieżąca **subskrypcja**, typ **usługi** i **zasób** (Usługa StorSimple Menedżer urządzeń). 
   3. Wybierz **Plan pomocy technicznej** z listy rozwijanej, jeśli masz wiele planów skojarzonych z Twoją subskrypcją. Potrzebujesz płatnego planu pomocy technicznej, aby umożliwić pomoc techniczną.
   4. Kliknij przycisk **Dalej**.

       ![Skontaktuj się z pomocą techniczną firmy Microsoft za pośrednictwem nowego portalu 2](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. W bloku **nowe żądanie obsługi** wybierz pozycję **krok 2**. W bloku **problemu** wykonaj następujące czynności:
    
    1. Wybierz **ważność**.
    2. Określ, czy problem jest związany z urządzeniem, czy z usługą StorSimple Menedżer urządzeń.
    3. Wybierz **kategorię** tego problemu i podaj więcej **szczegółowych informacji** o problemie.
    4. Podaj datę i godzinę rozpoczęcia problemu.
    5. W polu **przekazywanie plików**kliknij ikonę folderu, aby przejść do pakietu pomocy technicznej.
    6. Sprawdź **udostępnianie informacji diagnostycznych**.
    7. Kliknij przycisk **Dalej**.

       ![Skontaktuj się z pomocą techniczną firmy Microsoft za pośrednictwem nowego portalu 3](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. W bloku **nowe żądanie obsługi** kliknij pozycję **krok 3. informacje kontaktowe**. W bloku **informacje kontaktowe** wykonaj następujące czynności:

   1. W obszarze **Opcje kontaktu**Podaj preferowaną metodę kontaktu (telefon lub adres e-mail) i język. Czas odpowiedzi jest wybierany automatycznie na podstawie planu subskrypcji.
   2. W informacjach kontaktowych Podaj swoją nazwę, adres e-mail, kontakt opcjonalny, kraj/region. Zaznacz pole wyboru **Zapisz zmiany kontaktu w przyszłych żądaniach obsługi** .
   3. Kliknij pozycję **Utwórz**.
   
       ![Skontaktuj się z pomocą techniczną firmy Microsoft za pośrednictwem nowego portalu 4](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Pomoc techniczna firmy Microsoft będzie używać tych informacji w celu uzyskania dalszych informacji, diagnozowania i rozwiązywania problemów.
      Po przesłaniu żądania inżynier pomocy technicznej skontaktuje się z Tobą najszybciej, jak to możliwe, aby kontynuować żądanie.

## <a name="manage-a-support-request"></a>Zarządzanie żądaniem obsługi

Po utworzeniu biletu pomocy technicznej możesz zarządzać jego cyklem życia z poziomu portalu.

#### <a name="to-manage-your-support-requests"></a>Aby zarządzać żądaniami pomocy technicznej

1. Aby przejść do strony pomoc i obsługa techniczna, przejdź do okna **przeglądaj > pomoc i obsługa techniczna**.

    ![Zarządzanie żądaniami obsługi](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. W bloku **Pomoc i obsługa techniczna** zostanie wyświetlona tabelaryczna lista wszystkich żądań obsługi.

    ![Zarządzanie żądaniami obsługi 2](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Wybierz i kliknij żądanie pomocy technicznej. Możesz wyświetlić stan i szczegóły tego żądania. Kliknij pozycję **+ Nowy komunikat** , jeśli chcesz kontynuować na tym żądaniu.

    ![Zarządzanie żądaniami obsługi 3](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Rozpocznij sesję pomocy technicznej w program Windows PowerShell dla usługi StorSimple

Aby rozwiązać wszelkie problemy, które mogą wystąpić w przypadku urządzenia z systemem StorSimple, należy skontaktować się z zespołem pomoc techniczna firmy Microsoft. Aby zalogować się na urządzeniu, pomoc techniczna firmy Microsoft może być konieczne użycie sesji obsługi.

Wykonaj następujące kroki, aby rozpocząć sesję obsługi:

#### <a name="to-start-a-support-session"></a>Aby rozpocząć sesję pomocy technicznej

1. Uzyskaj dostęp do urządzenia bezpośrednio przy użyciu konsoli szeregowej lub za pośrednictwem sesji Telnet z komputera zdalnego. W tym celu wykonaj kroki opisane w temacie [Korzystanie z programu, aby nawiązać połączenie z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. W otwartej sesji naciśnij klawisz **Enter** , aby wyświetlić wiersz polecenia.
3. W menu konsoli szeregowej wybierz opcję 1, **Zaloguj się z pełnymi prawami dostępu**.
4. W wierszu polecenia wpisz następujące hasło:
   
    `Password1`
5. W wierszu polecenia wpisz następujące polecenie:
   
    `Enable-HcsSupportAccess`
6. Zostanie wyświetlony zaszyfrowany ciąg. Skopiuj ten ciąg do edytora tekstu, takiego jak Notatnik.
7. Zapisz ten ciąg i wyślij go w wiadomości e-mail do pomoc techniczna firmy Microsoft.

> [!IMPORTANT]
> Dostęp do pomocy technicznej można wyłączyć, uruchamiając `Disable-HcsSupportAccess` . Urządzenie StorSimple podejmie również próbę wyłączenia dostępu do pomocy technicznej 8 godzin po zainicjowaniu sesji. Najlepszym rozwiązaniem jest zmiana poświadczeń urządzenia StorSimple po zainicjowaniu sesji obsługi.


## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak diagnozować i rozwiązywać problemy związane z urządzeniem z serii StorSimple 8000](storsimple-8000-troubleshoot-deployment.md)
