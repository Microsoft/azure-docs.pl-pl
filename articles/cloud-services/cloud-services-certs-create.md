---
title: Cloud Services (klasyczny) i certyfikaty zarządzania | Microsoft Docs
description: Dowiedz się więcej na temat sposobu tworzenia i wdrażania certyfikatów dla usług w chmurze oraz do uwierzytelniania za pomocą interfejsu API zarządzania na platformie Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c73f9812f344eecf4e51f43405b48693ddfa191b
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739738"
---
# <a name="certificates-overview-for-azure-cloud-services-classic"></a>Omówienie certyfikatów dla Cloud Services platformy Azure (wersja klasyczna)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Certyfikaty są używane na platformie Azure na potrzeby usług Cloud Services ([certyfikaty usługi](#what-are-service-certificates)) oraz do uwierzytelniania za pomocą interfejsu API zarządzania ([certyfikaty zarządzania](#what-are-management-certificates)). Ten temat zawiera ogólne omówienie obu typów certyfikatów, jak [tworzyć](#create) i wdrażać je na platformie Azure.

Certyfikaty używane na platformie Azure to certyfikaty x. 509 v3 i mogą być podpisane przez inny zaufany certyfikat lub mogą być podpisane z podpisem własnym. Certyfikat z podpisem własnym jest podpisany przez własnego twórcę, dlatego nie jest domyślnie zaufany. Większość przeglądarek umożliwia zignorowanie tego problemu. W przypadku tworzenia i testowania usług w chmurze należy używać tylko certyfikatów z podpisem własnym. 

Certyfikaty używane przez platformę Azure mogą zawierać klucz publiczny. Certyfikaty mają odcisk palca, który umożliwia identyfikowanie ich w sposób niejednoznaczny. Ten odcisk palca jest używany w [pliku konfiguracji](cloud-services-configure-ssl-certificate-portal.md) platformy Azure w celu zidentyfikowania certyfikatu, który ma być używany przez usługę w chmurze. 

>[!Note]
>Usługa Azure Cloud Services nie akceptuje zaszyfrowanego certyfikatu AES256-SHA256.

## <a name="what-are-service-certificates"></a>Co to są certyfikaty usługi?
Certyfikaty usługi są dołączone do usług w chmurze i umożliwiają bezpieczną obustronną komunikację z usługą. Na przykład, jeśli wdrożono rolę sieci Web, należy podać certyfikat, który może uwierzytelniać uwidoczniony punkt końcowy HTTPS. Certyfikaty usług zdefiniowane w definicji usługi są automatycznie wdrażane na maszynie wirtualnej, na której działa wystąpienie roli. 

Certyfikat usługi można przekazać na platformę Azure przy użyciu witryny Azure Portal lub klasycznego modelu wdrażania. Certyfikaty usługi są skojarzone z konkretną usługą w chmurze. Są one przypisane do wdrożenia w pliku definicji usługi.

Certyfikaty usługi mogą być zarządzane oddzielnie od usług i mogą być zarządzane przez różne osoby. Na przykład deweloper może przekazać pakiet usługi, który odwołuje się do certyfikatu przekazanego wcześniej przez Menedżera IT do platformy Azure. Menedżer IT może zarządzać tym certyfikatem i odnawiać go (zmieniając konfigurację usługi) bez konieczności przekazywania nowego pakietu usługi. Aktualizowanie bez nowego pakietu usługi jest możliwe, ponieważ nazwa logiczna, nazwa magazynu i lokalizacja certyfikatu znajduje się w pliku definicji usługi i podczas gdy odcisk palca certyfikatu jest określony w pliku konfiguracji usługi. Aby zaktualizować certyfikat, należy tylko przekazać nowy certyfikat i zmienić wartość odcisku palca w pliku konfiguracji usługi.

>[!Note]
>[Cloud Services często zadawane pytania — konfiguracja i zarządzanie](cloud-services-configuration-and-management-faq.md) zawiera kilka przydatnych informacji o certyfikatach.

## <a name="what-are-management-certificates"></a>Co to są certyfikaty zarządzania?
Certyfikaty zarządzania umożliwiają uwierzytelnianie przy użyciu klasycznego modelu wdrażania. Wiele programów i narzędzi (takich jak program Visual Studio lub zestaw Azure SDK) używa tych certyfikatów do zautomatyzowania konfigurowania i wdrażania różnych usług platformy Azure. Nie są one w rzeczywistości związane z usługami w chmurze. 

> [!WARNING]
> Ostrożnie! Te typy certyfikatów umożliwiają osobom, które uwierzytelniają się za ich pomocą, Zarządzanie subskrypcją, z którą są skojarzone. 
> 
> 

### <a name="limitations"></a>Ograniczenia
Istnieje limit 100 certyfikatów zarządzania na subskrypcję. Istnieje również limit 100 certyfikatów zarządzania dla wszystkich subskrypcji w ramach identyfikatora użytkownika określonego administratora usługi. Jeśli identyfikator użytkownika administratora konta został już użyty do dodania certyfikatów zarządzania 100 i istnieje potrzeba więcej certyfikatów, możesz dodać współadministratora, aby dodać dodatkowe certyfikaty. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Utwórz nowy certyfikat z podpisem własnym
Możesz użyć dowolnego dostępnego narzędzia do utworzenia certyfikatu z podpisem własnym, o ile są one zgodne z tymi ustawieniami:

* Certyfikat X. 509.
* Zawiera klucz publiczny.
* Utworzono dla wymiany kluczy (plik PFX).
* Nazwa podmiotu musi być zgodna z domeną używaną do uzyskiwania dostępu do usługi w chmurze.

    > Nie można uzyskać certyfikatu TLS/SSL dla cloudapp.net (lub dla dowolnej domeny powiązanej z platformą Azure). Nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną w celu uzyskania dostępu do aplikacji. Na przykład **contoso.NET**, a nie **contoso.cloudapp.NET**.

* Minimum 2048-bitowe szyfrowanie.
* **Tylko certyfikat usługi**: certyfikat po stronie klienta musi znajdować się w *osobistym* magazynie certyfikatów.

Istnieją dwa proste metody tworzenia certyfikatu w systemie Windows przy użyciu `makecert.exe` Narzędzia lub usług IIS.

### <a name="makecertexe"></a>Makecert.exe
To narzędzie jest przestarzałe i nie jest już udokumentowane w tym miejscu. Aby uzyskać więcej informacji, zobacz [ten artykuł w witrynie MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Jeśli chcesz użyć certyfikatu z adresem IP zamiast z domeny, użyj adresu IP w parametrze-DnsName.


Jeśli chcesz użyć tego [certyfikatu w portalu zarządzania](/previous-versions/azure/azure-api-management-certs), wyeksportuj go do pliku **. cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
W Internecie znajduje się wiele stron, które obejmują, jak to zrobić za pomocą usług IIS. [Poniżej](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) znajduje się świetna wiedza, którą uważam za dobrze. 

### <a name="linux"></a>Linux
W [tym](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artykule opisano sposób tworzenia certyfikatów przy użyciu protokołu SSH.

## <a name="next-steps"></a>Następne kroki
[Przekaż certyfikat usługi do Azure Portal](cloud-services-configure-ssl-certificate-portal.md).

Przekaż [certyfikat interfejsu API zarządzania](/previous-versions/azure/azure-api-management-certs) do Azure Portal.