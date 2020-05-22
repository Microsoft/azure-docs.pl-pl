---
title: Zwróć lub Zastąp urządzenie Azure Stack Edge | Microsoft Docs
description: Opisuje sposób zwracania lub zamiany urządzenia Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: bb73494dd5fe22c3be645f732f9d0958e48edb64
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743635"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>Zwróć lub Zastąp urządzenie Azure Stack Edge

W tym artykule opisano sposób czyszczenia danych, a następnie zwrócenia urządzenia Azure Stack Edge. Po zwróceniu urządzenia można także usunąć zasób skojarzony z urządzeniem lub zamówić urządzenie zamienne.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Czyszczenie danych z dysków z danymi na urządzeniu
> * Otwórz bilet pomocy technicznej, aby zwrócić urządzenie
> * Pakowanie urządzenia i planowanie odbioru
> * Usuń zasób w Azure Portal
> * Pobierz urządzenie zastępcze

## <a name="erase-data-from-the-device"></a>Wymazywanie danych z urządzenia

Aby wyczyścić dane z dysków danych urządzenia, należy zresetować urządzenie. Możesz zresetować urządzenie przy użyciu lokalnego interfejsu użytkownika sieci Web lub interfejsu programu PowerShell.

Przed zresetowaniem Utwórz kopię danych lokalnych na urządzeniu, jeśli jest to konieczne. Dane z urządzenia można skopiować do kontenera usługi Azure Storage.

Aby zresetować urządzenie przy użyciu lokalnego interfejsu użytkownika sieci Web, wykonaj następujące czynności.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja > Resetowanie urządzenia**.
2. Wybierz pozycję **Zresetuj urządzenie**.

    ![Resetowanie urządzenia](media/azure-stack-edge-return-device/device-reset-1.png)

3. Po wyświetleniu monitu o potwierdzenie Sprawdź ostrzeżenie i wybierz pozycję **tak** , aby kontynuować.

    ![Potwierdzenie resetowania](media/azure-stack-edge-return-device/device-reset-2.png)  

Zresetowanie powoduje wymazanie danych z dysków danych urządzenia. W zależności od ilości danych na urządzeniu proces ten zajmie około 30-40 minut.

