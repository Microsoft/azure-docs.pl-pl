---
title: Rozwiązywanie problemów z dwoma protokołami dla Azure NetApp Files | Microsoft Docs
description: Opisuje komunikaty o błędach i rozwiązania, które mogą pomóc w rozwiązywaniu problemów z podwójnym protokołem Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/01/2020
ms.author: b-juche
ms.openlocfilehash: cc046a27fec1b9e361ff840c7ae0f077e2987b67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654210"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Rozwiązywanie problemów z woluminami używającymi dwóch protokołów

W tym artykule opisano rozwiązania przyczyn błędów, które mogą wystąpić podczas tworzenia woluminów dwuprotokołowych lub zarządzania nimi.

## <a name="error-conditions-and-resolutions"></a>Warunki błędów i rozwiązania

|     Warunki błędów    |     Rozwiązanie    |
|-|-|
| Tworzenie woluminu z podwójnym protokołem nie powiodło się z powodu błędu `This Active Directory has no Server root CA Certificate` .    |     Jeśli ten błąd występuje podczas tworzenia woluminu dwuprotokołowego, upewnij się, że certyfikat głównego urzędu certyfikacji jest przekazywany na koncie NetApp.    |
| Tworzenie woluminu z podwójnym protokołem nie powiodło się z powodu błędu `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  Należy wziąć pod uwagę następujące rozwiązania:   <ul><li>Upewnij się, że podczas dołączania Active Directory (AD) do konta NetApp jest dodawany wymagany certyfikat główny. Zobacz [przekazywanie Active Directory publiczny certyfikat główny urzędu certyfikacji](create-volumes-dual-protocol.md#upload-active-directory-certificate-authority-public-root-certificate).   </li><li>Na serwerze DNS może brakować rekordu wskaźnika (PTR) maszyny hosta usługi AD. Należy utworzyć strefę wyszukiwania wstecznego na serwerze DNS, a następnie dodać rekord PTR maszyny hosta usługi AD w tej strefie wyszukiwania wstecznego. <br> Załóżmy na przykład, że adres IP komputera usługi AD to, nazwa `1.1.1.1` hosta maszyny usługi AD (jak znaleziono przy użyciu `hostname` polecenia) `AD1` , a domena to `myDomain.com` .  Rekord PTR dodany do strefy wyszukiwania wstecznego powinien mieć wartość `1.1.1.1`  ->  `AD1.myDomain.com` . </li></ul>  |
| Tworzenie woluminu z podwójnym protokołem nie powiodło się z powodu błędu `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Ten błąd wskazuje, że hasło usługi AD jest niepoprawne, gdy Active Directory jest przyłączony do konta NetApp. Zaktualizuj połączenie usługi AD przy użyciu poprawnego hasła i spróbuj ponownie. |
| Tworzenie woluminu z podwójnym protokołem nie powiodło się z powodu błędu `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Ten błąd wskazuje, że usługa DNS jest nieosiągalna. Przyczyną może być fakt, że adres IP DNS jest niepoprawny lub występuje problem z siecią. Sprawdź adres IP DNS wprowadzony w polu połączenie usługi AD i upewnij się, że adres IP jest prawidłowy. <br> Upewnij się również, że usługi AD i wolumin są w tym samym regionie i w tej samej sieci wirtualnej. Jeśli znajdują się w różnych sieci wirtualnychach, upewnij się, że Komunikacja równorzędna sieci wirtualnej jest ustanowiona między tymi dwoma sieci wirtualnych.|
| Wystąpił błąd podczas instalowania dwuprotokołowego woluminu. | Dwuprotokołowy wolumin obsługuje zarówno system plików NFS, jak i protokół SMB.  W przypadku próby uzyskania dostępu do zainstalowanego woluminu w systemie UNIX system próbuje zamapować użytkownika systemu UNIX, którego używasz, do użytkownika systemu Windows. Jeśli nie zostanie znalezione żadne mapowanie, wystąpi błąd "odmowa uprawnień". <br> Ta sytuacja ma zastosowanie również w przypadku korzystania z użytkownika "root" w celu uzyskania dostępu. <br> Aby uniknąć problemu "odmowa uprawnień", należy się upewnić, że Active Directory systemu Windows zawiera `pcuser` przed uzyskaniem dostępu do punktu instalacji. Po dodaniu `pcuser` problemu "odmowa uprawnień" odczekaj 24 godziny na wyczyszczenie wpisu pamięci podręcznej, zanim spróbujesz ponownie uzyskać dostęp. |

## <a name="next-steps"></a>Następne kroki  

* [Tworzenie woluminu dwuprotokołowego](create-volumes-dual-protocol.md)
* [Konfigurowanie klienta sieciowego systemu plików dla usługi Azure NetApp Files](configure-nfs-clients.md)
