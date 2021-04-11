---
title: Utwórz konto Video Indexer połączone z platformą Azure
titleSuffix: Azure Media Services
description: Dowiedz się, jak utworzyć konto Video Indexer połączone z platformą Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 35d01fd60d0549a528a45013add48bd203b86d6e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654683"
---
# <a name="create-a-video-indexer-account"></a>Utwórz konto Video Indexer

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. Za pomocą opcji płatne można utworzyć konto Video Indexer, które jest połączone z subskrypcją platformy Azure. Płacisz za minuty, aby uzyskać więcej informacji, zobacz [Cennik usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/).

W tym artykule pokazano, jak utworzyć konto Video Indexer połączone z subskrypcją platformy Azure i kontem Azure Media Services. Temat zawiera instrukcje dotyczące łączenia się z platformą Azure przy użyciu automatycznego (domyślnego) przepływu. Przedstawiono w nim również sposób ręcznego nawiązywania połączenia z platformą Azure (Zaawansowane).

W przypadku przechodzenia z *wersji próbnej* do *płatnego* konta Video Indexer można skopiować wszystkie filmy wideo i dostosowania modelu do nowego konta, jak to opisano w sekcji [Importowanie zawartości z konta wersji próbnej](#import-your-content-from-the-trial-account) .

Artykuł obejmuje również [łączenie Video Indexer konta z Azure Government](#video-indexer-in-azure-government).

## <a name="prerequisites-for-connecting-to-azure"></a>Wymagania wstępne dotyczące nawiązywania połączenia z platformą Azure

* Subskrypcja platformy Azure.

    Jeśli nie masz jeszcze subskrypcji platformy Azure, zarejestruj się, aby skorzystać z [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/free/).
* Domena usługi Azure Active Directory (Azure AD).

    Jeśli nie masz domeny usługi Azure AD, Utwórz tę domenę przy użyciu subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie niestandardowymi nazwami domen w usłudze Azure AD](../../active-directory/enterprise-users/domains-manage.md)
* Użytkownik w domenie usługi Azure AD z rolą **administratora aplikacji** . Ten element członkowski będzie używany podczas łączenia konta Video Indexer z platformą Azure.

    Ten użytkownik powinien być użytkownikiem usługi Azure AD przy użyciu konta służbowego. Nie używaj konta osobistego, takiego jak outlook.com, live.com lub hotmail.com.

    ![Wszyscy użytkownicy usługi Azure AD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Dodatkowe wymagania wstępne dotyczące automatycznego przepływu

* Użytkownik i członek w domenie usługi Azure AD.

    Ten element członkowski będzie używany podczas łączenia konta Video Indexer z platformą Azure.

    Ten użytkownik powinien być członkiem subskrypcji platformy Azure z rolą **właściciela** albo rolami **administratora dostępu** **współautora** i użytkownika. Można dwa razy dodać użytkownika z dwiema rolami. Jeden raz z współautor i raz z administratorem dostępu użytkowników. Aby uzyskać więcej informacji, zobacz [Wyświetlanie dostępu użytkownika do zasobów platformy Azure](../../role-based-access-control/check-access.md).

    ![Kontrola dostępu](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Dodatkowe wymagania wstępne dotyczące przepływu ręcznego

* Zarejestruj dostawcę zasobów EventGrid przy użyciu Azure Portal.

    W [Azure Portal](https://portal.azure.com/)przejdź do pozycji **subskrypcje**— > [subskrypcja]->**ResourceProviders**.

    Wyszukaj ciąg **Microsoft. Media** i **Microsoft. EventGrid**. Jeśli nie jest w stanie "zarejestrowano", kliknij pozycję **zarejestruj**. Rejestracja może potrwać kilka minut.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Utwórz nowe konto na platformie Azure 

> [!NOTE]
> Jeśli subskrypcja platformy Azure korzysta z uwierzytelniania wieloskładnikowego opartego na certyfikatach, należy wykonać następujące czynności na urządzeniu, na którym zainstalowano wymagane certyfikaty.

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
1. Wybierz przycisk **Utwórz nieograniczone konto** :

    ![Utwórz nowe konto Video Indexer](./media/create-account/create-unlimited-account.png)
1. Gdy zostanie wyświetlona lista subskrypcje, wybierz subskrypcję, której chcesz użyć.

    ![Łączenie Video Indexer z platformą Azure](./media/create-account/new-account-on-azure-subscription.png)
1. Wybierz region platformy Azure z obsługiwanych lokalizacji: zachodnie stany USA 2, Europa Północna lub Azja Wschodnia.
1. W obszarze **konto Azure Media Services** wybierz jedną z następujących opcji:

    * Aby utworzyć nowe konto Media Services, wybierz pozycję **Utwórz nową grupę zasobów**. Podaj nazwę grupy zasobów.

        Na platformie Azure zostanie utworzone nowe konto w ramach subskrypcji, w tym nowe konto usługi Azure Storage.  
    * Aby użyć istniejącego konta Media Services, wybierz opcję **Użyj istniejącego zasobu**. Z listy konta wybierz swoje konto.

        Konto Media Services musi mieć ten sam region, co konto Video Indexer.

        > [!NOTE]
        > Aby zminimalizować czas trwania indeksowania i niską przepływność, zdecydowanie zalecamy dostosowanie typu i liczby [jednostek zarezerwowanych](../previous/media-services-scale-media-processing-overview.md ) na koncie Media Services do **10 jednostek zarezerwowanych S3**. [Aby zmienić zarezerwowane jednostki, zobacz Korzystanie z portalu](../previous/media-services-portal-scale-media-processing.md). Jednostki zarezerwowane są rozliczone na Twoje konto, a [szczegóły cennika](https://azure.microsoft.com/pricing/details/media-services/#analytics).
    * Aby ręcznie skonfigurować połączenie, wybierz link **Przełącz do konfiguracji ręcznej** .

        Aby uzyskać szczegółowe informacje, zobacz sekcję [łączenie z usługą Azure ręcznie](#connect-to-azure-manually-advanced-option) (opcja zaawansowana), która znajduje się poniżej.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. Ta operacja może potrwać kilka minut.

    Po nawiązaniu połączenia z platformą Azure nowe konto Video Indexer będzie wyświetlane na liście kont:

    ![nowe konto](./media/create-account/new-account.png)
1. Upewnij się, że punkt końcowy przesyłania strumieniowego konta Media Services jest uruchomiony, zanim będzie możliwe odtworzenie filmów wideo w aplikacji sieci Web Video Indexer (kliknij przycisk Uruchom, jeśli jest to stan zatrzymany).

> [!TIP]
> Aby zapewnić przyjazny nazwa wyświetlania na koncie, przejdź do pozycji **Ustawienia**.

## <a name="connect-to-azure-manually-advanced-option"></a>Ręczne nawiązywanie połączenia z platformą Azure (opcja zaawansowana)

Jeśli połączenie z platformą Azure zakończyło się niepowodzeniem, możesz spróbować rozwiązać problem, łącząc go ręcznie.

> [!NOTE]
> Zdecydowanie zaleca się, aby następujące trzy konta były w tym samym regionie: konto Video Indexer, które nawiązujesz połączenie z kontem Media Services, a także konto usługi Azure Storage połączone z tym samym kontem Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Utwórz i skonfiguruj konto Media Services

1. Użyj witryny [Azure](https://portal.azure.com/) Portal, aby utworzyć konto Azure Media Services, zgodnie z opisem w temacie [Tworzenie konta](../previous/media-services-portal-create-account.md).

     Upewnij się, że konto Media Services zostało utworzone przy użyciu klasycznych interfejsów API. 
 
    ![Media Services klasyczny interfejs API](./media/create-account/enable-classic-api.png)


    Podczas tworzenia konta magazynu dla konta Media Services wybierz opcję **StorageV2** dla rodzaju konta i **Geograficznie nadmiarowy** (GRS) dla pól replikacji.

    ![Nowe konto AMS](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Upewnij się, że Zanotuj Media Services nazwy zasobów i kont. Będą one potrzebne do wykonania kroków opisanych w następnej sekcji.
1. Dostosuj typ i liczbę [jednostek zarezerwowanych](../previous/media-services-scale-media-processing-overview.md ) do **10 jednostek zarezerwowanych** w ramach utworzonego konta Media Services. [Aby zmienić zarezerwowane jednostki, zobacz Korzystanie z portalu](../previous/media-services-portal-scale-media-processing.md).

    Jednostki zarezerwowane są rozliczone na Twoje konto, Wyświetl [szczegóły cennika](https://azure.microsoft.com/pricing/details/media-services/#analytics).
1. Przed rozpoczęciem odtwarzania filmów wideo w aplikacji internetowej Video Indexer należy uruchomić domyślny **punkt końcowy przesyłania strumieniowego** nowego konta Media Services.

    Na nowym koncie Media Services wybierz pozycję **punkty końcowe przesyłania strumieniowego**. Następnie wybierz punkt końcowy przesyłania strumieniowego i naciśnij przycisk Uruchom.

    ![Punkty końcowe przesyłania strumieniowego](./media/create-account/create-ams-account-se.png)
4. Aby Video Indexer uwierzytelnić się przy użyciu interfejsu API Media Services, należy utworzyć aplikację usługi AD. Poniższe kroki przeprowadzą Cię przez proces uwierzytelniania usługi Azure AD opisany w temacie [Rozpoczynanie pracy z uwierzytelnianiem w usłudze Azure AD przy użyciu Azure Portal](../previous/media-services-portal-get-started-with-aad.md):

    1. Na nowym koncie Media Services wybierz pozycję **dostęp do interfejsu API**.
    2. Wybierz [metodę uwierzytelniania nazwy głównej usługi](../previous/media-services-portal-get-started-with-aad.md).
    3. Pobierz identyfikator klienta i klucz tajny klienta

        Po **wybraniu opcji** -> **klucze**, Dodaj **Opis**, naciśnij przycisk **Zapisz**, a wartość klucza zostanie wypełniona.

        Jeśli klucz zostanie wygaśnie, właściciel konta będzie musiał skontaktować się z pomocą techniczną Video Indexer, aby odnowić klucz.

        > [!NOTE]
        > Upewnij się, że Zanotuj wartość klucza i identyfikator aplikacji. Będzie ona potrzebna do wykonania kroków opisanych w następnej sekcji.

### <a name="connect-manually"></a>Połącz ręcznie

W oknie dialogowym **Tworzenie nowego konta w ramach subskrypcji platformy Azure na** stronie [Video Indexer](https://www.videoindexer.ai/) wybierz łącze **Przełącz na konfigurację ręczną** .

W oknie dialogowym podaj następujące informacje:

|Ustawienie|Opis|
|---|---|
|Region konta Video Indexer|Nazwa regionu konta Video Indexer. W celu uzyskania lepszej wydajności i niższych kosztów zdecydowanie zaleca się określenie nazwy regionu, w którym znajdują się zasoby Azure Media Services i konta usługi Azure Storage. |
|Dzierżawa usługi Azure AD|Nazwa dzierżawy usługi Azure AD, na przykład "contoso.onmicrosoft.com". Informacje o dzierżawie można pobrać z Azure Portal. Umieść kursor nad nazwą zalogowanego użytkownika w prawym górnym rogu. Znajdź nazwę z prawej strony **domeny**.|
|Identyfikator subskrypcji|Subskrypcja platformy Azure, w ramach której należy utworzyć to połączenie. Identyfikator subskrypcji można pobrać z Azure Portal. Wybierz pozycję **wszystkie usługi** w lewym panelu i wyszukaj frazę "subskrypcje". Wybierz pozycję **subskrypcje** i wybierz żądany identyfikator z listy subskrypcji.|
|Nazwa grupy zasobów Azure Media Services|Nazwa grupy zasobów, w której utworzono konto Media Services.|
|Nazwa zasobu usługi multimediów|Nazwa konta Azure Media Services utworzonego w poprzedniej sekcji.|
|Identyfikator aplikacji|Identyfikator aplikacji usługi Azure AD (z uprawnieniami dla określonego konta Media Services), które zostały utworzone w poprzedniej sekcji.|
|Klucz aplikacji|Klucz aplikacji usługi Azure AD utworzony w poprzedniej sekcji. |

### <a name="import-your-content-from-the-trial-account"></a>Importowanie zawartości z konta *wersji próbnej*

Podczas tworzenia nowego konta możesz zaimportować zawartość z konta *próbnego* do nowego konta. Jeśli zaznaczono opcję *Importuj* w oknie dialogowym **Tworzenie nowego konta w ramach subskrypcji platformy Azure** , wszystkie dostosowania nośników i modeli zawartości zostaną skopiowane z konta *próbnego* na nowe konto.

Możliwość importowania zawartości jest ważna zarówno w przypadku zautomatyzowanych, jak i ręcznych metod opisanych powyżej.

> [!NOTE]
> Zawartość można zaimportować tylko raz z każdego konta.
>
> Konto *wersji próbnej* nie jest availagle w chmurze Azure Government.

## <a name="azure-media-services-considerations"></a>Zagadnienia dotyczące Azure Media Services

Stosuje się następujące Azure Media Services powiązane zagadnienia:

* Jeśli planujesz nawiązać połączenie z istniejącym kontem Media Services, upewnij się, że konto Media Services zostało utworzone przy użyciu klasycznych interfejsów API. 
 
    ![Media Services klasyczny interfejs API](./media/create-account/enable-classic-api.png)
* Jeśli łączysz się z istniejącym kontem Media Services, Video Indexer nie zmieni istniejącej konfiguracji **jednostek zarezerwowanych** multimediów.

   Może być konieczne dostosowanie typu i liczby jednostek zarezerwowanych multimediów zgodnie ze planowanym obciążeniem. Pamiętaj, że jeśli obciążenie jest wysokie i nie masz wystarczającej liczby jednostek lub szybkości, przetwarzanie filmów wideo może spowodować błędy przekroczenia limitu czasu.
* W przypadku nawiązania połączenia z nowym kontem Media Services Video Indexer automatycznie uruchamia domyślny **punkt końcowy przesyłania strumieniowego** :

    ![Media Services punkt końcowy przesyłania strumieniowego](./media/create-account/ams-streaming-endpoint.png)

    Punkty końcowe przesyłania strumieniowego mają znaczny czas uruchamiania. W związku z tym może upłynąć kilka minut od momentu połączenia Twojego konta z platformą Azure, dopóki Twoje wideo nie zostanie przesłane i będzie widoczne w aplikacji internetowej Video Indexer.
* Jeśli łączysz się z istniejącym kontem Media Services, Video Indexer nie zmieni domyślnej konfiguracji punktu końcowego przesyłania strumieniowego. Jeśli nie ma uruchomionego **punktu końcowego przesyłania strumieniowego**, nie możesz oglądać filmów wideo z tego konta Media Services ani Video Indexer.
* W przypadku automatycznego łączenia Video Indexer ustawia **jednostki zarezerwowane** multimediów na 10 jednostek S3:

    ![Media Services jednostek zarezerwowanych](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-indexer-account"></a>Automatyzowanie tworzenia konta Video Indexer

Aby zautomatyzować tworzenie konta, należy wykonać dwa kroki:
 
1. Użyj Azure Resource Manager, aby utworzyć konto Azure Media Services i aplikację usługi Azure AD.

    Zapoznaj się z przykładem [szablonu tworzenia konta Media Services](https://github.com/Azure-Samples/media-services-v3-arm-templates).
1. Wywołaj [Tworzenie konta za pomocą aplikacji Media Services i usługi Azure AD](https://videoindexer.ai.azure.us/account/login?source=apim).

## <a name="video-indexer-in-azure-government"></a>Video Indexer w Azure Government

### <a name="prerequisites-for-connecting-to-azure-government"></a>Wymagania wstępne dotyczące nawiązywania połączenia z usługą Azure Government

-   Subskrypcja platformy Azure w [Azure Government](../../azure-government/index.yml).
- Konto usługi Azure AD w Azure Government.
- Wszystkie wymagania wstępne dotyczące uprawnień i zasobów zgodnie z powyższym opisem w sekcji [wymagania wstępne dotyczące nawiązywania połączenia z platformą Azure](#prerequisites-for-connecting-to-azure). Upewnij się, że zawarto sprawdzanie [dodatkowych wymagań wstępnych dotyczących automatycznego przepływu](#additional-prerequisites-for-automatic-flow) i [dodatkowych wymagań wstępnych dotyczących przepływu ręcznego](#additional-prerequisites-for-manual-flow).

### <a name="create-new-account-via-the-azure-government-portal"></a>Utwórz nowe konto za pośrednictwem portalu Azure Government

> [!NOTE]
> W chmurze Azure Government nie są dostępne środowiska *próbne* Video Indexer.

Aby utworzyć płatne konto za pośrednictwem portalu Video Indexer:

1. Przejdź do strony https://videoindexer.ai.azure.us 
1. Zaloguj się przy użyciu konta usługi Azure Government Azure AD.
1.  Jeśli nie masz żadnych kont Video Indexer w Azure Government, których jesteś właścicielem lub współautorem, uzyskasz puste środowisko, z którego można rozpocząć tworzenie konta. 

    Pozostała część przepływu jest opisana powyżej, tylko regiony do wyboru będą regionami rządowymi, w których dostępny jest indeksator wideo 

    Jeśli już jesteś współautorem lub administratorem istniejącego konta Video Indexer w usłudze Azure Government, nastąpi przekierowanie do tego konta. w razie potrzeby można rozpocząć wykonywanie kroków w celu utworzenia dodatkowego konta, zgodnie z powyższym opisem.
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Utwórz nowe konto za pośrednictwem interfejsu API na Azure Government

Aby utworzyć płatne konto w Azure Government, postępuj zgodnie z instrukcjami w temacie [Create-wpłacony-Account](). Ten punkt końcowy interfejsu API obejmuje tylko regiony w chmurze dla instytucji rządowych.

### <a name="limitations-of-video-indexer-on-azure-government"></a>Ograniczenia Video Indexer na Azure Government

*   W chmurze dla instytucji rządowych nie są dostępne żadne ręczne moderowanie zawartości. 

    W chmurze publicznej, gdy zawartość jest uznawana za obraźliwą w oparciu o moderowanie zawartości, klient może poprosił o ludzi, aby przyjrzeć się tej zawartości i potencjalnie spowodować przywrócenie tej decyzji.  
*   Brak kont w wersji próbnej. 
* Opis usługi Bing — w chmurze gov nie będzie prezentowany opis zidentyfikowanych osobistości i nazwanych jednostek. Jest to tylko funkcja interfejsu użytkownika. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym samouczkiem Usuń zasoby, których nie planujesz używać.

### <a name="delete-a-video-indexer-account"></a>Usuwanie konta Video Indexer

Jeśli chcesz usunąć konto Video Indexer, możesz je usunąć z witryny internetowej Video Indexer. Aby usunąć konto, musisz być właścicielem.

Wybierz pozycję konto **— >**  ->  **usunąć to konto**. 

Konto zostanie trwale usunięte w ciągu 90 dni.

## <a name="next-steps"></a>Następne kroki

Możesz programowo korzystać z konta próbnego i/lub z kontami Video Indexer, które są połączone z platformą Azure, postępując zgodnie z instrukcjami w temacie: [Korzystanie z interfejsów API](video-indexer-use-apis.md).

Należy używać tego samego użytkownika usługi Azure AD, który był używany podczas nawiązywania połączenia z platformą Azure.
