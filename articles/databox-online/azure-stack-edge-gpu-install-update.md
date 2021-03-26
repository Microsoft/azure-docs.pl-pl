---
title: Zainstaluj aktualizację na urządzeniu z systemem Azure Stack Edge w wersji Pro Microsoft Docs
description: Zawiera opis sposobu stosowania aktualizacji przy użyciu Azure Portal i lokalnego interfejsu użytkownika sieci Web dla urządzeń z systemem Azure Stack EDGE Pro procesora GPU i klastra Kubernetes na urządzeniu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: 8bb858bc29ac18d110f2679c3681fd4d27b72baa
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607793"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Aktualizowanie procesora GPU w Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano kroki wymagane do zainstalowania aktualizacji na Azure Stack Edge z procesorem GPU przy użyciu lokalnego interfejsu użytkownika sieci Web i za pośrednictwem Azure Portal. Zastosowanie aktualizacji lub poprawek oprogramowania w celu zapewnienia aktualności urządzenia Azure Stack Edge i skojarzonego z nim klastra Kubernetes.

Procedura opisana w tym artykule została wykonana przy użyciu innej wersji oprogramowania, ale proces pozostaje taki sam dla bieżącej wersji oprogramowania.

> [!IMPORTANT]
> - Aktualizacja **2103** to bieżąca aktualizacja i odnosi się do:
>   - Wersja oprogramowania urządzenia — **2.2.1540.2890**
>   - Serwer Kubernetes w wersji **1.17.3**
>   - Wersja IoT Edge: **0.1.0-beta13**
>   - Wersja sterownika procesora GPU: **460.32.03**
>   - Wersja CUDA: **11,2**
>    
>    Aby uzyskać informacje o nowościach w tej aktualizacji, przejdź do informacji o [wersji](azure-stack-edge-gpu-2103-release-notes.md).
> - Aby można było zastosować aktualizację 2103, urządzenie musi mieć uruchomioną 2010. Jeśli nie korzystasz z minimalnej obsługiwanej wersji, zobaczysz następujący błąd: *nie można zainstalować pakietu aktualizacji, ponieważ jego zależności nie są spełnione*.
> - Ta aktualizacja wymaga zastosowania kolejno dwóch aktualizacji. Najpierw należy zastosować aktualizacje oprogramowania urządzenia, a następnie aktualizacje Kubernetes.
> - Należy pamiętać, że zainstalowanie aktualizacji lub poprawki powoduje ponowne uruchomienie urządzenia. Ta aktualizacja zawiera aktualizacje oprogramowania urządzenia i aktualizacje Kubernetes. Mając na względzie, że Azure Stack EDGE Pro jest urządzeniem z jednym węzłem, wszystkie we/wy w toku są zakłócone, a w przypadku tej aktualizacji urządzenie będzie miało czas przestoju do 1,5 godzin.

Aby zainstalować aktualizacje na urządzeniu, należy najpierw skonfigurować lokalizację serwera aktualizacji. Po skonfigurowaniu serwera aktualizacji można zastosować aktualizacje za pośrednictwem interfejsu użytkownika Azure Portal lub lokalnego interfejsu użytkownika sieci Web.

Wszystkie wymienione kroki zostały opisane poniżej.

