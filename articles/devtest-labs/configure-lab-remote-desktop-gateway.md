---
title: Konfigurowanie laboratorium do korzystania z bramy Pulpit zdalny w programie Azure DevTest Labs
description: Dowiedz się, jak skonfigurować laboratorium w Azure DevTest Labs przy użyciu bramy usług pulpitu zdalnego w celu zapewnienia bezpiecznego dostępu do maszyn wirtualnych laboratorium bez konieczności ujawniania portu RDP.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b15d4d39199c1a30eae292ece67f4553b656f530
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639591"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurowanie laboratorium w Azure DevTest Labs, aby można było korzystać z bramy usług pulpitu zdalnego
W Azure DevTest Labs można skonfigurować bramę usług pulpitu zdalnego dla laboratorium, aby zapewnić bezpieczny dostęp do maszyn wirtualnych laboratorium (VM) bez konieczności ujawniania portu RDP. Laboratorium to centralne miejsce, w którym użytkownicy laboratorium mogą wyświetlać i łączyć się ze wszystkimi maszynami wirtualnymi, do których mają dostęp. Przycisk **Połącz** na stronie **maszyny wirtualnej** tworzy plik RDP specyficzny dla komputera, który można otworzyć w celu nawiązania połączenia z maszyną. Możesz bardziej dostosować i zabezpieczyć połączenie RDP, łącząc laboratorium z bramą usług pulpitu zdalnego. 

Ta metoda jest bezpieczniejsza, ponieważ użytkownik laboratorium uwierzytelnia się bezpośrednio na maszynie bramy lub może używać poświadczeń firmowych na komputerze bramy przyłączonym do domeny, aby połączyć się z ich maszynami. Laboratorium obsługuje również uwierzytelnianie tokenu na maszynie bramy, która umożliwia użytkownikom łączenie się z maszynami wirtualnymi w laboratorium bez konieczności podawania portu RDP Internetu. W tym artykule przedstawiono przykład sposobu konfigurowania laboratorium, które używa uwierzytelniania tokenu do łączenia się z maszynami laboratorium.

## <a name="architecture-of-the-solution"></a>Architektura rozwiązania

