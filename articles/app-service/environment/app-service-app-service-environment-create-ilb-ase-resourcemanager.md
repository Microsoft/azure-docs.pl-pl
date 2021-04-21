---
title: Tworzenie aplikacji ASE z równoważeniem obciążenia w wersji 1
description: Utwórz środowisko App Service za pomocą wewnętrznego równoważenia obciążenia (ILB ASE). Ten doc jest dostarczany tylko dla klientów, którzy korzystają ze starszej wersji 1 ase.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a6cc1cae640b97ecb3d95ee1e4f8ec34750e32d2
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833014"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Jak tworzyć środowisko ASE wewnętrznego modułu równoważenia przy użyciu szablonów usługi Azure Resource Manager

> [!NOTE] 
> Ten artykuł dotyczy wersji App Service Environment 1. Istnieje nowsza wersja pakietu App Service Environment, która jest łatwiejsza w użyciu i działa w bardziej zaawansowanej infrastrukturze. Aby dowiedzieć się więcej na temat nowej wersji, zacznij od [wprowadzenia do App Service Environment.](intro.md)
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
App Service środowiska można tworzyć za pomocą wewnętrznego adresu sieci wirtualnej zamiast publicznego adresu VIP.  Ten adres wewnętrzny jest dostarczany przez składnik platformy Azure o nazwie wewnętrzny równoważenie obciążenia (ILB).  Można utworzyć ase z równoważeniem obciążenia przy użyciu Azure Portal.  Można ją również tworzyć przy użyciu automatyzacji za pomocą Azure Resource Manager szablonów.  Ten artykuł zawiera instrukcje i składnię potrzebną do utworzenia ase z równoważeniem obciążenia za pomocą Azure Resource Manager szablonów.

Istnieją trzy kroki związane z automatyzacją tworzenia ase z równoważeniem obciążenia:

1. Najpierw podstawowe środowiska ASE są tworzone w sieci wirtualnej przy użyciu adresu wewnętrznego równoważenia obciążenia zamiast publicznego adresu VIP.  W ramach tego kroku nazwa domeny głównej jest przypisywana do usługi ASE z równoważeniem obciążenia.
2. Po utworzeniu usługi ASE z równoważeniem obciążenia jest przekazywany certyfikat protokołu TLS/SSL.  
3. Przekazany certyfikat TLS/SSL jest jawnie przypisany do usługi ASE z równoważeniem obciążenia jako jego "domyślny" certyfikat protokołu TLS/SSL.  Ten certyfikat protokołu TLS/SSL będzie używany dla ruchu TLS do aplikacji w asecie ZLB, gdy aplikacje są adresowane przy użyciu wspólnej domeny głównej przypisanej do ase (np. `https://someapp.mycustomrootcomain.com` )

## <a name="creating-the-base-ilb-ase"></a>Tworzenie podstawowego ase z równoważeniem obciążenia
Przykładowy Azure Resource Manager i skojarzony z nim plik parametrów są dostępne w witrynie GitHub [tutaj.][quickstartilbasecreate]

Większość parametrów w pliku w *azuredeploy.parameters.jssą* wspólne dla tworzenia zarówno ase ILB, jak i ase powiązane z publiczny adres VIP.  Na poniższej liście wywoływują parametry specjalnej uwagi lub są unikatowe podczas tworzenia aplikacji ASE z równoważeniem obciążenia:

* *internalLoadBalancingMode:* w większości przypadków ustaw tę wartość na 3, co oznacza, że zarówno ruch HTTP/HTTPS na portach 80/443, jak i porty kanału sterowania/danych nasłuchiwać przez usługę FTP w środowisku ASE, zostaną powiązane z wewnętrznym adresem sieci wirtualnej z przydzielonym wewnętrznym wewnętrznym równoważeniem obciążenia.  Jeśli zamiast tego ta właściwość zostanie ustawiona na wartość 2, tylko porty powiązane z usługą FTP (kanały sterowania i kanały danych) będą powiązane z adresem ILB, a ruch HTTP/HTTPS pozostanie na publicznych adresach VIP.
* *dnsSuffix:* ten parametr definiuje domyślną domenę główną, która zostanie przypisana do ase.  W publicznej wersji usługi Azure App Service domyślną domeną główną dla wszystkich aplikacji internetowych jest *azurewebsites.net*.  Jednak ze względu na to, że środowiska ASE z wewnętrznym równoważeniem obciążenia są wewnętrzne dla sieci wirtualnej klienta, użycie domyślnej domeny głównej usługi publicznej nie ma sensu.  Zamiast tego środowiska ASE z wewnętrznym równoważeniem obciążenia powinny mieć domyślną domenę główną, która ma sens do użycia w wewnętrznej sieci wirtualnej firmy.  Na przykład hipotetyczna firma Contoso Corporation może używać domyślnej domeny głównej *internal-contoso.com* dla aplikacji, które mają być rozpoznawalne i dostępne tylko w sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount:* ten parametr jest automatycznie domyślnie ustawiany na wartość 0 wazuredeploy.js *pliku,* ponieważ ase ILB ma tylko jeden adres ILB.  Nie ma jawnych adresów IP-SSL dla zestawu ASE z równoważeniem obciążenia, dlatego pula adresów IP-SSL dla tego zestawu ase musi być ustawiona na zero. W przeciwnym razie wystąpi błąd aprowizowania. 