Alternatywnie można nawiązać połączenie z interfejsem programu PowerShell urządzenia i użyć `Reset-HcsAppliance` polecenia cmdlet w celu wymazania danych z dysków danych. Aby uzyskać więcej informacji, zobacz [Resetowanie urządzenia](azure-stack-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Jeśli wymieniasz lub uaktualniasz nowe urządzenie, zalecamy zresetowanie urządzenia tylko po odebraniu nowego urządzenia.
> - Zresetowanie urządzenia spowoduje usunięcie tylko tych danych z urządzenia. Dane znajdujące się w chmurze nie zostaną usunięte i nie będą zbierać [opłat](https://azure.microsoft.com/pricing/details/storage/). Te dane należy usunąć oddzielnie przy użyciu narzędzia do zarządzania magazynem w chmurze, takiego jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Otwórz bilet pomocy technicznej

Aby rozpocząć proces powrotu, wykonaj następujące czynności.

1. Otwórz bilet pomocy technicznej z pomoc techniczna firmy Microsoft wskazujący, że chcesz zwrócić urządzenie. Wybierz typ problemu jako **urządzenie Azure Stack Edge**.

    ![Otwieranie biletu pomocy technicznej](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Inżynier pomoc techniczna firmy Microsoft skontaktuje się z Tobą. Podaj szczegóły dotyczące wysyłki.
3. Jeśli potrzebujesz pola wysyłania zwrotnego, możesz je zażądać. Odpowiedź **tak** na pytanie **wymaga pustego pola do zwrócenia**.


## <a name="schedule-a-pickup"></a>Planowanie odbioru

1. Zamknij urządzenie. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja > ustawienia zarządzania**.
2. Wybierz pozycję **Zamknij**. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **tak** , aby kontynuować. Aby uzyskać więcej informacji, zobacz [zarządzanie mocą](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Odłącz kable zasilające i Usuń wszystkie kable sieciowe z urządzenia.
4. Przygotuj pakiet wysyłki przy użyciu własnego pola lub pustego pola otrzymanego z platformy Azure. Umieść urządzenie i zasilacze, które zostały dostarczone z urządzeniem w polu.
5. Należy umieścić etykietę wysyłkową uzyskaną z platformy Azure w pakiecie.
6. Uzgodnij odbiór urządzenia z regionalną firmą kurierską. W przypadku powrotu urządzenia w Stanach Zjednoczonych przewoźnik może być UPS lub FedEx. Aby zaplanować odbiór przy użyciu zasilacza UPS:

    1. Zadzwoń do lokalnego oddziału firmy UPS (numer bezpłatny dla danego kraju/regionu).
    2. W wywołaniu Zadzwoń na numer śledzenia wycofywania przesyłki, jak pokazano na wydrukowanej etykiecie.
    3. Jeśli numer śledzenia nie jest ujęty w cudzysłów, zasilacz UPS będzie wymagał dodatkowej opłaty podczas odbioru.

    Zamiast planowania odbioru, można również wycofać Azure Stack krawędź w najbliższej lokalizacji docelowej.

## <a name="delete-the-resource"></a>Usuń zasób

Gdy urządzenie zostanie odebrane w centrum danych platformy Azure, urządzenie jest sprawdzane pod kątem uszkodzenia lub wszelkich oznak naruszenia.

- Jeśli urządzenie zostanie nienaruszone i w dobrym kształcie, licznik rozliczeń zostanie zatrzymany dla tego zasobu. Pomoc techniczna firmy Microsoft skontaktuje się z Tobą, aby upewnić się, że urządzenie zostało zwrócone. Następnie można usunąć zasób skojarzony z urządzeniem w Azure Portal.
- Jeśli urządzenie dotrze do znacznego uszkodzenia, mogą wystąpić grzywny. Aby uzyskać szczegółowe informacje, zobacz [często zadawane pytania dotyczące zgubionych lub uszkodzonych urządzeń](https://azure.microsoft.com/pricing/details/databox/edge/) oraz [warunków użytkowania produktów](https://www.microsoft.com/licensing/product-licensing/products).  


Urządzenie można usunąć w Azure Portal:
-    Po złożeniu zamówienia i przed przygotowaniem urządzenia przez firmę Microsoft.
-    Po zwróceniu urządzenia do firmy Microsoft przejdzie ona do fizycznej kontroli w centrum danych platformy Azure, a pomoc techniczna firmy Microsofte wywołania, aby potwierdzić, że urządzenie zostało zwrócone.

Jeśli urządzenie zostało aktywowane na inną subskrypcję lub lokalizację, firma Microsoft przeniesie zamówienie do nowej subskrypcji lub lokalizacji w ciągu jednego dnia roboczego. Po przeniesieniu zamówienia można usunąć ten zasób.


Wykonaj następujące kroki, aby usunąć urządzenie i zasób w Azure Portal.

1. W Azure Portal przejdź do zasobu, a następnie kliknij pozycję **Przegląd**. Na pasku poleceń wybierz pozycję **Usuń**.

    ![Wybierz pozycję Usuń](media/azure-stack-edge-return-device/delete-resource-1.png)

2. W bloku **Usuń urządzenie** wpisz nazwę urządzenia, które chcesz usunąć, a następnie wybierz pozycję **Usuń**.

    ![Potwierdzenie usunięcia](media/azure-stack-edge-return-device/delete-resource-2.png)

Po pomyślnym usunięciu urządzenia i skojarzonego z nim zasobu zostanie wyświetlone powiadomienie.

## <a name="get-a-replacement-device"></a>Pobierz urządzenie zastępcze

Urządzenie zamienne jest wymagane, gdy istniejące urządzenie ma awarię sprzętową lub wymaga uaktualnienia. Jeśli urządzenie ma problem sprzętowy, wykonaj następujące czynności:

1. [Otwórz bilet pomocy technicznej na potrzeby problemu sprzętowego](#open-a-support-ticket). Pomoc techniczna firmy Microsoft ustali, że jednostka zamienna pola (FRU) nie jest dostępna dla tego wystąpienia lub urządzenie wymaga uaktualnienia sprzętowego. W obu przypadkach pomoc techniczna będzie zamawiać urządzenie zamienne.
2. [Utwórz nowy zasób](azure-stack-edge-deploy-prep.md#create-a-new-resource) dla urządzenia zastępczego. Upewnij się, że pole wyboru jest zaznaczone z **urządzeniem Azure Stack Edge**. 
3. Po otrzymaniu urządzenia zastępczego [Zainstaluj](azure-stack-edge-deploy-install.md) i [Aktywuj](azure-stack-edge-deploy-connect-setup-activate.md) urządzenie zamienne dla nowego zasobu.
4. Wykonaj wszystkie kroki, aby zwrócić oryginalne urządzenie:
    1. Otwórz kolejny bilet, aby przywrócić oryginalne urządzenie.
    2. [Wymazywanie danych z urządzenia](#erase-data-from-the-device).
    3. [Zaplanuj pobranie](#schedule-a-pickup).
    5. [Usuń zasób](#delete-the-resource) skojarzony z zwróconym urządzeniem.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać przepustowością](azure-stack-edge-manage-bandwidth-schedules.md).
