---
title: Instalowanie aktualizacji na urządzeniu z serii Azure Data Box Gateway | Microsoft Docs
description: Opisuje sposób stosowania aktualizacji przy użyciu Azure Portal i lokalnego interfejsu użytkownika sieci Web dla urządzeń z serii Azure Data Box Gateway
services: databox
author: twooley
ms.service: databox
ms.topic: article
ms.date: 06/30/2020
ms.author: twooley
ms.openlocfilehash: 3972aecf70a664980e8279152eb001915ae1bb53
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783879"
---
# <a name="update-your-azure-data-box-gateway"></a>Aktualizowanie Azure Data Box Gateway

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji na Azure Data Box Gateway za pośrednictwem lokalnego interfejsu użytkownika sieci Web i przez Azure Portal. Aby zapewnić aktualność urządzenia Data Box Gateway, należy zastosować aktualizacje lub poprawki oprogramowania.

> [!IMPORTANT]
>
> - Aktualizacja **1911** jest zgodna z wersją oprogramowania **1.6.1049.786** na urządzeniu. Aby uzyskać informacje na temat tej aktualizacji, przejdź do informacji o [wersji](data-box-gateway-1911-release-notes.md).
>
> - Należy pamiętać, że zainstalowanie aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. Ponieważ Data Box Gateway jest urządzeniem z jednym węzłem, wszystkie trwające operacji we/wy są zakłócone, a urządzenie będzie miało przestoje do 30 minut w przypadku aktualizacji oprogramowania.

Wszystkie wymienione kroki zostały opisane poniżej.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Zalecamy zainstalowanie aktualizacji za pomocą Azure Portal. Urządzenie automatycznie skanuje w poszukiwaniu aktualizacji raz dziennie. Po udostępnieniu aktualizacji zobaczysz powiadomienie w portalu. Następnie można pobrać i zainstalować aktualizacje.

> [!NOTE]
> Upewnij się, że urządzenie jest w dobrej kondycji, a stan jest wyświetlany jako **online** przed kontynuowaniem instalacji aktualizacji.