## <a name="configure-update-server"></a>Konfiguruj serwer aktualizacji

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **Konfiguracja**  >  **serwera aktualizacji**.
   
    ![Konfiguruj aktualizacje 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. Na stronie **Wybierz typ serwera aktualizacji** z listy rozwijanej wybierz opcję z Microsoft Update Server (domyślnie) lub Windows Server Update Services.  
   
    W przypadku aktualizowania z Windows Server Update Services określ identyfikator URI serwera. Serwer o tym identyfikatorze URI będzie wdrażać aktualizacje na wszystkich urządzeniach podłączonych do tego serwera.

    ![Konfiguruj aktualizacje 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    Serwer WSUS służy do zarządzania aktualizacjami i ich dystrybucji za pośrednictwem konsoli zarządzania. Serwer WSUS można też źródło aktualizacji dla innych serwerów WSUS w organizacji. Serwer WSUS, który działa jako źródło aktualizacji, nosi nazwę serwera nadrzędnego. W implementacji programu WSUS co najmniej jeden serwer WSUS w sieci musi być możliwe nawiązanie połączenia z usługą Microsoft Update, aby uzyskać informacje o dostępności aktualizacji. Jako administrator można określić - na podstawie zabezpieczeń sieci i konfiguracja — jak wiele serwerów WSUS połączyć się bezpośrednio z witryny Microsoft Update.
    
    Aby uzyskać więcej informacji, przejdź do [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Zalecamy zainstalowanie aktualizacji za pomocą Azure Portal. Urządzenie automatycznie skanuje w poszukiwaniu aktualizacji raz dziennie. Po udostępnieniu aktualizacji zobaczysz powiadomienie w portalu. Można wtedy pobrać i zainstalować aktualizacje.

> [!NOTE]
> Upewnij się, że urządzenie jest w dobrej kondycji, a stan pokazuje, że **urządzenie działa prawidłowo** . przed kontynuowaniem instalacji aktualizacji.

1. Gdy aktualizacje są dostępne dla urządzenia, zobaczysz powiadomienie. Wybierz powiadomienie lub z górnego paska poleceń, **Zaktualizuj urządzenie**. Umożliwi to zastosowanie aktualizacji oprogramowania dla urządzeń.

    ![Wersja oprogramowania po aktualizacji](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. W bloku **aktualizacje urządzeń** Sprawdź, czy zostały sprawdzone postanowienia licencyjne związane z nowymi funkcjami w informacjach o wersji.

    Możesz **pobrać i zainstalować** aktualizacje lub po prostu **pobrać** aktualizacje. Następnie możesz zainstalować te aktualizacje później.

    ![Wersja oprogramowania po aktualizacji Update 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Jeśli chcesz pobrać i zainstalować aktualizacje, zaznacz opcję automatycznie instaluj aktualizacje po zakończeniu pobierania.

    ![Wersja oprogramowania po aktualizacji Update 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. Rozpocznie się pobieranie aktualizacji. Zobaczysz powiadomienie o tym, że pobieranie jest w toku.

    ![Wersja oprogramowania po aktualizacji Update 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Transparent powiadomienia jest również wyświetlany w Azure Portal. Wskazuje to postęp pobierania.

    ![Wersja oprogramowania po aktualizacji Update 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Możesz wybrać to powiadomienie lub wybrać pozycję **Aktualizuj urządzenie** , aby wyświetlić szczegółowy stan aktualizacji.

    ![Wersja oprogramowania po aktualizacji 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. Po zakończeniu pobierania transparent powiadomień aktualizuje, aby wskazać zakończenie. W przypadku wybrania opcji pobrania i zainstalowania aktualizacji Instalacja rozpocznie się automatycznie.

    W przypadku wybrania opcji Pobierz tylko aktualizacje, wybierz powiadomienie, aby otworzyć blok **aktualizacje urządzeń** . Wybierz pozycję **Zainstaluj**.
  
5. Zobaczysz powiadomienie o tym, że instalacja jest w toku. W portalu jest również wyświetlany alert informacyjny wskazujący, że instalacja jest w toku. Urządzenie przejdzie w tryb offline i jest w trybie konserwacji.
   
    ![Wersja oprogramowania po aktualizacji Update 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. W przypadku urządzenia z 1 węzłem urządzenie zostanie ponownie uruchomione po zainstalowaniu aktualizacji. Alert krytyczny podczas ponownego uruchamiania wskazuje, że puls urządzenia został utracony.

    ![Wersja oprogramowania po aktualizacji Update 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Wybierz Alert, aby wyświetlić odpowiednie zdarzenie urządzenia.
    
    ![Wersja oprogramowania po aktualizacji 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. Po ponownym uruchomieniu oprogramowanie urządzenia zakończy aktualizację. Po zakończeniu aktualizacji można sprawdzić z poziomu lokalnego interfejsu użytkownika sieci Web, że oprogramowanie urządzenia zostało zaktualizowane. Nie zaktualizowano wersji oprogramowania Kubernetes.

    ![Wersja oprogramowania po aktualizacji Update 13](./media/azure-stack-edge-gpu-install-update/portal-update-12.png)

8. Zobaczysz transparent powiadomienia informujący, że aktualizacje urządzeń są dostępne. Wybierz ten transparent, aby rozpocząć aktualizowanie oprogramowania Kubernetes na urządzeniu. 

    ![Wersja oprogramowania po aktualizacji Update 13](./media/azure-stack-edge-gpu-install-update/portal-update-13.png) 


    ![Wersja oprogramowania po aktualizacji 14](./media/azure-stack-edge-gpu-install-update/portal-update-14-a.png) 

    Jeśli wybierzesz pozycję **Aktualizuj urządzenie** na górnym pasku poleceń, zobaczysz postęp aktualizacji.  

    ![Wersja oprogramowania po aktualizacji 15](./media/azure-stack-edge-gpu-install-update/portal-update-14-b.png) 


8. Aktualizacje stanu urządzenia na **urządzeniu działają prawidłowo** po zainstalowaniu aktualizacji. 

    ![Wersja oprogramowania po aktualizacji Update 16](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

    Przejdź do lokalnego interfejsu użytkownika sieci Web, a następnie przejdź do strony **aktualizacji oprogramowania** . Sprawdź, czy aktualizacja Kubernetes została zainstalowana pomyślnie, a wersja oprogramowania odzwierciedla to.

    ![Wersja oprogramowania po aktualizacji Update 17](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


Po pomyślnym zainstalowaniu aktualizacji oprogramowania i Kubernetes urządzenia powiadomienie baneru zniknie. Urządzenie ma teraz najnowszą wersję oprogramowania urządzenia i Kubernetes.


## <a name="use-the-local-web-ui"></a>Korzystanie z lokalnego interfejsu użytkownika sieci Web

W przypadku korzystania z lokalnego interfejsu użytkownika sieci Web należy wykonać dwa kroki:

* Pobierz aktualizację lub poprawkę
* Zainstaluj aktualizację lub poprawkę

Wszystkie wymienione kroki zostały szczegółowo opisane poniżej.


### <a name="download-the-update-or-the-hotfix"></a>Pobierz aktualizację lub poprawkę

Wykonaj następujące kroki, aby pobrać aktualizację. Aktualizację można pobrać z lokalizacji dostarczonej przez firmę Microsoft lub z katalogu Microsoft Update.


Wykonaj następujące kroki, aby pobrać aktualizację z katalogu Microsoft Update.

1. Uruchom przeglądarkę i przejdź do [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Przeszukiwanie wykazu](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. W polu wyszukiwania w wykazie Microsoft Update wprowadź numer bazy wiedzy (KB) poprawki lub warunków dla aktualizacji, które chcesz pobrać. Na przykład wprowadź **Azure Stack EDGE Pro**, a następnie kliknij przycisk **Wyszukaj**.
   
    Lista aktualizacji zostanie wyświetlona jako **Azure Stack krawędź aktualizacji 2103**.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. Kliknij pozycję **Pobierz**. Istnieją dwa pakiety do pobrania: KB 4613486 i KB 46134867, które są zgodne z aktualizacjami oprogramowania urządzenia (*SoftwareUpdatePackage.exe*) i aktualizacjami Kubernetes (*Kubernetes_Package.exe*). Pobierz pakiety do folderu w systemie lokalnym. Możesz również skopiować folder do udziału sieciowego, który jest osiągalny z urządzenia.

### <a name="install-the-update-or-the-hotfix"></a>Zainstaluj aktualizację lub poprawkę

Przed zainstalowaniem aktualizacji lub poprawki upewnij się, że:

 - Aktualizacja lub poprawka jest pobierana lokalnie na hoście lub dostępna za pośrednictwem udziału sieciowego.
 - Urządzenie jest w dobrej kondycji, jak pokazano na stronie **Przegląd** lokalnego interfejsu użytkownika sieci Web.

   ![aktualizowanie urządzenia](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Wykonanie tej procedury zajmuje około 20 minut. Wykonaj następujące kroki, aby zainstalować aktualizację lub poprawkę.

1. W lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Zanotuj wersję oprogramowania, która jest uruchamiana. 
   
   ![Aktualizacja urządzenia 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Podaj ścieżkę do pliku aktualizacji. Możesz również przejść do pliku instalacyjnego aktualizacji, jeśli znajduje się on w udziale sieciowym. Wybierz plik aktualizacji oprogramowania z sufiksem *SoftwareUpdatePackage.exe* .

   ![Aktualizacja urządzenia 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. Wybierz przycisk **Zastosuj**.

   ![Aktualizacja urządzenia 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak** , aby wykonać operację. Jeśli urządzenie jest urządzeniem z jednym węzłem, po zastosowaniu aktualizacji urządzenie zostanie ponownie uruchomione i wystąpiło przestoje. 
   
   ![Aktualizacja urządzenia 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. Rozpocznie się aktualizacja. Po pomyślnym zaktualizowaniu urządzenia zostanie ono ponownie uruchomione. Lokalny interfejs użytkownika nie jest dostępny w tym czasie trwania.
   
6. Po ponownym uruchomieniu nastąpi przekierowanie do strony **logowania** . Aby sprawdzić, czy oprogramowanie urządzenia zostało zaktualizowane, w lokalnym interfejsie użytkownika sieci Web przejdź do pozycji **konserwacja**  >  **Aktualizowanie oprogramowania**. Dla bieżącej wersji wyświetlana wersja oprogramowania powinna być **Azure Stack Edge 2103**. 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. Teraz zaktualizujesz wersję oprogramowania Kubernetes. Powtórz powyższe kroki. Podaj ścieżkę do pliku aktualizacji Kubernetes z sufiksem *Kubernetes_Package.exe* .  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. Wybierz pozycję **Zastosuj aktualizację**.

   ![Aktualizacja urządzenia 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak** , aby wykonać operację.

10. Po pomyślnym zainstalowaniu aktualizacji Kubernetes nie ma zmiany w wyświetlanym oprogramowaniu w ramach   >  **aktualizacji oprogramowania** do konserwacji.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).