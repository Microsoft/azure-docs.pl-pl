---
title: 'Szybki Start: Tworzenie profilu warstwy Standardowa/Premium platformy Azure — Azure Portal'
description: W tym przewodniku szybki start przedstawiono, jak korzystać z usługi Azure Front-Standard/Premium dla aplikacji sieci Web o wysokiej dostępności i wysokiej wydajności przy użyciu Azure Portal.
services: frontdoor
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 175fb82a5fdf300915f89c3d8cdc238638a742e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565133"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Szybki Start: Tworzenie profilu warstwy Standardowa/Premium platformy Azure — Azure Portal

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? Wyświetl [dokumenty z przodu platformy Azure](../front-door-overview.md).

W tym przewodniku szybki start dowiesz się, jak utworzyć profil warstwy Standardowa/Premium platformy Azure przy użyciu Azure Portal. Możesz utworzyć profil warstwy Standardowa/Premium platformy Azure za pomocą opcji *szybkie tworzenie* z konfiguracjami podstawowymi lub przy użyciu opcji *Tworzenie niestandardowe* z bardziej zaawansowanymi konfiguracjami. W przypadku *tworzenia niestandardowego* można wdrożyć dwa Web Apps. Następnie utworzysz profil warstwy Standardowa/Premium platformy Azure przy użyciu dwóch Web Apps jako pochodzenia. Następnie sprawdź łączność z Twoim Web Apps przy użyciu nazwy hosta frontonu platformy Azure w warstwie Standardowa i w warstwie Premium.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-front-door-profile---quick-create"></a>Utwórz profil frontonu — szybkie tworzenie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Na stronie głównej lub w menu platformy Azure wybierz pozycję **+ Utwórz zasób**. Wyszukaj *przód/Premium w warstwie Standardowa (wersja zapoznawcza)*. Następnie wybierz pozycję **Utwórz**.

1. Na stronie **Porównaj oferty** wybierz pozycję **szybkie tworzenie**. Następnie wybierz pozycję **Kontynuuj, aby utworzyć drzwi tylne**.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Zrzut ekranu przedstawiający oferty porównania.":::

1. Na stronie **Tworzenie profilu frontu drzwi** wprowadź lub wybierz poniższe ustawienia.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Zrzut ekranu przedstawiający stronę szybkiego tworzenia drzwi do przodu.":::    

    | Ustawienia | Wartość |
    | --- | --- |
    | **Subskrypcja**  | Wybierz subskrypcję. |
    | **Grupa zasobów**  | Wybierz pozycję **Utwórz nową** i wprowadź *contoso-appService* w polu tekstowym.|
    | **Nazwa** | Nadaj nazwę profilowi. W tym przykładzie zastosowano **contoso-AFD-quickcreate**. |
    | **Warstwa** | Wybierz jednostkę SKU w warstwie Standardowa lub Premium. Standardowa jednostka SKU to optymalizacja zawartości zoptymalizowana. Jednostki SKU w warstwie Premium są oparte na standardowej jednostce SKU i koncentrują się na zabezpieczeniach. Zobacz [porównanie warstw](tier-comparison.md).  |
    | **Nazwa punktu końcowego** | Wprowadź globalnie unikatową nazwę dla punktu końcowego. |
    | **Typ źródła** | Wybierz typ zasobu dla źródła. W tym przykładzie wybieramy usługę App Service jako źródło z włączonym prywatnym linkiem. |
    | **Nazwa hosta źródła** | Wprowadź nazwę hosta dla źródła. |
    | **Włącz link prywatny** | Jeśli chcesz mieć połączenie prywatne między drzwiami i pochodzeniem z platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z tematem [wskazówki dotyczące łącza prywatnego](concept-private-link.md) i [Włącz prywatne łącze](./how-to-enable-private-link-web-app.md).
    | **Buforowanie** | Zaznacz to pole wyboru, jeśli chcesz, aby zawartość pamięci podręcznej była bliżej użytkowników globalnie przy użyciu punktów obecności i sieci firmy Microsoft na platformie Azure. |
    | **Zasady WAF** | Wybierz pozycję **Utwórz nowe** lub Wybierz istniejące zasady WAF z listy rozwijanej, jeśli chcesz włączyć tę funkcję. |

    > [!NOTE]
    > Podczas tworzenia profilu usługi Azure Front-Standard/Premium należy wybrać źródło z tej samej subskrypcji, w którym są tworzone drzwi.

1. Wybierz kolejno pozycje **Recenzja + Utwórz** , aby utworzyć profil i uruchomić przód.

   > [!NOTE]
   > Propagacja konfiguracji do wszystkich punktów POP krawędzi może potrwać kilka minut.

