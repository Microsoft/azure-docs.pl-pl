---
title: Publikowanie Pulpit zdalny z serwer proxy aplikacji usługi Azure Active Directory
description: Informacje dotyczące konfigurowania serwera proxy aplikacji przy użyciu Usługi pulpitu zdalnego (RDS)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: bfa9367e06c099e21a54ad8c03f8d5ab853aaafb
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348079"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Publikowanie pulpitu zdalnego przy użyciu serwera proxy aplikacji usługi Azure AD

Usługa Pulpit zdalny i platforma Azure serwer proxy aplikacji usługi Azure AD współdziałać w celu poprawy produktywności pracowników, którzy znajdują się poza siecią firmową. 

Zaznaczeni odbiorcy tego artykułu:
- Bieżąca klienci proxy aplikacji, którzy chcą zaoferować więcej aplikacji użytkownikom końcowym, publikując aplikacje lokalne za pomocą Usługi pulpitu zdalnego.
- Bieżąca Usługi pulpitu zdalnego klientów, którzy chcą zmniejszyć powierzchnię ataku na ich wdrożenie przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD. W tym scenariuszu przedstawiono zestaw weryfikacji dwuetapowej i kontroli dostępu warunkowego do usług pulpitu zdalnego.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Jak serwer proxy aplikacji jest dopasowany do standardowego wdrożenia usług RDS

Standardowe wdrożenie usług pulpitu zdalnego obejmuje różne usługi ról Pulpit zdalny działających w systemie Windows Server. Analizując [architekturę usługi pulpitu zdalnego](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture), dostępnych jest wiele opcji wdrażania. W przeciwieństwie do innych opcji wdrażania usług pulpitu zdalnego, [wdrożenie usług pulpitu zdalnego za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) (pokazane na poniższym diagramie) ma trwałe połączenie wychodzące z serwera, na którym działa usługa łącznika. Inne wdrożenia opuszczają otwarte połączenia przychodzące za pomocą modułu równoważenia obciążenia.

