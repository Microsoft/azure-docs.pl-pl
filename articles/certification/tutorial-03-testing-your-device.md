---
title: Program certyfikowany na platformie Azure — samouczek — testowanie urządzenia
description: Przewodnik krok po kroku dotyczący testowania urządzenia za pomocą usługi AICS w portalu Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: ef82d44ef44189c0430ba9789baf3279fbe49a9c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310958"
---
# <a name="tutorial-test-and-submit-your-device"></a>Samouczek: testowanie i przesyłanie urządzenia

Kolejna główna faza procesu certyfikacji (chociaż może zostać ukończona przed dodaniem szczegółów urządzenia) obejmuje testowanie urządzenia. Za pomocą portalu będziesz używać usługi certyfikatów Azure IoT (AICS) w celu zademonstrowania wydajności urządzenia zgodnie z naszymi wymaganiami dotyczącymi certyfikacji. Po pomyślnym przekazaniu fazy testowania zostanie przesłane urządzenie do ostatecznego przeglądu i zatwierdzenia przez zespół certyfikacji platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Łączenie urządzenia z IoT Hub przy użyciu usługi Device Provisioning Service (DPS)
> * Przetestuj urządzenie zgodnie z wybranymi programami certyfikacji
> * Prześlij urządzenie do przeglądu przez zespół certyfikacji platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Należy się zalogować i utworzyć projekt dla swojego urządzenia w [portalu certyfikowanego urządzenia platformy Azure](https://certify.azure.com). Aby uzyskać więcej informacji, zobacz [samouczek](tutorial-01-creating-your-project.md).
- Obowiązkowe Zalecamy przygotowanie urządzenia i ręczne zweryfikowanie ich wydajności zgodnie z wymaganiami certyfikacji. Jest to spowodowane tym, że jeśli chcesz ponownie przetestować przy użyciu innego kodu urządzenia lub programu certyfikacji, musisz utworzyć nowy projekt.

## <a name="connecting-your-device-using-dps"></a>Łączenie urządzenia przy użyciu usługi DPS

Wszystkie Certyfikowane urządzenia są wymagane do zaprezentowania możliwości nawiązania połączenia z IoT Hub przy użyciu usługi DPS. Poniższe kroki przedstawiają sposób pomyślnego nawiązania połączenia urządzenia w celu przetestowania w portalu.

1. Aby rozpocząć etap testowania, wybierz `Connect & test` link na stronie Podsumowanie projektu:  

    ![Połącz i Testuj łącze](./media/images/connect-and-test-link.png)

1. W zależności od wybranych certyfikatów zobaczysz wymagane testy na stronie "łączenie & test". Zapoznaj się z tymi tematami, aby upewnić się, że są one stosowane do prawidłowego programu certyfikacji.  

    ![Strona łączenie i testowanie](./media/images/connect-and-test.png)

1. Połącz urządzenie z IoT Hub przy użyciu usługi Device Provisioning (DPS). Usługa DPS obsługuje opcje łączności kluczy symetrycznych, certyfikacji X. 509 i Trusted Platform Module (TPM). Jest to wymagane w przypadku wszystkich certyfikatów.

    - *Aby uzyskać więcej informacji na temat łączenia urządzenia z usługą Azure IoT Hub przy użyciu usługi DPS, odwiedź stronę [Omówienie aprowizacji urządzeń](../iot-dps/about-iot-dps.md "Omówienie usługi Device Provisioning").*
    
1. W przypadku korzystania z kluczy symetrycznych zostanie wyświetlony monit o skonfigurowanie usługi DPS przy użyciu podanego zakresu identyfikatora usługi DPS, identyfikatora urządzenia, klucza uwierzytelniania i punktu końcowego usługi DPS. W przeciwnym razie użytkownik zostanie poproszony o podanie certyfikatu X. 509 lub klucza poręczenia.

1. Po skonfigurowaniu urządzenia przy użyciu usługi DPS Potwierdź połączenie, klikając `Connect` przycisk w dolnej części strony. Po pomyślnym nawiązaniu połączenia możesz przechodzić do fazy testowania, klikając `Next` przycisk.  

    ![Połącz i Testuj połączone](./media/images/connected.png)

## <a name="testing-your-device"></a>Testowanie urządzenia

Po pomyślnym nawiązaniu połączenia urządzenia z usługą AICS można przystąpić do uruchamiania testów certyfikacji specyficznych dla programu certyfikacyjnego, którego dotyczy.

1. W **przypadku certyfikacji urządzenia z certyfikatem platformy Azure**: na karcie "Wybierz możliwość urządzenia" Przejrzyj i wybierz testy, które chcesz uruchomić na urządzeniu.
1. **W przypadku certyfikatów Plug and Play IoT**: uważnie Przejrzyj parametry, które będą sprawdzane podczas testu zadeklarowanego w modelu urządzenia.
1. **W przypadku certyfikacji zarządzanej za pośrednictwem przeglądarki Edge** nie są wymagane żadne dodatkowe kroki poza demonstrowaniem łączności.
1. Po ukończeniu niezbędnych przygotowań dla określonego programu certyfikacyjnego wybierz opcję `Next` przejścia do fazy "test".
1. Wybierz `Run tests` na stronie, aby rozpocząć Uruchamianie AICS z urządzeniem.
1. Po otrzymaniu powiadomienia o przekazaniu testów wybierz opcję `Finish` powrotu do strony podsumowania.

![Test zakończony niepowodzenie](./media/images/test-pass.png)

7. Jeśli masz dodatkowe pytania lub potrzebujesz pomocy w rozwiązywaniu problemów z usługą AICS, odwiedź nasze wskazówki dotyczące rozwiązywania problemów.

> [!NOTE]
> Mimo że będziesz mieć możliwość ukończenia procesu certyfikacji online dla usługi IoT Plug and Play i usługi Edge zarządzanej bez konieczności przesyłania urządzenia do przeglądu ręcznego, możesz skontaktować się z członkiem zespołu urządzenia z certyfikatem platformy Azure w celu przeprowadzenia dalszej weryfikacji urządzenia po przetestowaniu za pomocą naszej usługi Automation.

## <a name="submitting-your-device-for-review"></a>Przesyłanie urządzenia do przeglądu

Po zakończeniu wszystkich obowiązkowych pól w sekcji "Szczegóły urządzenia" i pomyślnie przeszedł automatyczne testowanie w procesie "Connect & test" możesz teraz powiadomić zespół z certyfikatem platformy Azure, który jest gotowy do przeglądu certyfikacji.

1. Wybierz `Submit for review` na stronie Podsumowanie projektu:  

    ![Przejrzyj i certyfikowanie linku](./media/images/review-and-certify.png)

1. Potwierdź przesłane dane w oknie podręcznym. Po przesłaniu urządzenia wszystkie szczegóły urządzenia będą tylko do odczytu do momentu zażądania edycji. (Zobacz [jak edytować informacje o urządzeniu po opublikowaniu](./how-to-edit-published-device.md)).  

    ![Uruchom okno dialogowe przeglądu certyfikacji](./media/images/start-certification-review.png)

1. Po przesłaniu projektu Strona Podsumowanie projektu będzie wskazywać, że projekt jest `Under Certification Review` członkiem zespołu certyfikacji platformy Azure:  

    ![W obszarze przegląd](./media/images/review-and-certify-under-review.png)

1. W ciągu 5-7 dni roboczych oczekujesz, że odpowiedź e-mail od zespołu certyfikacji platformy Azure na adres podany w profilu firmy dotycząca stanu przesłania urządzenia.

    - Zatwierdzenie zatwierdzone  
        Po przejrzeniu i zatwierdzeniu projektu otrzymasz wiadomość e-mail. Wiadomość e-mail obejmuje zestaw plików, w tym identyfikator urządzenia z certyfikatem platformy Azure, wskazówki dotyczące użycia znaczków oraz inne informacje na temat sposobu, w jaki można wzmocnić komunikat, że urządzenie jest certyfikowane. Gratulacje!

    - Oczekujące przesyłanie  
        W przypadku, gdy projekt nie jest zatwierdzony, będzie można wprowadzić zmiany w szczegółach projektu, a następnie ponownie przesłać urządzenie w celu certyfikacji. Zostanie wysłana wiadomość e-mail z informacją o tym, dlaczego projekt nie został zatwierdzony, i kroki ponownego przesłania do certyfikacji.

## <a name="next-steps"></a>Następne kroki

Gratulacje! Urządzenie pomyślnie przekazało wszystkie testy i zostało zatwierdzone przez program urządzenia z certyfikatem platformy Azure. Możesz teraz opublikować urządzenie w naszym katalogu certyfikowanych urządzeń platformy Azure, w którym klienci mogą kupować Twoje produkty bez obaw w ich wydajności dzięki platformie Azure.
> [!div class="nextstepaction"]
> [Samouczek: publikowanie urządzenia](tutorial-04-publishing-your-device.md)

