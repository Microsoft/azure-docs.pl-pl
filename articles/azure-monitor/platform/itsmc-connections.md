---
title: Łącznik zarządzania usługami IT w usłudze Azure Monitor
description: Ten artykuł zawiera informacje dotyczące sposobu łączenia produktów/usług ITSM z łącznikiem zarządzania usługami IT (ITSMC) w usłudze Azure Monitor w celu centralnego monitorowania elementów roboczych ITSM i zarządzania nimi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 0773492c3042a6f8c906aa6ba1bc3c76ea8c0d8f
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870589"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Łączenie produktów/usług ITSM z łącznikiem zarządzania usługami IT
Ten artykuł zawiera informacje dotyczące konfigurowania połączenia między produktem/usługą ITSM a łącznikiem zarządzania usługami IT (ITSMC) w usłudze Log Analytics w celu centralnego zarządzania elementami roboczymi. Aby uzyskać więcej informacji na temat ITSMC, zobacz [Omówienie](../../azure-monitor/platform/itsmc-overview.md).

Obsługiwane są następujące produkty/usługi ITSM. Wybierz produkt, aby wyświetlić szczegółowe informacje o tym, jak podłączyć produkt do ITSMC.

- [Menedżer serwisowy centrum systemu](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance (Provance)](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Łącznik ITSM może łączyć się tylko z wystąpieniami ServiceNow opartymi na chmurze. Lokalne wystąpienia ServiceNow nie są obecnie obsługiwane.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Łączenie programu System Center Service Manager z łącznikiem zarządzania usługami IT na platformie Azure

W poniższych sekcjach przedstawiono szczegółowe informacje na temat podłączania produktu programu System Center Service Manager do programu ITSMC na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że spełnione są następujące wymagania wstępne:

- ITSMC zainstalowany. Więcej informacji: [Dodawanie rozwiązania złącza do zarządzania usługami IT](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Aplikacja sieci Web programu Service Manager (aplikacja sieci Web) jest wdrażana i konfigurowana. Informacje o aplikacji sieci Web znajdują się [tutaj](#create-and-deploy-service-manager-web-app-service).
- Połączenie hybrydowe utworzone i skonfigurowane. Więcej informacji: [Konfigurowanie połączenia hybrydowego](#configure-the-hybrid-connection).
- Obsługiwane wersje programu Service Manager: 2012 R2 lub 2016.
- Rola użytkownika: [Zaawansowany operator](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Procedura połączenia

Aby połączyć wystąpienie programu System Center Service Manager z programem ITSMC, należy użyć następującej procedury:

1. W witrynie Azure portal przejdź do **witryny Wszystkie zasoby** i poszukaj **servicedesku (YourWorkspaceName)**

2.  W **obszarze ŹRÓDŁA DANYCH OBSZARU ROBOCZEGO** kliknij pozycję Połączenia **ITSM**.

    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części prawego okienka kliknij pozycję **Dodaj**.

4. Podaj informacje w sposób opisany w poniższej tabeli i kliknij przycisk **OK,** aby utworzyć połączenie.

> [!NOTE]
> 
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia programu System Center Service Manager, które chcesz połączyć z ITSMC.  Tej nazwy należy użyć później podczas konfigurowania elementów roboczych w tym wystąpieniu/ widoku szczegółowe analizy dziennika. |
| **Typ partnera**   | Wybierz **Menedżera serwisów centrum systemu**. |
| **Adres URL serwera**   | Wpisz adres URL aplikacji sieci Web programu Service Manager. Więcej informacji na temat aplikacji sieci Web programu Service Manager znajduje się [tutaj](#create-and-deploy-service-manager-web-app-service).
| **Client ID (Identyfikator klienta)**   | Wpisz identyfikator klienta wygenerowany (przy użyciu skryptu automatycznego) do uwierzytelniania aplikacji sieci Web. Więcej informacji na temat automatycznego skryptu znajduje się [tutaj.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Client Secret (Wpis tajny klienta)**   | Wpisz klucz tajny klienta, wygenerowany dla tego identyfikatora.   |
| **Synchronizacja danych**   | Wybierz elementy robocze programu Service Manager, które chcesz zsynchronizować za pośrednictwem programu ITSMC.  Te elementy robocze są importowane do usługi Log Analytics. **Opcje:**  Incydenty, żądania zmian.|
| **Zakres synchronizacji danych** | Wpisz liczbę ostatnich dni, od których mają być dane. **Maksymalny limit:** 120 dni. |
| **Tworzenie nowego elementu konfiguracji w rozwiązaniu ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie ITSM. Po wybraniu tej opcji usługa Log Analytics tworzy elementy Ci, których dotyczy problem, jako elementy konfiguracji (w przypadku nieistniejących elementów interfejsu użytkownika) w obsługiwanym systemie ITSM. **Domyślnie**: wyłączone. |

![Połączenie z menedżerem usług](media/itsmc-connections/service-manager-connection.png)

**Po pomyślnym podłączeniu i synchronizacji:**

- Wybrane elementy robocze z programu Service Manager są importowane do usługi Azure **Log Analytics.** Podsumowanie tych elementów pracy można wyświetlić na kafelku łącznika zarządzania usługami IT.

- Zdarzenia można tworzyć z alertów usługi Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu programu Service Manager.


Dowiedz się więcej: [Tworzenie elementów roboczych ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Tworzenie i wdrażanie usługi aplikacji sieci Web programu Service Manager

Aby połączyć lokalnego menedżera usług z ITSMC na platformie Azure, firma Microsoft utworzyła aplikację sieci Web programu Service Manager w witrynie GitHub.

Aby skonfigurować aplikację SIECI Web ITSM dla menedżera usług Service Manager, wykonaj następujące czynności:

- **Wdrażanie aplikacji sieci Web** — wdrażanie aplikacji sieci Web, ustawianie właściwości i uwierzytelnienie za pomocą usługi Azure AD. Aplikację sieci web można wdrożyć przy użyciu [skryptu automatycznego](../../azure-monitor/platform/itsmc-service-manager-script.md) dostarczonego przez firmę Microsoft.
- **Skonfiguruj połączenie** - [hybrydowe Skonfiguruj to połączenie](#configure-the-hybrid-connection)ręcznie.

#### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci Web
Użyj [skryptu](../../azure-monitor/platform/itsmc-service-manager-script.md) automatycznego, aby wdrożyć aplikację sieci Web, ustawić właściwości i uwierzytelnić się za pomocą usługi Azure AD.

Uruchom skrypt, podając następujące wymagane szczegóły:

- Szczegóły subskrypcji platformy Azure
- Nazwa grupy zasobów
- Lokalizacja
- Szczegóły serwera programu Service Manager (nazwa serwera, domena, nazwa użytkownika i hasło)
- Prefiks nazwy witryny aplikacji sieci Web
- Obszar nazw servicebus.

Skrypt tworzy aplikację sieci Web przy użyciu nazwy, która została określona (wraz z kilkoma dodatkowymi ciągami, aby uczynić ją unikatową). Generuje adres **URL aplikacji sieci Web,** **identyfikator klienta** i **klucz tajny klienta**.

Zapisz wartości, użyj ich podczas tworzenia połączenia z ITSMC.

**Sprawdzanie instalacji aplikacji sieci Web**

1. Przejdź do**witryny** **Zasoby portalu** > Azure .
2. Wybierz aplikację sieci Web, kliknij pozycję **Ustawienia** > **ustawień aplikacji**.
3. Potwierdź informacje o wystąpieniu programu Service Manager, które zostały podane podczas wdrażania aplikacji za pośrednictwem skryptu.

### <a name="configure-the-hybrid-connection"></a>Konfigurowanie połączenia hybrydowego

Poniższa procedura służy do konfigurowania połączenia hybrydowego, które łączy wystąpienie programu Service Manager z ITSMC na platformie Azure.

1. Znajdź aplikację sieci Web programu Service Manager w obszarze **Zasoby platformy Azure**.
2. Kliknij **pozycję Ustawienia** > **sieci**.
3. W obszarze **Połączenia hybrydowe**kliknij pozycję **Konfigurowanie punktów końcowych połączenia hybrydowego**.

    ![Sieć połączeń hybrydowych](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. W bloku **Połączenia hybrydowe** kliknij pozycję **Dodaj połączenie hybrydowe**.

    ![Dodanie połączenia hybrydowego](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. W bloku **Dodawanie połączeń hybrydowych** kliknij pozycję **Utwórz nowe połączenie hybrydowe**.

    ![Nowe połączenie hybrydowe](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Wpisz następujące wartości:

   - **Nazwa punktu końcowego**: Określ nazwę nowego połączenia hybrydowego.
   - **Host programu EndPoint:** FQDN serwera zarządzania programu Service Manager.
   - **Port punktu końcowego**: Typ 5724
   - **Obszar nazw servicebus:** Użyj istniejącego obszaru nazw servicebus lub utwórz nowy.
   - **Lokalizacja**: wybierz lokalizację.
   - **Nazwa**: Określ nazwę servicebus, jeśli ją tworzysz.

     ![Wartości połączeń hybrydowych](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kliknij **przycisk OK,** aby zamknąć blok **Utwórz połączenie hybrydowe** i rozpocząć tworzenie połączenia hybrydowego.

    Po utworzeniu połączenia hybrydowego jest on wyświetlany pod blokiem.

7. Po utworzeniu połączenia hybrydowego wybierz połączenie i kliknij przycisk **Dodaj wybrane połączenie hybrydowe**.

    ![Nowe połączenie hybrydowe](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurowanie konfiguracji odbiornika

Poniższa procedura służy do konfigurowania konfiguracji odbiornika dla połączenia hybrydowego.

1. W bloku **Połączenia hybrydowe** kliknij pozycję **Pobierz Menedżera połączeń** i zainstaluj go na komputerze, na którym jest uruchomione wystąpienie programu System Center Service Manager.

    Po zakończeniu instalacji opcja **interfejsu użytkownika Menedżera połączeń hybrydowych** jest dostępna w menu **Start.**

2. Kliknij **pozycję Interfejs użytkownika menedżera połączeń hybrydowych** , zostanie wyświetlony monit o podanie poświadczeń platformy Azure.

3. Zaloguj się przy użyciu poświadczeń platformy Azure i wybierz subskrypcję, w której utworzono połączenie hybrydowe.

4. Kliknij pozycję **Zapisz**.

Połączenie hybrydowe zostanie pomyślnie podłączone.

![udane połączenie hybrydowe](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Po utworzeniu połączenia hybrydowego sprawdź i przetestuj połączenie, odwiedzając wdrożoną aplikację sieci Web programu Service Manager. Upewnij się, że połączenie zakończy się pomyślnie, zanim spróbujesz połączyć się z ITSMC na platformie Azure.

Na poniższym przykładzie przedstawiono szczegóły pomyślnego połączenia:

![Test połączenia hybrydowego](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Łączenie servicenow z łącznikiem zarządzania usługami IT na platformie Azure

W poniższych sekcjach podano szczegółowe informacje na temat podłączania produktu ServiceNow do usługi ITSMC na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że spełnione są następujące wymagania wstępne:
- ITSMC zainstalowany. Więcej informacji: [Dodawanie rozwiązania złącza do zarządzania usługami IT](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Obsługiwane wersje ServiceNow: Nowy Jork, Madryt, Londyn, Kingston, Dżakarta, Stambuł, Helsinki, Genewa.
> [!NOTE]
> ITSMC obsługuje tylko oficjalną ofertę SaaS od Service Now. Prywatne wdrożenia usługi Teraz nie są obsługiwane. 

**Administratorzy ServiceNow muszą wykonać następujące czynności w swoim wystąpieniu ServiceNow:**
- Generowanie identyfikatora klienta i klucza tajnego klienta dla produktu ServiceNow. Aby uzyskać informacje na temat generowania identyfikatora klienta i klucza tajnego, zobacz następujące informacje, zgodnie z wymaganiami:

    - [Konfigurowanie OAuth dla Nowego Jorku](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie OAuth dla Madrytu](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie OAuth dla Londynu](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie OAuth dla firmy Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie OAuth dla Dżakarty](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie OAuth dla Stambułu](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie OAuth dla Helsinek](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Konfigurowanie OAuth dla Genewy](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> W ramach definicji "Set up OAuth" zalecamy:
>
> 1) **Zaktualizuj żywotność tokenu odświeżania do 90 dni (7 776 000 sekund):** Jako część [Set up OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) w fazie 2: [Tworzenie punktu końcowego dla klientów, aby uzyskać dostęp do wystąpienia](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) Po definicji punktu końcowego, w ServiceNow bloku wyszukiwania dla systemu OAuth niż wybierz rejestr aplikacji. Wybierz nazwę OAuth, który został zdefiniowany i zaktualizować pole Odświeżanie tokenu Żywotność do 7,776,000 (90 dni w sekundach).
> Na końcu kliknij przycisk aktualizuj.
> 2) **Zalecamy ustanowienie wewnętrznej procedury w celu zapewnienia, że połączenie pozostanie żywe:** Zgodnie z okresem życia tokenu odświeżania, aby odświeżyć token. Upewnij się, że wykonać następujące operacje przed oczekiwanym czasem wygaśnięcia tokenu odświeżania (kilka dni przed wygaśnięciem okresu ważności tokenu odświeżania, zalecamy):
>
>>  1) [Zakończenie procesu ręcznej synchronizacji konfiguracji złącza ITSM](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-resync-servicenow)
 >> 2) Odwołaj do starego tokenu odświeżania, ponieważ nie zaleca się przechowywania starych kluczy ze względów bezpieczeństwa. W servicenow bloku wyszukiwania systemu OAuth niż wybierz Zarządzania tokenami. Wybierz stary token z listy zgodnie z nazwą OAuth i datą wygaśnięcia. Kliknij odwołać dostęp i niż na Revoke.

- Zainstaluj aplikację użytkownika dla integracji usługi Microsoft Log Analytics (aplikacja ServiceNow). [Dowiedz się więcej](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Utwórz rolę użytkownika integracji dla zainstalowanej aplikacji użytkownika. Informacje na temat tworzenia roli użytkownika integracji znajdują się [tutaj](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Procedura połączenia**
Aby utworzyć połączenie ServiceNow, użyj następującej procedury:


1. W witrynie Azure portal przejdź do **witryny Wszystkie zasoby** i poszukaj **servicedesku (YourWorkspaceName)**

2.  W **obszarze ŹRÓDŁA DANYCH OBSZARU ROBOCZEGO** kliknij pozycję Połączenia **ITSM**.
    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części prawego okienka kliknij pozycję **Dodaj**.

4. Podaj informacje w sposób opisany w poniższej tabeli i kliknij przycisk **OK,** aby utworzyć połączenie.


> [!NOTE]
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia ServiceNow, które chcesz połączyć z ITSMC.  Ta nazwa jest używana później w usłudze Log Analytics podczas konfigurowania elementów roboczych w tym ITSM/ widok szczegółowe analizy dziennika. |
| **Typ partnera**   | Wybierz **ServiceNow**. |
| **Nazwę użytkownika**   | Wpisz nazwę użytkownika integracji utworzoną w aplikacji ServiceNow, aby obsługiwać połączenie z ITSMC. Więcej informacji: [Tworzenie roli użytkownika aplikacji ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Hasło**   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** Nazwa użytkownika i hasło są używane tylko do generowania tokenów uwierzytelniania i nie są przechowywane w dowolnym miejscu w usłudze ITSMC.  |
| **Adres URL serwera**   | Wpisz adres URL wystąpienia ServiceNow, które chcesz połączyć z ITSMC. Adres URL powinien wskazywać obsługiwaną wersję SaaS z sufiksem ".servicenow.com".|
| **Client ID (Identyfikator klienta)**   | Wpisz identyfikator klienta, którego chcesz użyć dla uwierzytelniania OAuth2, który został wygenerowany wcześniej.  Więcej informacji na temat generowania identyfikatora klienta i klucza [tajnego: OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Client Secret (Wpis tajny klienta)**   | Wpisz klucz tajny klienta, wygenerowany dla tego identyfikatora.   |
| **Zakres synchronizacji danych**   | Wybierz elementy robocze ServiceNow, które chcesz zsynchronizować z usługą Azure Log Analytics, za pośrednictwem ITSMC.  Wybrane wartości są importowane do analizy dziennika.   **Opcje:**  Zdarzenia i żądania zmian.|
| **Synchronizacja danych** | Wpisz liczbę ostatnich dni, od których mają być dane. **Maksymalny limit:** 120 dni. |
| **Tworzenie nowego elementu konfiguracji w rozwiązaniu ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie ITSM. Po wybraniu tej opcji ITSMC tworzy zagrożone CI jako elementy konfiguracji (w przypadku nieistniejących wiązek) w obsługiwanym systemie ITSM. **Domyślnie**: wyłączone. |

![Połączenie ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Po pomyślnym podłączeniu i synchronizacji:**

- Wybrane elementy robocze z wystąpienia ServiceNow są importowane do usługi Azure **Log Analytics.** Podsumowanie tych elementów pracy można wyświetlić na kafelku łącznika zarządzania usługami IT.

- Zdarzenia można tworzyć z alertów usługi Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu ServiceNow.

Dowiedz się więcej: [Tworzenie elementów roboczych ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Tworzenie roli użytkownika integracji w aplikacji ServiceNow

Użytkownik następującą procedurę:

1. Odwiedź [sklep ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) i zainstaluj **aplikację użytkownika dla usługi ServiceNow i integracji oms firmy Microsoft** w wystąpieniu ServiceNow.
   
   >[!NOTE]
   >W ramach trwającego przejścia z pakietu Microsoft Operations Management Suite (OMS) do usługi Azure Monitor usługa OMS jest teraz nazywana usługą Log Analytics.     
2. Po instalacji odwiedź lewy pasek nawigacyjny wystąpienia ServiceNow, wyszukaj i wybierz integratora usługi Microsoft OMS.  
3. Kliknij **pozycję Lista kontrolna instalacji**.

   Stan jest wyświetlany jako **Nie ukończone,** jeśli rola użytkownika jest jeszcze do utworzenia.

4. W polach tekstowych obok **pozycji Utwórz użytkownika integracji**wprowadź nazwę użytkownika, który może połączyć się z ITSMC na platformie Azure.
5. Wprowadź hasło dla tego użytkownika i kliknij przycisk **OK**.  

> [!NOTE]
> 
> Te poświadczenia są używane do nawiązywać połączenie ServiceNow na platformie Azure.

Nowo utworzony użytkownik jest wyświetlany z przypisanymi rolami domyślnymi.

**Role domyślne:**
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   Itil
-   template_editor
-   view_changer

Po pomyślnym utworzeniu użytkownika stan **Listy kontrolnej sprawdź instalację** zostanie przeniesiony do zakończonego, zawierając szczegóły roli użytkownika utworzonej dla aplikacji.

> [!NOTE]
> 
> Itsm Connector może wysyłać zdarzenia do ServiceNow bez innych modułów zainstalowanych w wystąpieniu ServiceNow. Jeśli używasz modułu EventManagement w wystąpieniu ServiceNow i chcesz utworzyć zdarzenia lub alerty w ServiceNow przy użyciu łącznika, dodaj następujące role do użytkownika integracji:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Połącz provance z łącznikiem zarządzania usługami IT na platformie Azure

W poniższych sekcjach podano szczegółowe informacje na temat podłączania produktu Provance do usługi ITSMC na platformie Azure.


### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że spełnione są następujące wymagania wstępne:


- ITSMC zainstalowany. Więcej informacji: [Dodawanie rozwiązania złącza do zarządzania usługami IT](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Aplikacja Provance powinna być zarejestrowana w usłudze Azure AD, a identyfikator klienta jest udostępniany. Aby uzyskać szczegółowe informacje, zobacz [jak skonfigurować uwierzytelnianie usługi Active Directory](../../app-service/configure-authentication-provider-aad.md).

- Rola użytkownika: Administrator.

### <a name="connection-procedure"></a>Procedura połączenia

Aby utworzyć połączenie Provance, użyj następującej procedury:

1. W witrynie Azure portal przejdź do **witryny Wszystkie zasoby** i poszukaj **servicedesku (YourWorkspaceName)**

2.  W **obszarze ŹRÓDŁA DANYCH OBSZARU ROBOCZEGO** kliknij pozycję Połączenia **ITSM**.
    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części prawego okienka kliknij pozycję **Dodaj**.

4. Podaj informacje w sposób opisany w poniższej tabeli i kliknij przycisk **OK,** aby utworzyć połączenie.

> [!NOTE]
> 
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia Provance, które chcesz połączyć z ITSMC.  Tej nazwy należy użyć później podczas konfigurowania elementów roboczych w tym ITSM / widok szczegółowe analizy dziennika. |
| **Typ partnera**   | Wybierz **Provance**. |
| **Nazwę użytkownika**   | Wpisz nazwę użytkownika, który może połączyć się z ITSMC.    |
| **Hasło**   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** Nazwa użytkownika i hasło są używane tylko do generowania tokenów uwierzytelniania i nie są przechowywane w dowolnym miejscu w usłudze ITSMC._|
| **Adres URL serwera**   | Wpisz adres URL wystąpienia Provance, które chcesz połączyć z ITSMC. |
| **Client ID (Identyfikator klienta)**   | Wpisz identyfikator klienta do uwierzytelniania tego połączenia, które zostały wygenerowane w wystąpieniu Provance.  Więcej informacji na temat identyfikatora klienta można znaleźć [w temacie konfigurowanie uwierzytelniania usługi Active Directory](../../app-service/configure-authentication-provider-aad.md). |
| **Zakres synchronizacji danych**   | Wybierz elementy robocze Provance, które chcesz zsynchronizować z usługą Azure Log Analytics, za pośrednictwem ITSMC.  Te elementy robocze są importowane do analizy dziennika.   **Opcje:**   Incydenty, żądania zmian.|
| **Synchronizacja danych** | Wpisz liczbę ostatnich dni, od których mają być dane. **Maksymalny limit:** 120 dni. |
| **Tworzenie nowego elementu konfiguracji w rozwiązaniu ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie ITSM. Po wybraniu tej opcji ITSMC tworzy zagrożone CI jako elementy konfiguracji (w przypadku nieistniejących wiązek) w obsługiwanym systemie ITSM. **Domyślnie**: wyłączone.|

![Połączenie Provance](media/itsmc-connections/itsm-connections-provance-latest.png)

**Po pomyślnym podłączeniu i synchronizacji:**

- Wybrane elementy robocze z tego wystąpienia Provance są importowane do usługi Azure **Log Analytics.** Podsumowanie tych elementów pracy można wyświetlić na kafelku łącznika zarządzania usługami IT.

- Zdarzenia można tworzyć z alertów usługi Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu Provance.

Dowiedz się więcej: [Tworzenie elementów roboczych ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Łączenie programu Cherwell z łącznikiem zarządzania usługami IT na platformie Azure

W poniższych sekcjach podano szczegółowe informacje na temat podłączania produktu Cherwell do usługi ITSMC na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że spełnione są następujące wymagania wstępne:

- ITSMC zainstalowany. Więcej informacji: [Dodawanie rozwiązania złącza do zarządzania usługami IT](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Wygenerowany identyfikator klienta. Więcej informacji: [Generowanie identyfikatora klienta dla Cherwell](#generate-client-id-for-cherwell).
- Rola użytkownika: Administrator.

### <a name="connection-procedure"></a>Procedura połączenia

Aby utworzyć połączenie Provance, użyj następującej procedury:

1. W witrynie Azure portal przejdź do **witryny Wszystkie zasoby** i poszukaj **servicedesku (YourWorkspaceName)**

2.  W **obszarze ŹRÓDŁA DANYCH OBSZARU ROBOCZEGO** kliknij pozycję Połączenia **ITSM**.
    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części prawego okienka kliknij pozycję **Dodaj**.

4. Podaj informacje w sposób opisany w poniższej tabeli i kliknij przycisk **OK,** aby utworzyć połączenie.

> [!NOTE]
> 
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia Cherwell, które chcesz połączyć z ITSMC.  Tej nazwy należy użyć później podczas konfigurowania elementów roboczych w tym ITSM / widok szczegółowe analizy dziennika. |
| **Typ partnera**   | Wybierz **Cherwell.** |
| **Nazwę użytkownika**   | Wpisz nazwę użytkownika Cherwell, która może połączyć się z ITSMC. |
| **Hasło**   | Wpisz hasło skojarzone z tą nazwą użytkownika. **Uwaga:** Nazwa użytkownika i hasło są używane tylko do generowania tokenów uwierzytelniania i nie są przechowywane w dowolnym miejscu w usłudze ITSMC.|
| **Adres URL serwera**   | Wpisz adres URL wystąpienia Cherwell, które chcesz połączyć z ITSMC. |
| **Client ID (Identyfikator klienta)**   | Wpisz identyfikator klienta do uwierzytelniania tego połączenia, które zostały wygenerowane w wystąpieniu Cherwell.   |
| **Zakres synchronizacji danych**   | Wybierz elementy robocze Cherwell, które chcesz zsynchronizować za pośrednictwem ITSMC.  Te elementy robocze są importowane do analizy dziennika.   **Opcje:**  Incydenty, żądania zmian. |
| **Synchronizacja danych** | Wpisz liczbę ostatnich dni, od których mają być dane. **Maksymalny limit:** 120 dni. |
| **Tworzenie nowego elementu konfiguracji w rozwiązaniu ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie ITSM. Po wybraniu tej opcji ITSMC tworzy zagrożone CI jako elementy konfiguracji (w przypadku nieistniejących wiązek) w obsługiwanym systemie ITSM. **Domyślnie**: wyłączone. |


![Połączenie Provance](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Po pomyślnym podłączeniu i synchronizacji:**

- Wybrane elementy robocze z tego wystąpienia Cherwell są importowane do usługi Azure **Log Analytics.** Podsumowanie tych elementów pracy można wyświetlić na kafelku łącznika zarządzania usługami IT.

- Zdarzenia można tworzyć z alertów usługi Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu Cherwell.

Dowiedz się więcej: [Tworzenie elementów roboczych ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generowanie identyfikatora klienta dla Cherwell

Aby wygenerować identyfikator/klucz klienta dla Cherwell, należy użyć następującej procedury:

1. Zaloguj się do instancji Cherwell jako administrator.
2. Kliknij pozycję Ustawienia**klienta interfejsu API edycji** **zabezpieczeń** > REST .
3. Wybierz **pozycję Utwórz nowy** > **klucz tajny klienta**.

    ![Identyfikator użytkownika Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Następne kroki
 - [Tworzenie elementów roboczych ITSM z alertów platformy Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