1. Następnie kliknij przycisk **Utwórz** , aby rozpocząć wdrażanie i uruchamianie profilu front-drzwi.

1. W przypadku włączenia linku prywatnego przejdź do swojego źródła (w tym przykładzie usługi App Service). Wybierz pozycję **Sieć**  >  **Skonfiguruj prywatny link**. Następnie wybierz oczekujące żądanie z platformy Azure, a następnie kliknij przycisk Zatwierdź. Po kilku sekundach aplikacja będzie dostępna za pomocą zewnętrznych drzwi platformy Azure w bezpieczny sposób.

## <a name="create-front-door-profile---custom-create"></a>Utwórz profil frontonu — tworzenie niestandardowe

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Tworzenie aplikacji sieci Web z dwoma wystąpieniami jako źródło

Jeśli masz już skonfigurowaną grupę źródłową lub początkową, Pomiń, aby utworzyć wersję Standard/Premium (wersja zapoznawcza) dla aplikacji.

W tym przykładzie tworzymy aplikację sieci Web z dwoma wystąpieniami, które działają w różnych regionach świadczenia usługi Azure. Oba wystąpienia aplikacji sieci Web działają w trybie *aktywny/aktywny* , więc jeden może przyjmować ruch. Ta konfiguracja różni się od konfiguracji *aktywnej/gotowości* , która działa jako tryb failover.

Jeśli nie masz jeszcze aplikacji sieci Web, wykonaj następujące kroki, aby skonfigurować przykładową aplikację internetową.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób**  >  **webapp**.

1. Na karcie **podstawy** na stronie **Tworzenie aplikacji sieci Web** wprowadź lub wybierz poniższe informacje.

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | **Subskrypcja**               | Wybierz subskrypcję. |
    | **Grupa zasobów**       | Wybierz pozycję **Utwórz nową** i wprowadź *FrontDoorQS_rg1* w polu tekstowym.|
    | **Nazwa**                   | Wprowadź unikatową **nazwę** aplikacji sieci Web. W tym przykładzie zastosowano *WebAppContoso-001*. |
    | **Publikowanie** | Wybierz pozycję **Kod**. |
    | **Stos środowiska uruchomieniowego**         | Wybierz pozycję **.NET Core 2,1 (LTS)**. |
    | **System operacyjny**          | Wybierz pozycję **Windows**. |
    | **Region**           | Wybierz pozycję **środkowe stany USA**. |
    | **Plan systemu Windows** | Wybierz pozycję **Utwórz nowy** i wprowadź *myAppServicePlanCentralUS* w polu tekstowym. |
    | **Jednostka SKU i rozmiar** | Wybierz pozycję **standardowa S1 100 ACU, 1,75 GB pamięci**. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Szybkie tworzenie jednostki SKU w warstwie Premium w Azure Portal":::

1. Wybierz pozycję **Przegląd + Utwórz**, Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz**. Wdrożenie może potrwać kilka minut

Po zakończeniu wdrażania Utwórz drugą aplikację sieci Web. Użyj tych samych ustawień, co powyżej, z wyjątkiem następujących ustawień:

| Ustawienie          | Wartość     |
| ---              | ---  |
| **Grupa zasobów**   | Wybierz pozycję **Utwórz nową** i wprowadź *FrontDoorQS_rg2*. |
| **Nazwa**             | Wprowadź unikatową nazwę aplikacji sieci Web, w tym przykładzie *WebAppContoso-002*.  |
| **Region**           | Inny region, w tym przykładzie południowo- *środkowe stany USA* |
| **Plan App Service**  >  **Plan systemu Windows**         | Wybierz pozycję **Nowy** i wprowadź *myAppServicePlanSouthCentralUS*, a następnie wybierz przycisk **OK**. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Tworzenie warstwy przedniej/Premium (wersja zapoznawcza) dla aplikacji

Skonfiguruj platformę Azure Front-based Standard/Premium (wersja zapoznawcza), aby skierować ruch użytkowników na podstawie najmniejszego opóźnienia między dwoma serwerami usługi Web Apps. Zabezpiecz również swoje tylne drzwi za pomocą zapory aplikacji sieci Web. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
 
1. Na stronie głównej lub w menu platformy Azure wybierz pozycję **+ Utwórz zasób**. Wyszukaj *przód/Premium w warstwie Standardowa (wersja zapoznawcza)*. Następnie wybierz pozycję **Utwórz**.

1. Na stronie **Porównywanie ofert** wybierz pozycję **Tworzenie niestandardowe**. Następnie wybierz pozycję **Kontynuuj, aby utworzyć drzwi tylne**.

