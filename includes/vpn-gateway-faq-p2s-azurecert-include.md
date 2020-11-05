---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5ef67580928a45609f50d3fe798eb9d054265c0a
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376011"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Co należy zrobić, jeśli podczas nawiązywania połączenia przy użyciu uwierzytelniania certyfikatu występuje niezgodność certyfikatu?

Usuń zaznaczenie pola wyboru **"Weryfikuj tożsamość serwera, sprawdzając certyfikat"** lub **Dodaj nazwę FQDN serwera wraz z certyfikatem** podczas ręcznego tworzenia profilu. Można to zrobić, uruchamiając polecenie **Rasphone** z wiersza polecenia i wybierając profil z listy rozwijanej.

Pomijanie sprawdzania poprawności tożsamości serwera nie jest zalecane, ale z uwierzytelnianiem przy użyciu certyfikatu platformy Azure, ten sam certyfikat jest używany do sprawdzania poprawności serwera w protokole tunelowania sieci VPN (IKEv2/SSTP) i protokół EAP. Ponieważ certyfikat serwera i nazwa FQDN są już weryfikowane przez protokół tunelowania sieci VPN, nadmiarowe sprawdzenie poprawności tego samego ponownie w protokole EAP.

![Uwierzytelnianie punkt-lokacja](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Certyfikat serwera")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Czy można użyć własnego głównego urzędu certyfikacji PKI do generowania certyfikatów dla połączeń punkt-lokacja?

Tak. Wcześniej można było używać tylko certyfikatów głównych z podpisem własnym. Nadal można przesłać 20 certyfikatów głównych.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Czy mogę używać certyfikatów z Azure Key Vault?

Nie.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Jakie narzędzia umożliwiają tworzenie certyfikatów?

Możesz użyć rozwiązania infrastruktury kluczy publicznych przedsiębiorstwa (wewnętrznej infrastruktury kluczy publicznych), programu Azure PowerShell, narzędzia MakeCert lub protokołu OpenSSL.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Czy są dostępne instrukcje dotyczące ustawień i parametrów certyfikatów?

* **Wewnętrzne rozwiązanie infrastruktury kluczy publicznych/rozwiązanie infrastruktury kluczy publicznych w przedsiębiorstwie:** zobacz procedurę [generowania certyfikatów](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** procedurę można znaleźć w artykule dotyczącym programu [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** procedurę można znaleźć w artykule dotyczącym narzędzia [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL** 

    * W przypadku eksportowania certyfikatów należy przekonwertować certyfikat główny na format Base64.

    * Certyfikat klienta:

      * W przypadku tworzenia klucza prywatnego należy określić długość równą 4096.
      * W przypadku tworzenia certyfikatu dla parametru *-extensions* należy określić wartość *usr_cert*.
