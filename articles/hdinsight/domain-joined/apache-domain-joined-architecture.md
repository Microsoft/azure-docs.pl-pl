---
title: Architektura usługi Azure HDInsight z pakiet Enterprise Security
description: Dowiedz się, jak planować zabezpieczenia usługi Azure HDInsight za pomocą pakiet Enterprise Security.
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 404cd8652a4daf602f3326696ef5df2c3a23a548
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943322"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Korzystanie z pakietu Enterprise Security w usłudze HDInsight

Standardowy klaster usługi Azure HDInsight to klaster z pojedynczym użytkownikiem. Jest to odpowiednie dla większości firm, które mają mniejsze zespoły aplikacji tworzące duże obciążenia danych. Każdy użytkownik może utworzyć dedykowany klaster na żądanie i zniszczyć go, gdy nie jest już potrzebne.

Wiele przedsiębiorstw zostało przeniesionych na model, w którym zespoły IT zarządzają klastrami, a wiele zespołów aplikacji współużytkuje klastry. Te duże przedsiębiorstwa potrzebują dostępu wielodostępnego do każdego klastra w usłudze Azure HDInsight.

Usługa HDInsight korzysta ze popularnego dostawcy tożsamości — Active Directory — w sposób zarządzany. Dzięki integracji usługi HDInsight z usługą [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md)można uzyskać dostęp do klastrów przy użyciu poświadczeń domeny.

Maszyny wirtualne w usłudze HDInsight są przyłączone do domeny w podanej domenie. Dzięki temu wszystkie usługi działające w usłudze HDInsight (Apache Ambari, Apache Hive Server, Apache Ranger, Apache Spark Thrift Server i inne) bezproblemowo działają dla uwierzytelnionego użytkownika. Administratorzy mogą następnie utworzyć silne zasady autoryzacji za pomocą platformy Apache Ranger w celu zapewnienia kontroli dostępu opartej na rolach dla zasobów w klastrze.

## <a name="integrate-hdinsight-with-active-directory"></a>Integracja usługi HDInsight z usługą Active Directory

Apache Hadoop Open Source opiera się na protokole Kerberos na potrzeby uwierzytelniania i zabezpieczeń. W związku z tym węzły klastra HDInsight z pakiet Enterprise Security (ESP) są przyłączone do domeny, która jest zarządzana przez usługę Azure AD DS. Zabezpieczenia protokołu Kerberos są skonfigurowane dla składników usługi Hadoop w klastrze.

Następujące elementy są tworzone automatycznie:

- Jednostka usługi dla każdego składnika Hadoop
- Podmiot zabezpieczeń maszyny dla każdej maszyny, która jest dołączona do domeny
- Jednostka organizacyjna (OU) dla każdego klastra do przechowywania tych usług i podmiotów nazw maszyn

Aby podsumować, należy skonfigurować środowisko przy użyciu programu:

- Domena Active Directory (zarządzana przez usługę Azure AD DS). **Nazwa domeny nie może być krótsza niż 39 znaków, aby można było korzystać z usługi Azure HDInsight.**
- Secure LDAP (LDAPs) włączone w usłudze Azure AD DS.
- Odpowiednie połączenia sieciowe z sieci wirtualnej HDInsight z siecią wirtualną platformy Azure AD DS w przypadku wybrania dla nich oddzielnych sieci wirtualnych. Maszyna wirtualna w sieci wirtualnej usługi HDInsight powinna mieć wiersz wglądu w usługę Azure AD DS za pomocą komunikacji równorzędnej sieci wirtualnej. Jeśli usługi HDInsight i Azure AD DS są wdrożone w tej samej sieci wirtualnej, nastąpi automatyczne połączenie i nie jest wymagana żadna dodatkowa akcja.

## <a name="set-up-different-domain-controllers"></a>Konfigurowanie różnych kontrolerów domeny

