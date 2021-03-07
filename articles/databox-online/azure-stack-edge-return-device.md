---
title: Zwróć urządzenie Azure Stack Edge w firmie Pro | Microsoft Docs
description: Dowiedz się, jak wyczyścić dane i zwrócić urządzenie Azure Stack EDGE Pro, a następnie usuń zasób skojarzony z urządzeniem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: cb11d7d3b2da9ab793cb18814e4021ea7afeb806
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443594"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Zwróć urządzenie Azure Stack Edge w wersji Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano sposób czyszczenia danych, a następnie zwrócenia urządzenia z usługą Azure Stack EDGE Pro. Po zwróceniu urządzenia można także usunąć zasób skojarzony z urządzeniem.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Czyszczenie danych z dysków z danymi na urządzeniu
> * Inicjowanie zwrotu urządzenia w witrynie Azure Portal
> * Pakowanie urządzenia i planowanie odbioru
> * Usuń zasób w Azure Portal

## <a name="erase-data-from-the-device"></a>Wymazywanie danych z urządzenia

Aby wyczyścić dane z dysków danych urządzenia, należy zresetować urządzenie.

Przed zresetowaniem Utwórz kopię danych lokalnych na urządzeniu, jeśli jest to konieczne. Dane z urządzenia można skopiować do kontenera usługi Azure Storage. 

Możesz zainicjować zwracanie urządzenia nawet przed zresetowaniem urządzenia.