1. Gdy aktualizacje są dostępne dla urządzenia, zobaczysz powiadomienie. Wybierz powiadomienie lub z górnego paska poleceń, **Zaktualizuj urządzenie**. Umożliwi to zastosowanie aktualizacji oprogramowania dla urządzeń.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. W bloku **aktualizacje urządzeń** Sprawdź, czy zostały sprawdzone postanowienia licencyjne związane z nowymi funkcjami w informacjach o wersji.

    Możesz **pobrać i zainstalować** aktualizacje lub po prostu **pobrać** aktualizacje. Następnie można później zainstalować te aktualizacje.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    Jeśli chcesz pobrać i zainstalować aktualizacje, zaznacz opcję automatycznie instaluj aktualizacje po zakończeniu pobierania.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. Rozpocznie się pobieranie aktualizacji. Zobaczysz powiadomienie o tym, że pobieranie jest w toku.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    Transparent powiadomienia jest również wyświetlany w Azure Portal. Wskazuje to postęp pobierania.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    Możesz wybrać to powiadomienie lub wybrać pozycję **Aktualizuj urządzenie** , aby wyświetlić szczegółowy stan aktualizacji.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. Po zakończeniu pobierania transparent powiadomień aktualizuje, aby wskazać zakończenie. W przypadku wybrania opcji pobrania i zainstalowania aktualizacji Instalacja rozpocznie się automatycznie.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    W przypadku wybrania opcji Pobierz tylko aktualizacje, wybierz powiadomienie, aby otworzyć blok **aktualizacje urządzeń** . Wybierz pozycję **Zainstaluj**.
  
    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. Zobaczysz powiadomienie o tym, że instalacja jest w toku.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    W portalu jest również wyświetlany alert informacyjny wskazujący, że instalacja jest w toku. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. W przypadku urządzenia z 1 węzłem urządzenie zostanie ponownie uruchomione po zainstalowaniu aktualizacji. Alert krytyczny podczas ponownego uruchamiania wskazuje, że puls urządzenia zostanie utracony.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    Wybierz Alert, aby wyświetlić odpowiednie zdarzenie urządzenia.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. Stan urządzenia jest aktualizowany do **trybu online** po zainstalowaniu aktualizacji.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    Na górnym pasku poleceń wybierz pozycję **aktualizacje urządzeń**. Sprawdź, czy aktualizacja została zainstalowana pomyślnie, a wersja oprogramowania urządzenia odzwierciedla to.

    ![Wersja oprogramowania po aktualizacji](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web

W przypadku korzystania z lokalnego interfejsu użytkownika sieci Web należy wykonać dwa kroki:

- Pobierz aktualizację lub poprawkę
- Zainstaluj aktualizację lub poprawkę

Wszystkie wymienione kroki zostały szczegółowo opisane poniżej.

### <a name="download-the-update-or-the-hotfix"></a>Pobierz aktualizację lub poprawkę

Wykonaj następujące kroki, aby pobrać aktualizację. Aktualizację można pobrać z lokalizacji dostarczonej przez firmę Microsoft lub z katalogu Microsoft Update.

Wykonaj następujące kroki, aby pobrać aktualizację z katalogu Microsoft Update.

1. Uruchom przeglądarkę i przejdź do [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

   ![Przeszukiwanie wykazu](./media/data-box-gateway-apply-updates/download-update-1.png)

2. W polu wyszukiwania w wykazie Microsoft Update wprowadź numer bazy wiedzy (KB) poprawki lub warunków dla aktualizacji, które chcesz pobrać. Na przykład wprowadź **Azure Data Box Gateway**, a następnie kliknij przycisk **Wyszukaj**.

   Lista aktualizacji zostanie wyświetlona jako **Azure Data Box Gateway 1911**.

   ![Przeszukiwanie wykazu](./media/data-box-gateway-apply-updates/download-update-2.png)

3. Kliknij pozycję **Pobierz**. Istnieje pojedynczy plik do pobrania o nazwie *SoftwareUpdatePackage.exe* , który odnosi się do aktualizacji oprogramowania urządzenia. Pobierz plik do folderu w systemie lokalnym. Możesz również skopiować folder do udziału sieciowego, który jest osiągalny z urządzenia.

   ![Przeszukiwanie wykazu](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>Zainstaluj aktualizację lub poprawkę

Przed zainstalowaniem aktualizacji lub poprawki upewnij się, że:

- Aktualizacja lub poprawka jest pobierana lokalnie na hoście lub dostępna za pośrednictwem udziału sieciowego.
- Urządzenie jest w dobrej kondycji, jak pokazano na stronie **Przegląd** lokalnego interfejsu użytkownika sieci Web.

   ![aktualizowanie urządzenia](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

Wykonanie tej procedury zajmuje około 20 minut. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Zanotuj wersję oprogramowania, która jest uruchamiana.

   ![aktualizowanie urządzenia](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. Podaj ścieżkę do pliku aktualizacji. Możesz również przejść do pliku instalacyjnego aktualizacji, jeśli znajduje się on w udziale sieciowym. Wybierz plik aktualizacji oprogramowania z sufiksem *SoftwareUpdatePackage.exe* .

   ![aktualizowanie urządzenia](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. Wybierz pozycję **Zastosuj**.

   ![aktualizowanie urządzenia](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak** , aby wykonać operację. Jeśli urządzenie jest urządzeniem z jednym węzłem, po zastosowaniu aktualizacji urządzenie zostanie ponownie uruchomione i wystąpiło przestoje.
   ![aktualizowanie urządzenia](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. Rozpocznie się aktualizacja. Po pomyślnym zaktualizowaniu urządzenia zostanie ono ponownie uruchomione. Lokalny interfejs użytkownika nie jest dostępny w tym czasie trwania.

6. Po ponownym uruchomieniu nastąpi przekierowanie do strony **logowania** . Aby sprawdzić, czy oprogramowanie urządzenia zostało zaktualizowane, w lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Wyświetlana wersja oprogramowania w tym przykładzie to **1.6.1049.786**.

   ![aktualizowanie urządzenia](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu Azure Data Box Gateway](data-box-gateway-manage-users.md).
