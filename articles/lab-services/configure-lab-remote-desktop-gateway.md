---
title: Konfigurowanie laboratorium do używania bramy usług pulpitu zdalnego w laboratoriach devtest platformy Azure
description: Dowiedz się, jak skonfigurować laboratorium w laboratorium usługi Azure DevTest Labs za pomocą bramy pulpitu zdalnego, aby zapewnić bezpieczny dostęp do maszyn wirtualnych w laboratorium bez konieczności udostępniania portu RDP.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: eac195babebf300aa9770d35b7b98eba29c234cf
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460991"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurowanie laboratorium w laboratoriach devtest platformy Azure do korzystania z bramy pulpitu zdalnego
W laboratoriach DevTest Azure można skonfigurować bramę pulpitu zdalnego dla laboratorium, aby zapewnić bezpieczny dostęp do maszyn wirtualnych laboratorium bez konieczności udostępniania portu RDP. Laboratorium zapewnia centralne miejsce dla użytkowników laboratorium, aby wyświetlić i połączyć się ze wszystkimi maszynami wirtualnymi, do których mają dostęp. Przycisk **Połącz** na stronie **Maszyna wirtualna** tworzy plik RDP specyficzny dla komputera, który można otworzyć, aby połączyć się z urządzeniem. Połączenie RDP można dodatkowo dostosować i zabezpieczyć, łącząc laboratorium z bramą pulpitu zdalnego. 

Takie podejście jest bezpieczniejsze, ponieważ użytkownik laboratorium uwierzytelnia się bezpośrednio na komputerze bramy lub może używać poświadczeń firmy na komputerze bramy przyłączanym do domeny, aby połączyć się z ich komputerami. Laboratorium obsługuje również przy użyciu uwierzytelniania tokenu na komputerze bramy, który umożliwia użytkownikom łączenie się z ich maszyn wirtualnych laboratorium bez portu RDP narażone na internet. W tym artykule przedstawia się przykład konfigurowania laboratorium, w ramach którego można połączyć się z komputerami laboratoryjnymi za pomocą uwierzytelniania tokenu.

## <a name="architecture-of-the-solution"></a>Architektura rozwiązania

