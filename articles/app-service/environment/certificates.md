---
title: Powiązania certyfikatów
description: Wyjaśnij wiele tematów dotyczących certyfikatów na App Service Environment. Dowiedz się, jak powiązania certyfikatów działają w przypadku aplikacji z jednym dzierżawcą w środowisku ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 306445e26e5b236b49273b9ab8888ecc610bc075
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88962047"
---
# <a name="certificates-and-the-app-service-environment"></a>Certyfikaty i App Service Environment 

App Service Environment (ASE) to wdrożenie Azure App Service, które działa w ramach Virtual Network platformy Azure. Można ją wdrożyć za pomocą punktu końcowego aplikacji dostępnego z Internetu lub punktu końcowego aplikacji znajdującego się w sieci wirtualnej. Jeśli środowisko ASE zostanie wdrożone za pomocą punktu końcowego dostępnego z Internetu, to wdrożenie jest nazywane zewnętrznym środowiskiem ASE. Jeśli środowisko ASE zostanie wdrożone z punktem końcowym w sieci wirtualnej, to wdrożenie jest nazywane ILB ASE. Więcej informacji na temat ILB ASE można znaleźć na stronie [Tworzenie i używanie dokumentu ILB ASE](./create-ilb-ase.md) .

Środowisko ASE to pojedynczy system dzierżawy. Ponieważ jest to pojedynczy dzierżawca, niektóre funkcje są dostępne tylko w środowisku ASE, które nie są dostępne w App Service z wieloma dzierżawcami. 

## <a name="ilb-ase-certificates"></a>Certyfikaty środowiska ILB ASE 

Jeśli używasz zewnętrznego środowiska ASE, Twoje aplikacje są osiągalne w witrynie [nazwa_aplikacji]. [asename]. p. azurewebsites. NET. Domyślnie wszystkie środowisk ASE, nawet ILB środowisk ASE, są tworzone przy użyciu certyfikatów, które są zgodne z tym formatem. W przypadku środowiska ILB ASE aplikacje są osiągane na podstawie nazwy domeny określonej podczas tworzenia ILB ASE. Aby aplikacje obsługiwały protokół TLS, należy przekazać certyfikaty. Uzyskaj prawidłowy certyfikat TLS/SSL przy użyciu wewnętrznych urzędów certyfikacji, kupując certyfikat od zewnętrznego wystawcy lub korzystając z certyfikatu z podpisem własnym. 

Dostępne są dwie opcje konfigurowania certyfikatów w środowisku ILB ASE.  Można ustawić wieloznaczny certyfikat domyślny dla ILB ASE lub ustawić certyfikaty dla poszczególnych aplikacji sieci Web w środowisku ASE.  Niezależnie od dokonanego wyboru należy prawidłowo skonfigurować następujące atrybuty certyfikatu:

- **Temat:** Ten atrybut musi być ustawiony na *. [domena-główna — w tym miejscu] dla wieloznacznego certyfikatu ILB ASE. Jeśli tworzysz certyfikat dla aplikacji, powinien on mieć wartość [nazwa_aplikacji]. [domena-główna — w tym miejscu]
- **Alternatywna nazwa podmiotu:** Ten atrybut musi zawierać oba *. [domena-główna — w tym miejscu] i *. SCM. [domena-główna — w tym miejscu] dla wieloznacznego certyfikatu ILB ASE. Jeśli tworzysz certyfikat dla aplikacji, powinien on mieć wartość [nazwa_aplikacji]. [domena-główna — w tym miejscu] i [nazwa_aplikacji]. SCM. [domena-główna — w tym miejscu].

Jako trzeci wariant można utworzyć certyfikat ILB ASE, który zawiera wszystkie nazwy poszczególnych aplikacji w sieci SAN certyfikatu zamiast używać odwołań do symboli wieloznacznych. Problem z tą metodą polega na tym, że musisz znać nazwy aplikacji umieszczanych w środowisku ASE lub trzeba aktualizować certyfikat ILB ASE.

### <a name="upload-certificate-to-ilb-ase"></a>Przekaż certyfikat do ILB ASE 

Po utworzeniu ILB ASE w portalu należy ustawić certyfikat dla ILB ASE. Dopóki certyfikat nie zostanie ustawiony, środowisko ASE wyświetli transparent, że certyfikat nie został ustawiony.  