Usługa HDInsight obecnie obsługuje tylko AD DS platformy Azure jako główny kontroler domeny, który jest wykorzystywany przez klaster do komunikacji z protokołem Kerberos. Jednak inne skomplikowane konfiguracje Active Directory są możliwe, tak długo, jak taka konfiguracja prowadzi do włączenia usługi Azure AD DS dla dostępu HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Usługa Azure AD DS](../../active-directory-domain-services/overview.md) zapewnia domenę zarządzaną, która jest w pełni zgodna z systemem Windows Server Active Directory. Firma Microsoft dba o zarządzanie, stosowanie poprawek i monitorowanie domeny w konfiguracji o wysokiej dostępności (HA). Klaster można wdrożyć bez obaw o utrzymywanie kontrolerów domeny.

Użytkownicy, grupy i hasła są synchronizowane z usługą Azure AD. Jednokierunkowa synchronizacja z wystąpienia usługi Azure AD do usługi Azure AD DS umożliwia użytkownikom logowanie się do klastra przy użyciu tych samych poświadczeń firmowych.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie klastrów usługi HDInsight za pomocą protokołu ESP przy użyciu AD DS platformy Azure](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Lokalne Active Directory lub Active Directory na maszynach wirtualnych IaaS

Jeśli masz lokalne wystąpienie Active Directory lub bardziej skomplikowane Active Directory konfiguracje dla domeny, możesz synchronizować te tożsamości z usługą Azure AD przy użyciu Azure AD Connect. Następnie można włączyć usługę Azure AD DS w tym Active Directory dzierżawie.

Ponieważ protokół Kerberos opiera się na skrótach haseł, należy [włączyć synchronizację skrótów haseł na platformie Azure AD DS](../../active-directory-domain-services/tutorial-create-instance.md).

Jeśli używasz Federacji z Active Directory Federation Services (AD FS), musisz włączyć synchronizację skrótów haseł. (Aby uzyskać zalecaną konfigurację, zobacz [ten film wideo](https://youtu.be/qQruArbu2Ew)). Synchronizacja skrótów haseł ułatwia odzyskiwanie po awarii w przypadku niepowodzenia infrastruktury AD FS, a także pomaga w zapewnianiu nieujawnionej ochrony poświadczeń. Aby uzyskać więcej informacji, zobacz [Włączanie synchronizacji skrótów haseł przy użyciu synchronizacji Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

Korzystanie z lokalnych Active Directory lub Active Directory na maszynach wirtualnych IaaS bez usług Azure AD i Azure AD DS nie jest obsługiwaną konfiguracją klastrów usługi HDInsight z użyciem protokołu ESP.

Jeśli jest używana Federacja, a skróty haseł są synchronizowane prawidłowo, ale nie są dostępne błędy uwierzytelniania, sprawdź, czy dla jednostki usługi programu PowerShell jest włączone uwierzytelnianie hasła w chmurze. Jeśli nie, musisz ustawić [zasady odnajdowania obszaru macierzystego (HRD)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) dla dzierżawy usługi Azure AD. Aby sprawdzić i ustawić zasady HRD:

1. Zainstaluj [moduł programu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)w wersji zapoznawczej.

   ```powershell
   Install-Module AzureAD
   ```

2. Nawiązywanie połączenia przy użyciu poświadczeń administratora globalnego (Administrator dzierżawy).

   ```powershell
   Connect-AzureAD
   ```

3. Sprawdź, czy Microsoft Azure PowerShell główna usługa została już utworzona.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Jeśli nie istnieje, Utwórz nazwę główną usługi.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Utwórz zasady i Dołącz je do tej jednostki usługi.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie klastrów usługi HDInsight przy użyciu ESP](apache-domain-joined-configure-using-azure-adds.md)
- [Konfigurowanie zasad Apache Hive dla klastrów usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-run-hive.md)
- [Zarządzanie klastrami usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-manage.md)