Poazuredeploy.parameters.js *w* pliku dla środowiska ASE z równoważeniem obciążenia można utworzyć je przy użyciu następującego fragmentu kodu programu PowerShell.  Zmień ścieżki plików, aby dopasować Azure Resource Manager plików szablonu na komputerze.  Pamiętaj również, aby podać własne wartości dla Azure Resource Manager wdrożenia i nazwy grupy zasobów.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Po przesłaniu Azure Resource Manager ase z równoważeniem obciążenia może potrwać kilka godzin.  Po zakończeniu tworzenia środowisko ASE z równoważeniem obciążenia będzie wyświetlane w środowisku użytkownika portalu na liście środowisk App Service dla subskrypcji, która wyzwoliła wdrożenie.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Przekazywanie i konfigurowanie "domyślnego" certyfikatu TLS/SSL
Po utworzeniu usługi ASE z równoważeniem obciążenia należy skojarzyć certyfikat TLS/SSL z tym programem jako "domyślny" certyfikat TLS/SSL na użytek ustanawiania połączeń TLS/SSL z aplikacjami.  Kontynuując hipotetyczny przykład firmy Contoso Corporation, jeśli domyślny sufiks DNS w programie ASE to *internal-contoso.com, połączenie* do programu wymaga certyfikatu TLS/SSL, który jest prawidłowy dla *`https://some-random-app.internal-contoso.com`* **.internal-contoso.com*. 

Istnieją różne sposoby uzyskiwania prawidłowego certyfikatu TLS/SSL, w tym wewnętrznych urzędu certyfikacji, kupowania certyfikatu od zewnętrznego wystawcy i używania certyfikatu z podpisem własnym.  Niezależnie od źródła certyfikatu TLS/SSL należy prawidłowo skonfigurować następujące atrybuty certyfikatu:

* *Temat:* ten atrybut musi być ustawiony na **.your-root-domain-here.com*
* *Alternatywna nazwa podmiotu:* ten atrybut musi zawierać zarówno **.your-root-domain-here.com*, i **.scm.your-root-domain-here.com*.  Przyczyną drugiego wpisu jest to, że połączenia TLS z witryną SCM/Kudu skojarzoną z każdą aplikacją będą dokonywane przy użyciu adresu *formularza your-app-name.scm.your-root-domain-here.com*.

Mając do ręki prawidłowy certyfikat TLS/SSL, wymagane są dwa dodatkowe kroki przygotowawcze.  Certyfikat TLS/SSL musi zostać przekonwertowany/zapisany jako plik pfx.  Pamiętaj, że plik pfx musi zawierać wszystkie certyfikaty pośrednie i główne, a także musi być zabezpieczony hasłem.

Następnie wynikowy plik pfx należy przekonwertować na ciąg base64, ponieważ certyfikat TLS/SSL zostanie przekazany przy użyciu Azure Resource Manager szablonu.  Ponieważ Azure Resource Manager są plikami tekstowymi, plik pfx należy przekonwertować na ciąg base64, aby można go było doliczyć jako parametr szablonu.

Poniższy fragment kodu programu PowerShell przedstawia przykład generowania certyfikatu z podpisem własnym, eksportowania certyfikatu jako pliku pfx, konwertowania pliku pfx na ciąg zakodowany w formacie base64, a następnie zapisywania ciągu zakodowanych w formacie base64 do oddzielnego pliku.  Kod programu PowerShell do kodowania base64 został dostosowany z [bloga Skrypty programu PowerShell][examplebase64encoding].

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Po pomyślnym wygenerowaniu certyfikatu TLS/SSL i przekonwertowaniu go na ciąg zakodowany w formacie base64 można użyć przykładowego szablonu usługi Azure Resource Manager w usłudze GitHub służącego do konfigurowania domyślnego certyfikatu [TLS/SSL.][configuringDefaultSSLCertificate]

