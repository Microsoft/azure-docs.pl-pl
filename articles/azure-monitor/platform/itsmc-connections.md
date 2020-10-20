---
title: łącznik zarządzania usługami IT w Azure Monitor
description: Ten artykuł zawiera informacje o sposobach łączenia narzędzia ITSM produktów/usług z usługą łącznik zarządzania usługami IT (ITSMC) w Azure Monitor, aby centralnie monitorować elementy robocze narzędzia ITSM i zarządzać nimi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: b2b8bed9233073e629ee3bce2630fd373c87663b
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215879"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Łączenie produktów/usług ITSM za pomocą łącznika zarządzania usługami IT
Ten artykuł zawiera informacje dotyczące sposobu konfigurowania połączenia między produktem narzędzia ITSM/usługą a łącznik zarządzania usługami IT (ITSMC) w Log Analytics, aby centralnie zarządzać elementami roboczymi. Aby uzyskać więcej informacji na temat ITSMC, zobacz [Omówienie](./itsmc-overview.md).

Obsługiwane są następujące produkty/usługi narzędzia ITSM. Wybierz produkt, aby wyświetlić szczegółowe informacje na temat sposobu łączenia produktu z programem ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Od 1 do 2020 Cherwell i Provance narzędzia ITSM integracji z usługą Azure alert nie będą już dostępne dla nowych klientów. Nowe połączenia narzędzia ITSM nie będą obsługiwane. 
> Istniejące połączenia narzędzia ITSM będą obsługiwane.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Łączenie System Center Service Manager z łącznik zarządzania usługami IT na platformie Azure

W poniższych sekcjach znajdują się szczegółowe informacje dotyczące sposobu łączenia produktu System Center Service Manager na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

