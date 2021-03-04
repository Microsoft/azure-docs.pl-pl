---
title: Połącz usługi ServiceNow z łącznik zarządzania usługami IT
description: Dowiedz się, jak połączyć usługi ServiceNow z łącznik zarządzania usługami IT (ITSMC) w Azure Monitor, aby centralnie monitorować elementy robocze narzędzia ITSM i zarządzać nimi.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 51d16ae53e34dacfc41e3d31b01115f9c55ca9f7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039550"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Połącz usługi ServiceNow z łącznik zarządzania usługami IT

W tym artykule opisano sposób konfigurowania połączenia między wystąpieniem usługi ServiceNow i łącznik zarządzania usługami IT (ITSMC) w Log Analytics, dzięki czemu można centralnie zarządzać elementami roboczymi usługi IT Service Management (narzędzia ITSM).

## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że dla połączenia zostały spełnione następujące wymagania wstępne.

### <a name="itsmc-installation"></a>Instalacja ITSMC

Informacje o instalowaniu ITSMC można znaleźć [w temacie Dodawanie rozwiązania łącznik zarządzania usługami IT](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> ITSMC obsługuje tylko oficjalną ofertę oprogramowania jako usługi (SaaS) z usługi ServiceNow. Prywatne wdrożenia usługi ServiceNow nie są obsługiwane.

### <a name="oauth-setup"></a>Konfiguracja protokołu OAuth

Obsługiwane są usługi ServiceNow wersje: Paryż, Orlando, Nowy Jork, Madryt, Londyn, Kingston, Dżakarta, Stambuł, Helsinki i Genewa.

Administratorzy usługi ServiceNow muszą generować identyfikator klienta i klucz tajny klienta dla swojego wystąpienia usługi usługi ServiceNow. W razie potrzeby zobacz następujące informacje:

- [Konfigurowanie protokołu OAuth dla paryskiego](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurowanie protokołu OAuth dla Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurowanie protokołu OAuth dla Nowego Jorku](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurowanie protokołu OAuth dla usługi Madryt](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurowanie protokołu OAuth dla Londyn](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurowanie protokołu OAuth dla Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurowanie uwierzytelniania OAuth dla Dżakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurowanie uwierzytelniania OAuth dla programu Stambuł](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurowanie protokołu OAuth dla usługi Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Konfigurowanie protokołu OAuth dla usługi Genewa](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)

W ramach konfigurowania protokołu OAuth zalecamy:

1. [Utwórz punkt końcowy dla klientów, aby uzyskać dostęp do wystąpienia](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Zaktualizuj cykl życia tokenu odświeżania:

   1. W okienku **usługi ServiceNow** Wyszukaj ciąg **system OAuth**, a następnie wybierz pozycję **Rejestr aplikacji**. 
   1. Wybierz nazwę zdefiniowanego uwierzytelniania OAuth i Zmień **token odświeżania cykl życia** na **7 776 000 sekund** (90 dni). 
   1. Wybierz pozycję **Aktualizuj**. 

1. Ustanów wewnętrzną procedurę, aby upewnić się, że połączenie pozostaje aktywne. Kilka dni przed oczekiwanym wygaśnięciem tokenu odświeżania cykl życia wykonaj następujące operacje:

   1. [Ukończ proces synchronizacji ręcznej dla konfiguracji łącznika narzędzia ITSM](./itsmc-resync-servicenow.md).

   1. Odwołaj do starego tokenu odświeżania. Nie zalecamy zachowywania starych kluczy ze względów bezpieczeństwa. 
   
      1. W okienku **usługi ServiceNow** Wyszukaj pozycję **system OAuth**, a następnie wybierz pozycję **Zarządzaj tokenami**. 
      
      1. Wybierz stary token z listy zgodnie z nazwą i datą wygaśnięcia uwierzytelniania OAuth.

         ![Zrzut ekranu przedstawiający listę tokenów protokołu OAuth.](media/itsmc-connections-servicenow/snow-system-oauth.png)
      1. Wybierz pozycję **odwołaj dostęp do**  >  **odwołania**.

## <a name="install-the-user-app-and-create-the-user-role"></a>Instalowanie aplikacji użytkownika i tworzenie roli użytkownika

Aby zainstalować aplikację użytkownika usługi Now i utworzyć dla niej rolę użytkownika integracji, należy wykonać poniższą procedurę. Te poświadczenia będą używane do nawiązania połączenia usługi ServiceNow na platformie Azure.

> [!NOTE]
> ITSMC obsługuje tylko oficjalną aplikację użytkownika dla integracji z programem Microsoft Log Analytics, która została pobrana ze sklepu usługi ServiceNow. ITSMC nie obsługuje żadnego pozyskiwania kodu na stronie usługi ServiceNow ani żadnej aplikacji, która nie jest częścią oficjalnego rozwiązania usługi ServiceNow. 

1. Odwiedź [usługi ServiceNow sklepu](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) i zainstaluj **aplikację użytkownika dla usługi ServiceNow i integrację pakietu Microsoft OMS** w wystąpieniu usługi ServiceNow.
   
   >[!NOTE]
   >W ramach trwającego przejścia z Microsoft Operations Management Suite (OMS) do Azure Monitor pakiet OMS jest teraz nazywany Log Analytics.     
2. Po zakończeniu instalacji przejdź na lewym pasku nawigacyjnym wystąpienia usługi ServiceNow, a następnie wyszukaj i wybierz **Integrator Microsoft OMS**.  
3. Wybierz pozycję **Lista kontrolna instalacji**.

   Stan jest wyświetlany jako  **nieukończony** , ponieważ rola użytkownika nie została jeszcze utworzona.

4. W polu tekstowym obok pozycji **Utwórz użytkownika integracji** wprowadź nazwę użytkownika, który może nawiązać połączenie z ITSMC na platformie Azure.
5. Wprowadź hasło dla tego użytkownika, a następnie wybierz przycisk **OK**.  

Nowo utworzony użytkownik zostanie wyświetlony z domyślnymi przypisanymi rolami:

- personalize_choices
- import_transformer
- x_mioms_microsoft. User
- ITIL
- template_editor
- view_changer

Po pomyślnym utworzeniu użytkownika stan **sprawdzenia listy kontrolnej instalacji** zostanie przesunięty do **ukończonego** i zawiera listę szczegółów roli użytkownika utworzonej dla aplikacji.

> [!NOTE]
> ITSMC może wysyłać zdarzenia do usługi ServiceNow bez żadnych innych modułów zainstalowanych w wystąpieniu usługi ServiceNow. Jeśli używasz modułu EventManagement w wystąpieniu usługi usługi ServiceNow i chcesz utworzyć zdarzenia lub alerty w usługi ServiceNow przy użyciu łącznika, Dodaj następujące role do użytkownika integracji:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Tworzenie połączenia
Aby utworzyć połączenie usługi ServiceNow, należy wykonać poniższą procedurę.

> [!NOTE]
> Alerty wysyłane z Azure Monitor mogą utworzyć jeden z następujących elementów w usługi ServiceNow: zdarzenia, zdarzenia lub alerty. 

1. W Azure Portal przejdź do pozycji **wszystkie zasoby** i Znajdź pozycję **Servicedesk (YourWorkspaceName)**.

2. W obszarze **źródła danych obszaru roboczego** wybierz pozycję **połączenia narzędzia ITSM**.

   ![Zrzut ekranu, który pokazuje wybór źródła danych.](media/itsmc-overview/add-new-itsm-connection.png)

3. W górnej części okienka po prawej stronie wybierz pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie wybierz przycisk **OK**.

   | **Pole** | **Opis** |
   | --- | --- |
   | **Nazwa połączenia**   | Wprowadź nazwę wystąpienia usługi ServiceNow, które chcesz połączyć z ITSMC. Tej nazwy można użyć później w Log Analytics podczas konfigurowania elementów roboczych narzędzia ITSM i wyświetlania szczegółowej analizy. |
   | **Typ partnera**   | Wybierz pozycję **usługi ServiceNow**. |
   | **Adres URL serwera**   | Wprowadź adres URL wystąpienia usługi ServiceNow, które chcesz połączyć z ITSMC. Adres URL powinien wskazywać obsługiwaną wersję SaaS z sufiksem *. ServiceNow.com* (na przykład `https://XXXXX.service-now.com/` ).|
   | **Nazwa użytkownika**   | Wprowadź nazwę użytkownika integracji utworzoną w aplikacji usługi ServiceNow, aby umożliwić obsługę połączenia z usługą ITSMC.|
   | **Password** (Hasło)   | Wprowadź hasło skojarzone z tą nazwą użytkownika. **Uwaga**: Nazwa użytkownika i hasło są używane do generowania tokenów uwierzytelniania. Nie są one przechowywane w żadnym miejscu w ramach usługi ITSMC.  |
   | **Identyfikator klienta**   | Wprowadź identyfikator klienta, który ma być używany na potrzeby uwierzytelniania OAuth2 wygenerowanego wcześniej. Aby uzyskać więcej informacji na temat generowania identyfikatora klienta i wpisu tajnego, zobacz [Konfigurowanie protokołu OAuth](https://old.wiki/index.php/OAuth_Setup). |
   | **Klucz tajny klienta**   | Wprowadź klucz tajny klienta wygenerowany dla tego identyfikatora.   |
   | **Zakres synchronizacji danych (w dniach)** | Wprowadź liczbę dni, z których dane mają być używane. Limit wynosi 120 dni. |
   | **Elementy robocze do zsynchronizowania**   | Wybierz usługi ServiceNow elementy robocze, które chcesz synchronizować z platformą Azure Log Analytics, za pomocą ITSMC. Wybrane wartości są importowane do Log Analytics. Dostępne są zdarzenia i żądania zmiany.|
   | **Utwórz nowy element konfiguracji w produkcie narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie narzędzia ITSM. Po wybraniu tej opcji ITSMC tworzy elementy konfiguracji (jeśli nie istnieje) w obsługiwanym systemie narzędzia ITSM. Jest on domyślnie wyłączony. |

![Zrzut ekranu pól i opcji dodawania połączenia usługi ServiceNow.](media/itsmc-connections-servicenow/itsm-connection-servicenow-connection-latest.png)

Po pomyślnym nawiązaniu połączenia i zsynchronizowaniu:

- Wybrane elementy robocze z wystąpienia usługi ServiceNow są importowane do Log Analytics. Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku **Łącznik zarządzania usługami IT** .

- Możesz tworzyć zdarzenia z Log Analytics alertów lub rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu usługi usługi ServiceNow.

> [!NOTE]
> Usługi ServiceNow ma limit szybkości dla żądań na godzinę. Aby skonfigurować limit, zdefiniuj **przychodzące limity szybkości interfejsu API REST** w wystąpieniu usługi ServiceNow.

## <a name="next-steps"></a>Następne kroki

* [Przegląd łącznik ITSM](itsmc-overview.md)
* [Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Rozwiązywanie problemów z łącznik ITSM](./itsmc-resync-servicenow.md)
