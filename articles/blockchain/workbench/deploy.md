---
title: Wdrażanie usługi Azure łańcucha bloków Workbench w wersji zapoznawczej
description: Jak wdrożyć usługę Azure łańcucha bloków Workbench w wersji zapoznawczej
ms.date: 07/16/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: b46a35b45a51d0cc76942c4ca142c4c7792a28b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87077025"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Wdrażanie usługi Azure łańcucha bloków Workbench w wersji zapoznawczej

Usługa Azure łańcucha bloków Workbench Preview została wdrożona przy użyciu szablonu rozwiązania w portalu Azure Marketplace. Szablon upraszcza wdrażanie składników niezbędnych do tworzenia aplikacji łańcucha bloków. Po wdrożeniu program łańcucha bloków Workbench zapewnia dostęp do aplikacji klienckich w celu tworzenia użytkowników i aplikacji łańcucha bloków oraz zarządzania nimi.

Aby uzyskać więcej informacji na temat składników łańcucha bloków Workbench, zobacz [Azure łańcucha bloków Workbench Architecture](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Łańcucha bloków Workbench umożliwia wdrożenie księgi łańcucha bloków wraz z zestawem odpowiednich usług platformy Azure najczęściej używanych do kompilowania aplikacji opartych na łańcucha bloków. Wdrożenie łańcucha bloków Workbench powoduje zainicjowanie obsługi następujących usług platformy Azure w grupie zasobów w ramach subskrypcji platformy Azure.

* Plan App Service (standardowa)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL Database (standardowa S0)
* Konto usługi Azure Storage (standardowa LRS)
* Zestaw skalowania maszyn wirtualnych o pojemności 1
* Virtual Network grupę zasobów (z Load Balancer, sieciową grupą zabezpieczeń, publicznym adresem IP, Virtual Network)
* Usługa Azure łańcucha bloków. Jeśli używasz wcześniejszego wdrożenia programu łańcucha bloków Workbench, Rozważ ponowne wdrożenie usługi Azure łańcucha bloków Workbench do korzystania z usług Azure łańcucha bloków.

Poniżej przedstawiono przykładowe wdrożenie utworzone w grupie zasobów **myblockchain** .

![Przykładowe wdrożenie](media/deploy/example-deployment.png)

Koszt usługi łańcucha bloków Workbench to zagregowany koszt podstawowych usług platformy Azure. Informacje o cenach dla usług platformy Azure można obliczyć przy użyciu [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Wymagania wstępne

Usługa Azure łańcucha bloków Workbench wymaga konfiguracji usługi Azure AD i rejestracji aplikacji. Przed wdrożeniem można [ręcznie określić konfiguracje](#azure-ad-configuration) usługi Azure AD lub uruchomić wdrożenie skryptu. W przypadku ponownego wdrażania programu łańcucha bloków Workbench, zobacz [Konfiguracja usługi Azure AD](#azure-ad-configuration) , aby zweryfikować konfigurację usługi Azure AD.

> [!IMPORTANT]
> Workbench nie musi być wdrożona w tej samej dzierżawie, która jest używana do rejestrowania aplikacji usługi Azure AD. Workbench należy wdrożyć w dzierżawie, w której masz wystarczające uprawnienia do wdrażania zasobów. Aby uzyskać więcej informacji o dzierżawach usługi Azure AD, zobacz [jak uzyskać dzierżawę Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) i [zintegrować aplikacje z Azure Active Directory](../../active-directory/develop/quickstart-register-app.md).

## <a name="deploy-blockchain-workbench"></a>Wdróż łańcucha bloków Workbench

Po zakończeniu kroków wstępnych można przystąpić do wdrożenia łańcucha bloków Workbench. W poniższych sekcjach opisano sposób wdrażania struktury.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz swoje konto w prawym górnym rogu i przejdź do odpowiedniej dzierżawy usługi Azure AD, w której chcesz wdrożyć usługę Azure łańcucha bloków Workbench.
1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Wybierz pozycję **łańcucha bloków**  >  **Azure łańcucha bloków Workbench (wersja zapoznawcza)**.

    ![Tworzenie usługi Azure łańcucha bloków Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Prefiks zasobu | Krótki unikatowy identyfikator wdrożenia. Ta wartość jest używana jako podstawa nazw zasobów. |
    | Nazwa użytkownika maszyny wirtualnej | Nazwa użytkownika jest używana jako administrator dla wszystkich maszyn wirtualnych. |
    | Typ uwierzytelniania | Wybierz, jeśli chcesz użyć hasła lub klucza w celu nawiązania połączenia z maszynami wirtualnymi. |
    | Hasło | Hasło służy do łączenia się z maszynami wirtualnymi. |
    | Protokół SSH | Użyj klucza publicznego RSA w formacie jednowierszowym rozpoczynającym się od **protokołu SSH-RSA** lub użyj wielowierszowego formatu PEM. Klucze SSH można generować przy użyciu `ssh-keygen` systemu Linux i OS X lub przy użyciu usługi PuTTYGen w systemie Windows. Więcej informacji na temat kluczy SSH znajduje się w temacie [jak używać kluczy SSH w systemie Windows na platformie Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Baza danych i hasło łańcucha bloków | Określ hasło, które ma być używane na potrzeby dostępu do bazy danych utworzonej w ramach wdrożenia. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów ( \` ), podwójny cudzysłów ("), apostrof (), kreska () i semicolumn (;) |
    | Region wdrożenia | Określ, gdzie mają zostać wdrożone zasoby łańcucha bloków Workbench. Aby zapewnić najlepszą dostępność, powinno to być zgodne z ustawieniem lokalizacja **regionu** . Nie wszystkie regiony są dostępne w trakcie okresu zapoznawczego. Funkcje mogą być niedostępne w niektórych regionach. Usługa Azure łańcucha bloków Data Manager jest dostępna w następujących regionach świadczenia usługi Azure: Wschodnie stany USA i Europa Zachodnia.|
    | Subskrypcja | Określ subskrypcję platformy Azure, której chcesz użyć dla danego wdrożenia. |
    | Grupy zasobów | Utwórz nową grupę zasobów, wybierając pozycję **Utwórz nową** i określić unikatową nazwę grupy zasobów. |
    | Lokalizacja | Określ region, w którym ma zostać wdrożona struktura. |

1. Wybierz **przycisk OK** , aby zakończyć sekcję Konfiguracja ustawienia podstawowe.

1. W obszarze **Ustawienia zaawansowane** wybierz, czy chcesz utworzyć nową sieć łańcucha bloków, czy użyć istniejącej sieci łańcucha bloków typu potwierdzenie urzędu certyfikacji.

    W przypadku **tworzenia nowego**:

    Opcja *Utwórz nową* umożliwia wdrożenie księgi kworum usługi Azure łańcucha bloków z domyślną podstawową jednostką SKU.

    ![Ustawienia zaawansowane dla nowej sieci łańcucha bloków](media/deploy/advanced-blockchain-settings-new.png)

    | Ustawienie | Opis  |
    |---------|--------------|
    | Warstwa cenowa usługi Azure łańcucha bloków Service | Wybierz **podstawową** lub **standardową** warstwę usługi Azure łańcucha bloków, która jest używana do łańcucha bloków Workbench |
    | Ustawienia usługi Azure Active Directory | Wybierz pozycję **Dodaj później**.</br>Uwaga: w przypadku wybrania opcji [wstępnego skonfigurowania usługi Azure AD](#azure-ad-configuration) lub ponownego wdrożenia wybierz opcję *dodania teraz*. |
    | Wybór maszyny wirtualnej | Wybierz preferowaną wydajność magazynu i rozmiar maszyny wirtualnej dla sieci łańcucha bloków. Wybierz mniejszy rozmiar maszyny wirtualnej, na przykład *standardowa DS1 v2* , jeśli masz subskrypcję z niskimi limitami usług, takimi jak platforma Azure w warstwie Bezpłatna. |

    Do **użycia istniejące**:

    Opcja *Użyj istniejącej* umożliwia określenie sieci łańcucha bloków (Ethereum potwierdzenie urzędu certyfikacji). Punkty końcowe mają poniższe wymagania.

   * Punkt końcowy musi być siecią łańcucha bloków typu Ethereum (PoA).
   * Punkt końcowy musi być publicznie dostępny za pośrednictwem sieci.
   * Sieć łańcucha bloków PoA powinna być skonfigurowana w taki sposób, aby cena gazu była ustawiona na zero.

     > [!NOTE]
     > Konta Workbench łańcucha bloków nie są finansowane. Jeśli środki są wymagane, transakcje nie powiodą się.

     ![Ustawienia zaawansowane dla istniejącej sieci łańcucha bloków](media/deploy/advanced-blockchain-settings-existing.png)

     | Ustawienie | Opis  |
     |---------|--------------|
     | Punkt końcowy RPC Ethereum | Podaj punkt końcowy RPC istniejącej sieci łańcucha bloków. Punkt końcowy rozpoczyna się od https://lub http://i ma numer portu. Na przykład `http<s>://<network-url>:<port>` |
     | Ustawienia usługi Azure Active Directory | Wybierz pozycję **Dodaj później**.</br>Uwaga: w przypadku wybrania opcji [wstępnego skonfigurowania usługi Azure AD](#azure-ad-configuration) lub ponownego wdrożenia wybierz opcję *dodania teraz*. |
     | Wybór maszyny wirtualnej | Wybierz preferowaną wydajność magazynu i rozmiar maszyny wirtualnej dla sieci łańcucha bloków. Wybierz mniejszy rozmiar maszyny wirtualnej, na przykład *standardowa DS1 v2* , jeśli masz subskrypcję z niskimi limitami usług, takimi jak platforma Azure w warstwie Bezpłatna. |

1. Wybierz pozycję **Przegląd + Utwórz** , aby zakończyć ustawienia zaawansowane.

1. Przejrzyj podsumowanie, aby sprawdzić, czy parametry są dokładne.

    ![Podsumowanie](media/deploy/blockchain-workbench-summary.png)

1. Wybierz pozycję **Utwórz** , aby zaakceptować warunki i wdrożyć platformę Azure łańcucha bloków Workbench.

Wdrożenie może potrwać do 90 minut. Aby monitorować postęp, można użyć Azure Portal. W nowo utworzonej grupie zasobów wybierz pozycję **wdrożenia > przegląd** , aby wyświetlić stan wdrożonych artefaktów.

> [!IMPORTANT]
> Po wdrożeniu należy wykonać Active Directory ustawienia. W przypadku wybrania opcji **Dodaj później** należy uruchomić [skrypt konfiguracji usługi Azure AD](#azure-ad-configuration-script).  W przypadku wybrania opcji **Dodaj teraz** należy [skonfigurować adres URL odpowiedzi](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Adres URL sieci Web łańcucha bloków Workbench

Po zakończeniu wdrożenia programu łańcucha bloków Workbench Nowa grupa zasobów zawiera zasoby Workbench łańcucha bloków. Usługi łańcucha bloków Workbench są dostępne za pomocą adresu URL sieci Web. Poniższe kroki pokazują, jak pobrać internetowy adres URL wdrożonej struktury.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz nazwę grupy zasobów, która została określona podczas wdrażania łańcucha bloków Workbench.
1. Wybierz nagłówek kolumny **Typ** , aby posortować listę alfabetycznie według typu.
1. Istnieją dwa zasoby z typem **App Service**. Wybierz zasób typu **App Service** *bez* sufiksu "-API".

    ![Lista usługi App Service](media/deploy/resource-group-list.png)

1. W App Service **Przegląd** skopiuj wartość **adresu URL** , która reprezentuje adres URL sieci Web do wdrożonego łańcucha bloków Workbench.

    ![Podstawowe informacje o usłudze App Service](media/deploy/app-service.png)

Aby skojarzyć niestandardową nazwę domeny z łańcucha bloków Workbench, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci Web w Azure App Service przy użyciu Traffic Manager](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Skrypt konfiguracji usługi Azure AD

Usługa Azure AD musi być skonfigurowana do ukończenia wdrożenia usługi łańcucha bloków Workbench. Aby przeprowadzić konfigurację, użyjesz skryptu programu PowerShell.

1. W przeglądarce przejdź do [adresu URL sieci Web łańcucha bloków Workbench](#blockchain-workbench-web-url).
1. Zobaczysz instrukcje dotyczące konfigurowania usługi Azure AD przy użyciu Cloud Shell. Skopiuj polecenie i uruchom Cloud Shell.

    ![Uruchom skrypt usługi AAD](media/deploy/launch-aad-script.png)

1. Wybierz dzierżawę usługi Azure AD, w której wdrożono łańcucha bloków Workbench.
1. W Cloud Shell środowisku PowerShell Wklej i uruchom polecenie.
1. Po wyświetleniu monitu wprowadź dzierżawę usługi Azure AD, której chcesz użyć dla łańcucha bloków Workbench. Będzie to dzierżawca zawierający użytkowników do łańcucha bloków Workbench.

    > [!IMPORTANT]
    > Uwierzytelniony użytkownik musi mieć uprawnienia do tworzenia rejestracji aplikacji usługi Azure AD i przyznawania delegowanych uprawnień aplikacji w dzierżawie. Może być konieczne poproszenie administratora dzierżawy o uruchomienie skryptu konfiguracji usługi Azure AD lub utworzenie nowej dzierżawy.

    ![Wprowadź dzierżawę usługi Azure AD](media/deploy/choose-tenant.png)

1. Zostanie wyświetlony monit o uwierzytelnienie w dzierżawie usługi Azure AD przy użyciu przeglądarki. Otwórz adres URL sieci Web w przeglądarce, wprowadź kod i Uwierzytelnij.

    ![Uwierzytelnianie przy użyciu kodu](media/deploy/authenticate.png)

1. Skrypt wyprowadza kilka komunikatów o stanie. Jeśli dzierżawa została pomyślnie zainicjowana, otrzymasz komunikat o stanie **sukces** .
1. Przejdź do adresu URL łańcucha bloków Workbench. Zostanie wyświetlona prośba o zgodę na przyznanie uprawnień do odczytu katalogu. Umożliwia to aplikacji sieci Web łańcucha bloków Workbench dostęp do użytkowników w dzierżawie. Jeśli jesteś administratorem dzierżawy, możesz wybrać opcję wyrażania zgody na całą organizację. Ta opcja akceptuje zgodę dla wszystkich użytkowników w dzierżawie. W przeciwnym razie każdy użytkownik zostanie poproszony o zgodę przy pierwszym użyciu aplikacji sieci Web łańcucha bloków Workbench.
1. Wybierz pozycję **Akceptuj** , aby wyrazić zgodę.

     ![Wyrażanie zgody na odczyt profilów użytkowników](media/deploy/graph-permission-consent.png)

1. Po udzieleniu zgody można użyć aplikacji sieci Web łańcucha bloków Workbench.

Ukończono wdrożenie usługi Azure łańcucha bloków Workbench. Aby uzyskać sugestie dotyczące rozpoczynania pracy z wdrożeniem, zobacz [następne kroki](#next-steps) .

## <a name="azure-ad-configuration"></a>Konfiguracja usługi Azure AD

W przypadku wybrania opcji ręcznego konfigurowania lub weryfikowania ustawień usługi Azure AD przed wdrożeniem wykonaj wszystkie kroki opisane w tej sekcji. Jeśli wolisz automatycznie konfigurować ustawienia usługi Azure AD, użyj [skryptu konfiguracji usługi Azure AD](#azure-ad-configuration-script) po wdrożeniu łańcucha bloków Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Rejestracja aplikacji interfejsu API łańcucha bloków Workbench

Wdrożenie łańcucha bloków Workbench wymaga rejestracji aplikacji usługi Azure AD. Do zarejestrowania aplikacji potrzebna jest dzierżawa usługi Azure Active Directory (Azure AD). Możesz użyć istniejącej dzierżawy lub utworzyć nową dzierżawę. Jeśli używasz istniejącej dzierżawy usługi Azure AD, potrzebujesz wystarczających uprawnień do rejestrowania aplikacji, przyznawania uprawnień interfejs API programu Graph i zezwalania na dostęp gościa w ramach dzierżawy usługi Azure AD. Jeśli nie masz wystarczających uprawnień w istniejącej dzierżawie usługi Azure AD, Utwórz nową dzierżawę.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz swoje konto w prawym górnym rogu i przejdź do odpowiedniej dzierżawy usługi Azure AD. Dzierżawa powinna być dzierżawą subskrypcji administratora subskrypcji, w której wdrożono usługę Azure łańcucha bloków Workbench i masz wystarczające uprawnienia do rejestrowania aplikacji.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**. Wybierz pozycję **rejestracje aplikacji**  >  **Nowa rejestracja**.

    ![Rejestrowanie aplikacji](media/deploy/app-registration.png)

1. Podaj **nazwę** wyświetlaną i wybierz **konta w tym katalogu organizacji**.

    ![Tworzenie rejestracji aplikacji](media/deploy/app-registration-create.png)

1. Wybierz pozycję **zarejestruj** , aby zarejestrować aplikację usługi Azure AD.

### <a name="modify-manifest"></a>Modyfikuj manifest

Następnie musisz zmodyfikować manifest, aby użyć ról aplikacji w usłudze Azure AD, aby określić administratorów łańcucha bloków Workbench.  Aby uzyskać więcej informacji na temat manifestów aplikacji, zobacz [Azure Active Directory manifest aplikacji](../../active-directory/develop/reference-app-manifest.md).

1. Dla manifestu wymagany jest identyfikator GUID. Identyfikator GUID można wygenerować za pomocą polecenia programu PowerShell `[guid]::NewGuid()` lub poleceń `New-GUID` cmdlet. Innym rozwiązaniem jest użycie witryny sieci Web generatora GUID.
1. W przypadku zarejestrowanej aplikacji wybierz pozycję **manifest** w sekcji **Zarządzanie** .
1. Następnie zaktualizuj sekcję **appRoles** manifestu. Zamień `"appRoles": []` na podany kod JSON. Pamiętaj, aby zastąpić wartość `id` pola wygenerowanym identyfikatorem GUID.
    ![Edytuj manifest](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > **Administrator** wartości jest wymagany do identyfikowania administratorów łańcucha bloków Workbench.

1. W manifeście Zmień również wartość **Oauth2AllowImplicitFlow** na **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Wybierz pozycję **Zapisz** , aby zapisać zmiany manifestu.

### <a name="add-graph-api-required-permissions"></a>Dodaj interfejs API programu Graph wymagane uprawnienia

Aplikacja interfejsu API musi zażądać uprawnień od użytkownika w celu uzyskania dostępu do katalogu. Dla aplikacji interfejsu API ustaw następujące wymagane uprawnienia:

1. W rejestracji aplikacji *interfejsu API łańcucha bloków* wybierz pozycję **uprawnienia interfejsu API**. Domyślnie dodawane jest uprawnienie interfejs API programu Graph **User. Read** .
1. Aplikacja Workbench wymaga dostępu do odczytu do informacji o profilu podstawowym użytkownika. W obszarze *skonfigurowane uprawnienia* wybierz pozycję **Dodaj uprawnienie**. W obszarze **interfejsy API firmy Microsoft** wybierz pozycję **Microsoft Graph**.
1. Ponieważ aplikacja Workbench używa uwierzytelnionych poświadczeń użytkownika, wybierz pozycję **uprawnienia delegowane**.
1. W kategorii *użytkownik* wybierz uprawnienie **User. ReadBasic. All** .

    ![Konfiguracja rejestracji aplikacji usługi Azure AD pokazująca dodanie Microsoft Graph User. ReadBasic. All — uprawnienie delegowane](media/deploy/add-graph-user-permission.png)

    Wybierz pozycję **Dodaj uprawnienia**.

1. W obszarze *skonfigurowane uprawnienia* wybierz pozycję **Udziel zgody administratora** dla domeny, a następnie wybierz pozycję **tak** dla monitu weryfikacyjnego.

   ![Udzielenie uprawnień](media/deploy/client-app-grant-permissions.png)

   Przyznanie uprawnień umożliwia łańcucha bloków Workbench dostęp do użytkowników w katalogu. Uprawnienie Odczyt jest wymagane do wyszukania i dodania członków do łańcucha bloków Workbench.

### <a name="get-application-id"></a>Pobierz identyfikator aplikacji

Identyfikator aplikacji i informacje o dzierżawie są wymagane do wdrożenia. Zbieranie i przechowywanie informacji do użycia podczas wdrażania.

1. W przypadku zarejestrowanej aplikacji wybierz pozycję **Przegląd**.
1. Skopiuj i Zapisz wartość **identyfikatora aplikacji** do późniejszego użycia podczas wdrażania.

    ![Właściwości aplikacji interfejsu API](media/deploy/app-properties.png)

    | Ustawienie do przechowywania  | Użycie we wdrożeniu |
    |------------------|-------------------|
    | Identyfikator aplikacji (klienta) | Azure Active Directory Instalatora > identyfikator aplikacji |

### <a name="get-tenant-domain-name"></a>Pobierz nazwę domeny dzierżawy

Zbierz i Zapisz Active Directory nazwę domeny dzierżawy, w której są zarejestrowane aplikacje.

W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**. Wybierz pozycję **Niestandardowe nazwy domen**. Skopiuj i Zapisz nazwę domeny.

![Nazwa domeny](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Ustawienia użytkownika-gościa

Jeśli masz użytkowników-Gości w dzierżawie usługi Azure AD, postępuj zgodnie z dodatkowymi krokami, aby zapewnić prawidłowe działanie przypisywania i zarządzania przez program łańcucha bloków Workbench.

1. Przełącz swoją dzierżawę usługi Azure AD i wybierz pozycję **Azure Active Directory > ustawienia użytkownika > zarządzanie ustawieniami współpracy zewnętrznej**.
1. Ustawianie **uprawnień użytkownika-gościa jest ograniczone** do **nie**.
    ![Ustawienia współpracy zewnętrznej](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfigurowanie adresu URL odpowiedzi

Po wdrożeniu usługi Azure łańcucha bloków Workbench należy skonfigurować **adres URL odpowiedzi** aplikacji klienta usługi Azure Active Directory (Azure AD) WDROŻONEGO adresu URL sieci Web łańcucha bloków Workbench.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Sprawdź, czy jesteś w dzierżawie, w której zarejestrowano aplikację kliencką usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację kliencką usługi Azure AD, która została zarejestrowana w sekcji wymagania wstępne.
1. Wybierz pozycję **Uwierzytelnianie**.
1. Określ główny adres URL sieci Web wdrożenia usługi Azure łańcucha bloków Workbench, który został pobrany w sekcji [adresu URL sieci Web łańcucha bloków Workbench](#blockchain-workbench-web-url) . Adres URL odpowiedzi jest poprzedzony prefiksem `https://` . Na przykład `https://myblockchain2-7v75.azurewebsites.net`

    ![Adresy URL odpowiedzi uwierzytelniania](media/deploy/configure-reply-url.png)

1. W sekcji **Ustawienia zaawansowane** Sprawdź **tokeny dostępu** i **tokeny identyfikatorów**.

    ![Zaawansowane ustawienia uwierzytelniania](media/deploy/authentication-advanced-settings.png)

1. Wybierz pozycję **Zapisz** , aby zaktualizować rejestrację klienta.

## <a name="remove-a-deployment"></a>Usuwanie wdrożenia

Gdy wdrożenie nie jest już potrzebne, można usunąć wdrożenie, usuwając grupę zasobów łańcucha bloków Workbench.

1. W Azure Portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
1. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usuwanie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

    ![Usuwanie grupy zasobów](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule z pozostałymi krokami wdrożono usługę Azure łańcucha bloków Workbench. Aby dowiedzieć się, jak utworzyć aplikację łańcucha bloków, przejdź do następnej artykułu z artykułem.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji łańcucha bloków w usłudze Azure Blockchain Workbench](create-app.md)
