---
title: Rozwiązywanie problemów z woluminami SMB lub Dual-Protocol dla Azure NetApp Files | Microsoft Docs
description: Zawiera opis komunikatów o błędach i rozwiązań, które mogą pomóc w rozwiązywaniu problemów z protokołem SMB lub podwójną obsługą Azure NetApp Files.
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
ms.date: 02/02/2021
ms.author: b-juche
ms.openlocfilehash: 237fb514229f370fcba79133232e80a6a655048f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104420"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>Rozwiązywanie problemów z woluminami SMB lub Dual-Protocol

W tym artykule opisano rozwiązania przyczyn błędów, które mogą wystąpić podczas tworzenia woluminów dwuprotokołowych lub zarządzania nimi.

## <a name="errors-for-dual-protocol-volumes"></a>Błędy dla podwójnych woluminów protokołów

|     Warunki błędów    |     Rozwiązania    |
|-|-|
| Protokół LDAP za pośrednictwem protokołu TLS jest włączony, a Tworzenie woluminu z podwójnym protokołem kończy się niepowodzeniem z powodu błędu `This Active Directory has no Server root CA Certificate` .    |     Jeśli ten błąd występuje podczas tworzenia woluminu dwuprotokołowego, upewnij się, że certyfikat głównego urzędu certyfikacji jest przekazywany na koncie NetApp.    |
| Tworzenie woluminu z podwójnym protokołem nie powiodło się z powodu błędu `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  Na serwerze DNS może brakować rekordu wskaźnika (PTR) maszyny hosta usługi AD. Należy utworzyć strefę wyszukiwania wstecznego na serwerze DNS, a następnie dodać rekord PTR maszyny hosta usługi AD w tej strefie wyszukiwania wstecznego. <br> Załóżmy na przykład, że adres IP komputera usługi AD to, nazwa `10.X.X.X` hosta maszyny usługi AD (jak znaleziono przy użyciu `hostname` polecenia) `AD1` , a domena to `contoso.com` .  Rekord PTR dodany do strefy wyszukiwania wstecznego powinien mieć wartość `10.X.X.X`  ->  `contoso.com` .   |
| Tworzenie woluminu z podwójnym protokołem nie powiodło się z powodu błędu `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.X.X.X, port 88 using TCP\\n**[ 950] FAILURE` . |     Ten błąd wskazuje, że hasło usługi AD jest niepoprawne, gdy Active Directory jest przyłączony do konta NetApp. Zaktualizuj połączenie usługi AD przy użyciu poprawnego hasła i spróbuj ponownie. |
| Tworzenie woluminu z podwójnym protokołem nie powiodło się z powodu błędu `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Ten błąd wskazuje, że usługa DNS jest nieosiągalna. Przyczyną może być fakt, że adres IP DNS jest niepoprawny lub występuje problem z siecią. Sprawdź adres IP DNS wprowadzony w polu połączenie usługi AD i upewnij się, że adres IP jest prawidłowy. <br> Upewnij się również, że usługi AD i wolumin są w tym samym regionie i w tej samej sieci wirtualnej. Jeśli znajdują się w różnych sieci wirtualnychach, upewnij się, że Komunikacja równorzędna sieci wirtualnej jest ustanowiona między tymi dwoma sieci wirtualnych.|
| Wystąpił błąd podczas instalowania dwuprotokołowego woluminu. | Dwuprotokołowy wolumin obsługuje zarówno system plików NFS, jak i protokół SMB.  W przypadku próby uzyskania dostępu do zainstalowanego woluminu w systemie UNIX system próbuje zamapować użytkownika systemu UNIX, którego używasz, do użytkownika systemu Windows. Jeśli nie zostanie znalezione żadne mapowanie, wystąpi błąd "odmowa uprawnień". <br> Ta sytuacja ma zastosowanie również w przypadku korzystania z użytkownika "root" w celu uzyskania dostępu. <br> Aby uniknąć problemu "odmowa uprawnień", należy się upewnić, że Active Directory systemu Windows zawiera `pcuser` przed uzyskaniem dostępu do punktu instalacji. Po dodaniu `pcuser` problemu "odmowa uprawnień" odczekaj 24 godziny na wyczyszczenie wpisu pamięci podręcznej, zanim spróbujesz ponownie uzyskać dostęp. |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>Typowe błędy dla woluminów SMB i podwójnych protokołów

|     Warunki błędów    |     Rozwiązania    |
|-|-|
| Tworzenie woluminu SMB lub Dual-Protocol nie powiodło się z powodu następującego błędu: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | Ten błąd wskazuje, że system DNS jest nieosiągalny. <br> Weź pod uwagę następujące rozwiązania: <ul><li>Sprawdź, czy są dodawane i czy wolumin jest wdrażany w tym samym regionie.</li> <li>Sprawdź, czy Dodawanie i wolumin używają tej samej sieci wirtualnej. Jeśli są używane różne sieci wirtualnych, upewnij się, że sieci wirtualnych są połączone za pomocą komunikacji równorzędnej. Zapoznaj się z [zaleceniami dotyczącymi planowania Azure NetApp Files sieci](azure-netapp-files-network-topologies.md). </li> <li>Serwer DNS może mieć zastosowane sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń).  W związku z tym nie zezwala na przepływ ruchu. W takim przypadku Otwórz sieciowych grup zabezpieczeń w systemie DNS lub AD, aby nawiązać połączenie z różnymi portami. Wymagania dotyczące portów można znaleźć w temacie [wymagania dotyczące połączeń Active Directory](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). </li></ul> <br>Te same rozwiązania mają zastosowanie do systemu Azure. Dodatki platformy Azure należy wdrożyć w tym samym regionie. Sieć wirtualna powinna znajdować się w tym samym regionie lub komunikacji równorzędnej z siecią wirtualną używaną przez wolumin. | 
| Tworzenie woluminu SMB lub Dual-Protocol nie powiodło się z powodu następującego błędu: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>Upewnij się, że wprowadzona nazwa użytkownika jest poprawna. </li> <li>Upewnij się, że użytkownik jest częścią grupy administratorów, która ma uprawnienia do tworzenia kont komputera. </li> <li> Jeśli korzystasz z platformy Azure, upewnij się, że użytkownik należy do grupy usługi Azure AD `Azure AD DC Administrators` . </li></ul> | 
| Tworzenie woluminu SMB lub Dual-Protocol nie powiodło się z powodu następującego błędu: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.X.X.X, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | Upewnij się, że hasło wprowadzone do dołączenia do połączenia z usługą AD jest poprawne. |
| Tworzenie woluminu SMB lub Dual-Protocol nie powiodło się z powodu następującego błędu: `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.X.X.X, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | Upewnij się, że ścieżka jednostki organizacyjnej określona do przyłączenia do połączenia z usługą AD jest poprawna. Jeśli używasz platformy Azure, upewnij się, że ścieżka jednostki organizacyjnej to `OU=AADDC Computers` . |

## <a name="next-steps"></a>Następne kroki  

* [Tworzenie woluminu SMB](azure-netapp-files-create-volumes-smb.md)
* [Tworzenie woluminu dwuprotokołowego](create-volumes-dual-protocol.md)
* [Konfigurowanie klienta sieciowego systemu plików dla usługi Azure NetApp Files](configure-nfs-clients.md)
