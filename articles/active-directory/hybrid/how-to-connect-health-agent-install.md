---
title: Instalacja agenta programu Azure AD Connect Health | Microsoft Docs
description: Jest to strona programu Azure AD Connect Health opisująca instalację agenta usług AD FS i synchronizacji.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: da3ae5e86833eb3e7eb71d7e47cb6f963d37b9cf
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410729"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalowanie agenta programu Azure AD Connect Health

W tym dokumencie opisano instalowanie i konfigurowanie agentów programu Azure AD Connect Health. Agentów możesz pobrać [tutaj](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Wymagania

Poniższa tabela zawiera listę wymagań dotyczących używania programu Azure AD Connect Health.

| Wymaganie | Opis |
| --- | --- |
| Usługa Azure AD — wersja Premium |Azure AD Connect Health to funkcja usługi Azure AD w wersji Premium, dlatego wymaga tej usługi. <br /><br />Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure AD — wersja Premium](../fundamentals/active-directory-get-started-premium.md) <br />Aby skorzystać z bezpłatnej 30-dniowej wersji próbnej, zobacz [Włączanie wersji próbnej](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Aby rozpocząć pracę z programem Azure AD Connect Health, musisz być administratorem globalnym usługi Azure AD |Domyślnie tylko administratorzy globalni mogą instalować i konfigurować agentów kondycji, aby rozpoczynać pracę z programem Azure AD Connect Health, uzyskiwać dostęp do portalu i wykonywać dowolne operacje w programie. Aby więcej informacji, zobacz [Administering your Azure AD directory](../fundamentals/active-directory-whatis.md) (Administrowanie katalogiem usługi Azure AD). <br /><br /> Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można zezwolić na dostęp do Azure AD Connect Health innym użytkownikom w organizacji. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach na platformie Azure (RBAC) dla Azure AD Connect Health.](how-to-connect-health-operations.md#manage-access-with-azure-rbac) <br /><br />**Ważne:** Konto używane podczas instalowania agentów musi być kontem służbowym. Nie może być kontem Microsoft. Aby uzyskać więcej informacji, zobacz [Tworzenie konta na platformie Azure jako organizacja](../fundamentals/sign-up-organization.md) |
| Na każdym serwerze docelowym jest zainstalowany agent programu Azure AD Connect Health | Program Azure AD Connect Health wymaga zainstalowania i skonfigurowania agentów kondycji na serwerach docelowych w celu umożliwienia odbioru danych i zapewnienia możliwości monitorowania i analizy. <br /><br />Na przykład aby uzyskać dane z infrastruktury usług AD FS, musisz zainstalować agenta na serwerach proxy usług AD FS i aplikacji internetowej. Podobnie, aby uzyskać dane dotyczące lokalnej infrastruktury usług AD DS, agent musi być zainstalowany na kontrolerach domeny. <br /><br /> |
| Łączność wychodząca z punktami końcowymi usług Azure | Podczas instalowania i w czasie pracy agent wymaga łączności z punktami końcowymi usługi Azure AD Connect Health. Jeśli łączność wychodząca jest blokowana przy użyciu zapór, nie zapomnij dodać następujących punktów końcowych do listy lokalizacji dozwolonych. Zobacz sekcję dotyczącą [punktów końcowych łączności wychodzącej](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) |
|Łączność wychodząca na podstawie adresów IP | W przypadku filtrowania zapory opartego na adresach IP zapoznaj się z [zakresem adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).|
| Inspekcja protokołu TLS dla ruchu wychodzącego jest filtrowana lub wyłączona | Krok rejestracji agenta lub operacje przekazywania danych mogą się nie powieść, jeśli istnieje Inspekcja lub przerwa dla ruchu wychodzącego w warstwie sieciowej. Przeczytaj więcej [na temat sposobu konfigurowania inspekcji protokołu TLS](/previous-versions/tn-archive/ee796230(v=technet.10)) |
| Porty zapory na serwerze, na którym jest uruchomiony agent |Agent wymaga, aby poniższe porty zapory były otwarte w celu komunikacji z punktami końcowymi usług programu Azure AD Connect Health.<br /><br /><li>Port TCP 443</li><li>Port TCP 5671</li> <br />Należy zauważyć, że port 5671 nie jest już wymagany dla najnowszej wersji agenta. Uaktualnij do najnowszej wersji, aby był wymagany tylko port 443. Dowiedz się więcej na temat [włączania portów zapory](/previous-versions/sql/sql-server-2008/ms345310(v=sql.100)) |
| Zezwalaj na następujące witryny sieci web, jeśli są włączone zwiększone zabezpieczenia programu Internet Explorer |Jeśli zwiększone zabezpieczenia programu Internet Explorer są włączone na serwerze, na którym ma zostać zainstalowany agent, musisz zezwolić na otwieranie poniższych witryn sieci Web.<br /><br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.NET</li><li>Serwer federacyjny Twojej organizacji zaufany przez usługę Azure Active Directory. Na przykład: https:\//sts.contoso.com</li> Dowiedz się więcej [na temat konfigurowania programu IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Jeśli masz serwer proxy w sieci, zobacz uwagi poniżej.|
| Upewnij się, że zainstalowano program PowerShell w wersji 4.0 lub nowszej | <li>System Windows Server 2012 jest dostarczany z programem PowerShell w wersji 3.0, która jest niewystarczająca dla agenta.</li><li>System Windows Server 2012 R2 i jego nowsze wersje są dostarczane z wystarczająco nową wersją programu PowerShell.</li>|
|Wyłącz standard FIPS|Standard FIPS nie jest obsługiwany przez agentów programu Azure AD Connect Health.|

> [!IMPORTANT]
> Instalowanie agenta Azure AD Connect Health w systemie Windows Server Core nie jest obsługiwane.


> [!NOTE]
> Jeśli masz wysoce zablokowanie i wyjątkowo ograniczone środowisko, musisz dodać adresy URL wymienione na liście punktów końcowych poniżej, oprócz tych wymienionych w powyższej konfiguracji programu IE zwiększonych zabezpieczeń. 


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Łączność wychodząca z punktami końcowymi usług Azure

 Podczas instalowania i w czasie pracy agent wymaga łączności z punktami końcowymi usługi Azure AD Connect Health. Jeśli łączność wychodząca jest blokowana przy użyciu zapór, upewnij się, że następujące adresy URL nie są domyślnie blokowane. Nie należy wyłączać monitorowania zabezpieczeń ani inspekcji tych adresów URL, ale Zezwalaj na tak jak w przypadku innego ruchu internetowego. Umożliwiają one komunikację z punktami końcowymi usługi Azure AD Connect Health. Dowiedz się, jak [sprawdzić łączność wychodzącą przy użyciu Test-AzureADConnectHealthConnectivity](#test-connectivity-to-azure-ad-connect-health-service).

| Środowisko domeny | Wymagane punkty końcowe usług platformy Azure |
| --- | --- |
| Ogólne publiczne | <li>&#42;.blob.core.windows.net </li><li>&#42;. aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-port: 5671 (niewymagane w najnowszej wersji agenta)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *ten punkt końcowy jest używany tylko do celów odnajdywania podczas rejestracji.</li> |
| Azure (Niemcy) | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de *ten punkt końcowy jest używany tylko do celów odnajdywania podczas rejestracji.</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *ten punkt końcowy jest używany tylko do celów odnajdywania podczas rejestracji.</li> |


## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Pobieranie i instalowanie agenta programu Azure AD Connect Health

* Sprawdź, czy [wymagania programu Azure AD Connect Health zostały spełnione](how-to-connect-health-agent-install.md#requirements).
* Wprowadzenie do korzystania z programu Azure AD Connect Health dla usług AD FS
    * [Pobierz agenta programu Azure AD Connect Health dla usług AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Zobacz instrukcje instalacji](#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Wprowadzenie do korzystania z programu Azure AD Connect Health do celów synchronizacji
    * [Pobierz i zainstaluj najnowszą wersję programu Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). Agent kondycji do celów synchronizacji zostanie zainstalowany w ramach instalacji programu Azure AD Connect (w wersji 1.0.9125.0 lub nowszej).
* Wprowadzenie do korzystania z programu Azure AD Connect Health dla usług AD DS
    * [Pobierz Azure AD Connect Health agenta dla AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Zobacz instrukcje instalacji](#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Instalowanie agenta programu Azure AD Connect Health Agent dla usług AD FS

> [!NOTE]
> Serwer usług AD FS nie powinien być serwerem synchronizacji. Nie instaluj agenta usług AD FS na serwerze synchronizacji.
>

Przed rozpoczęciem instalacji upewnij się, że nazwa hosta serwera usług AD FS jest unikatowa i nie występuje w usłudze AD FS.
Aby rozpocząć instalowanie agenta, kliknij dwukrotnie pobrany plik exe. Na pierwszym ekranie kliknij przycisk Instaluj.

![Rozpoczęcie instalacji AD FS Azure AD Connect Health](./media/how-to-connect-health-agent-install/install1.png)

Po zakończeniu instalacji kliknij przycisk Konfiguruj teraz.

![Zakończenie instalacji AD FS Azure AD Connect Health](./media/how-to-connect-health-agent-install/install2.png)

Spowoduje to uruchomienie okna programu PowerShell w celu zainicjowania procesu rejestracji agenta. Po wyświetleniu monitu zaloguj się przy użyciu konta usługi Azure AD, które ma dostęp do przeprowadzenia rejestracji agenta. Taki dostęp ma domyślnie konto administratora globalnego.

![Azure AD Connect Health AD FS skonfigurować logowanie](./media/how-to-connect-health-agent-install/install3.png)

Po zalogowaniu się działanie programu PowerShell będzie kontynuowane. Gdy program PowerShell zakończy pracę, możesz go zamknąć. Konfiguracja zostanie zakończona.

Na tym etapie usługi agenta powinni zostać uruchomione automatyczne i zezwolić agentowi na bezpieczne przekazanie wymaganych danych do usługi w chmurze.

Jeśli nie zostały spełnione wszystkie wymagania wstępne, które zostały opisane w poprzednich sekcjach, w oknie programu PowerShell będą wyświetlane ostrzeżenia. Przed zainstalowaniem agenta upewnij się, że zostały spełnione [wymagania](how-to-connect-health-agent-install.md#requirements). Poniższy zrzut ekranu przedstawia przykład tego rodzaju błędów.

![Azure AD Connect Health AD FS konfigurowania skryptu](./media/how-to-connect-health-agent-install/install4.png)

Aby sprawdzić, czy agent został zainstalowany, znajdź następujące usługi na serwerze: Jeśli konfiguracja została zakończona, te usługi powinny być uruchomione. W przeciwnym razie pozostaną one zatrzymane do czasu zakończenia konfiguracji.

* Usługa diagnostyczna usług AD FS programu Azure AD Connect Health
* Usługa szczegółowych informacji usług AD FS programu Azure AD Connect Health
* Usługa monitorowania usług AD FS programu Azure AD Connect Health

![Azure AD Connect Health AD FS Services](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Włączanie inspekcji dla usług AD FS

> [!NOTE]
> Informacje w tej sekcji dotyczą tylko serwerów usług AD FS. Nie musisz wykonywać tych instrukcji na serwerach proxy aplikacji internetowej.
>

Aby funkcja Analiza użycia mogła zbierać i analizować dane, agent programu Azure AD Connect Health potrzebuje informacji zawartych w dziennikach inspekcji usług AD FS. Dzienniki te nie są domyślnie włączone. Do włączania inspekcji usług AD FS i lokalizowania dzienników inspekcji usług AD FS na własnych serwerach usług AD FS służą poniższe procedury.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Aby włączyć inspekcję usług AD FS w systemie Windows Server 2012 R2

1. Otwórz okno **Zasady zabezpieczeń lokalnych** , klikając pozycję **Menedżer serwera** na ekranie startowym lub pasku zadań na pulpicie, a następnie kliknij pozycję **Narzędzia/Zasady zabezpieczeń lokalnych**.
2. Przejdź do folderu **Ustawienia zabezpieczeń\Zasady lokalne\Przypisywanie praw użytkownika** , a następnie kliknij dwukrotnie pozycję **Generuj inspekcje zabezpieczeń**.
3. Na karcie **Ustawianie zabezpieczeń lokalnych** sprawdź, czy jest wymienione konto usługi AD FS. Jeśli go nie ma, kliknij pozycję **Dodaj użytkownika lub grupę** i dodaj je do listy, a następnie kliknij przycisk **OK**.
4. Aby włączyć inspekcję, otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie: ```auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable```
5. Zamknij okno **Zasady zabezpieczeń lokalnych**.
<br />   -- **Poniższe kroki są wymagane wyłącznie w przypadku podstawowych serwerów usług AD FS.** -- <br />
6. Otwórz przystawkę **Zarządzanie usługami AD FS** (w Menedżerze serwera kliknij pozycję Narzędzia, a następnie wybierz pozycję Zarządzanie usługami AD FS).
7. W okienku **Akcje** kliknij pozycję **Edytuj właściwości usługi federacyjnej**.
8. W oknie dialogowym **właściwości usługa federacyjna** kliknij kartę **zdarzenia** .
9. Zaznacz pola wyboru **Inspekcje zakończone sukcesem i Inspekcje zakończone niepowodzeniem** , a następnie kliknij przycisk **OK**.
10. Pełne rejestrowanie można włączyć za pomocą programu PowerShell przy użyciu polecenia: ```Set-AdfsProperties -LOGLevel Verbose``` .

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Aby włączyć inspekcję usług AD FS w systemie Windows Server 2016

1. Otwórz okno **Zasady zabezpieczeń lokalnych** , klikając pozycję **Menedżer serwera** na ekranie startowym lub pasku zadań na pulpicie, a następnie kliknij pozycję **Narzędzia/Zasady zabezpieczeń lokalnych**.
2. Przejdź do folderu **Ustawienia zabezpieczeń\Zasady lokalne\Przypisywanie praw użytkownika** , a następnie kliknij dwukrotnie pozycję **Generuj inspekcje zabezpieczeń**.
3. Na karcie **Ustawianie zabezpieczeń lokalnych** sprawdź, czy jest wymienione konto usługi AD FS. Jeśli go nie ma, kliknij pozycję **Dodaj użytkownika lub grupę** i dodaj konto usług AD FS do listy, a następnie kliknij przycisk **OK**.
4. Aby włączyć inspekcję, otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie: <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>
5. Zamknij okno **Zasady zabezpieczeń lokalnych**.
<br />   -- **Poniższe kroki są wymagane wyłącznie w przypadku podstawowych serwerów usług AD FS.** -- <br />
6. Otwórz przystawkę **Zarządzanie usługami AD FS** (w Menedżerze serwera kliknij pozycję Narzędzia, a następnie wybierz pozycję Zarządzanie usługami AD FS).
7. W okienku **Akcje** kliknij pozycję **Edytuj właściwości usługi federacyjnej**.
8. W oknie dialogowym **właściwości usługa federacyjna** kliknij kartę **zdarzenia** .
9. Zaznacz pola wyboru **Inspekcje zakończone sukcesem i Inspekcje zakończone niepowodzeniem** , a następnie kliknij przycisk **OK**. Powinno to być włączone domyślnie.
10. Otwórz okno programu PowerShell i uruchom następujące polecenie: ```Set-AdfsProperties -AuditLevel Verbose```.

Zwróć uwagę, że poziom inspekcji „basic” (podstawowy) jest włączony domyślnie. Przeczytaj więcej na temat [ulepszenia inspekcji usług AD FS w systemie Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Aby zlokalizować dzienniki inspekcji usług AD FS

1. Otwórz **Podgląd zdarzeń**.
2. Przejdź do dzienników systemu Windows i wybierz pozycję **Zabezpieczenia**.
3. Po prawej stronie kliknij pozycję **Filtruj bieżące dzienniki**.
4. W obszarze Źródło zdarzenia wybierz pozycję **Inspekcja usług AD FS**.

    Poniżej znajdziesz [krótką uwagę z tematu zawierającego często zadawane pytania](reference-connect-health-faq.md#operations-questions) dotyczącą dzienników inspekcji.

![Dzienniki inspekcji usług AD FS](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Zasady grupy mogą wyłączyć inspekcję usług AD FS. Jeśli inspekcja usług AD FS jest wyłączona, analiza użycia dotycząca aktywności logowania jest niedostępna. Upewnij się, że nie masz zasad grupy, które wyłączają inspekcję usług AD FS.>
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Instalowanie agenta programu Azure AD Connect Health do celów synchronizacji

W najnowszej kompilacji programu Azure AD Connect agent programu Azure AD Connect Health do celów synchronizacji jest instalowany automatycznie. Aby używać programu Azure AD Connect do celów synchronizacji, musisz pobrać i zainstalować najnowszą wersję programu Azure AD Connect. Najnowszą wersję możesz pobrać [tutaj](https://www.microsoft.com/download/details.aspx?id=47594).

Aby sprawdzić, czy agent został zainstalowany, znajdź następujące usługi na serwerze: Jeśli konfiguracja została zakończona, te usługi powinny być uruchomione. W przeciwnym razie pozostaną one zatrzymane do czasu zakończenia konfiguracji.

* Usługa szczegółowych informacji synchronizacji programu Azure AD Connect Health
* Usługa monitorowania synchronizacji programu Azure AD Connect Health

![Weryfikowanie programu Azure AD Connect Health do celów synchronizacji](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Pamiętaj, że do korzystania z programu Azure AD Connect Health jest wymagana usługa Azure AD w wersji Premium. Jeśli nie masz usługi Azure AD w wersji Premium, nie możesz ukończyć konfiguracji w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [stronę wymagań](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Ręczne rejestrowanie programu Azure AD Connect Health do celów synchronizacji

Jeśli rejestracja agenta programu Azure AD Connect Health do celów synchronizacji nie powiedzie się po pomyślnym zainstalowaniu programu Azure AD Connect, możesz użyć poniższego polecenia programu PowerShell, aby ręcznie zarejestrować agenta.

> [!IMPORTANT]
> Użycie tego polecenia programu PowerShell jest wymagane tylko wtedy, gdy rejestracja agenta nie powiedzie się po zainstalowaniu programu Azure AD Connect.
>
>

Poniższe polecenie programu PowerShell jest wymagane TYLKO w przypadku, gdy rejestracja agenta kondycji zakończy się niepowodzeniem nawet po pomyślnym zainstalowaniu i skonfigurowaniu programu Azure AD Connect. Usługi programu Azure AD Connect Health zostaną uruchomione po pomyślnym zarejestrowaniu agenta.

Agenta programu Azure AD Connect Health do celów synchronizacji możesz zarejestrować ręcznie, używając następującego polecenia programu PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Polecenie przyjmuje następujące parametry:

* AttributeFiltering: $true (domyślnie) — jeśli program Azure AD Connect nie synchronizuje domyślnego zestawu atrybutów i został dostosowany do korzystania z filtrowanego zestawu atrybutów. W przeciwnym razie $false.
* StagingMode: $false (domyślnie) — jeśli serwer programu Azure AD Connect NIE JEST w trybie przejściowym, wartość $true, jeśli serwer jest skonfigurowany na tryb przejściowy.

Po wyświetleniu monitu o uwierzytelnienie musisz użyć tego samego konta administratora globalnego (na przykład admin@domain.onmicrosoft.com), które było używane podczas konfigurowania programu Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Instalowanie agenta programu Azure AD Connect Health dla usług AD DS

Aby rozpocząć instalowanie agenta, kliknij dwukrotnie pobrany plik exe. Na pierwszym ekranie kliknij przycisk Instaluj.

![Azure AD Connect Health agenta do uruchomienia AD DS instalacji](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Po zakończeniu instalacji kliknij przycisk Konfiguruj teraz.

![Zakończenie instalacji agenta Azure AD Connect Health AD DS](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Zostanie uruchomiony wiersz polecenia, a następnie pewne polecenia programu PowerShell, które spowodują wykonanie polecenia Register-AzureADConnectHealthADDSAgent. Po wyświetleniu monitu zaloguj się do platformy Azure.

![Azure AD Connect Health agenta dla AD DS Skonfiguruj logowanie](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Po zalogowaniu się działanie programu PowerShell będzie kontynuowane. Gdy program PowerShell zakończy pracę, możesz go zamknąć. Konfiguracja zostanie zakończona.

W tym momencie usługi powinny uruchomić się automatycznie, umożliwiając agentowi monitorowanie i zbieranie danych. Jeśli nie zostały spełnione wszystkie wymagania wstępne, które zostały opisane w poprzednich sekcjach, w oknie programu PowerShell będą wyświetlane ostrzeżenia. Przed zainstalowaniem agenta upewnij się, że zostały spełnione [wymagania](how-to-connect-health-agent-install.md#requirements). Poniższy zrzut ekranu przedstawia przykład tego rodzaju błędów.

![Agent Azure AD Connect Health dla AD DS konfigurowania skryptu](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Aby sprawdzić, czy agent został zainstalowany, znajdź następujące usługi na kontrolerze domeny:

* Usługa szczegółowych informacji usług AD DS programu Azure AD Connect Health
* Usługa monitorowania usług AD DS programu Azure AD Connect Health

Jeśli konfiguracja została zakończona, te usługi powinny być uruchomione. W przeciwnym razie pozostaną one zatrzymane do czasu zakończenia konfiguracji.

![Agent Azure AD Connect Health dla usług AD DS Services](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quick-agent-installation-in-multiple-servers"></a>Szybka instalacja agenta na wielu serwerach

1. Utwórz konto użytkownika w usłudze Azure AD przy użyciu hasła.
2. Przypisz rolę **właściciela** dla tego lokalnego konta usługi AAD w Azure AD Connect Health za pośrednictwem portalu. Wykonaj kroki opisane [tutaj](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Przypisz rolę do wszystkich wystąpień usługi. 
3. Pobierz plik MSI. exe na lokalny kontroler domeny w celu instalacji.
4. Uruchom następujący skrypt, aby przeprowadzić rejestrację. Zastąp parametry nowym kontem użytkownika utworzonym wraz z jego hasłem. 

```powershell
AdHealthAddsAgentSetup.exe /quiet
Start-Sleep 30
$userName = "NEWUSER@DOMAIN"
$secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
$myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
 
Register-AzureADConnectHealthADDSAgent -Credential $myCreds

```

1. Gdy skończysz, możesz usunąć dostęp do konta lokalnego, wykonując jedną lub więcej z następujących czynności: 
    * Usuń przypisanie roli dla konta lokalnego dla programu AAD Connect Health
    * Obróć hasło do konta lokalnego. 
    * Wyłącz konto lokalne usługi AAD
    * Usuwanie konta lokalnego usługi AAD  

## <a name="agent-registration-using-powershell"></a>Rejestracja agenta przy użyciu programu PowerShell

Po zainstalowaniu agenta za pomocą odpowiedniego pliku setup.exe możesz przeprowadzić rejestrację agenta przy użyciu poniższych poleceń programu PowerShell w zależności od roli. Otwórz okno programu PowerShell i wykonaj odpowiednie polecenie:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

W tych poleceniach akceptowana jest wartość „Credential” jako parametr w celu ukończenia rejestracji w sposób nieinterakcyjny bądź na maszynie z systemem Server Core.
* Wartość Credential może być przechowywana w zmiennej programu PowerShell przekazywanej jako parametr.
* Możesz użyć dowolnej tożsamości usługi Azure AD, która ma dostęp do rejestracji agentów i dla której NIE WŁĄCZONO uwierzytelniania MFA.
* Domyślnie administratorzy globalni mają dostęp do przeprowadzenia rejestracji agenta. Na wykonanie tego kroku możesz również zezwolić innym, mniej uprzywilejowanym tożsamościom. Przeczytaj więcej [na temat kontroli dostępu opartej na rolach (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Konfigurowanie agentów programu Azure AD Connect Health na potrzeby korzystania z serwera proxy HTTP

Agentów programu Azure AD Connect Health możesz skonfigurować tak, aby współpracowali z serwerem proxy HTTP.

> [!NOTE]
> * Polecenie „Netsh WinHttp set ProxyServerAddress” nie jest obsługiwane, ponieważ do wysyłania żądań sieci web agent używa przestrzeni nazw System.Net zamiast usług HTTP systemu Microsoft Windows.
> * Skonfigurowany adres serwera proxy HTTP jest używany do przekazywania szyfrowanych komunikatów HTTPS.
> * Uwierzytelniane (przy użyciu metody HTTPBasic) serwery proxy nie są obsługiwane.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Zmienianie konfiguracji serwera proxy agenta kondycji

Agenta programu Azure AD Connect Health możesz skonfigurować na potrzeby korzystania z serwera proxy HTTP za pomocą poniższych opcji.

> [!NOTE]
> Aby ustawienia serwera proxy zostały zaktualizowane, musisz uruchomić ponownie wszystkie usługi agenta programu Azure AD Connect Health. Uruchom następujące polecenie:<br />
> Restart-Service AzureADConnectHealth *
>
>

#### <a name="import-existing-proxy-settings"></a>Importowanie istniejących ustawień serwera proxy

##### <a name="import-from-internet-explorer"></a>Importowanie z programu Internet Explorer

Ustawienia serwera HTTP programu Internet Explorer można zaimportować do użycia przez agentów programu Azure AD Connect Health. Na wszystkich serwerach z uruchomionym agentem programu Health wykonaj następujące polecenie programu PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

##### <a name="import-from-winhttp"></a>Importowanie z usług WinHTTP

Ustawienia serwera proxy WinHTTP można zaimportować do użycia przez agentów programu Azure AD Connect Health. Na wszystkich serwerach z uruchomionym agentem programu Health wykonaj następujące polecenie programu PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Ręczne określanie adresów serwerów proxy

Serwer proxy możesz określić ręcznie na każdym serwerze, na którym jest uruchomiony agent kondycji, wykonując następujące polecenie programu PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Przykład: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress mojserwerproxy: 443*

* Parametr „address” może być rozpoznawalną nazwą serwera w systemie DNS lub adresem IPv4.
* Parametr „port” można pominąć. W przypadku pominięcia jako domyślny zostanie wybrany port 443.

#### <a name="clear-existing-proxy-configuration"></a>Czyszczenie istniejącej konfiguracji serwera proxy

Istniejącą konfigurację serwera proxy możesz wyczyścić, uruchamiając następujące polecenie:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Odczytywanie bieżących ustawień serwera proxy

Możesz odczytać aktualnie skonfigurowane ustawienia serwera proxy, wykonując następujące polecenie:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testowanie łączności z usługą Azure AD Connect Health

Mogą wystąpić problemy powodujące utratę łączności agenta programu Azure AD Connect Health z usługą Azure AD Connect Health. Ich przyczyną mogą być problemy z siecią, problemy z uprawnieniami lub różne inne czynniki.

Jeśli agent nie może wysyłać danych do programu Azure AD Connect Health przez ponad 2 godziny, w portalu zostanie wyświetlony alert o treści „Dane usługi kondycji są nieaktualne”. Możesz sprawdzić, czy agent programu Azure AD Connect Health, którego dotyczy alert, jest w stanie przekazywać dane do usługi Azure AD Connect Health, wykonując następujące polecenie programu PowerShell:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

Parametr roli obecnie przyjmuje następujące wartości:

* ADFS
* Synchronizuj
* ADDS

> [!NOTE]
> Aby korzystać z narzędzia łączności, najpierw musisz zakończyć rejestrację agenta. Jeśli nie możesz zakończyć rejestracji agenta, upewnij się, że zostały spełnione wszystkie [wymagania](how-to-connect-health-agent-install.md#requirements) programu Azure AD Connect Health. Ten test łączności jest wykonywany domyślnie podczas rejestracji agenta.
>
>

## <a name="related-links"></a>Linki pokrewne

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Operacje w programie Azure AD Connect Health](how-to-connect-health-operations.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md)
* [Używanie Azure AD Connect Health do synchronizacji](how-to-connect-health-sync.md)
* [Używanie programu Azure AD Connect Health z usługami AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
* [Historia wersji Azure AD Connect Health](reference-connect-health-version-history.md)
