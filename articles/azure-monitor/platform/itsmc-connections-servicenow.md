---
title: Połącz usługi ServiceNow z łącznik zarządzania usługami IT
description: Ten artykuł zawiera informacje na temat sposobu usługi ServiceNow z łącznik zarządzania usługami IT (ITSMC) w Azure Monitor, aby centralnie monitorować elementy robocze narzędzia ITSM i zarządzać nimi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729662"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Połącz usługi ServiceNow z łącznik zarządzania usługami IT

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania połączenia między wystąpieniem usługi usługi ServiceNow a łącznik zarządzania usługami IT (ITSMC) w Log Analytics, aby centralnie zarządzać elementami roboczymi.

Poniższe sekcje zawierają szczegółowe informacje dotyczące sposobu łączenia produktu usługi ServiceNow z ITSMC na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że zostały spełnione następujące wymagania wstępne:
- ITSMC. Więcej informacji: [Dodawanie rozwiązania łącznik zarządzania usługami IT](./itsmc-definition.md#add-it-service-management-connector).
- Usługi ServiceNow obsługiwane wersje: Orlando, Nowy Jork, Madryt, Londyn, Kingston, Dżakarta, Stambuł, Helsinki, Genewa.
- Dzisiaj alerty wysyłane z Azure Monitor mogą tworzyć w usługi ServiceNow jeden z następujących elementów: zdarzenia, zdarzenia lub alerty.
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
>     1. [Ukończ proces synchronizacji ręcznej dla konfiguracji łącznika narzędzia ITSM](./itsmc-resync-servicenow.md)
>     2. Odwołaj się do starego tokenu odświeżania, ponieważ nie zaleca się zachowywania starych kluczy ze względów bezpieczeństwa. W bloku usługi ServiceNow Wyszukiwanie systemu OAuth w systemie niż SELECT Manage tokens. Wybierz stary token z listy zgodnie z nazwą i datą wygaśnięcia uwierzytelniania OAuth.
> ![Definicja OAuth systemu ŚNIEGu](media/itsmc-connections/snow-system-oauth.png)
>     3. Kliknij pozycję odwołaj dostęp, a nie na odwoływanie.

- Zainstaluj aplikację użytkownika dla integracji z programem Microsoft Log Analytics (aplikacja usługi ServiceNow). [Dowiedz się więcej](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> ITSMC obsługuje tylko oficjalną aplikację użytkownika dla integracji z programem Microsoft Log Analytics, która została pobrana ze sklepu usługi ServiceNow. ITSMC nie obsługują żadnego pozyskiwania kodu na stronie usługi ServiceNow ani aplikacji, która nie jest częścią oficjalnego rozwiązania usługi ServiceNow. 
- Utwórz rolę użytkownika integracji dla zainstalowanej aplikacji użytkownika. Informacje o sposobie tworzenia roli użytkownika integracji są [tutaj](#create-integration-user-role-in-servicenow-app).

## <a name="connection-procedure"></a>**Procedura połączenia**
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

> [!NOTE]
> W usługi ServiceNow obowiązuje limit szybkości dla żądań na godzinę. W celu skonfigurowania limitu Użyj tego ograniczenia przez zdefiniowanie "ograniczanie szybkości interfejsu API REST" w wystąpieniu usługi ServiceNow.

## <a name="create-integration-user-role-in-servicenow-app"></a>Tworzenie roli użytkownika integracji w aplikacji usługi ServiceNow

Użytkownik Poniższa procedura:

1. Odwiedź witrynę [usługi ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) i zainstaluj **aplikację User dla programu usługi ServiceNow i integrację pakietu Microsoft OMS** z wystąpieniem usługi ServiceNow.
   
   >[!NOTE]
   >W ramach trwającego przejścia z Microsoft Operations Management Suite (OMS) do Azure Monitor pakiet OMS jest teraz określany jako Log Analytics.     
2. Po zakończeniu instalacji odwiedź lewy pasek nawigacyjny wystąpienia usługi ServiceNow, Wyszukaj i wybierz pakiet Microsoft OMS Integrator.  
3. Kliknij pozycję **Lista kontrolna instalacji**.

   Stan jest wyświetlany jako  **nieukończony** , Jeśli rola użytkownika nie została jeszcze utworzona.

4. W polach tekstowych obok pozycji **Utwórz użytkownika integracji** wprowadź nazwę użytkownika, który może nawiązać połączenie z ITSMC na platformie Azure.
5. Wprowadź hasło dla tego użytkownika, a następnie kliknij przycisk **OK**.  

> [!NOTE]
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
> Łącznik ITSM może wysyłać zdarzenia do usługi ServiceNow bez żadnych innych modułów zainstalowanych w wystąpieniu usługi ServiceNow. Jeśli używasz modułu EventManagement w wystąpieniu usługi usługi ServiceNow i chcesz utworzyć zdarzenia lub alerty w usługi ServiceNow za pomocą łącznika, Dodaj następujące role do użytkownika integracji:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>Następne kroki

* [Przegląd łącznik ITSM](itsmc-overview.md)
* [Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Rozwiązywanie problemów w łączniku ITSM](./itsmc-resync-servicenow.md)