- ITSMC. Więcej informacji: [Dodawanie rozwiązania łącznik zarządzania usługami IT](./itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Aplikacja sieci Web Service Manager (aplikacja sieci Web) została wdrożona i skonfigurowana. [Tutaj znajdziesz](#create-and-deploy-service-manager-web-app-service)informacje o aplikacji sieci Web.
- Utworzono i skonfigurowano połączenie hybrydowe. Więcej informacji: [Skonfiguruj połączenie hybrydowe](#configure-the-hybrid-connection).
- Obsługiwane wersje Service Manager: 2012 R2 lub 2016.
- Rola użytkownika:  [Zaawansowany operator](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).

> [!NOTE]
> 
> - Łącznik ITSM może łączyć się tylko z wystąpieniami usługi ServiceNow opartymi na chmurze. Lokalne wystąpienia usługi ServiceNow nie są obecnie obsługiwane.
> - Aby można było używać szablonów niestandardowych jako części akcji, parametr "projekcja" w SCSM powinien być zamapowany na "IncidentManagement! System. robocze. incydent. projekcja "

### <a name="connection-procedure"></a>Procedura połączenia

Aby połączyć wystąpienie System Center Service Manager z usługą ITSMC, wykonaj czynności opisane w poniższej procedurze:

1. W Azure Portal przejdź do pozycji **wszystkie zasoby** i Znajdź pozycję **Servicedesk (YourWorkspaceName).**

2.  W obszarze **źródła danych obszaru roboczego** kliknij pozycję **połączenia narzędzia ITSM**.

    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części okienka po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** , aby utworzyć połączenie.

> [!NOTE]
> 
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia System Center Service Manager, które chcesz połączyć z ITSMC.  Ta nazwa jest używana później podczas konfigurowania elementów roboczych w tym wystąpieniu/wyświetlania szczegółowej usługi log Analytics. |
| **Typ partnera**   | Wybierz **System Center Service Manager**. |
| **Adres URL serwera**   | Wpisz adres URL aplikacji sieci Web Service Manager. Więcej informacji na temat Service Manager aplikacji sieci Web znajduje się w [tym miejscu](#create-and-deploy-service-manager-web-app-service).
| **Identyfikator klienta**   | Wpisz wygenerowany przez siebie identyfikator klienta (przy użyciu skryptu automatycznego) do uwierzytelniania aplikacji sieci Web. Więcej informacji o zautomatyzowanym skrypcie znajduje się [tutaj.](./itsmc-service-manager-script.md)|
| **Klucz tajny klienta**   | Wpisz klucz tajny klienta wygenerowany dla tego identyfikatora.   |
| **Synchronizuj dane**   | Wybierz Service Manager elementy robocze, które chcesz synchronizować za pomocą ITSMC.  Te elementy robocze są importowane do Log Analytics. **Opcje:**  Zdarzenia, żądania zmiany.|
| **Zakres synchronizacji danych** | Wpisz liczbę dni, z których dane mają być używane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie narzędzia ITSM. Po wybraniu Log Analytics tworzy elementy konfiguracji, których to dotyczy, w ramach obsługiwanego systemu narzędzia ITSM. **Domyślnie**: wyłączone. |

![Połączenie programu Service Manager](media/itsmc-connections/service-manager-connection.png)

**Po pomyślnym nawiązaniu połączenia i zsynchronizowaniu**:

- Wybrane elementy robocze z Service Manager są importowane do usługi Azure **log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznik zarządzania usługami IT.

- Możesz tworzyć zdarzenia z alertów Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu Service Manager.


Dowiedz się więcej: [Utwórz narzędzia ITSM elementy robocze z alertów platformy Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Tworzenie i wdrażanie usługi Service Manager Web App Service

Aby połączyć Service Manager lokalnego z usługą ITSMC na platformie Azure, firma Microsoft stworzyła Service Manager aplikację sieci Web w witrynie GitHub.

Aby skonfigurować aplikację sieci Web narzędzia ITSM dla Service Manager, wykonaj następujące czynności:

- **Wdróż aplikację internetową** — Wdróż aplikację sieci Web, ustaw właściwości i Uwierzytelnij się z usługą Azure AD. Aplikację sieci Web można wdrożyć za pomocą [zautomatyzowanego skryptu](./itsmc-service-manager-script.md) dostarczonego przez firmę Microsoft.
- **Skonfiguruj połączenie**  -  hybrydowe [Skonfiguruj to połączenie](#configure-the-hybrid-connection)ręcznie.

#### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci Web
Użyj [skryptu](./itsmc-service-manager-script.md) automatycznego do wdrożenia aplikacji sieci Web, ustawienia właściwości i uwierzytelniania za pomocą usługi Azure AD.

Uruchom skrypt, podając następujące wymagane szczegóły:

- Szczegóły subskrypcji platformy Azure
- Nazwa grupy zasobów
- Lokalizacja
- Szczegóły serwera Service Manager (nazwa serwera, domena, nazwa użytkownika i hasło)
- Prefiks nazwy witryny dla aplikacji sieci Web
- ServiceBus przestrzeń nazw.

Skrypt tworzy aplikację internetową przy użyciu podanej nazwy (wraz z kilkoma dodatkowymi ciągami, aby była unikatowa). Generuje **adres URL aplikacji sieci Web**, **Identyfikator klienta** i **klucz tajny klienta**.

Zapisz wartości, korzystając z nich podczas tworzenia połączenia z ITSMC.

**Sprawdzanie instalacji aplikacji sieci Web**

1. Przejdź do **Azure portal**  >  **zasobów**Azure Portal.
2. Wybierz aplikację sieci Web, kliknij pozycję **Ustawienia**  >  **Ustawienia aplikacji**.
3. Potwierdź informacje dotyczące wystąpienia Service Manager podanego w czasie wdrażania aplikacji za pomocą skryptu.

### <a name="configure-the-hybrid-connection"></a>Skonfiguruj połączenie hybrydowe

Użyj poniższej procedury, aby skonfigurować połączenie hybrydowe łączące wystąpienie Service Manager z usługą ITSMC na platformie Azure.

1. Znajdź aplikację sieci Web Service Manager w obszarze **zasoby platformy Azure**.
2. Kliknij pozycję **Ustawienia**  >  **Sieć**.
3. W obszarze **połączenia hybrydowe**kliknij pozycję **Skonfiguruj punkty końcowe połączenia hybrydowego**.

    ![Sieci połączeń hybrydowych](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. W bloku **połączenia hybrydowe** kliknij pozycję **Dodaj połączenie hybrydowe**.

    ![Dodawanie połączenia hybrydowego](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. W bloku **dodaj połączenia hybrydowe** kliknij pozycję **Utwórz nowe połączenie hybrydowe**.

    ![Nowe połączenie hybrydowe](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Wpisz następujące wartości:

   - **Nazwa punktu końcowego**: Określ nazwę nowego połączenia hybrydowego.
   - **Host punktu końcowego**: nazwa FQDN serwera zarządzania Service Manager.
   - **Port punktu końcowego**: typ 5724
   - **ServiceBus — przestrzeń nazw**: Użyj istniejącej przestrzeni nazw ServiceBus lub Utwórz nową.
   - **Lokalizacja**: Wybierz lokalizację.
   - **Nazwa**: Określ nazwę ServiceBus, jeśli tworzysz ją.

     ![Wartości połączenia hybrydowego](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kliknij przycisk **OK** , aby zamknąć blok **Tworzenie połączenia hybrydowego** i rozpocząć tworzenie połączenia hybrydowego.

    Po utworzeniu połączenia hybrydowego zostanie ono wyświetlone w bloku.

7. Po utworzeniu połączenia hybrydowego wybierz połączenie, a następnie kliknij przycisk **Dodaj wybrane połączenie hybrydowe**.

    ![Nowe połączenie hybrydowe](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Skonfiguruj konfigurację odbiornika

Aby skonfigurować konfigurację odbiornika dla połączenia hybrydowego, należy wykonać czynności opisane w poniższej procedurze.

1. W bloku **połączenia hybrydowe** kliknij pozycję **Pobierz Menedżera połączeń** i zainstaluj go na komputerze, na którym jest uruchomione wystąpienie System Center Service Manager.

    Po zakończeniu instalacji **Menedżer połączeń hybrydowych opcja interfejsu użytkownika** jest dostępna w menu **Start** .

2. Kliknij przycisk **Menedżer połączeń hybrydowych interfejs użytkownika** , zostanie wyświetlony monit o podanie poświadczeń platformy Azure.

3. Zaloguj się przy użyciu poświadczeń platformy Azure i wybierz subskrypcję, w której utworzono połączenie hybrydowe.

4. Kliknij pozycję **Zapisz**.

Połączenie hybrydowe zostało pomyślnie nawiązane.

![pomyślne połączenie hybrydowe](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Po utworzeniu połączenia hybrydowego Sprawdź i przetestuj połączenie, odwiedzając wdrożoną aplikację sieci Web Service Manager. Upewnij się, że połączenie zostało nawiązane pomyślnie, zanim spróbujesz połączyć się z usługą ITSMC na platformie Azure.

Poniższy przykładowy obraz przedstawia szczegóły pomyślnego połączenia:

![Test połączenia hybrydowego](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Łączenie usługi ServiceNow łącznik zarządzania usługami IT z platformą Azure

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu łączenia produktu usługi ServiceNow z ITSMC na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że zostały spełnione następujące wymagania wstępne:
- ITSMC. Więcej informacji: [Dodawanie rozwiązania łącznik zarządzania usługami IT](./itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Usługi ServiceNow obsługiwane wersje: Orlando, Nowy Jork, Madryt, Londyn, Kingston, Dżakarta, Stambuł, Helsinki, Genewa.
> [!NOTE]
> ITSMC obsługuje teraz tylko oficjalną ofertę SaaS z usługi. Prywatne wdrożenia usługi są teraz nieobsługiwane. 

**Administratorzy usługi ServiceNow muszą wykonać następujące czynności w ich wystąpieniu usługi ServiceNow**:
- Generuj identyfikator klienta i klucz tajny klienta dla produktu usługi ServiceNow. Aby uzyskać informacje na temat sposobu generowania identyfikatora klienta i wpisu tajnego, zobacz następujące informacje zgodnie z wymaganiami:

    - [Konfigurowanie protokołu OAuth dla Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie protokołu OAuth dla Nowego Jorku](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie protokołu OAuth dla usługi Madryt](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie protokołu OAuth dla Londyn](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie protokołu OAuth dla Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie uwierzytelniania OAuth dla Dżakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie uwierzytelniania OAuth dla programu Stambuł](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie protokołu OAuth dla usługi Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie protokołu OAuth dla usługi Genewa](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> W ramach definicji "Konfigurowanie protokołu OAuth" zalecamy:
>
> 1) **Aktualizowanie tokenu odświeżania cykl życia do 90 dni (7 776 000 s):** Jako część [konfiguracji uwierzytelniania OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) w fazie 2: [Utwórz punkt końcowy dla klientów, aby uzyskać dostęp do tego wystąpienia](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) po definicji punktu końcowego, w bloku usługi ServiceNow Wyszukiwanie systemu OAuth od wybrania rejestru aplikacji. Wybierz nazwę uwierzytelniania OAuth, która została zdefiniowana, i zaktualizuj pole cykl życia tokenu odświeżania do 7 776 000 (90 dni w sekundach).
> Na koniec kliknij przycisk Aktualizuj.
> 2) **Zalecamy ustanowienie wewnętrznej procedury w celu upewnienia się, że połączenie pozostanie aktywne:** Zgodnie z cykl życia tokenem odświeżania, aby odświeżyć token. Upewnij się, że po upływie limitu czasu wygaśnięcia token odświeżenia (kilka dni przed wygaśnięciem tokenu odświeżania cykl życia), należy wykonać następujące operacje:
>
> 1. [Ukończ proces synchronizacji ręcznej dla konfiguracji łącznika narzędzia ITSM](./itsmc-resync-servicenow.md)
> 2. Odwołaj się do starego tokenu odświeżania, ponieważ nie zaleca się zachowywania starych kluczy ze względów bezpieczeństwa. W bloku usługi ServiceNow Wyszukiwanie systemu OAuth w systemie niż SELECT Manage tokens. Wybierz stary token z listy zgodnie z nazwą i datą wygaśnięcia uwierzytelniania OAuth.
> ![Definicja OAuth systemu ŚNIEGu](media/itsmc-connections/snow-system-oauth.png)
> 3. Kliknij pozycję odwołaj dostęp, a nie na odwoływanie.

- Zainstaluj aplikację użytkownika dla integracji z programem Microsoft Log Analytics (aplikacja usługi ServiceNow). [Dowiedz się więcej](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Utwórz rolę użytkownika integracji dla zainstalowanej aplikacji użytkownika. Informacje o sposobie tworzenia roli użytkownika integracji są [tutaj](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Procedura połączenia**
Aby utworzyć połączenie usługi ServiceNow, wykonaj czynności opisane w poniższej procedurze:


1. W Azure Portal przejdź do pozycji **wszystkie zasoby** i Znajdź pozycję **Servicedesk (YourWorkspaceName).**

2.  W obszarze **źródła danych obszaru roboczego** kliknij pozycję **połączenia narzędzia ITSM**.
    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części okienka po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** , aby utworzyć połączenie.


> [!NOTE]
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia usługi ServiceNow, które chcesz połączyć z ITSMC.  Tej nazwy można użyć później w Log Analytics podczas konfigurowania elementów roboczych w tym narzędzia ITSM/widoku szczegółowej analizy dzienników. |
| **Typ partnera**   | Wybierz pozycję **usługi ServiceNow**. |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika integracji utworzoną w aplikacji usługi ServiceNow, aby umożliwić obsługę połączenia z usługą ITSMC. Więcej informacji: [Utwórz rolę użytkownika aplikacji usługi ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Password** (Hasło)   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga**: Nazwa użytkownika i hasło są używane do generowania tokenów uwierzytelniania i nie są przechowywane w żadnym miejscu w ramach usługi ITSMC.  |
| **Adres URL serwera**   | Wpisz adres URL wystąpienia usługi ServiceNow, które chcesz połączyć z ITSMC. Adres URL powinien wskazywać obsługiwaną wersję SaaS z sufiksem ". servicenow.com".|
| **Identyfikator klienta**   | Wpisz identyfikator klienta, który ma być używany na potrzeby uwierzytelniania OAuth2 wygenerowanego wcześniej.  Więcej informacji na temat generowania identyfikatora klienta i klucza tajnego:   [konfiguracja uwierzytelniania OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Klucz tajny klienta**   | Wpisz klucz tajny klienta wygenerowany dla tego identyfikatora.   |
| **Zakres synchronizacji danych**   | Wybierz usługi ServiceNow elementy robocze, które chcesz synchronizować z usługą Azure Log Analytics przy użyciu ITSMC.  Wybrane wartości są importowane do usługi log Analytics.   **Opcje:**  Zdarzenia i żądania zmiany.|
| **Synchronizuj dane** | Wpisz liczbę dni, z których dane mają być używane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie narzędzia ITSM. W przypadku wybrania tej pozycji ITSMC tworzy odpowiednie usługi CIs jako elementy konfiguracji (w przypadku nieistniejących CIs) w obsługiwanym systemie narzędzia ITSM. **Domyślnie**: wyłączone. |

![Połączenie usługi ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Po pomyślnym nawiązaniu połączenia i zsynchronizowaniu**:

- Wybrane elementy robocze z wystąpienia usługi ServiceNow są importowane do usługi Azure **log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznik zarządzania usługami IT.

- Możesz tworzyć zdarzenia z alertów Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu usługi usługi ServiceNow.

Dowiedz się więcej: [Utwórz narzędzia ITSM elementy robocze z alertów platformy Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).


> [!NOTE]
> W usługi ServiceNow obowiązuje limit szybkości dla żądań na godzinę. W celu skonfigurowania limitu Użyj tego ograniczenia przez zdefiniowanie "ograniczanie szybkości interfejsu API REST" w wystąpieniu usługi ServiceNow.

### <a name="create-integration-user-role-in-servicenow-app"></a>Tworzenie roli użytkownika integracji w aplikacji usługi ServiceNow

Użytkownik Poniższa procedura:

1. Odwiedź witrynę [usługi ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) i zainstaluj **aplikację User dla programu usługi ServiceNow i integrację pakietu Microsoft OMS** z wystąpieniem usługi ServiceNow.
   
   >[!NOTE]
   >W ramach trwającego przejścia z Microsoft Operations Management Suite (OMS) do Azure Monitor pakiet OMS jest teraz określany jako Log Analytics.     
2. Po zakończeniu instalacji odwiedź lewy pasek nawigacyjny wystąpienia usługi ServiceNow, Wyszukaj i wybierz pakiet Microsoft OMS Integrator.  
3. Kliknij pozycję **Lista kontrolna instalacji**.

   Stan jest wyświetlany jako  **nieukończony** , Jeśli rola użytkownika nie została jeszcze utworzona.

4. W polach tekstowych obok pozycji **Utwórz użytkownika integracji**wprowadź nazwę użytkownika, który może nawiązać połączenie z ITSMC na platformie Azure.
5. Wprowadź hasło dla tego użytkownika, a następnie kliknij przycisk **OK**.  

> [!NOTE]
> 
> Te poświadczenia służą do nawiązywania połączenia usługi ServiceNow na platformie Azure.

Nowo utworzony użytkownik zostanie wyświetlony z przypisanymi domyślnymi rolami.

**Role domyślne**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft. User
-   ITIL
-   template_editor
-   view_changer

Po pomyślnym utworzeniu użytkownika stan **sprawdzenia listy kontrolnej instalacji** zostanie przesunięty do zakończono i zostanie wyświetlona lista szczegółów roli użytkownika utworzonej dla aplikacji.

> [!NOTE]
> 
> Łącznik ITSM może wysyłać zdarzenia do usługi ServiceNow bez żadnych innych modułów zainstalowanych w wystąpieniu usługi ServiceNow. Jeśli używasz modułu EventManagement w wystąpieniu usługi usługi ServiceNow i chcesz utworzyć zdarzenia lub alerty w usługi ServiceNow za pomocą łącznika, Dodaj następujące role do użytkownika integracji:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Łączenie Provance łącznik zarządzania usługami IT z platformą Azure

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu łączenia produktu Provance z ITSMC na platformie Azure.

> [!NOTE]
> 
> Od 1 do 2020 Provance narzędzia ITSM integracja z usługą Azure alert nie zostanie już włączona dla nowych klientów. Nowe połączenia narzędzia ITSM nie będą obsługiwane. 
> Istniejące połączenia narzędzia ITSM będą obsługiwane.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:


- ITSMC. Więcej informacji: [Dodawanie rozwiązania łącznik zarządzania usługami IT](./itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Aplikacja Provance powinna być zarejestrowana w usłudze Azure AD — jest dostępny identyfikator klienta. Aby uzyskać szczegółowe informacje, zobacz [How to configure Active Directory Authentication](../../app-service/configure-authentication-provider-aad.md).

- Rola użytkownika: administrator.

### <a name="connection-procedure"></a>Procedura połączenia

Aby utworzyć połączenie Provance, wykonaj czynności opisane w poniższej procedurze:

1. W Azure Portal przejdź do pozycji **wszystkie zasoby** i Znajdź pozycję **Servicedesk (YourWorkspaceName).**

2.  W obszarze **źródła danych obszaru roboczego** kliknij pozycję **połączenia narzędzia ITSM**.
    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części okienka po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** , aby utworzyć połączenie.

> [!NOTE]
> 
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia Provance, które chcesz połączyć z ITSMC.  Ta nazwa jest używana później podczas konfigurowania elementów roboczych w tym narzędzia ITSM/widoku szczegółowej analizy dzienników. |
| **Typ partnera**   | Wybierz pozycję **Provance**. |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika, która może nawiązać połączenie z usługą ITSMC.    |
| **Password** (Hasło)   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** Nazwa użytkownika i hasło są używane do generowania tokenów uwierzytelniania i nie są przechowywane w żadnym miejscu ITSMC service._|
| **Adres URL serwera**   | Wpisz adres URL wystąpienia Provance, które chcesz połączyć z ITSMC. |
| **Identyfikator klienta**   | Wpisz identyfikator klienta, aby uwierzytelnić to połączenie, które zostało wygenerowane w wystąpieniu usługi Provance.  Więcej informacji na temat identyfikatora klienta można znaleźć w temacie [How to configure Active Directory Authentication](../../app-service/configure-authentication-provider-aad.md). |
| **Zakres synchronizacji danych**   | Wybierz Provance elementy robocze, które chcesz synchronizować z platformą Azure Log Analytics, za pomocą ITSMC.  Te elementy robocze są importowane do usługi log Analytics.   **Opcje:**   Zdarzenia, żądania zmiany.|
| **Synchronizuj dane** | Wpisz liczbę dni, z których dane mają być używane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie narzędzia ITSM. W przypadku wybrania tej pozycji ITSMC tworzy odpowiednie usługi CIs jako elementy konfiguracji (w przypadku nieistniejących CIs) w obsługiwanym systemie narzędzia ITSM. **Domyślnie**: wyłączone.|

![Połączenie Provance](media/itsmc-connections/itsm-connections-provance-latest.png)

**Po pomyślnym nawiązaniu połączenia i zsynchronizowaniu**:

- Wybrane elementy robocze z tego wystąpienia Provance są importowane do usługi Azure **log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznik zarządzania usługami IT.

- Możesz tworzyć zdarzenia z alertów Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu usługi Provance.

Dowiedz się więcej: [Utwórz narzędzia ITSM elementy robocze z alertów platformy Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Łączenie Cherwell łącznik zarządzania usługami IT z platformą Azure

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu łączenia produktu Cherwell z ITSMC na platformie Azure.

> [!NOTE]
> 
> Od 1 do 2020 Cherwell narzędzia ITSM integracja z usługą Azure alert nie zostanie już włączona dla nowych klientów. Nowe połączenia narzędzia ITSM nie będą obsługiwane. 
> Istniejące połączenia narzędzia ITSM będą obsługiwane.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

- ITSMC. Więcej informacji: [Dodawanie rozwiązania łącznik zarządzania usługami IT](./itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Wygenerowano identyfikator klienta. Więcej informacji: [generowanie identyfikatora klienta dla Cherwell](#generate-client-id-for-cherwell).
- Rola użytkownika: administrator.

### <a name="connection-procedure"></a>Procedura połączenia

Aby utworzyć połączenie Cherwell, wykonaj czynności opisane w poniższej procedurze:

1. W Azure Portal przejdź do pozycji **wszystkie zasoby** i Znajdź pozycję **Servicedesk (YourWorkspaceName).**

2.  W obszarze **źródła danych obszaru roboczego** kliknij pozycję **połączenia narzędzia ITSM**.
    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części okienka po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** , aby utworzyć połączenie.

> [!NOTE]
> 
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia Cherwell, które chcesz połączyć z ITSMC.  Ta nazwa jest używana później podczas konfigurowania elementów roboczych w tym narzędzia ITSM/widoku szczegółowej analizy dzienników. |
| **Typ partnera**   | Wybierz pozycję **Cherwell.** |
| **Nazwa użytkownika**   | Wpisz nazwę użytkownika Cherwell, która może nawiązać połączenie z ITSMC. |
| **Password** (Hasło)   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** Nazwa użytkownika i hasło są używane do generowania tokenów uwierzytelniania i nie są przechowywane w żadnym miejscu w ramach usługi ITSMC.|
| **Adres URL serwera**   | Wpisz adres URL wystąpienia Cherwell, które chcesz połączyć z ITSMC. |
| **Identyfikator klienta**   | Wpisz identyfikator klienta, aby uwierzytelnić to połączenie, które zostało wygenerowane w wystąpieniu usługi Cherwell.   |
| **Zakres synchronizacji danych**   | Wybierz Cherwell elementy robocze, które chcesz synchronizować za pomocą ITSMC.  Te elementy robocze są importowane do usługi log Analytics.   **Opcje:**  Zdarzenia, żądania zmiany. |
| **Synchronizuj dane** | Wpisz liczbę dni, z których dane mają być używane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie narzędzia ITSM. W przypadku wybrania tej pozycji ITSMC tworzy odpowiednie usługi CIs jako elementy konfiguracji (w przypadku nieistniejących CIs) w obsługiwanym systemie narzędzia ITSM. **Domyślnie**: wyłączone. |


![Połączenie Provance](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Po pomyślnym nawiązaniu połączenia i zsynchronizowaniu**:

- Wybrane elementy robocze z tego wystąpienia Cherwell są importowane do usługi Azure **log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznik zarządzania usługami IT.

- Możesz tworzyć zdarzenia z alertów Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu usługi Cherwell.

Dowiedz się więcej: [Utwórz narzędzia ITSM elementy robocze z alertów platformy Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generuj identyfikator klienta dla Cherwell

Aby wygenerować identyfikator klienta/klucz dla Cherwell, należy wykonać poniższą procedurę:

1. Zaloguj się do wystąpienia Cherwell jako administrator.
2. Kliknij pozycję Edytuj **zabezpieczenia**  >  **Ustawienia klienta interfejsu API REST**.
3. Wybierz pozycję **Utwórz nowy**  >  **klucz tajny klienta**.

    ![Identyfikator użytkownika Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Następne kroki
 - [Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure](./itsmc-overview.md#create-itsm-work-items-from-azure-alerts)