![Architektura rozwiązania](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Akcja [Pobierz zawartość pliku RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) jest wywoływana po wybraniu przycisku **Połącz.1.** 
1. Akcja Pobierz zawartość pliku RDP wywołuje żądanie `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` tokenu uwierzytelniania.
    1. `{gateway-hostname}`to nazwa hosta bramy określona na stronie **Ustawienia laboratorium** dla twojego laboratorium w witrynie Azure portal. 
    1. `{lab-machine-name}`to nazwa urządzenia, z którą próbujesz się połączyć.
    1. `{port-number}`jest portem, na którym należy nawiązać połączenie. Zazwyczaj ten port jest 3389. Jeśli maszyna wirtualna laboratorium używa [udostępnionej](devtest-lab-shared-ip.md) funkcji IP w DevTest Labs, port będzie inny.
1. Brama pulpitu zdalnego `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` odracza wywołanie z funkcji platformy Azure w celu wygenerowania tokenu uwierzytelniania. Usługa DevTest Labs automatycznie zawiera klucz funkcyjny w nagłówku żądania. Klucz funkcyjny ma zostać zapisany w magazynie kluczy laboratorium. Nazwa tego klucza tajnego ma być wyświetlana jako **klucz tajny tokenu bramy** na stronie Ustawienia **laboratorium** dla laboratorium.
1. Oczekuje się, że funkcja platformy Azure zwróci token do uwierzytelniania tokenów opartych na certyfikatach na komputerze bramy.  
1. Akcja Pobierz zawartość pliku RDP zwraca następnie pełny plik RDP, w tym informacje o uwierzytelnianiu.
1. Otwórz plik RDP przy użyciu preferowanego programu połączenia RDP. Należy pamiętać, że nie wszystkie programy połączeń RDP obsługują uwierzytelnianie tokenów. Token uwierzytelniania ma datę wygaśnięcia, ustawioną przez aplikację funkcji. Nawiązuj połączenie z maszyną wirtualną laboratorium przed wygaśnięciem tokenu.
1. Gdy zdalna brama pulpitu uwierzytelnia token w pliku RDP, połączenie jest przekazywane do komputera laboratoryjego.

### <a name="solution-requirements"></a>Wymagania dotyczące rozwiązania
Aby pracować z funkcją uwierzytelniania tokenów DevTest Labs, istnieje kilka wymagań konfiguracyjnych dla maszyn bramy, usług nazw domen (DNS) i funkcji.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Wymagania dotyczące zdalnych komputerów bramy pulpitu
- Certyfikat TLS/SSL musi być zainstalowany na komputerze bramy w celu obsługi ruchu HTTPS. Certyfikat musi być zgodny z w pełni kwalifikowaną nazwą domeny (FQDN) modułu równoważenia obciążenia dla farmy bramy lub nazwy FQDN samego komputera, jeśli istnieje tylko jeden komputer. Certyfikaty TLS/SSL z symbolami wieloznaczowymi nie działają.  
- Certyfikat podpisywania zainstalowany na komputerach bramy. Utwórz certyfikat podpisywania przy użyciu [skryptu Create-SigningCertificate.ps1.](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)
- Zainstaluj moduł [uwierzytelniania podłączanego,](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) który obsługuje uwierzytelnianie tokenów bramy pulpitu zdalnego. Jednym z przykładów `RDGatewayFedAuth.msi` takiego modułu jest to, że pochodzi z [System Center Virtual Machine Manager (VMM) obrazy](/system-center/vmm/install-console?view=sc-vmm-1807). Aby uzyskać więcej informacji o centrum [systemowym,](https://docs.microsoft.com/system-center/) zobacz Dokumentacja systemu i [szczegóły cen](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Serwer bramy może obsługiwać `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`żądania kierowane do programu .

    Nazwa hosta bramy jest nazwami FQDN modułu równoważenia obciążenia farmy bramy lub nazwy FQDN samej maszyny, jeśli istnieje tylko jedna maszyna. Jest `{lab-machine-name}` to nazwa komputera laboratoryjnego, który próbujesz `{port-number}` połączyć, i port is, na którym zostanie nawiązyne połączenie.  Domyślnie ten port to 3389.  Jeśli jednak maszyna wirtualna korzysta z udostępnionej funkcji [IP](devtest-lab-shared-ip.md) w laboratoriach DevTest, port będzie inny.
- Moduł [żądanie routingu aplikacji](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) dla internetowego serwera informacyjnego `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` (IIS) może służyć do przekierowywania żądań do funkcji platformy azure, która obsługuje żądanie uzyskania tokenu do uwierzytelniania.


## <a name="requirements-for-azure-function"></a>Wymagania dotyczące funkcji platformy Azure
Funkcja platformy Azure obsługuje `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` żądanie w formacie i zwraca token uwierzytelniania na podstawie tego samego certyfikatu podpisywania zainstalowanego na komputerach bramy. Jest `{function-app-uri}` uri używany do uzyskania dostępu do funkcji. Klucz funkcyjny jest automatycznie przekazywany w nagłówku żądania. Aby uzyskać przykładową [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)funkcję, zobacz . 


## <a name="requirements-for-network"></a>Wymagania dotyczące sieci

- System DNS dla nazwy FQDN skojarzonej z certyfikatem TLS/SSL zainstalowanym na maszynach bramy musi kierować ruch do komputera bramy lub modułu równoważenia obciążenia farmy maszyn bramy.
- Jeśli maszyna laboratoryjna używa prywatnych adresów IP, musi istnieć ścieżka sieciowa z komputera bramy do komputera laboratoryjnego, poprzez udostępnianie tej samej sieci wirtualnej lub przy użyciu sieci wirtualnych równorzędnych.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurowanie laboratorium do używania uwierzytelniania tokenów 
W tej sekcji pokazano, jak skonfigurować laboratorium do używania komputera bramy pulpitu zdalnego, który obsługuje uwierzytelnianie tokenu. W tej sekcji nie opisano sposobu konfigurowania farmy bramy pulpitu zdalnego. Aby uzyskać te informacje, zobacz [przykład, aby utworzyć bramę pulpitu zdalnego](#sample-to-create-a-remote-desktop-gateway) sekcji na końcu tego artykułu. 

Przed zaktualizowaniem ustawień laboratorium należy zapisać klucz potrzebny do pomyślnego wykonania funkcji w celu zwrócenia tokenu uwierzytelniania w magazynie kluczy w laboratorium. Wartość klucza funkcji można uzyskać na stronie **Zarządzanie** dla funkcji w witrynie Azure portal. Aby uzyskać więcej informacji na temat zapisywania klucza tajnego w magazynie kluczy, zobacz [Dodawanie klucza tajnego do magazynu kluczy](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault). Zapisz nazwę klucza tajnego do późniejszego użycia.

Aby znaleźć identyfikator magazynu kluczy laboratorium, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Skonfiguruj laboratorium do używania uwierzytelniania tokenu, wykonując następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **pozycję Wszystkie usługi**, a następnie wybierz z listy pozycję **DevTest Labs.**
1. Z listy laboratoriów wybierz **laboratorium**.
1. Na stronie laboratorium wybierz **pozycję Konfiguracja i zasady**.
1. W menu po lewej stronie w sekcji **Ustawienia** wybierz pozycję **Ustawienia laboratorium**.
1. W sekcji **Pulpit zdalny** wprowadź w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP komputera bramy usług pulpitu zdalnego lub farmy dla pola **Nazwa hosta bramy.** Ta wartość musi być zgodna z FQDN certyfikatu TLS/SSL używanego na komputerach bramy.

    ![Opcje pulpitu zdalnego w ustawieniach laboratoryjnych](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. W sekcji **Pulpit zdalny** w przypadku klucza tajnego **tokenu bramy** wprowadź nazwę klucza tajnego utworzonego wcześniej. Ta wartość nie jest kluczem funkcyjnym, ale nazwą klucza tajnego w magazynie kluczy laboratorium, który przechowuje klucz funkcyjny.

    ![Klucz tajny tokenu bramy w ustawieniach laboratoryjnych](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Zapisz** Zmiany.

    > [!NOTE] 
    > Klikając przycisk **Zapisz,** użytkownik wyraża zgodę na [postanowienia licencyjne bramy usług pulpitu zdalnego.](https://www.microsoft.com/licensing/product-licensing/products) Aby uzyskać więcej informacji na temat bramy zdalnej, zobacz [Zapraszamy do usług pulpitu zdalnego](https://aka.ms/rds) i [Wdrażanie środowiska pulpitu zdalnego](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Jeśli preferowane jest konfigurowanie laboratorium za pomocą automatyzacji, zobacz [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) dla przykładowego skryptu programu PowerShell, aby ustawić ustawienia **tajnej nazwy hosta bramy** i **tokenu bramy.** [Repozytorium Usługi Azure DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) udostępnia również szablon usługi Azure Resource Manager, który tworzy lub aktualizuje laboratorium z **ustawieniami tajnej nazwy hosta bramy** i **tokenu bramy.**

## <a name="configure-network-security-group"></a>Konfigurowanie sieciowej grupy zabezpieczeń
Aby jeszcze bardziej zabezpieczyć laboratorium, sieciowej grupy zabezpieczeń (NSG) można dodać do sieci wirtualnej używanej przez maszyny wirtualne laboratorium. Aby uzyskać instrukcje dotyczące konfigurowania sieciowej grupy zabezpieczeń, zobacz [Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md).

Oto przykład sieciowej sieciowej, która zezwala tylko na ruch, który najpierw przechodzi przez bramę, aby dotrzeć do maszyn laboratoryjnych. Źródłem w tej regule jest adres IP komputera z pojedynczą bramą lub adres IP modułu równoważenia obciążenia przed komputerami bramy.

![Sieć grupy zabezpieczeń - reguły](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Przykładowy do utworzenia bramy pulpitu zdalnego

> [!NOTE] 
> Korzystając z przykładowych szablonów, użytkownik wyraża zgodę na [postanowienia licencyjne bramy usług pulpitu zdalnego.](https://www.microsoft.com/licensing/product-licensing/products) Aby uzyskać więcej informacji na temat bramy zdalnej, zobacz [Zapraszamy do usług pulpitu zdalnego](https://aka.ms/rds) i [Wdrażanie środowiska pulpitu zdalnego](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

[Repozytorium Usługi Azure DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) zawiera kilka przykładów ułatwiające konfigurowanie zasobów potrzebnych do korzystania z uwierzytelniania tokenów i bramy pulpitu zdalnego w laboratoriach DevTest. Te przykłady obejmują szablony usługi Azure Resource Manager dla maszyn bramy, ustawienia laboratorium i aplikacji funkcji.

Wykonaj następujące kroki, aby skonfigurować przykładowe rozwiązanie dla farmy bramy pulpitu zdalnego.

1. Utwórz certyfikat podpisywania.  Uruchom [create-signingCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Zapisz odcisk palca, hasło i kodowanie Base64 utworzonego certyfikatu.
2. Uzyskaj certyfikat TLS/SSL. Nazwa FQDN skojarzona z certyfikatem TLS/SSL musi być dla kontrolowanej domeny. Zapisz odcisk palca, hasło i kodowanie Base64 dla tego certyfikatu. Aby uzyskać odcisk palca za pomocą programu PowerShell, należy użyć następujących poleceń.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Aby uzyskać kodowanie Base64 przy użyciu programu PowerShell, użyj następującego polecenia.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Pobieraj [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)pliki z programu .

    Szablon wymaga dostępu do kilku innych szablonów Menedżera zasobów i powiązanych zasobów w tym samym podstawowym identyfikatorze URI. Skopiuj [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) wszystkie pliki z pliku RDGatewayFedAuth.msi do kontenera obiektów blob na koncie magazynu.  
4. Wdrażanie **pliku azuredeploy.json** z pliku [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). Szablon przyjmuje następujące parametry:
    - adminUsername – wymagane.  Nazwa użytkownika administratora dla maszyn bramy.
    - adminPassword – Wymagane. Hasło do konta administratora dla komputerów bramy.
    - instanceCount – liczba maszyn bramy do utworzenia.  
    - alwaysOn — wskazuje, czy zachować utworzoną aplikację usługi Azure Functions w stanie ciepłym, czy nie. Utrzymywanie aplikacji Usługi Azure usługi pozwoli uniknąć opóźnień, gdy użytkownicy po raz pierwszy próbują połączyć się z ich laboratorium maszyny Wirtualnej, ale ma wpływ na koszty.  
    - tokenLifetime — czas utworzenia tokenu będzie prawidłowy. Format to HH:MM:SS.
    - sslCertificate — kodowanie Base64 certyfikatu TLS/SSL dla maszyny bramy.
    - sslCertificatePassword — hasło certyfikatu TLS/SSL dla maszyny bramy.
    - sslCertificateThumbprint — odcisk palca certyfikatu do identyfikacji w lokalnym magazynie certyfikatów certyfikatu TLS/SSL.
    - signCertificate — kodowanie Base64 do podpisywania certyfikatu dla komputera bramy.
    - signCertificatePassword — hasło do podpisywania certyfikatu dla komputera bramy.
    - signCertificateThumbprint — odcisk palca certyfikatu do identyfikacji w lokalnym magazynie certyfikatów certyfikatu podpisywania.
    - _artifactsLocation — lokalizacja identyfikatora URI, gdzie można znaleźć wszystkie zasoby pomocnicze. Ta wartość musi być w pełni kwalifikowany UIR, a nie względna ścieżka.
    - _artifactsLocationSasToken — token sygnatury dostępu współdzielonego (SAS) używany do uzyskiwania dostępu do zasobów pomocniczych, jeśli lokalizacja jest kontem magazynu platformy Azure.

    Szablon można wdrożyć przy użyciu interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Oto opisy parametrów:

    - Punkt końcowy {storage-account-endpoint} można `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`uzyskać, uruchamiając plik .  {storage-acct-name} to nazwa konta magazynu zawierającego przekazane pliki.  
    - {nazwa kontenera} to nazwa kontenera w {storage-acct-name}, który zawiera przekazane pliki.  
    - {sas-token} można uzyskać, `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`uruchamiając plik . 
        - {storage-acct-name} to nazwa konta magazynu zawierającego przekazane pliki.  
        - {nazwa kontenera} to nazwa kontenera w {storage-acct-name}, który zawiera przekazane pliki.  
        - {utc-expiration-date} jest datą w czasie UTC, z którą token sygnatury dostępu Współdzielonego wygaśnie, a token sygnatury dostępu współdzielonego nie może być już używany do uzyskiwania dostępu do konta magazynu.

    Rejestrowanie wartości gatewayFQDN i gatewayIP z danych wyjściowych wdrożenia szablonu. Należy również zapisać wartość klucza funkcyjnego dla nowo utworzonej funkcji, którą można znaleźć na karcie [Ustawienia aplikacji funkcji.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
5. Skonfiguruj system DNS tak, aby funkcja FQDN certyfikatu TLS/SSL była kierowana na adres IP gatewayIP z poprzedniego kroku.

    Po utworzeniu farmy bramy usług pulpitu zdalnego i wykonaniu odpowiednich aktualizacji DNS jest ona gotowa do użycia przez laboratorium w laboratorium DevTest Labs. Ustawienia **tajnej nazwy hosta bramy** i **tokenu bramy** muszą być skonfigurowane do używania wdrożonych komputerów bramy. 

    > [!NOTE]
    > Jeśli maszyna laboratoryjna używa prywatnych adresów IP, musi istnieć ścieżka sieciowa z komputera bramy do komputera laboratoryjnego, poprzez udostępnianie tej samej sieci wirtualnej lub przy użyciu sieci wirtualnej równorzędnej.

    Po skonfigurowaniu bramy i laboratorium plik połączenia utworzony po kliknięciu przez użytkownika laboratorium connect **automatycznie** będzie zawierał informacje niezbędne do nawiązania połączenia przy użyciu uwierzytelniania tokenu.     

## <a name="next-steps"></a>Następne kroki
Zobacz następujący artykuł, aby dowiedzieć się więcej o usługach pulpitu zdalnego: [Dokumentacja usług pulpitu zdalnego](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