Poniżej przedstawiono *parametryazuredeploy.parameters.jspliku:*

* *appServiceEnvironmentName:* nazwa skonfigurowanego wewnętrznego konta usługi ASE.
* *existingAseLocation:* ciąg tekstowy zawierający region platformy Azure, w którym wdrożono ase z równoważeniem obciążenia.  Na przykład: "Południowo-środkowe usa".
* *pfxBlobString:* zakodowany w formacie based64 ciąg reprezentacji pliku pfx.  Korzystając z przedstawionego wcześniej fragmentu kodu, skopiuj ciąg zawarty w pliku "exportedcert.pfx.b64" i wklej go jako wartość *atrybutu pfxBlobString.*
* *password:* hasło używane do zabezpieczania pliku pfx.
* *certificateThumbprint:* odcisk palca certyfikatu.  Jeśli ta wartość zostanie pobrana z programu PowerShell (np. *$certificate. odcisk palca* z wcześniejszego fragmentu kodu), możesz użyć wartości w tej, w jaki jest.  Jeśli jednak skopiujemy wartość z okna dialogowego certyfikatu systemu Windows, pamiętaj, aby rozebrać dodatkowe spacje.  *CertificateThumbprint* powinien wyglądać mniej więcej tak: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName:* przyjazny identyfikator ciągu, który został przez Użytkownika użyty do tożsamości certyfikatu.  Nazwa jest używana jako część unikatowego Azure Resource Manager jednostki *Microsoft.Web/certificates* reprezentującej certyfikat TLS/SSL.  Nazwa musi **kończyć** się następującym sufiksem:  \_ yourASENameHere_InternalLoadBalancingASE.  Ten sufiks jest używany przez portal jako wskaźnik, że certyfikat jest używany do zabezpieczania asE z obsługą równoważenia obciążenia.

Poniżej przedstawiono skrócony przykład *azuredeploy.parameters.jsw* programie :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appServiceEnvironmentName": {
            "value": "yourASENameHere"
        },
        "existingAseLocation": {
            "value": "East US 2"
        },
        "pfxBlobString": {
            "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
        },
        "password": {
            "value": "PASSWORDGOESHERE"
        },
        "certificateThumbprint": {
            "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
        },
        "certificateName": {
            "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
        }
    }
}
```

Po *azuredeploy.parameters.jspliku* można skonfigurować domyślny certyfikat protokołu TLS/SSL przy użyciu następującego fragmentu kodu programu PowerShell.  Zmień ścieżki plików tak, aby dopasować Azure Resource Manager plików szablonu na komputerze.  Pamiętaj również, aby podać własne wartości dla Azure Resource Manager wdrożenia i nazwy grupy zasobów.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Po przesłaniu Azure Resource Manager szablonu zastosowanie zmiany na frontonie usługi ASE potrwa około czterdziestu minut.  Na przykład w przypadku domyślnego zestawu ASE o rozmiarze z dwoma frontoniami ukończenie szablonu potrwa około godziny i dwudziestu minut.  Gdy szablon jest uruchomiony, nie będzie można skalować ase.  

Po zakończeniu pracy szablonu dostęp do aplikacji w asecie z równoważeniem obciążenia będzie możliwy za pośrednictwem protokołu HTTPS, a połączenia zostaną zabezpieczone przy użyciu domyślnego certyfikatu TLS/SSL.  Domyślny certyfikat protokołu TLS/SSL będzie używany, gdy aplikacje w zestawie ASE z równoważeniem obciążenia są adresowane przy użyciu kombinacji nazwy aplikacji i domyślnej nazwy hosta.  Na przykład *`https://mycustomapp.internal-contoso.com`* użyj domyślnego certyfikatu TLS/SSL dla **.internal-contoso.com*.

Jednak podobnie jak aplikacje działające w publicznej usłudze wielodostępnej, deweloperzy mogą również konfigurować niestandardowe nazwy hostów dla poszczególnych aplikacji, a następnie konfigurować unikatowe powiązania certyfikatów SNI TLS/SSL dla poszczególnych aplikacji.  

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę z App Service środowiskami, [zobacz Wprowadzenie do App Service Environment](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

