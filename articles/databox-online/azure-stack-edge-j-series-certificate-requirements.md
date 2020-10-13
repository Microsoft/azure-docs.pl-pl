---
title: Wymagania dotyczące certyfikatów i rozwiązywanie problemów z Azure Stack EDGE Pro | Microsoft Docs
description: Opisuje wymagania dotyczące certyfikatów i rozwiązywanie problemów z błędami certyfikatów przy użyciu urządzenia Azure Stack EDGE Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: e67b507baf1c3271a7fe32318597722e52fd3890
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891388"
---
# <a name="certificate-requirements"></a>Wymagania certyfikatu

W tym artykule opisano wymagania dotyczące certyfikatu, które muszą zostać spełnione, aby można było zainstalować certyfikaty na urządzeniu z systemem Azure Stack brzeg Pro. Wymagania są związane z certyfikatami PFX, Urząd wystawiania, nazwa podmiotu certyfikatu i Alternatywna nazwa podmiotu oraz obsługiwane algorytmy certyfikatów.

## <a name="certificate-issuing-authority"></a>Urząd wystawiający certyfikaty

Wymagania dotyczące wystawiania certyfikatów są następujące:

* Certyfikaty muszą być wystawiane przez wewnętrzny urząd certyfikacji lub publiczny urząd certyfikacji.

* Korzystanie z certyfikatów z podpisem własnym nie jest obsługiwane.

* Certyfikat *wystawiony dla:* pole nie może być takie samo jak *wystawiony przez:* pole z wyjątkiem certyfikatów głównego urzędu certyfikacji.



## <a name="certificate-algorithms"></a>Algorytmy certyfikatów

Algorytmy certyfikatów muszą mieć następujące wymagania:

* Certyfikaty muszą używać algorytmu klucza RSA.

* Algorytm podpisu certyfikatu nie może być SHA1.

* Minimalny rozmiar klucza to 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Nazwa podmiotu certyfikatu i Alternatywna nazwa podmiotu

Certyfikaty muszą mieć następującą nazwę podmiotu i alternatywne wymagania dotyczące nazw podmiotu:

* Można użyć jednego certyfikatu obejmującego wszystkie spacje nazw w polach alternatywnej nazwy podmiotu (SAN) certyfikatu. Alternatywnie możesz użyć pojedynczych certyfikatów dla każdej przestrzeni nazw. Oba podejścia wymagają użycia symboli wieloznacznych dla punktów końcowych, gdy jest to wymagane, takich jak obiekt BLOB (Binary Large Object).

* Upewnij się, że nazwy podmiotów (nazwa pospolita w nazwie podmiotu) są częścią alternatywnych nazw podmiotu w rozszerzeniu alternatywnej nazwy podmiotu.

* Można użyć pojedynczego certyfikatu wieloznacznego obejmującego wszystkie spacje nazw w polach sieci SAN certyfikatu.

* Podczas tworzenia certyfikatu punktu końcowego należy skorzystać z następującej tabeli:

    |Type |Nazwa podmiotu (SN)  |Alternatywna nazwa podmiotu (SAN)  |Przykład nazwy podmiotu |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Lokalny interfejs użytkownika| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Pojedynczy certyfikat sieci SAN dla obu punktów końcowych|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Węzeł|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate to stałe.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>Certyfikat PFX

Certyfikaty PFX zainstalowane na urządzeniu Azure Stack EDGE Pro powinny spełniać następujące wymagania:

* Po uzyskaniu certyfikatów od urzędu certyfikacji upewnij się, że uzyskano pełny łańcuch podpisywania certyfikatów.

* Podczas eksportowania certyfikatu PFX upewnij się, że wybrano opcję **Dołącz wszystkie certyfikaty w łańcuchu, jeśli jest to możliwe** .

* Użyj certyfikatu PFX dla punktu końcowego, lokalnego interfejsu użytkownika, węzła, sieci VPN i Wi-Fi, ponieważ klucze publiczny i prywatny są wymagane dla Azure Stack EDGE Pro. Klucz prywatny musi mieć ustawiony atrybut klucza komputera lokalnego.

* Szyfrowanie PFX certyfikatu powinno mieć wartość 3DES. Jest to domyślne szyfrowanie używane podczas eksportowania z magazynu certyfikatów systemu Windows 10 lub Windows Server 2016. Aby uzyskać więcej informacji dotyczących algorytmu 3DES, zobacz [Triple DES](https://en.wikipedia.org/wiki/Triple_DES).

* Pliki PFX certyfikatu muszą mieć prawidłowy *podpis cyfrowy* i wartości *KeyEncipherment* w polu *użycie klucza* .

* Pliki PFX certyfikatu muszą mieć wartość *uwierzytelnianie serwera wartości (1.3.6.1.5.5.7.3.1)* i *uwierzytelnianie klienta (1.3.6.1.5.5.7.3.2)* w polu *ulepszone użycie klucza* .

* Hasła do wszystkich plików PFX certyfikatów muszą być takie same w czasie wdrażania, jeśli używasz narzędzia sprawdzania gotowości Azure Stack. Aby uzyskać więcej informacji, zobacz [Tworzenie certyfikatów dla usługi Azure Stack EDGE Pro przy użyciu narzędzia do sprawdzania gotowości centrum Azure Stack](azure-stack-edge-j-series-create-certificates-tool.md).

* Hasło do pliku PFX certyfikatu musi być złożonym hasłem. Zanotuj to hasło, ponieważ jest ono używane jako parametr wdrożenia.

Aby uzyskać więcej informacji, zobacz [Eksportowanie certyfikatów PFX z kluczem prywatnym](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

## <a name="next-steps"></a>Następne kroki

[Używanie certyfikatów w programie Azure Stack EDGE Pro](azure-stack-edge-j-series-manage-certificates.md)

[Utwórz certyfikaty dla Azure Stack EDGE Pro przy użyciu narzędzia do sprawdzania gotowości centrum Azure Stack](azure-stack-edge-j-series-create-certificates-tool.md)

[Eksportowanie certyfikatów PFX z kluczem prywatnym](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Rozwiązywanie problemów z błędami certyfikatów](azure-stack-edge-j-series-certificate-troubleshooting.md)