1. Na karcie **podstawowe**   wprowadź lub wybierz poniższe informacje, a następnie wybierz pozycję **Dalej: wpis tajny**. 

    | Ustawienie | Wartość |
    | --- | --- |
    | **Subskrypcja** | Wybierz subskrypcję. |
    | **Grupa zasobów** | Wybierz pozycję **Utwórz nową** i wprowadź *FrontDoorQS_rg0* w polu tekstowym. |
    | **Lokalizacja grupy zasobów** | Wybierz **Wschodnie stany USA** |
    | **Profile Name (Nazwa profilu)** | Wprowadź unikatową nazwę w tej subskrypcji **webapp-contoso-AFD** |
    | **Warstwa** | Wybierz pozycję **Premium**. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Utwórz profil frontonu":::

1. *Opcjonalne*: wpisy **tajne**. Jeśli planujesz korzystanie z certyfikatów zarządzanych, ten krok jest opcjonalny. Jeśli masz istniejące Key Vault na platformie Azure, które planujesz użyć do przeniesienia własnego certyfikatu dla domeny niestandardowej, a następnie wybierz pozycję **Dodaj certyfikat**. Możesz również dodać certyfikat w środowisku zarządzania po jego utworzeniu.

    > [!NOTE]
    > Musisz mieć odpowiednie uprawnienia, aby dodać certyfikat z Azure Key Vault jako użytkownik. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Zrzut ekranu przedstawiający Dodawanie wpisu tajnego w programie Custom Create.":::

1. Na karcie **punkt końcowy** wybierz pozycję **Dodaj punkt końcowy** i nadaj punktowi końcowemu unikatową nazwę globalnie. Po zakończeniu tworzenia środowiska można utworzyć wiele punktów końcowych w profilu Standard/Premium platformy Azure. W tym przykładzie używamy *contoso-frontonu*. Pozostaw wartość ustawienia limit czasu odpowiedzi pochodzenia (w sekundach) i stan jako domyślny. Wybierz pozycję **Dodaj** , aby dodać punkt końcowy.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Zrzut ekranu przedstawiający Dodawanie punktu końcowego.":::

1. Następnie Dodaj grupę pierwotną zawierającą dwie aplikacje sieci Web. Wybierz pozycję **+ Dodaj**   , aby otworzyć stronę **Dodawanie grupy źródłowej** . W polu Nazwa wprowadź *myOrignGroup*, a następnie wybierz pozycję **+ Dodaj źródło**.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Zrzut ekranu przedstawiający Dodawanie grupy pochodzenia.":::

1. Na stronie **Dodawanie źródła**   wprowadź lub wybierz poniższe informacje. Następnie wybierz pozycję **Dodaj**.

    | Ustawienie | Wartość |
    | --- | --- |
    | **Nazwa** | Wprowadź **webapp1** |
    | **Typ źródła** | Wybieranie **usługi App Services** |
    | **Nazwa hosta** | Wybierz pozycję `WebAppContoso-001.azurewebsites.net` |
    | **Nagłówek hosta źródła** | Wybierz pozycję `WebAppContoso-001.azurewebsites.net` |
    | **Inne pola** | Pozostaw wszystkie pozostałe pola jako domyślne. |

    > [!NOTE]
    > Podczas tworzenia profilu usługi Azure Front-Standard/Premium musisz wybrać źródło z tej samej subskrypcji, w której jest tworzony pakiet Azure Front-Standard/Premium.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Zrzut ekranu przedstawiający Dodawanie większej liczby źródeł.":::

1. Powtórz krok 8, aby dodać drugie źródło webapp002. Wybierz `webappcontoso-002.azurewebsite.net` jako **nazwę hosta źródła** i **nagłówek hosta źródła**.

1. Na stronie **Dodawanie grupy pochodzenia** zobaczysz dwa dodane źródła, pozostawiając wszystkie pozostałe pola jako domyślne.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Zrzut ekranu przedstawiający stronę Dodawanie grupy pochodzenia.":::

1. Następnie Dodaj trasę, aby zmapować punkt końcowy frontonu do grupy pierwotnej. Ta trasa przekazuje żądania z punktu końcowego do źródła. Wybierz pozycję **+ Dodaj** do trasy, aby skonfigurować trasę.  