Możesz zresetować urządzenie w lokalnym interfejsie użytkownika sieci Web lub w programie PowerShell. Aby uzyskać instrukcje dotyczące programu PowerShell, zobacz [Resetowanie urządzenia](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - Jeśli wymieniasz lub uaktualniasz nowe urządzenie, zalecamy zresetowanie urządzenia tylko po odebraniu nowego urządzenia.
> - Zresetowanie urządzenia spowoduje usunięcie tylko tych danych z urządzenia. Dane znajdujące się w chmurze nie zostaną usunięte i nie będą zbierać [opłat](https://azure.microsoft.com/pricing/details/storage/). Te dane należy usunąć oddzielnie przy użyciu narzędzia do zarządzania magazynem w chmurze, takiego jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="initiate-device-return"></a>Inicjowanie powrotu urządzenia

Aby rozpocząć proces powrotu, wykonaj następujące czynności.

1. Przejdź do zasobu Azure Stack EDGE Pro/Data Box Gateway w Azure Portal. W oknie **Przegląd** przejdź do paska poleceń w okienku po prawej stronie i wybierz pozycję **Zwróć urządzenie**. 

    ![Zwróć urządzenie 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. W bloku **urządzenia zwrotnego** w obszarze **podstawowe informacje**:

    1. Podaj numer seryjny urządzenia. Aby uzyskać numer seryjny urządzenia, przejdź do lokalnego interfejsu użytkownika sieci Web urządzenia, a następnie przejdź do **omówienia**.  
    
       ![Numer seryjny urządzenia 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Wprowadź numer tagu usługi. Numer znacznika usługi jest identyfikatorem składającym się z co najmniej pięciu znaków, który jest unikatowy dla Twojego urządzenia. Tag usługi znajduje się w prawym dolnym rogu urządzenia (w przypadku urządzenia). Pobierz tag informacji (jest to panel etykiet z slajdem). Ten panel zawiera informacje o systemie, takie jak tag usługi, karta sieciowa, adres MAC i tak dalej. 
    
       ![Numer znacznika usługi 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Z listy rozwijanej wybierz przyczynę powrotu.

       ![Zwróć urządzenie 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. W obszarze **Szczegóły wysyłki**:

    1. Podaj imię i nazwisko, nazwę firmy oraz pełny adres firmy. Wprowadź numer telefonu służbowego, w tym numer kierunkowy i identyfikator e-mail powiadomienia.
    2. Jeśli potrzebujesz pola wysyłania zwrotnego, możesz je zażądać. Odpowiedź **tak** na pytanie **wymaga pustego pola do zwrócenia**.

    ![Zwróć urządzenie 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Zapoznaj się z **warunkami zachowania poufności**, a następnie zaznacz pole wyboru w obszarze uwagi, które zostały przejrzane i zgadzasz się na warunki zachowania poufności informacji.

5. Wybierz pozycję **Inicjuj zwrot**.

    ![Zwróć urządzenie 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. Po przechwyceniu szczegółowych informacji na urządzeniu można powiadomić zespół operacji w programie Azure Stack Edge z pomocą poczty e-mail. Możesz użyć aplikacji poczty e-mail przy założeniu, że aplikacja poczty e-mail jest zainstalowana i skonfigurowana. Możesz również skopiować dane, aby utworzyć i wysłać wiadomość e-mail.

    ![Zwróć urządzenie 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Po otrzymaniu wiadomości e-mail przez zespół operacyjny programu Azure Stack Edge Po otrzymaniu tej etykiety można zaplanować odbiór urządzenia za pomocą operatora. 

## <a name="schedule-a-pickup"></a>Planowanie odbioru

Aby zaplanować odbiór, wykonaj następujące czynności.

1. Zamknij urządzenie. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja > ustawienia zarządzania**.
2. Wybierz pozycję **Zamknij**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak** , aby kontynuować. Aby uzyskać więcej informacji, zobacz [zarządzanie mocą](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Odłącz kable zasilające i Usuń wszystkie kable sieciowe z urządzenia.
4. Przygotuj pakiet wysyłki przy użyciu własnego pola lub pustego pola otrzymanego z platformy Azure. Umieść urządzenie i zasilacze, które zostały dostarczone z urządzeniem w polu.
5. Należy umieścić etykietę wysyłkową uzyskaną z platformy Azure w pakiecie.
6. Uzgodnij odbiór urządzenia z regionalną firmą kurierską. W przypadku powrotu urządzenia w Stanach Zjednoczonych przewoźnik może być UPS lub FedEx. Aby zaplanować odbiór przy użyciu zasilacza UPS:

    1. Zadzwoń do lokalnego oddziału firmy UPS (numer bezpłatny dla danego kraju/regionu).
    2. W wywołaniu Zadzwoń na numer śledzenia wycofywania przesyłki, jak pokazano na wydrukowanej etykiecie.
    3. Jeśli numer śledzenia nie jest ujęty w cudzysłów, zasilacz UPS będzie wymagał dodatkowej opłaty podczas odbioru.

    Zamiast planowania pobrania można również wycofać Azure Stack krawędY Pro w najbliższej lokalizacji docelowej.

## <a name="delete-the-resource"></a>Usuń zasób

Gdy urządzenie zostanie odebrane w centrum danych platformy Azure, urządzenie jest sprawdzane pod kątem uszkodzenia lub wszelkich oznak naruszenia.

- Jeśli urządzenie zostanie nienaruszone i jest w dobrym kształcie, licznik rozliczeń zostanie zatrzymany dla tego zasobu. Zespół Operations Manager w Azure Stack Edge skontaktuje się z Tobą, aby upewnić się, że urządzenie zostało zwrócone. Następnie można usunąć zasób skojarzony z urządzeniem w Azure Portal.
- Jeśli urządzenie dotrze do znacznego uszkodzenia, mogą wystąpić grzywny. Aby uzyskać szczegółowe informacje, zobacz [często zadawane pytania dotyczące zgubionych lub uszkodzonych urządzeń](https://azure.microsoft.com/pricing/details/databox/edge/) oraz [warunków użytkowania produktów](https://www.microsoft.com/licensing/product-licensing/products).  


Urządzenie można usunąć w Azure Portal:

- Po umieszczeniu zamówienia i przed przygotowaniem urządzenia przez firmę Microsoft.
- Po powrocie urządzenia do firmy Microsoft i rozpoczęciu działania zespołu operacji Azure Stack Edge w celu potwierdzenia, że urządzenie zostało zwrócone. Zespół operacyjny nie wywołuje do momentu, aż zwrócone urządzenie przejdzie do fizycznej kontroli w centrum danych platformy Azure.

Jeśli urządzenie zostało aktywowane na inną subskrypcję lub lokalizację, firma Microsoft przeniesie zamówienie do nowej subskrypcji lub lokalizacji w ciągu jednego dnia roboczego. Po przeniesieniu zamówienia można usunąć ten zasób.


Wykonaj następujące kroki, aby usunąć urządzenie i zasób w Azure Portal.

1. W Azure Portal przejdź do zasobu, a następnie kliknij pozycję **Przegląd**. Na pasku poleceń wybierz pozycję **Usuń**.

    ![Wybierz pozycję Usuń](media/azure-stack-edge-return-device/delete-resource-1.png)

2. W bloku **Usuń urządzenie** wpisz nazwę urządzenia, które chcesz usunąć, a następnie wybierz pozycję **Usuń**.

    ![Potwierdzenie usunięcia](media/azure-stack-edge-return-device/delete-resource-2.png)

Po pomyślnym usunięciu urządzenia i skojarzonego z nim zasobu zostanie wyświetlone powiadomienie.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak uzyskać zastępowanie Azure Stack Edge na urządzeniu](azure-stack-edge-replace-device.md).