![Serwer proxy aplikacji znajduje się między maszyną wirtualną usług pulpitu zdalnego i publicznym Internetem](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

We wdrożeniu RDS rola sieci Web usług pulpitu zdalnego i rola bramy usług pulpitu zdalnego są uruchamiane na maszynach z Internetem. Te punkty końcowe są ujawniane z następujących powodów:
- Witryna sieci Web usług pulpitu zdalnego udostępnia użytkownikowi publiczny punkt końcowy logowania i wyświetla różne aplikacje lokalne oraz pulpity, do których mają dostęp. Po wybraniu zasobu połączenie RDP jest tworzone przy użyciu aplikacji natywnej w systemie operacyjnym.
- Brama usług pulpitu zdalnego znajduje się na obrazie, gdy użytkownik uruchomi połączenie RDP. Brama usług pulpitu zdalnego obsługuje szyfrowany ruch RDP przez Internet i tłumaczy je na serwer lokalny, z którym łączy się użytkownik. W tym scenariuszu ruch odebrany przez bramę usług pulpitu zdalnego pochodzi z usługi Azure serwer proxy aplikacji usługi Azure AD.

>[!TIP]
>Jeśli usługa RDS nie została wdrożona wcześniej lub chcesz uzyskać więcej informacji przed rozpoczęciem, Dowiedz się, jak [bezproblemowo wdrożyć usługę RDS przy użyciu Azure Resource Manager i witryny Azure Marketplace](/windows-server/remote/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Wymagania

- Punkty końcowe bramy sieci Web i pulpitu zdalnego usług pulpitu zdalnego muszą znajdować się na tym samym komputerze i ze wspólnym katalogiem głównym. Brama usług pulpitu zdalnego i sieci Web jest publikowana jako pojedyncza aplikacja z serwerem proxy aplikacji, dzięki czemu można korzystać z logowania jednokrotnego między tymi dwiema aplikacjami.

- Powinien już być [wdrożony RDS](/windows-server/remote/remote-desktop-services/rds-in-azure)i [włączony serwer proxy aplikacji](application-proxy-add-on-premises-application.md).

- Użytkownicy końcowi muszą używać zgodnej przeglądarki, aby nawiązać połączenie z siecią Web usług pulpitu zdalnego lub klientem sieci Web usług pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [Obsługa konfiguracji klienta](#support-for-other-client-configurations).

- W przypadku publikowania w sieci Web usług pulpitu zdalnego zaleca się używanie tej samej wewnętrznej i zewnętrznej nazwy FQDN. Jeśli wewnętrzne i zewnętrzne nazwy FQDN różnią się od siebie, należy wyłączyć tłumaczenie nagłówka żądania, aby uniknąć otrzymywania nieprawidłowych linków przez klienta.

- Jeśli używasz usługi sieci Web usług pulpitu zdalnego w programie Internet Explorer, musisz włączyć dodatek ActiveX RDS.

- Jeśli używasz klienta sieci Web usług pulpitu zdalnego, musisz użyć łącznika serwera proxy aplikacji w [wersji 1.5.1975 lub nowszej](./application-proxy-release-version-history.md).

- W przypadku przepływu wstępnego uwierzytelniania usługi Azure AD użytkownicy mogą łączyć się tylko z zasobami opublikowanymi w okienku **RemoteApp i pulpitów** . Użytkownicy nie mogą łączyć się z pulpitem za pomocą okienka **Połącz z komputerem zdalnym** .

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Wdrażanie wspólnego scenariusza usług RDS i serwera proxy aplikacji

Po skonfigurowaniu usług pulpitu zdalnego i serwer proxy aplikacji usługi Azure AD platformy Azure dla danego środowiska wykonaj kroki, aby połączyć te dwa rozwiązania. Te kroki umożliwiają opublikowanie dwóch punktów końcowych usług pulpitu zdalnego opartych na sieci Web (Brama sieci Web i usług pulpitu zdalnego) jako aplikacji, a następnie kierowanie ruchu na pulpicie RDS w celu przechodzenia przez serwer proxy aplikacji.

### <a name="publish-the-rd-host-endpoint"></a>Publikowanie punktu końcowego hosta usług pulpitu zdalnego

1. [Opublikuj nową aplikację serwera proxy aplikacji](application-proxy-add-on-premises-application.md) o następujących wartościach:
   - Wewnętrzny adres URL: `https://\<rdhost\>.com/` , gdzie `\<rdhost\>` jest wspólnym katalogiem głównym usług pulpitu zdalnego sieci Web i bramy usług pulpitu zdalnego.
   - Zewnętrzny adres URL: to pole jest wypełniane automatycznie na podstawie nazwy aplikacji, ale można ją modyfikować. Użytkownicy będą przechodzić do tego adresu URL, gdy uzyskują dostęp do usług pulpitu zdalnego.
   - Metoda wstępnego uwierzytelniania: Azure Active Directory
   - Tłumaczenie nagłówków adresów URL: nie
2. Przypisz użytkowników do opublikowanej aplikacji usług pulpitu zdalnego. Upewnij się, że wszyscy mieli również dostęp do usług pulpitu zdalnego.
3. Pozostaw metodę logowania jednokrotnego dla aplikacji, ponieważ logowanie jednokrotne w **usłudze Azure AD jest wyłączone**.

   >[!Note]
   >Użytkownicy są monitowani o jednokrotne uwierzytelnienie w usłudze Azure AD i w sieci Web do usług pulpitu zdalnego, ale mają Logowanie jednokrotne do bramy usług pulpitu zdalnego.

4. Wybierz **Azure Active Directory**, a następnie **rejestracje aplikacji**. Wybierz aplikację z listy.
5. W obszarze **Zarządzaj** wybierz opcję **znakowanie**.
6. Zaktualizuj pole **adres URL strony głównej** , aby wskazywało punkt końcowy sieci Web usług pulpitu zdalnego (na przykład `https://\<rdhost\>.com/RDWeb` ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Bezpośredni ruch RDS do serwera proxy aplikacji

Połącz się z wdrożeniem usług pulpitu zdalnego jako administrator i Zmień nazwę serwera bramy usług pulpitu zdalnego dla wdrożenia. Ta konfiguracja zapewnia, że połączenia przechodzą przez usługę serwer proxy aplikacji usługi Azure AD platformy Azure.

1. Nawiąż połączenie z serwerem RDS z uruchomioną rolą brokera połączeń usług pulpitu zdalnego.
2. Uruchom **Menedżer serwera**.
3. Wybierz **usługi pulpitu zdalnego** z okienka po lewej stronie.
4. Wybierz pozycję **Omówienie**.
5. W sekcji Przegląd wdrożenia wybierz menu rozwijane i wybierz polecenie **Edytuj właściwości wdrożenia**.
6. Na karcie Brama usług pulpitu zdalnego Zmień pole **Nazwa serwera** na zewnętrzny adres URL ustawiony dla punktu końcowego hosta usług pulpitu zdalnego w serwerze proxy aplikacji.
7. Zmień wartość pola **metoda logowania** na **uwierzytelnianie hasła**.

   ![Ekran właściwości wdrożenia na serwerze RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Uruchom to polecenie dla każdej kolekcji. Zastąp *\<yourcollectionname\>* i *\<proxyfrontendurl\>* własnymi informacjami. To polecenie umożliwia logowanie jednokrotne między usługami pulpitu zdalnego sieci Web i bramą usług pulpitu zdalnego, a następnie optymalizuje wydajność:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Przykład:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Powyższe polecenie używa znacznika "nrequire".

9. Aby sprawdzić modyfikację niestandardowych właściwości protokołu RDP, a także wyświetlić zawartość pliku RDP, która zostanie pobrana z RDWeb dla tej kolekcji, uruchom następujące polecenie:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Po skonfigurowaniu Pulpit zdalny usługa Azure serwer proxy aplikacji usługi Azure AD została przejęta jako składnik usług pulpitu zdalnego dostępny z Internetu. Pozostałe publiczne punkty końcowe dostępne z Internetu można usunąć na maszynach sieci Web usług pulpitu zdalnego i bramy usług pulpitu zdalnego.

### <a name="enable-the-rd-web-client"></a>Włączanie klienta sieci Web usług pulpitu zdalnego
Jeśli chcesz również, aby użytkownicy mogli korzystać z klienta sieci Web usług pulpitu zdalnego, wykonaj kroki opisane w sekcji [Konfigurowanie klienta sieci web pulpit zdalny dla użytkowników](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin) , aby je włączyć.

Klient sieci Web Pulpit zdalny umożliwia użytkownikom uzyskiwanie dostępu do infrastruktury Pulpit zdalny organizacji za pomocą przeglądarki sieci Web zgodnej z HTML5, takiej jak Microsoft Edge, Internet Explorer 11, Google Chrome, Safari lub Mozilla Firefox (v 55.0 i nowsze).

## <a name="test-the-scenario"></a>Testowanie scenariusza

Przetestuj scenariusz w programie Internet Explorer na komputerze z systemem Windows 7 lub 10.

1. Przejdź do skonfigurowanego zewnętrznego adresu URL lub Znajdź swoją aplikację w [panelu Moje aplikacje](https://myapps.microsoft.com).
2. Zostanie wyświetlony monit o uwierzytelnienie w Azure Active Directory. Użyj konta przypisanego do aplikacji.
3. Zostanie wyświetlony monit o uwierzytelnienie w sieci Web usług pulpitu zdalnego.
4. Po pomyślnym uwierzytelnieniu usług pulpitu zdalnego możesz wybrać odpowiedni pulpit lub aplikację i rozpocząć pracę.

## <a name="support-for-other-client-configurations"></a>Obsługa innych konfiguracji klienta

Konfiguracja zakreślona w tym artykule dotyczy dostępu do pulpitu zdalnego za pośrednictwem sieci Web usług pulpitu zdalnego lub klienta sieci Web usług pulpitu zdalnego. W razie potrzeby można jednak obsługiwać inne systemy operacyjne lub przeglądarki. Różnica dotyczy używanej metody uwierzytelniania.

| Metoda uwierzytelniania | Obsługiwana konfiguracja klienta |
| --------------------- | ------------------------------ |
| Wstępne uwierzytelnianie    | Pulpit internetowy usług pulpitu zdalnego — Windows 7/10 przy użyciu przeglądarki Internet Explorer lub programu [Edge](/deployedge/edge-ie-mode) <br /> *Uwaga Portal moje aplikacje obsługuje tylko funkcję Edge.* |
| Wstępne uwierzytelnianie    | Klient usług pulpitu zdalnego w sieci Web — przeglądarka sieci Web zgodna z programem HTML5, taka jak Microsoft Edge, Internet Explorer 11, Google Chrome, Safari lub Mozilla Firefox (v 55.0 i nowsze) |
| Kazują | Każdy inny system operacyjny, który obsługuje aplikację Pulpit zdalny Microsoft |

Przepływ przed uwierzytelnianiem oferuje większe korzyści z zabezpieczeń niż przepływ przekazywania. Za pomocą wstępnego uwierzytelniania można używać funkcji uwierzytelniania usługi Azure AD, takich jak logowanie jednokrotne, dostęp warunkowy i weryfikacja dwuetapowa dla zasobów lokalnych. Należy również upewnić się, że tylko ruch uwierzytelniony dociera do sieci.

Aby można było korzystać z uwierzytelniania przekazującego, istnieją tylko dwa modyfikacje kroków wymienionych w tym artykule:
1. W obszarze [publikowanie punktu końcowego hosta usług pulpitu zdalnego](#publish-the-rd-host-endpoint) krok 1 ustaw metodę wstępnego uwierzytelniania na **przekazywanie**.
2. W obszarze [bezpośredni ruch RDS do serwera proxy aplikacji](#direct-rds-traffic-to-application-proxy)Pomiń całkowicie krok 8.

## <a name="next-steps"></a>Następne kroki
- [Włączanie dostępu zdalnego do programu SharePoint przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Zagadnienia dotyczące zabezpieczeń dotyczące zdalnego uzyskiwania dostępu do aplikacji przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-security.md)
- [Najlepsze rozwiązania dotyczące równoważenia obciążenia wielu serwerów aplikacji](application-proxy-high-availability-load-balancing.md#best-practices-for-load-balancing-among-multiple-app-servers)