1. Na stronie **Dodawanie trasy** wprowadź lub wybierz poniższe informacje. Następnie wybierz pozycję **Dodaj**.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Dodawanie trasy bez buforowania":::

    | Ustawienie | Wartość |
    | --- | --- |
    | **Nazwa** | Wprowadź **ścieżkę** |    
    | **Domeny** | Wybierz pozycję `contoso-frontend.z01.azurefd.net` | 
    | **Nazwa hosta** | Wybierz pozycję `WebAppContoso-001.azurewebsites.net` |
    | **Wzorce do dopasowania** | Pozostaw wartość domyślną. |
    | **Akceptowane protokoły** | Pozostaw wartość domyślną. | 
    | **Przekierowanie** | Pozostaw wartość domyślną, aby **przekierować cały ruch do korzystania z protokołu HTTPS**. | 
    | **Grupa pierwotna** | Wybierz pozycję Moja **Origin**. | 
    | **Ścieżka do źródła** | Pozostaw wartość domyślną. | 
    | **Protokół przekazywania** | Wybierz pozycję **Dopasuj żądanie przychodzące**. | 
    | **Buforowanie** | W tym przewodniku Szybki Start nie zaznaczaj pola wyboru. Jeśli chcesz, aby zawartość była buforowana na krawędziach, zaznacz pole wyboru **Włącz buforowanie**. |
    | **Reguły** | Pozostaw wartość domyślną. Po utworzeniu profilu dla drzwi przednich można utworzyć reguły niestandardowe i zastosować je do tras. |  
       
    >[!WARNING]
    > **Upewnij** się, że istnieje trasa dla każdego punktu końcowego. Brak trasy może spowodować niepowodzenie punktu końcowego.

1. Następnie wybierz pozycję **+ Dodaj** do zabezpieczeń, aby dodać zasady WAF. Wybierz pozycję **Dodaj** nowe i nadaj zasadom unikatową nazwę. Zaznacz pole wyboru **Dodaj ochronę bot**. Wybierz punkt końcowy w obszarze **domeny**, a następnie wybierz pozycję **Dodaj**.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="Dodawanie zasad WAF":::

1. Wybierz kolejno pozycje **Przegląd + Utwórz** i **Utwórz**. Rozpropagowanie konfiguracji do wszystkich punktów POP brzegowych trwa kilka minut. Teraz masz pierwszy profil i punkt końcowy z przodu.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Przegląd tworzenia niestandardowego":::

## <a name="verify-azure-front-door"></a>Weryfikuj drzwiczki frontonu platformy Azure

Po utworzeniu profilu warstwy Standardowa/Premium platformy Azure, aby konfiguracja została wdrożona globalnie, potrwa kilka minut. Po zakończeniu możesz uzyskać dostęp do utworzonego hosta frontonu. W przeglądarce przejdź do `contoso-frontend.z01.azurefd.net` . Twoje żądanie zostanie automatycznie przekazane do najbliższego serwera z określonych serwerów w grupie pierwotnej.

Jeśli te aplikacje zostały utworzone w tym przewodniku Szybki Start, zobaczysz stronę z informacjami.

Aby przetestować błyskawiczną globalną pracę w trybie failover, użyjemy następujących kroków:

1. Otwórz przeglądarkę, zgodnie z powyższym opisem, i przejdź do adresu frontonu: `contoso-frontend.azurefd.net` .

1. W Azure Portal Wyszukaj i wybierz pozycję *App Services*. Przewiń w dół, aby znaleźć jedną z aplikacji sieci Web, **WebAppContoso-001** w tym przykładzie.

1. Wybierz aplikację internetową, a następnie wybierz pozycję **Zatrzymaj** i **przycisk tak** , aby sprawdzić.

1. Odśwież przeglądarkę. Powinna zostać wyświetlona ta sama strona informacji.

   >[!TIP]
   >Istnieje nieco kilka opóźnień dla tych działań. Może być konieczne ponowne odświeżenie.

1. Znajdź inną aplikację sieci Web i zatrzymaj ją.

1. Odśwież przeglądarkę. Tym razem powinien zostać wyświetlony komunikat o błędzie.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Oba wystąpienia aplikacji sieci Web zostały zatrzymane":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu możesz usunąć wszystkie utworzone elementy. Usunięcie grupy zasobów spowoduje również usunięcie jej zawartości. Jeśli nie zamierzasz korzystać z tych drzwi, Usuń zasoby, aby uniknąć niepotrzebnych opłat.

1. W Azure Portal Wyszukaj i wybierz **grupy zasobów** lub wybierz pozycję **grupy zasobów** z menu Azure Portal.

1. Odfiltruj lub przewiń w dół, aby znaleźć grupę zasobów, np. **FrontDoorQS_rg0**.

1. Wybierz grupę zasobów, a następnie wybierz pozycję **Usuń grupę zasobów**.

   >[!WARNING]
   >Ta akcja to irreversable.

1. Wpisz nazwę grupy zasobów do zweryfikowania, a następnie wybierz pozycję **Usuń**.

Powtórz procedurę dla pozostałych dwóch grup.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak dodać domenę niestandardową do swoich drzwi.
> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](how-to-add-custom-domain.md)