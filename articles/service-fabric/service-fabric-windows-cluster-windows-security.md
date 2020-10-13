---
title: Zabezpieczanie klastra działającego w systemie Windows przy użyciu zabezpieczeń systemu Windows
description: Dowiedz się, jak skonfigurować zabezpieczenia typu węzeł-węzeł i klient-węzeł w klastrze autonomicznym z systemem Windows przy użyciu zabezpieczeń systemu Windows.
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: e97a951f6dc0a97b1cfa8f960ed762084c82d2ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839485"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Zabezpieczanie klastra autonomicznego w systemie Windows przy użyciu zabezpieczeń systemu Windows
Aby zapobiec nieautoryzowanemu dostępowi do klastra Service Fabric, należy zabezpieczyć klaster. Zabezpieczenia są szczególnie ważne w przypadku uruchamiania przez klaster obciążeń produkcyjnych. W tym artykule opisano sposób konfigurowania zabezpieczeń między węzłami i klientem-do-węzła przy użyciu zabezpieczeń systemu Windows w *ClusterConfig.JS* pliku.  Proces ten odnosi się do kroku Konfigurowanie zabezpieczeń [tworzenia klastra autonomicznego działającego w systemie Windows](service-fabric-cluster-creation-for-windows-server.md). Aby uzyskać więcej informacji o tym, jak Service Fabric korzysta z zabezpieczeń systemu Windows, zobacz [scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md).

