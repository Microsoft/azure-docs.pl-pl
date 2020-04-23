---
title: Certyfikaty usług w chmurze i zarządzania | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć certyfikaty i używać ich za pomocą platformy Microsoft Azure
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: cf2106302064df5ede02d18f253436047a5d33d8
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024612"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Certificates overview for Azure Cloud Services (Omówienie certyfikatów usług Azure Cloud Services)
Certyfikaty są używane na platformie Azure dla usług w chmurze[(certyfikaty usług)](#what-are-service-certificates)i do uwierzytelniania za pomocą interfejsu API zarządzania[(certyfikaty zarządzania).](#what-are-management-certificates) W tym temacie przedstawiono ogólne omówienie obu typów certyfikatów, jak [je utworzyć](#create) i wdrożyć na platformie Azure.

Certyfikaty używane na platformie Azure to certyfikaty x.509 w wersji 3 i mogą być podpisane przez inny zaufany certyfikat lub mogą być podpisywane samodzielnie. Certyfikat z podpisem własnym jest podpisany przez własnego twórcę, dlatego nie jest domyślnie zaufany. Większość przeglądarek umożliwia zignorowanie tego problemu. Certyfikaty z podpisem własnym należy używać tylko podczas tworzenia i testowania usług w chmurze. 

Certyfikaty używane przez platformę Azure mogą zawierać klucz publiczny. Certyfikaty mają odcisk palca, który zapewnia możliwość zidentyfikowania ich w jednoznaczny sposób. Ten odcisk palca jest używany w [pliku konfiguracji](cloud-services-configure-ssl-certificate-portal.md) platformy Azure, aby zidentyfikować certyfikat, którego powinna używać usługa w chmurze. 

>[!Note]
>Usługi w chmurze azure nie akceptuje AES256-SHA256 zaszyfrowany certyfikat.

## <a name="what-are-service-certificates"></a>Co to są certyfikaty usługi?
Certyfikaty usługi są dołączone do usług w chmurze i umożliwiają bezpieczną obustronną komunikację z usługą. Na przykład jeśli wdrożono rolę sieci web, należy podać certyfikat, który może uwierzytelnić narażony punkt końcowy HTTPS. Certyfikaty usług, zdefiniowane w definicji usługi, są automatycznie wdrażane na maszynie wirtualnej, na która jest uruchomiona wystąpienie roli. 

Certyfikat usługi można przekazać na platformę Azure przy użyciu witryny Azure Portal lub klasycznego modelu wdrażania. Certyfikaty usługi są skojarzone z konkretną usługą w chmurze. Są one przypisane do wdrożenia w pliku definicji usługi.

Certyfikaty usług mogą być zarządzane oddzielnie od usług i mogą być zarządzane przez różne osoby. Na przykład deweloper może przekazać pakiet usług, który odwołuje się do certyfikatu, który menedżer IT wcześniej przekazał na platformę Azure. Menedżer IT może zarządzać tym certyfikatem i odnawiać go (zmieniając konfigurację usługi) bez konieczności przekazywania nowego pakietu usługi. Aktualizowanie bez nowego pakietu usług jest możliwe, ponieważ nazwa logiczna, nazwa sklepu i lokalizacja certyfikatu znajduje się w pliku definicji usługi i gdy odcisk palca certyfikatu jest określony w pliku konfiguracji usługi. Aby zaktualizować certyfikat, należy tylko przekazać nowy certyfikat i zmienić wartość odcisku palca w pliku konfiguracji usługi.

>[!Note]
>Często zadawane [pytania dotyczące usług w chmurze — konfiguracja i zarządzanie](cloud-services-configuration-and-management-faq.md) zawiera przydatne informacje na temat certyfikatów.

## <a name="what-are-management-certificates"></a>Co to są certyfikaty zarządzania?
Certyfikaty zarządzania umożliwiają uwierzytelnianie przy użyciu klasycznego modelu wdrażania. Wiele programów i narzędzi (takich jak program Visual Studio lub zestaw Azure SDK) używa tych certyfikatów do zautomatyzowania konfigurowania i wdrażania różnych usług platformy Azure. Nie są one tak naprawdę związane z usługami w chmurze. 

> [!WARNING]
> Ostrożnie! Te typy certyfikatów umożliwiają każdemu, kto uwierzytelnia się z nimi, zarządzanie subskrypcją, z którą są skojarzone. 
> 
> 

### <a name="limitations"></a>Ograniczenia
Istnieje limit 100 certyfikatów zarządzania na subskrypcję. Istnieje również limit 100 certyfikatów zarządzania dla wszystkich subskrypcji w ramach identyfikatora użytkownika określonego administratora usługi. Jeśli identyfikator użytkownika administratora konta został już użyty do dodania 100 certyfikatów zarządzania i istnieje potrzeba zwiększenia liczby certyfikatów, można dodać współadministratora, aby dodać dodatkowe certyfikaty. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Tworzenie nowego certyfikatu z podpisem własnym
Za pomocą dowolnego dostępnego narzędzia można utworzyć certyfikat z podpisem własnym, o ile są one zgodne z następującymi ustawieniami:

* Certyfikat X.509.
* Zawiera klucz publiczny.
* Utworzono do wymiany kluczy (plik pfx).
* Nazwa podmiotu musi być zgodna z domeną używaną do uzyskiwania dostępu do usługi w chmurze.

    > Nie można uzyskać certyfikatu TLS/SSL dla cloudapp.net (lub dla dowolnej domeny związanej z platformą Azure); nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną do uzyskiwania dostępu do aplikacji. Na przykład **contoso.net**, nie **contoso.cloudapp.net**.

* Minimum szyfrowanie 2048-bitowe.
* **Tylko certyfikat serwisowy:** Certyfikat po stronie klienta musi znajdować się w magazynie certyfikatów *osobistych.*

Istnieją dwa proste sposoby tworzenia certyfikatu `makecert.exe` w systemie Windows za pomocą narzędzia lub usług IIS.

### <a name="makecertexe"></a>Makecert.exe
To narzędzie zostało przestarzałe i nie jest już tutaj udokumentowane. Aby uzyskać więcej informacji, zobacz [ten artykuł MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Jeśli chcesz użyć certyfikatu z adresem IP zamiast domeny, użyj adresu IP w parametrze -DnsName.


Jeśli chcesz użyć tego [certyfikatu z portalem zarządzania,](../azure-api-management-certs.md)wyeksportuj go do pliku **cer:**

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Istnieje wiele stron w Internecie, które obejmują, jak to zrobić z IIS. [Oto](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) wielki znalazłem, że myślę, że wyjaśnia to dobrze. 

### <a name="linux"></a>Linux
[W tym](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artykule opisano sposób tworzenia certyfikatów przy ssh.

## <a name="next-steps"></a>Następne kroki
[Przekaż certyfikat usługi do witryny Azure portal](cloud-services-configure-ssl-certificate-portal.md).

Przekaż [certyfikat interfejsu API zarządzania](../azure-api-management-certs.md) do witryny Azure portal.




