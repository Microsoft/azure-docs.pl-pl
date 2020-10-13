---
title: Podręcznik administrowania usługą Azure Data Box Disk za pomocą portalu | Microsoft Docs
description: Dowiedz się, jak zarządzać Data Box Disk przy użyciu Azure Portal. Zarządzaj zamówieniami, Zarządzaj dyskami i śledź stan zamówienia w miarę postępu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: a4ac56a4d588071f599f62e6de94d9ec7367a1ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87924757"
---
# <a name="use-azure-portal-to-administer-your-data-box-disk"></a>Administrowanie usługą Data Box Disk w witrynie Azure Portal

Samouczki w tym artykule dotyczą usługi Microsoft Azure Data Box Disk w wersji zapoznawczej. W artykule opisano niektóre złożone przepływy pracy i zadania związane z zarządzaniem, które można wykonywać w usłudze Data Box Disk. 

Można zarządzać usługą Data Box Disk w witrynie Azure Portal. Ten artykuł dotyczy zadań, które można wykonywać w witrynie Azure Portal. W witrynie Azure Portal można zarządzać zamówieniami, zarządzać dyskami oraz śledzić kolejne stany zamówienia aż do etapu końcowego.

## <a name="cancel-an-order"></a>Anulowanie zamówienia

Może się zdarzyć, że z różnych powodów będzie konieczne anulowanie złożonego zamówienia. Można anulować zamówienie tylko przed rozpoczęciem przygotowywania dysków. Po przygotowaniu dysków i przetworzeniu zamówienia nie można już anulować zamówienia. 

Aby anulować zamówienie, wykonaj poniższe czynności.

1.  Przejdź do pozycji **Przegląd > Anuluj**. 

    ![Anulowanie zamówienia — 1](media/data-box-portal-ui-admin/cancel-order1.png)

2.  Wprowadź przyczynę anulowania zamówienia.  

    ![Anulowanie zamówienia — 2](media/data-box-portal-ui-admin/cancel-order2.png)

3.  Po anulowaniu zamówienia jego stan w portalu zostanie zmieniony na **Anulowane**.

    ![Anulowanie zamówienia — 3](media/data-box-portal-ui-admin/cancel-order3.png)

Po anulowaniu zamówienia nie otrzymasz powiadomienia e-mail.

## <a name="clone-an-order"></a>Klonowanie zamówienia

Klonowanie jest przydatne w pewnych sytuacjach. Załóżmy na przykład, że użytkownik skorzystał z usługi Data Box Disk do transferu danych. W związku z wygenerowaniem większej ilości danych pojawiła się potrzeba użycia kolejnych dysków do przesłania danych na platformę Azure. W takim przypadku można po prostu sklonować to samo zamówienie.

Aby sklonować zamówienie, wykonaj następujące czynności.

1.  Przejdź do pozycji **Przegląd > Klonuj**. 

    ![Klonowanie zamówienia — 1](media/data-box-portal-ui-admin/clone-order1.png)

2.  Wszystkie szczegóły zamówienia pozostaną takie same. Nazwa zamówienia będzie taka jak nazwa oryginalnego zamówienia z dodaną końcówką *— klon*. Zaznacz pole wyboru, aby potwierdzić zapoznanie się z informacjami dotyczącymi prywatności. Kliknij przycisk **Utwórz**.    

W ciągu kilku minut sklonowanie zamówienie zostanie utworzone i wyświetlone w portalu.

