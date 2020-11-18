---
title: 'VPN Gateway platformy Azure: generowanie & eksportowanie certyfikatów dla P2S: MakeCert'
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i Wygeneruj certyfikaty klienta przy użyciu MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 55e22ebec5853d6b4f10b53be8e24f4dbebe4e1f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659781"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Generowanie i eksportowanie certyfikatów dla połączeń punkt-lokacja za pomocą MakeCert

Połączenia punkt-lokacja używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klienta przy użyciu MakeCert. Jeśli szukasz różnych instrukcji dotyczących certyfikatów, zobacz [Certificates-PowerShell](vpn-gateway-certificates-point-to-site.md) lub [Certificates-Linux](vpn-gateway-certificates-point-to-site-linux.md).

Chociaż zalecamy używanie [kroków programu PowerShell systemu Windows 10](vpn-gateway-certificates-point-to-site.md) do tworzenia certyfikatów, firma Microsoft udostępnia te instrukcje MakeCert jako opcjonalną metodę. Certyfikaty generowane przy użyciu jednej z tych metod można zainstalować w [dowolnym obsługiwanym systemie operacyjnym klienta](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). Jednak MakeCert ma następujące ograniczenie:

* MakeCert jest przestarzała. Oznacza to, że to narzędzie można usunąć w dowolnym momencie. Nie będzie to miało wpływu na wszystkie certyfikaty, które zostały już wygenerowane przy użyciu usługi MakeCert, gdy MakeCert nie jest już dostępny. MakeCert jest używana tylko do generowania certyfikatów, a nie mechanizmu weryfikacji.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Tworzenie certyfikatu głównego z podpisem własnym

Poniższe kroki pokazują, jak utworzyć certyfikat z podpisem własnym za pomocą MakeCert. Te kroki nie są specyficzne dla modelu wdrożenia. Są one prawidłowe dla Menedżer zasobów i klasycznych.

1. Pobierz i zainstaluj [MakeCert](/windows/win32/seccrypto/makecert).
2. Po instalacji można zwykle znaleźć narzędzie makecert.exe w tej ścieżce: "C:\Program Files (x86) \Windows Kits\10\bin \<arch> ". Chociaż jest możliwe, że został on zainstalowany w innej lokalizacji. Otwórz wiersz polecenia jako administrator i przejdź do lokalizacji narzędzia MakeCert. Można użyć poniższego przykładu, dostosowując do właściwej lokalizacji:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Utwórz i Zainstaluj certyfikat w osobistym magazynie certyfikatów na komputerze. Poniższy przykład tworzy odpowiedni plik *. cer* , który jest przekazywany do platformy Azure podczas konfigurowania P2S. Zastąp element "P2SRootCert" i "P2SRootCert. cer" nazwą, której chcesz użyć dla certyfikatu. Certyfikat znajduje się w "Certificates-Current User\Personal\Certificates".

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Eksportowanie klucza publicznego (CER)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Wyeksportowany plik CER musi zostać przekazany do platformy Azure. Aby uzyskać instrukcje, zobacz [Konfigurowanie połączenia punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Aby dodać dodatkowy zaufany certyfikat główny, zapoznaj się z [tą sekcją](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) artykułu.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Wyeksportuj certyfikat z podpisem własnym i klucz prywatny, aby go zapisać (opcjonalnie)

Możesz chcieć wyeksportować certyfikat główny z podpisem własnym i bezpiecznie go zapisać. W razie potrzeby można później zainstalować go na innym komputerze i generować więcej certyfikatów klienta lub wyeksportować inny plik. cer. Aby wyeksportować certyfikat główny z podpisem własnym jako plik PFX, wybierz certyfikat główny i wykonaj te same kroki, zgodnie z opisem w sekcji [Eksportowanie certyfikatu klienta](#clientexport).

## <a name="create-and-install-client-certificates"></a>Tworzenie i instalowanie certyfikatów klienta

Certyfikat z podpisem własnym nie jest instalowany bezpośrednio na komputerze klienckim. Należy wygenerować certyfikat klienta z certyfikatu z podpisem własnym. Następnie należy wyeksportować certyfikat klienta i zainstalować go na komputerze klienckim. Poniższe kroki nie są specyficzne dla modelu wdrożenia. Są one prawidłowe dla Menedżer zasobów i klasycznych.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Generowanie certyfikatu klienta

Na każdym komputerze klienckim nawiązującym połączenie z siecią wirtualną za pomocą połączenia typu punkt-lokacja musi być zainstalowany certyfikat klienta w celu uwierzytelniania. Certyfikat klienta jest generowany na podstawie certyfikatu głównego z podpisem własnym, a następnie eksportowany i instalowany jest certyfikat klienta. Jeśli certyfikat klienta nie jest zainstalowany, uwierzytelnianie kończy się niepowodzeniem. 

Poniższe kroki przeprowadzą Cię przez proces generowania certyfikatu klienta z certyfikatu głównego z podpisem własnym. Można generować wiele certyfikatów klienta z tego samego certyfikatu głównego. Po wygenerowaniu certyfikatów klienta przy użyciu poniższych kroków certyfikat klienta jest automatycznie instalowany na komputerze, który został użyty do wygenerowania certyfikatu. Jeśli chcesz zainstalować certyfikat klienta na innym komputerze klienckim, możesz wyeksportować certyfikat.
 
1. Na tym samym komputerze, który został użyty do utworzenia certyfikatu z podpisem własnym, Otwórz wiersz polecenia jako administrator.
2. Zmodyfikuj i uruchom przykład, aby wygenerować certyfikat klienta.
   * Zmień *wartość "P2SRootCert"* na nazwę elementu głównego z podpisem własnym, z którego jest generowany certyfikat klienta. Upewnij się, że używasz nazwy certyfikatu głównego, która jest bez względu na to, że wartość "CN =" została określona podczas tworzenia katalogu głównego z podpisem własnym.
   * Zmień *P2SChildCert* na nazwę, dla której chcesz wygenerować certyfikat klienta.

   W przypadku uruchomienia poniższego przykładu bez modyfikowania, wynik jest certyfikat klienta o nazwie P2SChildcert w osobistym magazynie certyfikatów, który został wygenerowany na podstawie certyfikatu głównego P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Eksportowanie certyfikatu klienta

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalowanie wyeksportowanego certyfikatu klienta

Aby zainstalować certyfikat klienta, zobacz [Instalowanie certyfikatu klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Następne kroki

Kontynuuj pracę z konfiguracją punkt-lokacja. 

* Aby uzyskać **Menedżer zasobów** kroki dotyczące modelu wdrażania, zobacz [Konfigurowanie P2S przy użyciu natywnego uwierzytelniania certyfikatu platformy Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Kroki **klasycznego** modelu wdrażania można znaleźć w temacie [Konfigurowanie połączenia sieci VPN typu punkt-lokacja z siecią wirtualną (klasyczną)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshooting Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).