![Architektura rozwiązania](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Akcja [Pobierz zawartość pliku RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) jest wywoływana po wybraniu przycisku **Połącz** . 1. 
1. Akcja Pobierz zawartość pliku RDP wywołuje `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` żądanie tokenu uwierzytelniania.
    1. `{gateway-hostname}` jest nazwą hosta bramy określoną na stronie **ustawień laboratorium** dla laboratorium w Azure Portal. 
    1. `{lab-machine-name}` to nazwa komputera, na którym próbujesz nawiązać połączenie.
    1. `{port-number}` to port, na którym należy nawiązać połączenie. Zazwyczaj ten port to 3389. Jeśli maszyna wirtualna laboratorium używa [udostępnionej funkcji IP](devtest-lab-shared-ip.md) w DevTest Labs, port będzie się różnić.
1. Brama usług pulpitu zdalnego odkłada wywołania z `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` usługi Azure Function w celu wygenerowania tokenu uwierzytelniania. Usługa DevTest Labs automatycznie uwzględnia klucz funkcji w nagłówku żądania. Klucz funkcji należy zapisać w magazynie kluczy laboratorium. Nazwa wpisu tajnego, który ma być pokazywany jako **wpis tajny tokenu bramy** na stronie **ustawień laboratorium** dla laboratorium.
1. Funkcja platformy Azure powinna zwrócić token uwierzytelniania tokenów opartych na certyfikatach na maszynę bramy.  
1. Akcja Pobierz zawartość pliku RDP zwraca pełny plik RDP, w tym informacje o uwierzytelnianiu.
1. Plik RDP jest otwierany przy użyciu preferowanego programu połączenia RDP. Należy pamiętać, że nie wszystkie programy połączeń RDP obsługują uwierzytelnianie tokenu. Token uwierzytelniania ma datę wygaśnięcia ustawioną przez aplikację funkcji. Nawiązać połączenie z maszyną wirtualną laboratorium przed wygaśnięciem tokenu.
1. Gdy komputer bramy usług pulpitu zdalnego uwierzytelnia token w pliku RDP, połączenie zostanie przekazane do maszyny laboratoryjnej.

### <a name="solution-requirements"></a>Wymagania dotyczące rozwiązania
Aby można było korzystać z funkcji uwierzytelniania tokenów DevTest Labs, istnieją pewne wymagania dotyczące konfiguracji dla maszyn bramy, usług nazw domen (DNS) i funkcji.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Wymagania dotyczące maszyn bramy usług pulpitu zdalnego
- Aby obsłużyć ruch HTTPS, na maszynie bramy musi być zainstalowany certyfikat TLS/SSL. Certyfikat musi odpowiadać w pełni kwalifikowanej nazwie domeny (FQDN) modułu równoważenia obciążenia dla farmy bramy lub nazwy FQDN samej maszyny, jeśli istnieje tylko jeden komputer. Nie działają certyfikaty wieloznacznych protokołów TLS/SSL.  
- Certyfikat podpisywania zainstalowany na maszynach bramy. Utwórz certyfikat podpisywania przy użyciu skryptu [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) .
- Zainstaluj [podłączany moduł uwierzytelniania](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) obsługujący uwierzytelnianie tokenu dla bramy usług pulpitu zdalnego. Przykładem takiego modułu jest `RDGatewayFedAuth.msi` dostarczany z [obrazami System Center Virtual Machine Manager (VMM)](/system-center/vmm/install-console?view=sc-vmm-1807&preserve-view=true). Aby uzyskać więcej informacji na temat programu System Center, zobacz [dokumentację programu System Center](/system-center/) i [szczegóły cennika](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Serwer bramy może obsługiwać żądania wysyłane do programu `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` .

    Brama-hostname to nazwa FQDN modułu równoważenia obciążenia farmy bramy lub nazwa FQDN maszyny, jeśli istnieje tylko jedna maszyna. `{lab-machine-name}`To nazwa maszyny laboratorium, którą próbujesz połączyć, oraz `{port-number}` port, na którym nastąpi połączenie.  Domyślnie ten port to 3389.  Jeśli jednak maszyna wirtualna korzysta z funkcji [udostępnionych adresów IP](devtest-lab-shared-ip.md) w DevTest Labs, port będzie się różnić.
- Moduł [żądania routingu aplikacji](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) dla programu Internet Information Server (IIS) może służyć do przekierowywania `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` żądań do funkcji platformy Azure, która obsługuje żądanie pobrania tokenu na potrzeby uwierzytelniania.


## <a name="requirements-for-azure-function"></a>Wymagania dotyczące funkcji platformy Azure
Funkcja platformy Azure obsługuje żądanie z formatem `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` i zwraca token uwierzytelniania na podstawie tego samego certyfikatu podpisywania zainstalowanego na maszynach bramy. `{function-app-uri}`Jest to identyfikator URI używany do uzyskiwania dostępu do funkcji. Klucz funkcji jest automatycznie przekazywać w nagłówku żądania. Aby uzyskać przykładową funkcję, zobacz [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) . 


## <a name="requirements-for-network"></a>Wymagania dotyczące sieci

- System DNS dla nazwy FQDN skojarzonej z certyfikatem TLS/SSL zainstalowanym na maszynach bramy musi kierować ruch do maszyny bramy lub modułu równoważenia obciążenia farmy maszyn bramy.
- Jeśli maszyna laboratoryjna używa prywatnych adresów IP, musi istnieć ścieżka sieciowa z maszyny bramy do maszyny laboratorium, przez udostępnianie tej samej sieci wirtualnej lub za pomocą równorzędnych sieci wirtualnych.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurowanie laboratorium do używania uwierzytelniania tokenu 
W tej sekcji pokazano, jak skonfigurować laboratorium do korzystania z maszyny bramy usług pulpitu zdalnego obsługującej uwierzytelnianie tokenu. Ta sekcja nie obejmuje sposobu konfigurowania farmy bramy usług pulpitu zdalnego. Aby uzyskać te informacje, zapoznaj się z sekcją [przykład, aby utworzyć bramę usług pulpitu zdalnego](#sample-to-create-a-remote-desktop-gateway) na końcu tego artykułu. 

Przed zaktualizowaniem ustawień laboratorium należy zapisać klucz wymagany do pomyślnego wykonania funkcji w celu zwrócenia tokenu uwierzytelniania w magazynie kluczy laboratorium. Wartość klucza funkcji można uzyskać na stronie **Zarządzanie** dla funkcji w Azure Portal. Aby uzyskać więcej informacji na temat sposobu zapisywania wpisu tajnego w magazynie kluczy, zobacz [Dodawanie wpisu tajnego do Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault). Zapisz nazwę wpisu tajnego do późniejszego użycia.

Aby znaleźć identyfikator magazynu kluczy laboratorium, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Skonfiguruj laboratorium do używania uwierzytelniania przy użyciu następujących kroków:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
1. Z listy laboratoriów wybierz **laboratorium**.
1. Na stronie laboratorium wybierz pozycję **Konfiguracja i zasady**.
1. W menu po lewej stronie w sekcji **Ustawienia** wybierz pozycję **Ustawienia laboratorium**.
1. W sekcji **pulpit zdalny** wprowadź w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP komputera bramy usług pulpitu zdalnego lub farmy dla pola **Nazwa hosta bramy** . Ta wartość musi być zgodna z nazwą FQDN certyfikatu TLS/SSL używanego na maszynach bramy.

    ![Opcje pulpitu zdalnego w ustawieniach laboratorium](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. W sekcji **pulpit zdalny** dla wpisu tajnego **tokenu bramy** wprowadź nazwę wpisu tajnego, który został utworzony wcześniej. Ta wartość nie jest samym kluczem funkcji, ale nazwą wpisu tajnego w magazynie kluczy laboratorium, który zawiera klucz funkcji.

    ![Wpis tajny tokenu bramy w ustawieniach laboratorium](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Zapisz** Wprowadzane.

    > [!NOTE] 
    > Klikając przycisk **Zapisz**, wyrażasz zgodę na [postanowienia licencyjne bramy pulpit zdalny](https://www.microsoft.com/licensing/product-licensing/products). Aby uzyskać więcej informacji na temat bramy zdalnej, zobacz [Zapraszamy do usługi pulpitu zdalnego](/windows-server/remote/remote-desktop-services/Welcome-to-rds) i [wdrażania środowiska pulpitu zdalnego](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Jeśli preferowane jest skonfigurowanie laboratorium za pośrednictwem automatyzacji, zobacz [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) przykładowego skryptu programu PowerShell, aby ustawić **nazwę hosta bramy** i ustawienia **klucza tajnego tokenu bramy** . [Azure DevTest Labs repozytorium GitHub](https://github.com/Azure/azure-devtestlab) udostępnia również szablon Azure Resource Manager, który tworzy lub aktualizuje laboratorium przy użyciu ustawień **nazwy hosta bramy** i **wpisu tajnego tokenu bramy** .

## <a name="configure-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń
Aby dodatkowo zabezpieczyć laboratorium, do sieci wirtualnej używanej przez maszyny wirtualne laboratorium można dodać grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń). Instrukcje dotyczące sposobu konfigurowania sieciowej grupy zabezpieczeń można znaleźć w temacie [Create, Change lub delete a Network Security Group](../virtual-network/manage-network-security-group.md).

Oto przykład sieciowej grupy zabezpieczeń, który zezwala tylko na ruch, który najpierw przechodzi przez bramę, aby dotrzeć do maszyn laboratorium. Źródłem w tej regule jest adres IP pojedynczej bramy lub adres IP modułu równoważenia obciążenia przed maszynami bramy.

![Sieciowe grupy zabezpieczeń — reguły](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Przykład tworzenia bramy usług pulpitu zdalnego

> [!NOTE] 
> Za pomocą przykładowych szablonów wyrażasz zgodę na [pulpit zdalny postanowień licencyjnych dotyczących bramy](https://www.microsoft.com/licensing/product-licensing/products). Aby uzyskać więcej informacji na temat bramy zdalnej, zobacz [Zapraszamy do usługi pulpitu zdalnego](/windows-server/remote/remote-desktop-services/Welcome-to-rds) i [wdrażania środowiska pulpitu zdalnego](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

[Azure DevTest Labs repozytorium GitHub](https://github.com/Azure/azure-devtestlab) zawiera kilka przykładów, które ułatwiają konfigurowanie zasobów wymaganych do korzystania z uwierzytelniania tokenów i bramy usług pulpitu zdalnego z DevTest Labs. Te przykłady obejmują szablony Azure Resource Manager dla maszyn bramy, ustawień laboratorium i aplikacji funkcji.

Wykonaj następujące kroki, aby skonfigurować przykładowe rozwiązanie dla farmy bramy usług pulpitu zdalnego.

1. Utwórz certyfikat podpisywania.  Uruchom [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Zapisz odcisk palca, hasło i kodowanie Base64 utworzonego certyfikatu.
2. Pobierz certyfikat TLS/SSL. Nazwa FQDN skojarzona z certyfikatem TLS/SSL musi być dla kontrolowanej domeny. Zapisz odcisk palca, hasło i kodowanie Base64 dla tego certyfikatu. Aby uzyskać odcisk palca przy użyciu programu PowerShell, użyj następujących poleceń.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import('path-to-certificate');
    $hash = $cer.GetCertHashString()
    ```

    Aby uzyskać kodowanie Base64 przy użyciu programu PowerShell, użyj następującego polecenia.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes('path-to-certificate'))
    ```
3. Pobierz pliki z [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) .

    Szablon wymaga dostępu do kilku innych Menedżer zasobów szablonów i powiązanych zasobów pod tym samym podstawowym identyfikatorem URI. Skopiuj wszystkie pliki z [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) i RDGatewayFedAuth.msi do kontenera obiektów BLOB na koncie magazynu.  
4. Wdróż **azuredeploy.jsw systemie** [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) . Szablon przyjmuje następujące parametry:
    - adminUsername — wymagane.  Nazwa użytkownika administratora dla maszyn bramy.
    - adminPassword — wymagane. Hasło konta administratora dla maszyn bramy.
    - instanceCount — liczba maszyn bramy do utworzenia.  
    - alwaysOn — wskazuje, czy aplikacja utworzona Azure Functions ma być w stanie ciepłym. Utrzymywanie Azure Functions aplikacji będzie unikać opóźnień, gdy użytkownicy próbują nawiązać połączenie z maszyną wirtualną laboratorium, ale ma to wpływ na koszty.  
    - tokenLifetime — długość czasu, przez jaki utworzony token będzie prawidłowy. Format to gg: MM: SS.
    - sslCertificate — kodowanie Base64 certyfikatu TLS/SSL dla maszyny bramy.
    - sslCertificatePassword — hasło protokołu TLS/SSL dla maszyny bramy.
    - sslCertificateThumbprint — odcisk palca certyfikatu służący do identyfikacji w lokalnym magazynie certyfikatów certyfikatu TLS/SSL.
    - signCertificate — kodowanie Base64 dla certyfikatu podpisywania dla maszyny bramy.
    - signCertificatePassword — hasło do podpisywania certyfikatu dla maszyny bramy.
    - signCertificateThumbprint — odcisk palca certyfikatu służący do identyfikacji w lokalnym magazynie certyfikatów certyfikatu podpisywania.
    - _artifactsLocation — lokalizacja URI, w której można znaleźć wszystkie zasoby pomocnicze. Ta wartość musi być w pełni kwalifikowana UIR, a nie ścieżką względną.
    - _artifactsLocationSasToken — token sygnatury dostępu współdzielonego (SAS) służący do uzyskiwania dostępu do zasobów pomocniczych, jeśli lokalizacja jest kontem usługi Azure Storage.

    Szablon można wdrożyć przy użyciu interfejsu wiersza polecenia platformy Azure, korzystając z następujących poleceń:

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Poniżej przedstawiono opisy parametrów:

    - {Storage-account-Endpoint} można uzyskać, uruchamiając `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` .  {Storage-ACCT-Name} to nazwa konta magazynu zawierającego przekazane pliki.  
    - {Container-Name} to nazwa kontenera w {Storage-ACCT-Name}, który zawiera przekazane pliki.  
    - {SAS-token} można uzyskać, uruchamiając `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` . 
        - {Storage-ACCT-Name} to nazwa konta magazynu zawierającego przekazane pliki.  
        - {Container-Name} to nazwa kontenera w {Storage-ACCT-Name}, który zawiera przekazane pliki.  
        - {UTC-Data wygaśnięcia} to data, w formacie UTC, z której token sygnatury dostępu współdzielonego wygaśnie i nie będzie można już używać tokenu SAS do uzyskiwania dostępu do konta magazynu.

    Zapisz wartości dla gatewayFQDN i gatewayIP z danych wyjściowych wdrożenia szablonu. Należy również zapisać wartość klucza funkcji dla nowo utworzonej funkcji, którą można znaleźć na karcie [Ustawienia aplikacji funkcji](../azure-functions/functions-how-to-use-azure-function-app-settings.md) .
5. Skonfiguruj system DNS tak, aby nazwa FQDN certyfikatu TLS/SSL była kierowane do adresu IP gatewayIP z poprzedniego kroku.

    Po utworzeniu farmy Pulpit zdalny Gateway i wprowadzeniu odpowiednich aktualizacji DNS będzie ona gotowa do użycia w laboratorium w DevTest Labs. Ustawienia **nazwy hosta bramy** i **wpisu tajnego tokenu bramy** muszą być skonfigurowane do używania wdrożonych maszyn bramy. 

    > [!NOTE]
    > Jeśli maszyna laboratoryjna używa prywatnych adresów IP, musi istnieć ścieżka sieciowa z komputera bramy do maszyny laboratorium, przez udostępnienie tej samej sieci wirtualnej lub za pomocą równorzędnej sieci wirtualnej.

    Po skonfigurowaniu zarówno bramy, jak i laboratorium, plik połączenia utworzony podczas kliknięcia przez użytkownika laboratorium w programie **Connect** będzie automatycznie zawierać informacje niezbędne do nawiązania połączenia przy użyciu uwierzytelniania tokenu.     

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem, aby dowiedzieć się więcej o Usługi pulpitu zdalnego: [usługi pulpitu zdalnego dokumentacja](/windows-server/remote/remote-desktop-services/Welcome-to-rds)