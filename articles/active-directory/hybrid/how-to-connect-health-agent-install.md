---
title: Instalowanie agentów programu Connect Health w Azure Active Directory
description: W tym Azure AD Connect Health artykule opisano instalację agenta dla Active Directory Federation Services (AD FS) i synchronizację.
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
ms.openlocfilehash: b680c275b92340cc7efba187769cb17602b08b45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973290"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalacja agenta Azure AD Connect Health

W tym artykule dowiesz się, jak zainstalować i skonfigurować agentów programu Azure Active Directory (Azure AD) Connect Health. Aby pobrać agentów, zobacz [te instrukcje](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Wymagania

W poniższej tabeli przedstawiono wymagania dotyczące korzystania z Azure AD Connect Health.

| Wymaganie | Opis |
| --- | --- |
| Azure AD — wersja Premium jest zainstalowany. |Azure AD Connect Health jest funkcją Azure AD — wersja Premium. Aby uzyskać więcej informacji, zobacz [Rejestrowanie się w usłudze Azure AD — wersja Premium](../fundamentals/active-directory-get-started-premium.md). <br /><br />Aby rozpocząć korzystanie z bezpłatnej 30-dniowej wersji próbnej, zobacz [Rozpoczynanie korzystania z wersji próbnej](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Jesteś administratorem globalnym w usłudze Azure AD. |Domyślnie tylko Administratorzy globalni mogą instalować i konfigurować agentów kondycji, uzyskiwać dostęp do portalu i wykonywać dowolne operacje w Azure AD Connect Health. Aby więcej informacji, zobacz [Administering your Azure AD directory](../fundamentals/active-directory-whatis.md) (Administrowanie katalogiem usługi Azure AD). <br /><br /> Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można zezwolić innym użytkownikom w organizacji na dostęp do Azure AD Connect Health. Aby uzyskać więcej informacji, zobacz [Azure RBAC dla Azure AD Connect Health](how-to-connect-health-operations.md#manage-access-with-azure-rbac). <br /><br />**Ważne**: Użyj konta służbowego, aby zainstalować agentów. Nie można użyć konto Microsoft. Aby uzyskać więcej informacji, zobacz [Rejestrowanie się w usłudze Azure jako organizacja](../fundamentals/sign-up-organization.md). |
| Agent Azure AD Connect Health jest instalowany na każdym serwerze dodanym. | Agenci kondycji muszą być zainstalowani i skonfigurowani na serwerach, aby mogły odbierać dane i zapewniać możliwości monitorowania i analizy. <br /><br />Na przykład aby uzyskać dane z infrastruktury Active Directory Federation Services (AD FS), należy zainstalować agenta na serwerze AD FS i serwerze proxy aplikacji sieci Web. Podobnie Aby uzyskać dane z infrastruktury lokalnej Azure AD Domain Services (Azure AD DS), należy zainstalować agenta na kontrolerach domeny.  |
| Punkty końcowe usługi platformy Azure mają łączność wychodzącą. | Podczas instalowania i w czasie pracy agent wymaga łączności z punktami końcowymi usługi Azure AD Connect Health. Jeśli zapory blokują łączność wychodzącą, Dodaj [punkty końcowe łączności wychodzącej](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) do listy dozwolonych. |
|Łączność wychodząca jest oparta na adresach IP. | Informacje o filtrowaniu zapory na podstawie adresów IP znajdują się w temacie [zakresy adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).|
| Inspekcja protokołu TLS dla ruchu wychodzącego jest filtrowana lub wyłączona. | Krok rejestracji agenta lub operacje przekazywania danych mogą się nie powieść, jeśli istnieje Inspekcja lub przerwa dla ruchu wychodzącego w warstwie sieciowej. Aby uzyskać więcej informacji, zobacz [Konfigurowanie inspekcji protokołu TLS](/previous-versions/tn-archive/ee796230(v=technet.10)). |
| Na portach zapory na serwerze jest uruchomiony Agent programu. |Agent wymaga otwarcia następujących portów zapory, aby mógł komunikować się z punktami końcowymi usługi Azure AD Connect Health: <br /><li>Port TCP 443</li><li>Port TCP 5671</li> <br />Najnowsza wersja agenta nie wymaga portu 5671. Uaktualnij do najnowszej wersji, aby wymagany był tylko port 443. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące portów i protokołów dla tożsamości hybrydowej](./reference-connect-ports.md). |
| W przypadku włączenia zwiększonych zabezpieczeń programu Internet Explorer Zezwalaj na określone witryny sieci Web.  |W przypadku włączenia zwiększonych zabezpieczeń programu Internet Explorer na serwerze, na którym jest instalowany Agent, należy zezwolić na następujące witryny sieci Web:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.NET</li><li>Serwer federacyjny dla organizacji, który jest zaufany przez usługę Azure AD (na przykład https: \/ /STS.contoso.com)</li> <br />Aby uzyskać więcej informacji, zobacz [jak skonfigurować program Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Jeśli masz serwer proxy w sieci, zobacz uwagę, która pojawia się na końcu tej tabeli.|
| Program PowerShell w wersji 4,0 lub nowszej jest zainstalowany. | System Windows Server 2012 zawiera program PowerShell w wersji 3,0. Ta wersja *nie* jest wystarczająca dla agenta.</br></br> System Windows Server 2012 R2 lub nowszy zawiera wystarczającą najnowszą wersję programu PowerShell.|
|FIPS (Federal Information Processing Standard) jest wyłączony.|Agenci Azure AD Connect Health nie obsługują standardu FIPS.|

> [!IMPORTANT]
> System Windows Server Core nie obsługuje instalowania agenta Azure AD Connect Health.


> [!NOTE]
> Jeśli masz wysoce zablokowanie i ograniczone środowisko, musisz dodać więcej adresów URL niż listy tabel dla zwiększonych zabezpieczeń programu Internet Explorer. Należy również dodać adresy URL, które są wymienione w tabeli w następnej sekcji.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Łączność wychodząca z punktami końcowymi usług Azure

Podczas instalacji i środowiska uruchomieniowego Agent musi mieć łączność z punktami końcowymi usługi Azure AD Connect Health. Jeśli zapory blokują łączność wychodzącą, upewnij się, że adresy URL w poniższej tabeli nie są domyślnie blokowane. 

Nie należy wyłączać monitorowania zabezpieczeń ani inspekcji tych adresów URL. Zamiast tego Zezwalaj na to, jak zezwolisz na inny ruch internetowy. 

Te adresy URL umożliwiają komunikację z punktami końcowymi usługi Azure AD Connect Health. W dalszej części tego artykułu dowiesz się, jak [sprawdzić łączność wychodzącą](#test-connectivity-to-azure-ad-connect-health-service) przy użyciu programu `Test-AzureADConnectHealthConnectivity` .

| Środowisko domeny | Wymagane punkty końcowe usług platformy Azure |
| --- | --- |
| Ogół publiczny | <li>&#42;.blob.core.windows.net </li><li>&#42;. aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-port: 5671 (ten punkt końcowy nie jest wymagany w najnowszej wersji agenta).</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (ten punkt końcowy jest używany tylko do celów odnajdywania podczas rejestracji).</li> |
| Azure (Niemcy) | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.Office.de (ten punkt końcowy jest używany tylko do celów odnajdywania podczas rejestracji).</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (ten punkt końcowy jest używany tylko do celów odnajdywania podczas rejestracji).</li> |


## <a name="install-the-agent"></a>Instalowanie agenta

Aby pobrać i zainstalować agenta Azure AD Connect Health: 

* Upewnij się, że spełniasz [wymagania](how-to-connect-health-agent-install.md#requirements) Azure AD Connect Health.
* Rozpocznij korzystanie z Azure AD Connect Health dla AD FS:
    * [Pobierz agenta Azure AD Connect Health dla AD FS](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Zapoznaj się z [instrukcjami dotyczącymi instalacji](#install-the-agent-for-ad-fs).
* Rozpocznij korzystanie z Azure AD Connect Health do synchronizacji:
    * [Pobierz i zainstaluj najnowszą wersję programu Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). Agent kondycji do celów synchronizacji jest instalowany w ramach instalacji Azure AD Connect (w wersji 1.0.9125.0 lub nowszej).
* Rozpocznij korzystanie z Azure AD Connect Health dla AD DS platformy Azure:
    * [Pobierz agenta Azure AD Connect Health dla AD DS platformy Azure](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Zapoznaj się z [instrukcjami dotyczącymi instalacji](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>Zainstaluj agenta dla AD FS

> [!NOTE]
> Serwer AD FS powinien być inny niż serwer synchronizacji. Na serwerze synchronizacji nie należy instalować agenta AD FS.
>

Przed zainstalowaniem agenta upewnij się, że nazwa hosta serwera AD FS jest unikatowa i nie występuje w usłudze AD FS.
Aby rozpocząć instalację agenta, kliknij dwukrotnie pobrany plik *exe* . W pierwszym oknie wybierz pozycję **Zainstaluj**.

![Zrzut ekranu przedstawiający okno instalacji agenta AD FS Azure AD Connect Health.](./media/how-to-connect-health-agent-install/install1.png)

Po zakończeniu instalacji wybierz pozycję **Konfiguruj teraz**.

![Zrzut ekranu przedstawiający komunikat z potwierdzeniem instalacji agenta AD FS Azure AD Connect Health.](./media/how-to-connect-health-agent-install/install2.png)

Zostanie otwarte okno programu PowerShell, w którym rozpocznie się proces rejestracji agenta. Po wyświetleniu monitu zaloguj się przy użyciu konta usługi Azure AD, które ma uprawnienia do rejestracji agenta. Domyślnie konto administratora globalnego ma uprawnienia.

![Zrzut ekranu przedstawiający okno logowania Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install3.png)

Po zalogowaniu program PowerShell kontynuuje działanie. Po zakończeniu można zamknąć program PowerShell. Konfiguracja została ukończona.

W tym momencie usługi agenta powinny być uruchamiane automatycznie, aby umożliwić agentowi bezpieczne przekazanie wymaganych danych do usługi w chmurze.

Jeśli wszystkie wymagania wstępne nie zostały spełnione, w oknie programu PowerShell są wyświetlane ostrzeżenia. Przed zainstalowaniem agenta upewnij się, że zostały [spełnione wymagania](how-to-connect-health-agent-install.md#requirements) . Poniższy zrzut ekranu przedstawia przykład tych ostrzeżeń.

![Zrzut ekranu przedstawiający Azure AD Connect Health AD FS konfigurowania skryptu.](./media/how-to-connect-health-agent-install/install4.png)

Aby sprawdzić, czy Agent został zainstalowany, poszukaj następujących usług na serwerze. Jeśli konfiguracja została zakończona, te usługi powinny być uruchomione. W przeciwnym razie są one zatrzymane do momentu zakończenia konfiguracji.

* Usługa diagnostyczna usług AD FS programu Azure AD Connect Health
* Usługa szczegółowych informacji usług AD FS programu Azure AD Connect Health
* Usługa monitorowania usług AD FS programu Azure AD Connect Health

![Zrzut ekranu przedstawiający Azure AD Connect Health AD FS Services.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Włącz inspekcję dla AD FS

> [!NOTE]
> Ta sekcja dotyczy tylko serwerów AD FS. Nie musisz wykonywać tych kroków na serwerach proxy aplikacji sieci Web.
>

Funkcja analizy użycia musi zbierać i analizować dane. Agent Azure AD Connect Health potrzebuje informacji w dziennikach inspekcji AD FS. Te dzienniki nie są domyślnie włączone. Poniższe procedury służą do włączania inspekcji AD FS i lokalizowania AD FS dzienników inspekcji na serwerach AD FS.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Aby włączyć inspekcję usług AD FS w systemie Windows Server 2012 R2

1. Na ekranie startowym Otwórz **Menedżer serwera**, a następnie otwórz pozycję **zasady zabezpieczeń lokalnych**. Lub na pasku zadań Otwórz **Menedżer serwera**, a następnie wybierz pozycję **narzędzia/zasady zabezpieczeń lokalnych**.
2. Przejdź do folderu *Przypisywanie praw zabezpieczeń \ zasady lokalne\przypisanie* . Następnie kliknij dwukrotnie pozycję **Generuj inspekcje zabezpieczeń**.
3. Na karcie **Ustawianie zabezpieczeń lokalnych** sprawdź, czy jest wymienione konto usługi AD FS. Jeśli nie ma go na liście, wybierz pozycję **Dodaj użytkownika lub grupę**, a następnie dodaj ją do listy. Następnie wybierz przycisk **OK**.
4. Aby włączyć inspekcję, Otwórz okno wiersza polecenia z podwyższonym poziomem uprawnień. Uruchom następujące polecenie: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Zamknij okno **Zasady zabezpieczeń lokalnych**.
    >[!Important]
    >Poniższe kroki są wymagane tylko w przypadku serwerów głównych AD FS. 
1. Otwórz przystawkę **Zarządzanie usługami AD FS**. (W **Menedżer serwera** wybierz pozycję **Narzędzia**  >  **Zarządzanie AD FSą**).
1. W okienku **Akcje** wybierz pozycję **Edytuj usługa federacyjna właściwości**.
1. W oknie dialogowym **właściwości usługa federacyjna** wybierz kartę **zdarzenia** .
1. Zaznacz pola wyboru **inspekcje zakończone sukcesem i inspekcje niepowodzeń** , a następnie wybierz przycisk **OK**.
1. Aby włączyć pełne rejestrowanie za pomocą programu PowerShell, użyj następującego polecenia: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Aby włączyć inspekcję usług AD FS w systemie Windows Server 2016

1. Na ekranie startowym Otwórz **Menedżer serwera**, a następnie otwórz pozycję **zasady zabezpieczeń lokalnych**. Lub na pasku zadań Otwórz **Menedżer serwera**, a następnie wybierz pozycję **narzędzia/zasady zabezpieczeń lokalnych**.
2. Przejdź do folderu *\ zasady Security Lokalne\przypisanie Rights przypisywania praw* , a następnie kliknij dwukrotnie pozycję **Generuj inspekcje zabezpieczeń**.
3. Na karcie **Ustawianie zabezpieczeń lokalnych** sprawdź, czy jest wymienione konto usługi AD FS. Jeśli nie ma go na liście, wybierz pozycję **Dodaj użytkownika lub grupę**, a następnie Dodaj do listy konto usługi AD FS. Następnie wybierz przycisk **OK**.
4. Aby włączyć inspekcję, Otwórz okno wiersza polecenia z podwyższonym poziomem uprawnień. Uruchom następujące polecenie: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Zamknij okno **Zasady zabezpieczeń lokalnych**.
    >[!Important]
    >Poniższe kroki są wymagane tylko w przypadku serwerów głównych AD FS.
1. Otwórz przystawkę **Zarządzanie usługami AD FS**. (W **Menedżer serwera** wybierz pozycję **Narzędzia**  >  **Zarządzanie AD FSą**).
1. W okienku **Akcje** wybierz pozycję **Edytuj usługa federacyjna właściwości**.
1. W oknie dialogowym **właściwości usługa federacyjna** wybierz kartę **zdarzenia** .
1. Zaznacz pola wyboru **inspekcje zakończone sukcesem i inspekcje niepowodzeń** , a następnie wybierz przycisk **OK**. Inspekcje sukcesów i inspekcje błędów powinny być domyślnie włączone.
1. Otwórz okno programu PowerShell i uruchom następujące polecenie: 

    `Set-AdfsProperties -AuditLevel Verbose`

Poziom inspekcji "podstawowa" jest domyślnie włączony. Aby uzyskać więcej informacji, zobacz [AD FS ulepszenie inspekcji w systemie Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Aby zlokalizować dzienniki inspekcji usług AD FS

1. Otwórz **Podgląd zdarzeń**.
2. Przejdź do pozycji **Dzienniki systemu Windows**, a następnie wybierz pozycję **zabezpieczenia**.
3. Po prawej stronie wybierz pozycję **Filtruj bieżące dzienniki**.
4. W obszarze **źródła zdarzeń** wybierz pozycję **Inspekcja AD FS**.

    Aby uzyskać więcej informacji na temat dzienników inspekcji, zobacz [pytania dotyczące operacji](reference-connect-health-faq.md#operations-questions).

    ![Zrzut ekranu przedstawiający okno Filtruj bieżący dziennik. W polu "źródła zdarzeń" jest zaznaczona wartość "AD FS inspekcja".](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Zasady grupy mogą wyłączyć inspekcję usług AD FS. Jeśli inspekcja AD FS jest wyłączona, analiza użycia informacji o działaniach związanych z logowaniem jest niedostępna. Upewnij się, że nie masz zasad grupy, które uniemożliwiają inspekcję AD FS.
>


## <a name="install-the-agent-for-sync"></a>Zainstaluj agenta na potrzeby synchronizacji

Agent Azure AD Connect Health dla synchronizacji jest instalowany automatycznie w najnowszej wersji programu Azure AD Connect. Aby użyć Azure AD Connect do synchronizacji, [Pobierz najnowszą wersję Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) i zainstaluj ją.

Aby sprawdzić, czy agent został zainstalowany, znajdź następujące usługi na serwerze: Jeśli konfiguracja została ukończona, usługi powinny być już uruchomione. W przeciwnym razie usługi zostaną zatrzymane do momentu zakończenia konfiguracji.

* Usługa szczegółowych informacji synchronizacji programu Azure AD Connect Health
* Usługa monitorowania synchronizacji programu Azure AD Connect Health

![Zrzut ekranu przedstawiający uruchomioną Azure AD Connect Health dla usług synchronizacji na serwerze.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Pamiętaj, że musisz mieć Azure AD — wersja Premium, aby użyć Azure AD Connect Health. Jeśli nie masz Azure AD — wersja Premium, nie możesz ukończyć konfiguracji w Azure Portal. Aby uzyskać więcej informacji, zobacz [wymagania](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Ręcznie Zarejestruj Azure AD Connect Health na potrzeby synchronizacji

Jeśli Azure AD Connect Health rejestracji agenta synchronizacji zakończy się niepowodzeniem po pomyślnym zainstalowaniu Azure AD Connect, można użyć polecenia programu PowerShell, aby ręcznie zarejestrować agenta.

> [!IMPORTANT]
> Tego polecenia programu PowerShell należy użyć tylko wtedy, gdy rejestracja agenta kończy się niepowodzeniem po zainstalowaniu Azure AD Connect.
>
>

Ręcznie Zarejestruj agenta Azure AD Connect Health na potrzeby synchronizacji przy użyciu następującego polecenia programu PowerShell. Usługi programu Azure AD Connect Health zostaną uruchomione po pomyślnym zarejestrowaniu agenta.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Polecenie przyjmuje następujące parametry:

* **AttributeFiltering**: `$true` (wartość domyślna), jeśli Azure AD Connect nie synchronizuje domyślnego zestawu atrybutów i został dostosowany do korzystania z filtrowanego zestawu atrybutów. W przeciwnym razie użyj `$false` .
* **Przejściowy**: `$false` (domyślnie), jeśli serwer Azure AD Connect *nie* jest w trybie przejściowym. Jeśli serwer jest skonfigurowany do pracy w trybie przejściowym, użyj `$true` .

Gdy zostanie wyświetlony monit o uwierzytelnienie, użyj tego samego konta administratora globalnego (na przykład admin@domain.onmicrosoft.com ), które zostało użyte do skonfigurowania Azure AD Connect.

## <a name="install-the-agent-for-azure-ad-ds"></a>Zainstaluj agenta dla usługi Azure AD DS

Aby rozpocząć instalację agenta, kliknij dwukrotnie pobrany plik *exe* . W pierwszym oknie wybierz pozycję **Zainstaluj**.

![Zrzut ekranu przedstawiający okno Azure AD Connect Health agenta dla AD DS instalacji.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Po zakończeniu instalacji wybierz pozycję **Konfiguruj teraz**.

![Zrzut ekranu przedstawiający okno, w którym kończy się Instalacja agenta Azure AD Connect Health dla AD DS platformy Azure.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Zostanie otwarte okno wiersza polecenia. Działa program PowerShell `Register-AzureADConnectHealthADDSAgent` . Po wyświetleniu monitu zaloguj się do platformy Azure.

![Zrzut ekranu przedstawiający okno logowania agenta Azure AD Connect Health dla AD DS platformy Azure.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Po zalogowaniu program PowerShell kontynuuje działanie. Po zakończeniu można zamknąć program PowerShell. Konfiguracja została ukończona.

W tym momencie usługi powinny być uruchamiane automatycznie, co pozwala agentowi monitorować i zbierać dane. Jeśli nie zostały spełnione wszystkie wymagania wstępne opisane w poprzednich sekcjach, w oknie programu PowerShell są wyświetlane ostrzeżenia. Przed zainstalowaniem agenta upewnij się, że zostały [spełnione wymagania](how-to-connect-health-agent-install.md#requirements) . Poniższy zrzut ekranu przedstawia przykład tych ostrzeżeń.

![Zrzut ekranu przedstawiający ostrzeżenie dotyczące Azure AD Connect Health agenta usługi Azure AD DS Configuration.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Aby sprawdzić, czy Agent jest zainstalowany, Wyszukaj następujące usługi na kontrolerze domeny:

* Usługa szczegółowych informacji usług AD DS programu Azure AD Connect Health
* Usługa monitorowania usług AD DS programu Azure AD Connect Health

Jeśli konfiguracja została zakończona, te usługi powinny być uruchomione. W przeciwnym razie są one zatrzymane do momentu zakończenia konfiguracji.

![Zrzut ekranu przedstawiający uruchomione usługi na kontrolerze domeny.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>Szybka instalacja agenta na wielu serwerach

1. Utwórz konto użytkownika w usłudze Azure AD. Zabezpiecz go przy użyciu hasła.
2. Przypisz rolę **właściciela** dla tego lokalnego konta usługi Azure AD w Azure AD Connect Health przy użyciu portalu. Wykonaj [te kroki](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Przypisz rolę do wszystkich wystąpień usługi. 
3. Pobierz plik msi *. exe* na lokalny kontroler domeny w celu instalacji.
4. Uruchom poniższy skrypt. Zastąp parametry nowym kontem użytkownika i jego hasłem. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

Po zakończeniu możesz usunąć dostęp do konta lokalnego, wykonując co najmniej jedno z następujących zadań: 
* Usuń przypisanie roli dla konta lokalnego Azure AD Connect Health.
* Obróć hasło do konta lokalnego. 
* Wyłącz konto lokalne usługi Azure AD.
* Usuń konto lokalne usługi Azure AD.  

## <a name="register-the-agent-by-using-powershell"></a>Rejestrowanie agenta przy użyciu programu PowerShell

Po zainstalowaniu odpowiedniego pliku *setup.exe* agenta można zarejestrować agenta za pomocą następujących poleceń programu PowerShell, w zależności od roli. Otwórz okno programu PowerShell i uruchom odpowiednie polecenie:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Te polecenia akceptują `Credential` jako parametr w celu przeprowadzenia rejestracji w trybie nieinteraktywnym lub do ukończenia rejestracji na komputerze z uruchomionym rdzeniem serwera. Należy pamiętać, że:
* Można przechwytywać `Credential` w zmiennej programu PowerShell, która jest przenoszona jako parametr.
* Możesz podać dowolną tożsamość usługi Azure AD, która ma uprawnienia do rejestrowania agentów, dla których *nie* włączono uwierzytelniania wieloskładnikowego.
* Domyślnie Administratorzy globalni mają uprawnienia do rejestrowania agentów. Aby wykonać ten krok, można również zezwolić na tożsamości o niższych uprawnieniach. Aby uzyskać więcej informacji, zobacz [Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Konfigurowanie agentów Azure AD Connect Health do korzystania z serwera proxy HTTP

Można skonfigurować agentów Azure AD Connect Health do pracy z serwerem proxy HTTP.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` nie jest obsługiwana. Agent używa System.Net zamiast usług HTTP systemu Windows, aby wykonywać żądania sieci Web.
> * Skonfigurowany adres serwera proxy HTTP jest używany do przekazywania szyfrowanych komunikatów HTTPS.
> * Uwierzytelniane (przy użyciu metody HTTPBasic) serwery proxy nie są obsługiwane.
>
>

### <a name="change-the-agent-proxy-configuration"></a>Zmiana konfiguracji serwera proxy agenta

Aby skonfigurować agenta Azure AD Connect Health do korzystania z serwera proxy HTTP, można:
* Importuj istniejące ustawienia serwera proxy.
* Ręcznie Określ adresy serwerów proxy.
* Wyczyść istniejącą konfigurację serwera proxy.

> [!NOTE]
> Aby zaktualizować ustawienia serwera proxy, należy ponownie uruchomić wszystkie usługi agenta Azure AD Connect Health. Uruchom następujące polecenie:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Importuj istniejące ustawienia serwera proxy

Można zaimportować ustawienia serwera proxy HTTP programu Internet Explorer, aby agenci Azure AD Connect Health mogli korzystać z tych ustawień. Na każdym serwerze, na którym jest uruchomiony Agent kondycji, uruchom następujące polecenie programu PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

Ustawienia serwera proxy WinHTTP można zaimportować, aby agenci Azure AD Connect Health mogli ich używać. Na każdym serwerze, na którym jest uruchomiony Agent kondycji, uruchom następujące polecenie programu PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Ręczne Określanie adresów serwera proxy

Można ręcznie określić serwer proxy. Na każdym serwerze, na którym jest uruchomiony Agent kondycji, uruchom następujące polecenie programu PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Oto przykład: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

W tym przykładzie:
* `address`Ustawienie może być rozpoznawaną przez system DNS nazwą serwera lub adresem IPv4.
* Możesz pominąć `port` . Jeśli to zrobisz, następnie 443 jest portem domyślnym.

#### <a name="clear-the-existing-proxy-configuration"></a>Wyczyść istniejącą konfigurację serwera proxy

Istniejącą konfigurację serwera proxy możesz wyczyścić, uruchamiając następujące polecenie:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Odczytywanie bieżących ustawień serwera proxy

Bieżące ustawienia serwera proxy można odczytać, uruchamiając następujące polecenie:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testowanie łączności z usługą Azure AD Connect Health

Czasami agent Azure AD Connect Health może utracić łączność z usługą Azure AD Connect Health. Przyczyną utraty łączności mogą być problemy z siecią, problemy z uprawnieniami i różne inne problemy.

Jeśli Agent nie może wysyłać danych do usługi Azure AD Connect Health przez dłużej niż dwie godziny, w portalu pojawi się następujący alert: "dane Usługa kondycji nie są aktualne". 

Aby dowiedzieć się, czy Agent Azure AD Connect Health, którego to dotyczy, można przekazać dane do usługi Azure AD Connect Health, uruchamiając następujące polecenie programu PowerShell:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

Parametr roli obecnie przyjmuje następujące wartości:

* ADFS
* Synchronizuj
* ADDS

> [!NOTE]
> Aby korzystać z narzędzia łączności, należy najpierw zarejestrować agenta. Jeśli nie możesz zakończyć rejestracji agenta, upewnij się, że zostały spełnione wszystkie [wymagania](how-to-connect-health-agent-install.md#requirements) dotyczące Azure AD Connect Health. Łączność jest domyślnie testowana podczas rejestracji agenta.
>
>

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami:

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Operacje Azure AD Connect Health](how-to-connect-health-operations.md)
* [Używanie programu Azure AD Connect Health z usługami AD FS](how-to-connect-health-adfs.md)
* [Używanie programu Azure AD Connect Health w celu synchronizacji](how-to-connect-health-sync.md)
* [Używanie Azure AD Connect Health z usługą Azure AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health — często zadawane pytania](reference-connect-health-faq.md)
* [Historia wersji programu Azure AD Connect Health](reference-connect-health-version-history.md)
