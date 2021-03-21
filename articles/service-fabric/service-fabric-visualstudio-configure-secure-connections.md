---
title: Konfigurowanie bezpiecznych połączeń klastra Service Fabric platformy Azure
description: Dowiedz się, jak skonfigurować bezpieczne połączenia obsługiwane przez klaster Service Fabric platformy Azure przy użyciu programu Visual Studio.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: d4d6b781d97d481793e69cf2ca97cca5b93ce432
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008535"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Konfigurowanie bezpiecznych połączeń z klastrem Service Fabric z programu Visual Studio
Dowiedz się, jak za pomocą programu Visual Studio bezpiecznie uzyskać dostęp do klastra usługi Azure Service Fabric przy użyciu skonfigurowanych zasad kontroli dostępu.

## <a name="cluster-connection-types"></a>Typy połączeń klastra
Klaster Service Fabric platformy Azure obsługuje dwa typy połączeń: połączenia **niezabezpieczone** i bezpieczne połączenia **oparte na certyfikatach x509** . (W przypadku klastrów Service Fabric hostowanych lokalnie obsługiwane są również uwierzytelniania **systemu Windows** i **dSTS** ). Należy skonfigurować typ połączenia klastra podczas tworzenia klastra. Po jego utworzeniu nie można zmienić typu połączenia.

Narzędzia Service Fabric programu Visual Studio obsługują wszystkie typy uwierzytelniania do nawiązywania połączenia z klastrem w celu opublikowania. Zapoznaj się z tematem [Konfigurowanie klastra Service Fabric z poziomu Azure Portal,](service-fabric-cluster-creation-via-portal.md) Aby uzyskać instrukcje dotyczące konfigurowania bezpiecznego klastra Service Fabric.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Konfigurowanie połączeń klastra w profilach publikowania
Jeśli publikujesz projekt Service Fabric w programie Visual Studio, użyj okna dialogowego **publikowanie Service Fabric aplikacji** , aby wybrać klaster Service Fabric platformy Azure. W obszarze **punkt końcowy połączenia** wybierz istniejący klaster w ramach subskrypcji.

![W celu skonfigurowania połączenia Service Fabric służy okno dialogowe * * publikowanie Service Fabric aplikacji * *.][publishdialog]

Okno dialogowe **publikowanie Service Fabric aplikacji** automatycznie weryfikuje poprawność połączenia z klastrem. Jeśli zostanie wyświetlony monit, zaloguj się do konta platformy Azure. Jeśli walidacja kończy się powodzeniem, oznacza to, że system ma zainstalowane poprawne certyfikaty do bezpiecznego połączenia z klastrem lub klaster nie jest zabezpieczony. Błędy sprawdzania poprawności mogą być spowodowane problemami z siecią lub bez poprawnego skonfigurowania systemu do łączenia się z bezpiecznym klastrem.

![Okno dialogowe * * publikowanie Service Fabric aplikacji * * weryfikuje istniejące, prawidłowo skonfigurowane Service Fabric połączenie klastra.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Aby nawiązać połączenie z bezpiecznym klastrem
1. Upewnij się, że masz dostęp do jednego z certyfikatów klienta, z którymi ufa klaster docelowy. Certyfikat jest zazwyczaj udostępniany jako plik wymiany informacji osobistych (pfx). Zobacz [Konfigurowanie klastra Service Fabric z Azure Portal,](service-fabric-cluster-creation-via-portal.md) aby skonfigurować serwer w taki sposób, aby zezwalał na dostęp do klienta.
2. Zainstaluj zaufany certyfikat. Aby to zrobić, kliknij dwukrotnie plik PFX lub Użyj skryptu programu PowerShell Import-PfxCertificate, aby zaimportować certyfikaty. Zainstaluj certyfikat do **certyfikatu: \ LocalMachine\My**. Po zaimportowaniu certyfikatu można zaakceptować wszystkie ustawienia domyślne.
3. Wybierz polecenie **Publikuj...** w menu skrótów projektu, aby otworzyć okno dialogowe **publikowanie aplikacji platformy Azure** , a następnie wybierz klaster docelowy. Narzędzie automatycznie rozwiązuje połączenie i zapisuje parametry bezpiecznego połączenia w profilu publikowania.
4. Opcjonalne: można edytować profil publikowania, aby określić bezpieczne połączenie z klastrem.
   
   Ponieważ ręcznie edytujesz plik XML profilu publikowania, aby określić informacje o certyfikacie, pamiętaj, aby zanotować nazwę magazynu certyfikatów, lokalizację magazynu i odcisk palca certyfikatu. Należy podać te wartości dla nazwy magazynu i lokalizacji magazynu certyfikatu. Aby uzyskać więcej informacji [, zobacz How to: Pobieranie odcisku palca certyfikatu](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) .
   
   Za pomocą parametrów *ClusterConnectionParameters* można określić parametry programu PowerShell, które mają być używane podczas nawiązywania połączenia z klastrem Service Fabric. Prawidłowe parametry są akceptowane przez polecenie cmdlet Connect-ServiceFabricCluster. Zobacz [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) , aby uzyskać listę dostępnych parametrów.
   
   W przypadku publikowania w klastrze zdalnym należy określić odpowiednie parametry dla danego klastra. Poniżej znajduje się przykład łączenia się z niezabezpieczonym klastrem:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Oto przykład łączenia się z bezpiecznym klastrem opartym na certyfikatach x509:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Edytuj wszystkie inne niezbędne ustawienia, takie jak parametry uaktualnienia i lokalizacja pliku parametrów aplikacji, a następnie Opublikuj aplikację w oknie dialogowym **publikowanie Service Fabric aplikacji** w programie Visual Studio.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uzyskiwania dostępu do klastrów Service Fabric, zobacz [wizualizowanie klastra przy użyciu Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