[![Klonuj kolejność 3](media/data-box-portal-ui-admin/clone-order3.png)](media/data-box-portal-ui-admin/clone-order3.png#lightbox) 

## <a name="delete-order"></a>Usuwanie zamówienia

Możesz usunąć zamówienie, gdy zostanie zakończone. Zamówienie zawiera dane osobowe, takie jak imię i nazwisko, adres i dane kontaktowe. Te dane osobowe są usuwane po usunięciu zamówienia.

Można usuwać tylko zamówienia, które zostały zakończone lub anulowane. Aby usunąć zamówienie, wykonaj następujące czynności.

1. Przejdź do sekcji **Wszystkie zasoby**. Wyszukaj zamówienie.

    ![Wyszukiwanie zamówień w usłudze Data Box Disk](media/data-box-portal-ui-admin/search-data-box-disk-orders.png)

2. Kliknij zamówienie, które chcesz usunąć, i przejdź do sekcji **Przegląd**. Na pasku poleceń kliknij pozycję **Usuń**.

    ![Usuwanie zamówienia w usłudze Data Box Disk — 1](media/data-box-portal-ui-admin/delete-order1.png)

3. Gdy pojawi się monit o potwierdzenie usunięcia zamówienia, podaj nazwę zamówienia. Kliknij polecenie **Usuń**.

     ![Usuwanie zamówienia w usłudze Data Box Disk — 2](media/data-box-portal-ui-admin/delete-order2.png)


## <a name="download-shipping-label"></a>Pobieranie etykiety wysyłkowej

W przypadku zgubienia lub utraty zwrotnej etykiety wysyłkowej dostarczonej wraz z dyskami konieczne będzie pobranie etykiety wysyłkowej. 

Aby pobrać etykietę wysyłkową, wykonaj następujące czynności.
1.  Przejdź do pozycji **Przegląd > Pobierz etykietę wysyłkową**. Ta opcja jest dostępna tylko po wysłaniu dysku. 

    ![Pobieranie etykiety wysyłkowej](media/data-box-portal-ui-admin/download-shipping-label.png)

2.  To spowoduje pobranie następującej zwrotnej etykiety wysyłkowej. Zapisz etykietę, wydrukuj ją i umieść na przesyłce zwrotnej.

    ![Przykładowa etykieta wysyłkowa](media/data-box-portal-ui-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Edytowanie adresu wysyłkowego

Może się zdarzyć, że po złożeniu zamówienia konieczne będzie edytowanie adresu wysyłkowego. Jest to możliwe tylko przed wysłaniem dysku. Po wysłaniu dysku ta opcja nie będzie już dostępna.

Aby edytować zamówienie, wykonaj następujące czynności.

1. Przejdź do pozycji **Szczegóły zamówienia > Edytuj adres wysyłkowy**.

    ![Edytowanie adresu wysyłkowego — 1](media/data-box-portal-ui-admin/edit-shipping-address1.png)

2. Możesz teraz edytować adres wysyłkowy. Następnie zapisz zmiany.

    ![Edytowanie adresu wysyłkowego — 2](media/data-box-portal-ui-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Edytowanie szczegółów powiadomienia

Może być konieczna zmiana danych użytkowników, którzy mają otrzymywać wiadomości e-mail z powiadomieniami o stanie zamówienia. Na przykład określony użytkownik może potrzebować informacji o dostarczeniu lub odebraniu dysku. Może być konieczne uzyskanie informacji o innym użytkowniku, gdy kopia danych zostanie ukończona, aby można było sprawdzić, czy dane znajdują się na koncie usługi Azure Storage, przed usunięciem go ze źródła. W takich przypadkach można edytować szczegóły powiadomień.

Aby edytować szczegóły powiadomień, wykonaj następujące czynności.

1. Przejdź do pozycji **Szczegóły zamówienia > Edytowanie szczegółów powiadomienia**.

    ![Edytowanie szczegółów powiadomienia — 1](media/data-box-portal-ui-admin/edit-notification-details1.png)

2. Możesz teraz edytować szczegóły powiadomień. Następnie zapisz zmiany.
 
    ![Edytowanie szczegółów powiadomienia — 2](media/data-box-portal-ui-admin/edit-notification-details2.png)

## <a name="view-order-status"></a>Wyświetlanie stanu zamówienia

|Stan zamówienia |Opis |
|---------|---------|
|Zamówione     | Pomyślnie złożono zamówienie. <br> Jeśli dyski są niedostępne, otrzymasz powiadomienie. <br>Jeśli dyski są dostępne, firma Microsoft identyfikuje dyski do wysłania i przygotowuje odpowiedni pakiet dysków.        |
|Przetworzone     | Zakończono przetwarzanie zamówienia. <br> Przetwarzanie zamówienia obejmuje następujące czynności:<li>Dyski są szyfrowane przy użyciu szyfrowania AES-128 funkcją BitLocker. </li> <li>Urządzenia Data Box Disk są blokowane w celu zabezpieczenia przed nieautoryzowanym dostępem.</li><li>W trakcie tego procesu jest generowany klucz dostępu, który odblokowuje dyski.</li>        |
|Wysłane     | Zamówienie zostało wysłane. Powinno zostać dostarczone do Ciebie w ciągu 1–2 dni.        |
|Dostarczono     | Zamówienie zostało dostarczone na adres wskazany w zamówieniu.        |
|Pobrane     |Przesyłka zwrotna została pobrana. <br> Po odebraniu przesyłki w centrum danych Azure dane zostaną automatycznie przekazane na platformę Azure.         |
|Odebrano     | Twoje dyski zostały odebrane w centrum danych Azure. Wkrótce rozpocznie się kopiowanie danych.        |
|Skopiowane dane     |Kopiowanie danych jest w toku.<br> Poczekaj na zakończenie kopiowania danych.         |
|Zakończone       |Zamówienie zostało pomyślnie zrealizowane.<br> Przed usunięciem danych lokalnych z serwerów upewnij się, że Twoje dane znajdują się na platformie Azure.         |
|Zakończone z błędami| Kopiowanie danych zostało ukończone, ale wystąpiły błędy. <br> Zapoznaj się z dziennikami błędów na potrzeby przekazywania, używając ścieżki podanej w **przeglądzie**. Aby uzyskać więcej informacji, przejdź do pozycji [Pobierz dzienniki błędów przekazywania](data-box-disk-troubleshoot-upload.md#download-logs).   |
|Anulowane            |Twoje zamówienie zostało anulowane. <br> Zamówienie mogło zostać anulowane przez Ciebie lub, w przypadku wystąpienia błędu, przez usługę.     |



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z usługą Data Box Disk](data-box-disk-troubleshoot.md).