Przekazany certyfikat musi być plikiem pfx. Po przekazaniu certyfikatu środowisko ASE wykona operację skalowania, aby ustawić certyfikat. 

Nie można utworzyć środowiska ASE i przekazać certyfikatu jako jednej akcji w portalu lub nawet w jednym szablonie. Jako osobną akcję można przekazać certyfikat przy użyciu szablonu zgodnie z opisem w dokumencie Tworzenie środowiska [ASE z szablonu](./create-from-template.md) .  

Jeśli chcesz szybko utworzyć certyfikat z podpisem własnym w celu przetestowania, możesz użyć następującego bitu programu PowerShell:

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password
```

Podczas tworzenia certyfikatu z podpisem własnym należy upewnić się, że nazwa podmiotu ma format CN = {ASE_NAME_HERE} _InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certyfikaty aplikacji 

Aplikacje hostowane w środowisku ASE mogą korzystać z funkcji certyfikatów skoncentrowanych na aplikacji, które są dostępne w App Service z wieloma dzierżawcami. Te funkcje obejmują:  

- SNI certyfikaty 
- Protokół SSL oparty na protokole IP, który jest obsługiwany tylko z zewnętrznym środowiskiem ASE.  ILB ASE nie obsługuje protokołu SSL opartego na protokole IP.
- Certyfikaty hostowane magazynu kluczy 

Instrukcje dotyczące przekazywania i zarządzania tymi certyfikatami są dostępne w temacie [Dodawanie certyfikatu TLS/SSL w Azure App Service](../configure-ssl-certificate.md).  Jeśli po prostu skonfigurujesz certyfikaty w taki sposób, aby odpowiadały niestandardowej nazwie domeny przypisanej do aplikacji sieci Web, wówczas te instrukcje będą wystarczające. Jeśli przekazujesz certyfikat dla aplikacji sieci Web ILB ASE z domyślną nazwą domeny, określ lokację SCM w sieci SAN certyfikatu zgodnie z wcześniejszym opisem. 

## <a name="tls-settings"></a>Ustawienia protokołu TLS 

Ustawienie protokołu TLS można skonfigurować na poziomie aplikacji.  

## <a name="private-client-certificate"></a>Prywatny certyfikat klienta 

Typowym przypadkiem użycia jest skonfigurowanie aplikacji jako klienta w modelu klient-serwer. Jeśli zabezpieczasz serwer przy użyciu certyfikatu prywatnego urzędu certyfikacji, musisz przekazać certyfikat klienta do swojej aplikacji.  Poniższe instrukcje spowodują załadowanie certyfikatów do truststore pracowników, na których uruchomiono aplikację. Jeśli załadujesz certyfikat do jednej aplikacji, możesz użyć go razem z innymi aplikacjami w tym samym planie App Service bez przekazywania certyfikatu.

Aby przekazać certyfikat do aplikacji w środowisku ASE:

1. Wygeneruj plik *CER* dla certyfikatu. 
2. Przejdź do aplikacji, która wymaga certyfikatu w Azure Portal
3. Przejdź do ustawień protokołu SSL w aplikacji. Kliknij pozycję Przekaż certyfikat. Wybierz pozycję Publiczna. Wybierz pozycję komputer lokalny. Podaj nazwę Przeglądaj i wybierz plik *CER* . Wybierz pozycję Przekaż. 
4. Skopiuj odcisk palca.
5. Przejdź do pozycji Ustawienia aplikacji. Utwórz ustawienie aplikacji WEBSITE_LOAD_ROOT_CERTIFICATES z odciskiem palca jako wartość. Jeśli masz wiele certyfikatów, możesz je umieścić w tym samym ustawieniu oddzielone przecinkami i bez odstępów, takich jak 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Certyfikat będzie dostępny dla wszystkich aplikacji w tym samym planie usługi App Service, które zostały skonfigurowane dla tego ustawienia. Jeśli potrzebujesz, aby była dostępna dla aplikacji w innym planie App Service, musisz powtórzyć operację ustawienia aplikacji w aplikacji w ramach tego planu App Service. Aby sprawdzić, czy certyfikat jest ustawiony, przejdź do konsoli programu kudu i wydaj następujące polecenie w konsoli debugowania programu PowerShell:

```azurepowershell-interactive
dir cert:\localmachine\root
```

Aby przeprowadzić testowanie, można utworzyć certyfikat z podpisem własnym i wygenerować plik *. cer* z następującym programem PowerShell: 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.cer"
export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```