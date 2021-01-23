---
title: Problemy z konfiguracją i zarządzaniem — często zadawane pytania
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących konfiguracji i zarządzania dla Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c5dd09292897d69f90606e8661b4e6cb28090612
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742594"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problemy związane z konfiguracją i zarządzaniem w usłudze Azure Cloud Services (wersja klasyczna): często zadawane pytania

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

W tym artykule opisano często zadawane pytania dotyczące problemów z konfiguracją i zarządzaniem w programie [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informacje o rozmiarze można także znaleźć na [stronie Cloud Services rozmiaru maszyny wirtualnej](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certyfikaty**

- [Dlaczego łańcuch certyfikatów protokołu TLS/SSL usługi w chmurze jest niekompletny?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Jaki jest cel "certyfikatu szyfrowania narzędzi systemu Windows Azure dla rozszerzeń"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Jak można wygenerować żądanie podpisania certyfikatu (CSR) bez "RDP-owanie" w tym wystąpieniu?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Mój certyfikat zarządzania usługą w chmurze wygasa. Jak odnowić?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Jak zautomatyzować instalację głównego certyfikatu TLS/SSL (pfx) i certyfikatu pośredniego (. p7b)?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Jaki jest cel certyfikatu "Microsoft Azure Service Management for MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorowanie i rejestrowanie**

- [Jakie są przyszłe możliwości usług w chmurze w Azure Portal, które mogą pomóc w zarządzaniu aplikacjami i ich monitorowaniu?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Dlaczego usługi IIS zatrzymują zapisywanie do katalogu dziennika?](#why-does-iis-stop-writing-to-the-log-directory)
- [Jak mogę włączyć rejestrowanie funkcji wad dla Cloud Services?](#how-do-i-enable-wad-logging-for-cloud-services)

**Konfiguracja sieci**

- [Jak mogę ustawić limit czasu bezczynności dla modułu równoważenia obciążenia platformy Azure?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Jak mogę skojarzyć statyczny adres IP z moją usługą w chmurze?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Jakie są funkcje i możliwości, które zapewnia usługa Azure Basic adresy IP/identyfikatory i DDOS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Jak włączyć protokół HTTP/2 na maszynie Cloud Services VM?](#how-to-enable-http2-on-cloud-services-vm)

**Uprawnienia**

- [Czy program Microsoft Internal inżynierowie mogą uzyskać dostęp do wystąpień usługi w chmurze bez uprawnień?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Nie mogę uzyskać pulpitu zdalnego z maszyną wirtualną usługi w chmurze przy użyciu pliku RDP. Pojawia się następujący błąd: Wystąpił błąd uwierzytelniania (kod: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Skalowanie**

- [Nie mogę skalować ponad wystąpieniami X](#i-cannot-scale-beyond-x-instances)
- [Jak można skonfigurować automatyczne skalowanie na podstawie metryk pamięci?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Ogólny**

- [Jak mogę dodać `nosniff` do mojej witryny sieci Web?](#how-do-i-add-nosniff-to-my-website)
- [Jak mogę dostosować usługi IIS dla roli sieci Web?](#how-do-i-customize-iis-for-a-web-role)
- [Jaki jest limit przydziału dla usługi w chmurze?](#what-is-the-quota-limit-for-my-cloud-service)
- [Dlaczego dysk na maszynie wirtualnej usługi w chmurze pokazuje bardzo mało wolnego miejsca na dysku?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Jak mogę dodać rozszerzenie chroniące przed złośliwym kodem dla mojego Cloud Services w sposób zautomatyzowany?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Jak włączyć Oznaczanie nazwy serwera (SNI) dla Cloud Services?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Jak dodać tagi do usługi w chmurze systemu Azure?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [W Azure Portal nie jest wyświetlana wersja zestawu SDK mojej usługi w chmurze. Jak można to zrobić?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Chcę zamknąć usługę w chmurze przez kilka miesięcy. Jak zmniejszyć koszt rozliczania usługi w chmurze bez utraty adresu IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certyfikaty

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Dlaczego łańcuch certyfikatów protokołu TLS/SSL usługi w chmurze jest niekompletny?
    
Zalecamy, aby klienci zainstalowali pełny łańcuch certyfikatów (certyfikat liścia, certyfikaty pośrednie i certyfikat główny) zamiast tylko certyfikatu liścia. W przypadku instalowania tylko certyfikatu liścia w systemie Windows można utworzyć łańcuch certyfikatów, przełączając listę CTL. Jeśli wystąpią problemy z siecią lub usługą DNS na platformie Azure lub Windows Update, gdy system Windows próbuje zweryfikować certyfikat, certyfikat może być uznawany za nieprawidłowy. Zainstalowanie pełnego łańcucha certyfikatów może uniknąć problemu. W blogu dotyczącym [sposobu instalowania łańcucha certyfikatów SSL](/archive/blogs/azuredevsupport/how-to-install-a-chained-ssl-certificate) pokazano, jak to zrobić.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Jaki jest cel "certyfikatu szyfrowania narzędzi systemu Windows Azure dla rozszerzeń"?

Te certyfikaty są tworzone automatycznie za każdym razem, gdy rozszerzenie zostanie dodane do usługi w chmurze. Najczęściej jest to rozszerzenie funkcji wad lub rozszerzenie RDP, ale może to być inne, takie jak rozszerzenie programu do ochrony przed złośliwym kodem lub modułu zbierającego dzienniki. Te certyfikaty są używane tylko do szyfrowania i odszyfrowywania konfiguracji prywatnej dla rozszerzenia. Data wygaśnięcia nigdy nie jest zaznaczona, dlatego nie ma znaczenia, czy certyfikat wygasł. 

Można zignorować te certyfikaty. Jeśli chcesz wyczyścić certyfikaty, możesz spróbować usunąć je wszystkie. Jeśli spróbujesz usunąć używany certyfikat, platforma Azure zgłosi błąd.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Jak można wygenerować żądanie podpisania certyfikatu (CSR) bez "RDP-owanie" w tym wystąpieniu?

Zapoznaj się z poniższymi wskazówkami:

[Uzyskiwanie certyfikatu do użycia z witrynami sieci Web systemu Windows Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR jest tylko plikiem tekstowym. Nie trzeba go tworzyć na komputerze, na którym certyfikat będzie ostatecznie używany.Mimo że ten dokument jest przeznaczony dla App Service, tworzenie CSR jest ogólne i ma zastosowanie również do Cloud Services.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Mój certyfikat zarządzania usługą w chmurze wygasa. Jak odnowić?

Aby odnowić certyfikaty zarządzania, można użyć następujących poleceń programu PowerShell:

```powershell
Add-AzureAccount
Select-AzureSubscription -Current -SubscriptionName <your subscription name>
Get-AzurePublishSettingsFile
```

Element **Get-AzurePublishSettingsFile** utworzy nowy certyfikat zarządzania w ramach   >  **certyfikatów zarządzania** subskrypcjami w Azure Portal. Nazwa nowego certyfikatu wygląda jak "YourSubscriptionNam]-[CurrentDate]-Credentials".

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Jak zautomatyzować instalację głównego certyfikatu TLS/SSL (pfx) i certyfikatu pośredniego (. p7b)?

Można zautomatyzować to zadanie przy użyciu skryptu uruchamiania (Batch/cmd/PowerShell) i zarejestrować ten skrypt uruchamiania w pliku definicji usługi. Dodaj skrypt uruchamiania i certyfikat (plik. p7b) w folderze projektu tego samego katalogu skryptu uruchomieniowego.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Jaki jest cel certyfikatu "Microsoft Azure Service Management for MachineKey"?

Ten certyfikat służy do szyfrowania kluczy komputera w rolach sieci Web platformy Azure. Aby dowiedzieć się więcej, zapoznaj się z [tym poradnikiem](/security-updates/securityadvisories/2018/4092731).

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- [Jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze](./cloud-services-startup-tasks.md)
- [Typowe zadania uruchamiania usługi w chmurze](./cloud-services-startup-tasks-common.md)

## <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Jakie są przyszłe możliwości usług w chmurze w Azure Portal, które mogą pomóc w zarządzaniu aplikacjami i ich monitorowaniu?

Możliwość wygenerowania nowego certyfikatu dla Remote Desktop Protocol (RDP) będzie dostępna wkrótce. Możesz również uruchomić następujący skrypt:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Możliwość wyboru obiektu BLOB lub lokalnego dla lokalizacji przekazywania csdef i cscfg będzie dostępna wkrótce. Za pomocą polecenia [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment?view=azuresmps-4.0.0&preserve-view=true)można ustawić każdą wartość lokalizacji.

Możliwość monitorowania metryk na poziomie wystąpienia. Dodatkowe możliwości monitorowania są dostępne w temacie [Jak monitorować Cloud Services](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Dlaczego usługi IIS zatrzymują zapisywanie do katalogu dziennika?
Przekroczono limit przydziału magazynu lokalnego do zapisu w katalogu dzienników.Aby rozwiązać ten problemy, można wykonać jedną z trzech czynności:
* Włączanie diagnostyki usług IIS i okresowe przenoszenie diagnostyki do magazynu obiektów BLOB.
* Ręcznie usuń pliki dziennika z katalogu rejestrowania.
* Zwiększ limit przydziału dla zasobów lokalnych.

Więcej informacji można znaleźć w następujących dokumentach:
* [Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage](../storage/common/storage-introduction.md)
* [Dzienniki usług IIS zatrzymują zapisywanie w usłudze w chmurze](/archive/blogs/cie/iis-logs-stops-writing-in-cloud-service)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Jak mogę włączyć rejestrowanie funkcji wad dla Cloud Services?
Możesz włączyć rejestrowanie w systemie Windows Diagnostyka Azure (funkcji wad) przy użyciu następujących opcji:
1. [Włącz z programu Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Włącz za poorednictwem kodu platformy .NET](./cloud-services-dotnet-diagnostics.md)
3. [Włącz za poorednictwem programu PowerShell](./cloud-services-diagnostics-powershell.md)

Aby uzyskać bieżące ustawienia funkcji wad usługi w chmurze, można użyć polecenia programu PowerShell [Get-AzureServiceDiagnosticsExtensions](./cloud-services-diagnostics-powershell.md#get-current-diagnostics-extension-configuration) lub wyświetlić go za pośrednictwem portalu z bloku "Cloud Services--> Extensions".


## <a name="network-configuration"></a>Konfiguracja sieci

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Jak mogę ustawić limit czasu bezczynności dla modułu równoważenia obciążenia platformy Azure?
Limit czasu można określić w pliku definicji usługi (csdef) w następujący sposób:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Zobacz [Nowy: konfigurowalny limit czasu bezczynności dla Azure Load Balancer](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) , aby uzyskać więcej informacji.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Jak mogę skojarzyć statyczny adres IP z moją usługą w chmurze?
Aby skonfigurować statyczny adres IP, należy utworzyć zastrzeżoną wartość adresu IP. Ten zastrzeżony adres IP można skojarzyć z nową usługą w chmurze lub istniejącym wdrożeniem. Aby uzyskać szczegółowe informacje, zobacz następujące dokumenty:
* [Jak utworzyć zastrzeżony adres IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Zarezerwuj adres IP istniejącej usługi w chmurze](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Skojarz zastrzeżony adres IP z nową usługą w chmurze](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Skojarz zastrzeżony adres IP z uruchomionym wdrożeniem](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Kojarzenie zastrzeżonego adresu IP z usługą w chmurze przy użyciu pliku konfiguracji usługi](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Jakie są funkcje i możliwości, które zapewnia usługa Azure Basic adresy IP/identyfikatory i DDOS?
Platforma Azure ma adresy IP/identyfikatory na serwerach fizycznych centrów danych, aby chronić je przed zagrożeniami. Ponadto klienci mogą wdrażać rozwiązania zabezpieczeń innych firm, takie jak zapory aplikacji sieci Web, zapory sieciowe, ochrona przed złośliwym oprogramowaniem, Wykrywanie intruzów, systemy zapobiegania (identyfikatory/adresy IP) i nie tylko. Aby uzyskać więcej informacji, zobacz [Ochrona danych i zasobów oraz zgodność z globalnymi standardami zabezpieczeń](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Firma Microsoft stale monitoruje serwery, sieci i aplikacje w celu wykrywania zagrożeń. Podejście do zarządzania zagrożeniami w systemie Azure multipronged używa wykrywania intruzów, rozproszonego ataku typu "odmowa usługi" (DDoS), testowania penetracji, analizy zachowań, wykrywania anomalii i uczenia maszynowego, aby stale wzmacniać ochronę i ograniczyć ryzyko. Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure chroni Cloud Services i maszyny wirtualne platformy Azure. Istnieje możliwość wdrożenia rozwiązań zabezpieczeń innych firm, takich jak ściany aplikacji sieci Web, zapory sieci, ochrona przed złośliwym kodem, systemy wykrywania i zapobiegania włamaniu (identyfikatory/adresy IP) itd.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Jak włączyć protokół HTTP/2 na maszynie Cloud Services VM?

Systemy Windows 10 i Windows Server 2016 są dostarczane z obsługą protokołu HTTP/2 zarówno po stronie klienta, jak i serwera. Jeśli klient (przeglądarka) nawiązuje połączenie z serwerem IIS za pośrednictwem protokołu TLS, który negocjuje protokół HTTP/2 za pośrednictwem rozszerzeń TLS, nie trzeba wprowadzać żadnych zmian po stronie serwera. Wynika to z faktu, że za pośrednictwem protokołu TLS jest domyślnie wysyłany nagłówek H2-14 określający użycie protokołu HTTP/2. Jeśli z drugiej strony klient wysyła nagłówek uaktualnienia w celu uaktualnienia do protokołu HTTP/2, należy wprowadzić zmianę poniżej na stronie serwera, aby upewnić się, że uaktualnienie działa i zostanie zakończone połączenie HTTP/2. 

1. Uruchom regedit.exe.
2. Przejdź do klucza rejestru: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Utwórz nową wartość DWORD o nazwie **DuoEnabled**.
4. Ustaw jej wartość na 1.
5. Uruchom ponownie serwer.
6. Przejdź do **domyślnej witryny sieci Web** i w obszarze **powiązania** Utwórz nowe powiązanie protokołu TLS z utworzonym certyfikatem z podpisem własnym. 

Aby uzyskać więcej informacji, zobacz:

- [Protokół HTTP/2 w usługach IIS](https://blogs.iis.net/davidso/http2)
- [Wideo: HTTP/2 w systemie Windows 10: przeglądarka, aplikacje i serwer sieci Web](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Te kroki można zautomatyzować za pomocą zadania uruchamiania, dzięki czemu po każdym utworzeniu nowego wystąpienia PaaS można wykonać powyższe zmiany w rejestrze systemowym. Aby uzyskać więcej informacji, zobacz [jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze](cloud-services-startup-tasks.md).

 
Po wykonaniu tej czynności można sprawdzić, czy protokół HTTP/2 został włączony, czy nie, korzystając z jednej z następujących metod:

- Włącz wersję protokołu w dziennikach usług IIS i zapoznaj się z dziennikami usług IIS. W dziennikach zostanie wyświetlony protokół HTTP/2. 
- Włącz narzędzie deweloperskie F12 w przeglądarce Internet Explorer lub Microsoft Edge i przejdź do karty sieć, aby zweryfikować protokół. 

Aby uzyskać więcej informacji, zobacz [http/2 w usługach IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Uprawnienia

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Jak można zaimplementować dostęp oparty na rolach dla Cloud Services?
Cloud Services nie obsługuje modelu kontroli dostępu opartej na rolach (Azure RBAC), ponieważ nie jest to usługa oparta na Azure Resource Manager.

Zobacz temat [Omówienie różnych ról na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Pulpit zdalny

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Czy program Microsoft Internal inżynierowie mogą uzyskać dostęp do wystąpień usługi w chmurze bez uprawnień?
Firma Microsoft stosuje się do ścisłego procesu, który nie zezwoli wewnętrznym inżynierom na zdalny dostęp do usługi w chmurze bez pisemnej zgody (wiadomości e-mail lub innej pisemnej komunikacji) od właściciela lub z niego wyznaczonego.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Nie mogę uzyskać pulpitu zdalnego z maszyną wirtualną usługi w chmurze przy użyciu pliku RDP. Pojawia się następujący błąd: Wystąpił błąd uwierzytelniania (kod: 0x80004005)

Ten błąd może wystąpić, jeśli używasz pliku RDP z komputera, który jest przyłączony do Azure Active Directory. Aby rozwiązać ten problem, wykonaj poniższe czynności:

1. Kliknij prawym przyciskiem myszy pobrany plik RDP, a następnie wybierz polecenie **Edytuj**.
2. Dodaj "&#92;" jako prefiks przed nazwą użytkownika. Na przykład użyj **.\username** zamiast  **nazwy użytkownika**.

## <a name="scaling"></a>Skalowanie

### <a name="i-cannot-scale-beyond-x-instances"></a>Nie mogę skalować ponad wystąpieniami X
W ramach subskrypcji platformy Azure obowiązuje limit liczby rdzeni, których można użyć. Skalowanie nie będzie działało, jeśli zostały użyte wszystkie dostępne rdzenie. Jeśli na przykład limit wynosi 100 rdzeni, oznacza to, że można mieć 100 wystąpienia maszyn wirtualnych o rozmiarze a1 dla usługi w chmurze lub 50 wystąpienia maszyn wirtualnych o rozmiarze a2.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Jak można skonfigurować automatyczne skalowanie na podstawie metryk pamięci?

Automatyczne skalowanie na podstawie metryk pamięci dla Cloud Services nie jest obecnie obsługiwane. 

Aby obejść ten problem, można użyć Application Insights. Automatyczne skalowanie obsługuje Application Insights jako źródło metryk i może skalować liczbę wystąpień roli na podstawie metryki gościa, na przykład "pamięć".  Musisz skonfigurować Application Insights w pliku pakietu projektu usługi w chmurze (*. cspkg) i włączyć rozszerzenie Diagnostyka Azure w usłudze w celu zaimplementowania tego feat.

Aby uzyskać więcej informacji na temat używania metryki niestandardowej za pośrednictwem Application Insights do konfigurowania automatycznego skalowania na Cloud Services, zobacz Wprowadzenie do [automatycznego skalowania według metryki niestandardowej na platformie Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Aby uzyskać więcej informacji na temat integrowania Diagnostyka Azure z Application Insights dla Cloud Services, zobacz [wysyłanie danych diagnostycznych usługi w chmurze, maszyny wirtualnej lub Service Fabric do Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Aby uzyskać więcej informacji na temat włączania Application Insights dla Cloud Services, zobacz [Application Insights for Azure Cloud Services](../azure-monitor/app/cloudservices.md)

Aby uzyskać więcej informacji na temat włączania rejestrowania Diagnostyka Azure dla Cloud Services, zobacz [Konfigurowanie diagnostyki dla Cloud Services platformy Azure i maszyn wirtualnych](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Ogólny

### <a name="how-do-i-add-nosniff-to-my-website"></a>Jak mogę dodać `nosniff` do mojej witryny sieci Web?
Aby uniemożliwić klientom wykrywanie typów MIME, Dodaj ustawienie w pliku *web.config* .

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Można go również dodać jako ustawienia w usługach IIS. Użyj następującego polecenia w artykule [typowe zadania uruchamiania](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Jak mogę dostosować usługi IIS dla roli sieci Web?
Użyj skryptu uruchamiania usług IIS z artykułu [typowe zadania uruchamiania](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) .

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Jaki jest limit przydziału dla usługi w chmurze?
Zobacz [limity dotyczące usługi](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Dlaczego dysk na maszynie wirtualnej usługi w chmurze pokazuje bardzo mało wolnego miejsca na dysku?
Jest to oczekiwane zachowanie i nie powinno spowodować jakiegokolwiek problemu dla aplikacji. Funkcja rejestrowania jest włączona dla dysku% głównego aplikacji% na maszynach wirtualnych usługi Azure PaaS, które zasadniczo zużywają dwukrotnie ilość miejsca, w którym zwykle zajmują się pliki. Należy jednak pamiętać o kilku kwestiach, które zasadniczo przenoszą tę funkcję na nieemisyjną.

Rozmiar dysku% głównego aplikacji% jest obliczany jako rozmiar <rozmiaru. cspkg + maksymalny rozmiar dziennika + margines wolnego miejsca> lub 1,5 GB, w zależności od tego, który jest większy. Rozmiar maszyny wirtualnej nie ma znaczenia dla tego obliczenia. Rozmiar maszyny wirtualnej ma wpływ tylko na rozmiar dysku tymczasowego C:. 

Zapis na dysku% głównego aplikacji% nie jest obsługiwany. Jeśli piszesz na maszynę wirtualną platformy Azure, musisz to zrobić w tymczasowym zasobie LocalStorage (lub innej opcji, takiej jak BLOB Storage, Azure Files itp.). Ilość wolnego miejsca w folderze% głównego aplikacji% jest niezrozumiała. Jeśli nie masz pewności, czy aplikacja zapisuje się na dysku% głównego aplikacji%, zawsze możesz pozwolić, aby usługa działała przez kilka dni, a następnie porównać rozmiary "Before" i "After". 

System Azure nie zapisze niczego na dysku% głównego aplikacji%. Po utworzeniu wirtualnego dysku twardego `.cspkg` i zainstalowaniu go na maszynie wirtualnej platformy Azure jedyną przyczyną, którą może zapisywać na tym dysku, jest aplikacja. 

Ustawienia dziennika nie są konfigurowalne, więc nie można go wyłączyć.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Jak mogę dodać rozszerzenie chroniące przed złośliwym kodem dla mojego Cloud Services w sposób zautomatyzowany?

Rozszerzenie chroniące przed złośliwym kodem można włączyć przy użyciu skryptu programu PowerShell w ramach zadania uruchamiania. Wykonaj kroki opisane w poniższych artykułach, aby je wdrożyć: 
 
- [Tworzenie zadania uruchamiania programu PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](/powershell/module/servicemanagement/azure.service/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0&preserve-view=true)

Aby uzyskać więcej informacji o scenariuszach wdrażania oprogramowania chroniącego przed złośliwym kodem i sposobach ich włączania z portalu, zobacz [scenariusze wdrażania oprogramowania chroniącego przed złośliwym kodem](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Jak włączyć Oznaczanie nazwy serwera (SNI) dla Cloud Services?

SNI można włączyć w Cloud Services przy użyciu jednej z następujących metod:

**Metoda 1. Korzystanie z programu PowerShell**

Powiązanie SNI można skonfigurować przy użyciu polecenia cmdlet **New-Webbinding** programu PowerShell w zadaniu uruchamiania dla wystąpienia roli usługi w chmurze poniżej:

```powershell
New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags
```

Zgodnie z opisem w [tym miejscu](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee790567(v=technet.10))$sslFlags może być jedną z następujących wartości:

|Wartość|Znaczenie|
------|------
|0|Brak SNI|
|1|SNI włączone|
|2|Powiązanie inne niż SNI, które używa centralnego magazynu certyfikatów|
|3|Powiązanie SNI używające centralnego magazynu certyfikatów|
 
**Metoda 2. Korzystanie z kodu**

Powiązanie SNI można również skonfigurować za pomocą kodu podczas uruchamiania roli, zgodnie z opisem w tym [wpisie w blogu](/archive/blogs/jianwu/expose-ssl-service-to-multi-domains-from-the-same-cloud-service):

```csharp
//<code snip> 
                var serverManager = new ServerManager(); 
                var site = serverManager.Sites[0]; 
                var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                serverManager.CommitChanges(); 
    //</code snip>
```
    
Korzystając z dowolnych z powyższych metod, odpowiednie certyfikaty (*. pfx) dla określonych nazw hostów muszą być najpierw zainstalowane w wystąpieniach roli przy użyciu zadania uruchamiania lub kodu, aby powiązanie SNI było skuteczne.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Jak dodać tagi do usługi w chmurze systemu Azure? 

Usługa w chmurze jest zasobem klasycznym. Tylko zasoby utworzone za pomocą Azure Resource Manager Tagi obsługi. Nie można stosować tagów do zasobów klasycznych, takich jak usługa w chmurze. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>W Azure Portal nie jest wyświetlana wersja zestawu SDK mojej usługi w chmurze. Jak można to zrobić?

Pracujemy nad wprowadzeniem tej funkcji na Azure Portal. W tym celu można użyć następujących poleceń programu PowerShell w celu uzyskania wersji zestawu SDK:

```powershell
Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot
```

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Chcę zamknąć usługę w chmurze przez kilka miesięcy. Jak zmniejszyć koszt rozliczania usługi w chmurze bez utraty adresu IP?

Usługa w chmurze, która została już wdrożona, jest rozliczana za użycie zasobów obliczeniowych i magazynu. Nawet Jeśli wyłączysz maszynę wirtualną platformy Azure, nadal będą naliczane opłaty za magazyn. 

Oto co można zrobić, aby zmniejszyć rozliczenia bez utraty adresu IP usługi:

1. Przed usunięciem wdrożeń [Zarezerwuj adres IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) .  Opłaty są naliczane tylko za ten adres IP. Aby uzyskać więcej informacji na temat rozliczania adresów IP, zobacz temat [adresy IP](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Usuń wdrożenia. Nie usuwaj xxx.cloudapp.net, tak aby można było z niego korzystać w przyszłości.
3. Jeśli chcesz ponownie wdrożyć usługę w chmurze przy użyciu tego samego rezerwowego adresu IP, który został zarezerwowany w ramach subskrypcji, zobacz [zastrzeżony adres IP adresy Cloud Services i Virtual Machines](https://azure.microsoft.com/blog/reserved-ip-addresses/).