> [!NOTE]
> Należy uważnie rozważyć wybór zabezpieczeń między węzłami, ponieważ nie istnieje uaktualnienie klastra z jednego wyboru zabezpieczeń do innego. Aby zmienić wybór zabezpieczeń, należy ponownie skompilować pełny klaster.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurowanie zabezpieczeń systemu Windows przy użyciu gMSA  
Przykład *ClusterConfig.gMSA.Windows.MultiMachine.JSw* pliku konfiguracyjnym pobrany z [Microsoft. Azure. servicefabric. WindowsServer. \<version> .. ](https://go.microsoft.com/fwlink/?LinkId=730690) pakiet autonomicznego klastra zip zawiera szablon służący do konfigurowania zabezpieczeń systemu Windows przy użyciu [konta usługi zarządzanego przez grupę (gMSA)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)):  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **Ustawienie konfiguracji** | **Opis** |
| --- | --- |
| ClusterCredentialType |Ustaw *system Windows* , aby włączyć zabezpieczenia systemu Windows na potrzeby komunikacji z węzłem węzła.  | 
| ServerCredentialType |Ustaw *system Windows* , aby włączyć zabezpieczenia systemu Windows na potrzeby komunikacji z węzłami klienta. |
| WindowsIdentities |Zawiera tożsamość klastra i klienta. |
| ClustergMSAIdentity |Konfiguruje zabezpieczenia między węzłami. Konto usługi zarządzane przez grupę. |
| ClusterSPN |Zarejestrowana nazwa SPN dla konta usługi gMSA|
| ClientIdentities |Konfiguruje zabezpieczenia klienta-węzła. Tablica kont użytkowników klienta. |
| Tożsamość |Dodaj użytkownika domeny domena \ nazwa_użytkownika dla tożsamości klienta. |
| IsAdmin |Ustaw wartość true, aby określić, że użytkownik domeny ma dostęp klienta administratora lub wartość false dla dostępu klienta. |

> [!NOTE]
> Wartość ClustergMSAIdentity musi być w formacie " mysfgmsa@mydomain ".

[Zabezpieczenia węzła do węzła](service-fabric-cluster-security.md#node-to-node-security) konfiguruje się za pomocą ustawienia **ClustergMSAIdentity** , gdy Usługa Service Fabric musi działać w ramach gMSA. Aby można było tworzyć relacje zaufania między węzłami, muszą one być wzajemnie świadome. Można to zrobić na dwa różne sposoby: Określ konto usługi zarządzane przez grupę, które zawiera wszystkie węzły w klastrze, lub określ grupę maszyn domeny obejmującą wszystkie węzły w klastrze. Zdecydowanie zalecamy użycie podejścia do [konta usługi zarządzanego przez grupę (gMSA)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) , szczególnie w przypadku większych klastrów (więcej niż 10 węzłów) lub dla klastrów, które mogą się zwiększać lub zmniejszać.  
Takie podejście nie wymaga tworzenia grupy domeny, dla której administratorzy klastrów mają przyznane prawa dostępu do dodawania i usuwania członków. Te konta są również przydatne do automatycznego zarządzania hasłami. Aby uzyskać więcej informacji, zobacz [wprowadzenie z kontami usług zarządzanymi przez grupę](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)).  
 
[Zabezpieczenia węzła klienta](service-fabric-cluster-security.md#client-to-node-security) są konfigurowane przy użyciu **ClientIdentities**. Aby ustanowić relację zaufania między klientem a klastrem, należy skonfigurować klaster, aby wiedzieć, które tożsamości klientów mogą ufać. Można to zrobić na dwa różne sposoby: Określ użytkowników grupy domeny, którzy mogą łączyć się lub określić użytkowników węzła domeny, którzy mogą nawiązywać połączenia. Service Fabric obsługuje dwa różne typy kontroli dostępu dla klientów, którzy są połączeni z klastrem Service Fabric: administrator i użytkownik. Kontrola dostępu pozwala administratorowi klastra ograniczyć dostęp do niektórych typów operacji klastra dla różnych grup użytkowników, co sprawia, że klaster jest bezpieczniejszy.  Administratorzy mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu i zapisu). Użytkownicy domyślnie mają dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwości zapytania) i możliwość rozpoznawania aplikacji i usług. Aby uzyskać więcej informacji na temat kontroli dostępu, zobacz [Kontrola dostępu oparta na rolach dla klientów Service Fabric](service-fabric-cluster-security-roles.md).  
 
W poniższym przykładzie sekcja **zabezpieczeń** konfiguruje zabezpieczenia systemu Windows przy użyciu gMSA i określa, że maszyny w *ServiceFabric.clusterA.contoso.com* gMSA są częścią klastra i że *CONTOSO\usera* ma dostęp klienta administratora:  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurowanie zabezpieczeń systemu Windows przy użyciu grupy maszyn  
Ten model jest przestarzały. Zaleca się użycie gMSA zgodnie z powyższym opisem. Przykład *ClusterConfig.Windows.MultiMachine.JSw* pliku konfiguracyjnym pobrany z [Microsoft. Azure. servicefabric. WindowsServer. \<version> .. ](https://go.microsoft.com/fwlink/?LinkId=730690) pakiet autonomicznego klastra zip zawiera szablon służący do konfigurowania zabezpieczeń systemu Windows.  Zabezpieczenia systemu Windows są konfigurowane w sekcji **Właściwości** : 

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **Ustawienie konfiguracji** | **Opis** |
| --- | --- |
| ClusterCredentialType |Ustaw *system Windows* , aby włączyć zabezpieczenia systemu Windows na potrzeby komunikacji z węzłem węzła.  |
| ServerCredentialType |Ustaw *system Windows* , aby włączyć zabezpieczenia systemu Windows na potrzeby komunikacji z węzłami klienta. |
| WindowsIdentities |Zawiera tożsamość klastra i klienta. |
| ClusterIdentity |Aby skonfigurować zabezpieczenia między węzłami, należy użyć nazwy grupy maszyn domain\machinegroup. |
| ClientIdentities |Konfiguruje zabezpieczenia klienta-węzła. Tablica kont użytkowników klienta. |  
| Tożsamość |Dodaj użytkownika domeny domena \ nazwa_użytkownika dla tożsamości klienta. |  
| IsAdmin |Ustaw wartość true, aby określić, że użytkownik domeny ma dostęp klienta administratora lub wartość false dla dostępu klienta. |  

[Zabezpieczenia węzła do węzła](service-fabric-cluster-security.md#node-to-node-security) konfiguruje się za pomocą **ClusterIdentity** , jeśli chcesz użyć grupy maszyn w ramach domena usługi Active Directory. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy maszyn w Active Directory](/previous-versions/commerce-server/aa545347(v=cs.70)).

[Zabezpieczenia klient-węzeł](service-fabric-cluster-security.md#client-to-node-security) są konfigurowane przy użyciu **ClientIdentities**. Aby ustanowić relację zaufania między klientem a klastrem, należy skonfigurować klaster, aby znać tożsamości klientów, które mogą ufać klastrowi. Można ustanowić relację zaufania na dwa różne sposoby:

- Określ grupę domen, którą mogą nawiązywać połączenia.
- Określ węzeł domeny Użytkownicy, którzy mogą nawiązywać połączenia.

Service Fabric obsługuje dwa różne typy kontroli dostępu dla klientów, którzy są połączeni z klastrem Service Fabric: administrator i użytkownik. Kontrola dostępu pozwala administratorowi klastra ograniczyć dostęp do niektórych typów operacji klastra dla różnych grup użytkowników, co sprawia, że klaster jest bezpieczniejszy.  Administratorzy mają pełny dostęp do możliwości zarządzania (w tym możliwości odczytu i zapisu). Użytkownicy domyślnie mają dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwości zapytania) i możliwość rozpoznawania aplikacji i usług.  

W poniższym przykładzie sekcja **zabezpieczeń** konfiguruje zabezpieczenia systemu Windows, określa, że maszyny w usłudze *servicefabric/ClusterName. contoso. com* są częścią klastra i określają, że *CONTOSO\usera* ma dostęp klienta administratora:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric nie należy wdrażać na kontrolerze domeny. Upewnij się, że ClusterConfig.json nie uwzględnia adresu IP kontrolera domeny w przypadku korzystania z grupy maszyn lub konta usługi zarządzanego przez grupę (gMSA).
>
>

## <a name="next-steps"></a>Następne kroki
Po skonfigurowaniu zabezpieczeń systemu Windows w *ClusterConfig.JS* pliku należy wznowić proces tworzenia klastra w temacie [Tworzenie klastra autonomicznego działającego w systemie Windows](service-fabric-cluster-creation-for-windows-server.md).

Aby uzyskać więcej informacji na temat zabezpieczeń między węzłami, zabezpieczeń klienta do węzła i kontroli dostępu opartej na rolach, zobacz [scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md).

Zobacz [nawiązywanie połączenia z zabezpieczonym klastrem](service-fabric-connect-to-secure-cluster.md) , aby poznać przykłady łączenia przy użyciu programu PowerShell lub FabricClient.
