---
title: Konfigurowanie klastrów na potrzeby integracji Azure Active Directory
titleSuffix: Azure HDInsight
description: Dowiedz się, jak skonfigurować i skonfigurować klaster usługi HDInsight zintegrowany z Active Directory przy użyciu funkcji Azure Active Directory Domain Services i pakiet Enterprise Security.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seodec18,seoapr2020, contperf-fy21q2
ms.date: 10/30/2020
ms.openlocfilehash: 15869a547ec5debee939c956d7495bfa58357555
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946923"
---
# <a name="configure-hdinsight-clusters-for-azure-active-directory-integration-with-enterprise-security-package"></a>Konfigurowanie klastrów usługi HDInsight na potrzeby integracji Azure Active Directory z usługą pakiet Enterprise Security

Ten artykuł zawiera podsumowanie i Omówienie procesu tworzenia i konfigurowania klastra usługi HDInsight zintegrowanego z usługą Azure Active Directory. Ta integracja polega na funkcji HDInsight o nazwie pakiet Enterprise Security (ESP), Azure Active Directory Domain Services (Azure AD-DS) i istniejących lokalnie Active Directory lokalnych.

Aby zapoznać się z szczegółowym samouczkiem krok po kroku na temat konfigurowania i konfigurowania domeny na platformie Azure oraz tworzenia klastra obsługującego ESP, a następnie synchronizowania lokalnych użytkowników, zobacz [Tworzenie i konfigurowanie klastrów pakiet Enterprise Security w usłudze Azure HDInsight](apache-domain-joined-create-configure-enterprise-security-cluster.md).

## <a name="background"></a>Tło

Pakiet Enterprise Security (ESP) zapewnia Active Directory integrację usługi Azure HDInsight. Ta Integracja umożliwia użytkownikom domeny korzystanie z poświadczeń domeny w celu uwierzytelniania w klastrach usługi HDInsight i uruchamiania zadań Big Data.

> [!NOTE]  
> Partycja ESP jest ogólnie dostępna w usłudze HDInsight 3,6 i 4,0 dla następujących typów klastrów: Apache Spark, Interactive, Hadoop i HBase. ESP dla typu klastra Apache Kafka jest w wersji zapoznawczej tylko z obsługą najlepszego nakładu pracy. Klastry ESP utworzone przed datą "ESP" (1 października 2018) nie są obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było utworzyć klaster usługi HDInsight z włączoną funkcją ESP, należy wykonać kilka wymagań wstępnych:

- Istniejąca Active Directory lokalnego i Azure Active Directory.
- Włącz usługę Azure AD — DS.
- Sprawdź stan kondycji usługi Azure AD — DS, aby upewnić się, że synchronizacja została ukończona.
- Utwórz i Autoryzuj tożsamość zarządzaną.
- Ukończ konfigurację sieci dla systemu DNS i związanych z nimi problemów.

Każdy z tych elementów zostanie szczegółowo omówiony poniżej. Aby zapoznać się z przewodnikiem dotyczącym wykonywania wszystkich tych kroków, zobacz [Tworzenie i konfigurowanie klastrów pakiet Enterprise Security w usłudze Azure HDInsight](apache-domain-joined-create-configure-enterprise-security-cluster.md).

### <a name="enable-azure-ad-ds"></a>Włączanie usług Azure AD DS

Przed utworzeniem klastra usługi HDInsight przy użyciu ESP należy włączyć AD DS platformy Azure. Aby uzyskać więcej informacji, zobacz [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

Gdy usługa Azure AD DS jest włączona, wszyscy użytkownicy i obiekty domyślnie uruchamiają synchronizację z usługi Azure Active Directory (Azure AD) do platformy Azure AD DS. Długość operacji synchronizacji zależy od liczby obiektów w usłudze Azure AD. Synchronizacja może potrwać kilka dni dla setek tysięcy obiektów.

Nazwa domeny, która jest używana z usługą Azure AD DS nie może mieć więcej niż 39 znaków, do pracy z usługą HDInsight.

Można synchronizować tylko te grupy, które wymagają dostępu do klastrów usługi HDInsight. Ta opcja synchronizowania tylko określonych grup jest nazywana *synchronizacją w zakresie*. Aby uzyskać instrukcje, zobacz [Konfigurowanie synchronizacji z zakresem z usługi Azure AD do domeny zarządzanej](../../active-directory-domain-services/scoped-synchronization.md).

Po włączeniu bezpiecznego protokołu LDAP Umieść nazwę domeny w polu Nazwa podmiotu. I Alternatywna nazwa podmiotu w certyfikacie. Jeśli nazwa domeny to *contoso100.onmicrosoft.com*, upewnij się, że dokładna nazwa istnieje w nazwie podmiotu certyfikatu i Alternatywna nazwa podmiotu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej AD DS platformy Azure](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Poniższy przykład tworzy certyfikat z podpisem własnym. Nazwa domeny *contoso100.onmicrosoft.com* znajduje się w obu tych polach `Subject` (nazwa podmiotu) i `DnsName` (alternatywna nazwa podmiotu).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

> [!NOTE]  
> Tylko Administratorzy dzierżawy mają uprawnienia do włączania usługi Azure AD DS. Jeśli magazyn klastra jest Azure Data Lake Storage Gen1 lub Gen2, należy wyłączyć usługę Azure AD Multi-Factor Authentication tylko dla użytkowników, którzy będą musieli uzyskać dostęp do klastra przy użyciu podstawowego uwierzytelniania Kerberos.
>
> Możesz użyć [zaufanych adresów IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) lub [dostępu warunkowego](../../active-directory/conditional-access/overview.md) , aby wyłączyć Multi-Factor Authentication dla określonych użytkowników *tylko* wtedy, gdy uzyskują dostęp do zakresu adresów IP dla sieci wirtualnej klastra usługi HDInsight. W przypadku korzystania z dostępu warunkowego upewnij się, że punkt końcowy usługi Active Directory jest włączony w sieci wirtualnej HDInsight.
>
> Jeśli magazyn klastra jest magazynem obiektów blob platformy Azure, nie należy wyłączać Multi-Factor Authentication.

### <a name="check-azure-ad-ds-health-status"></a>Sprawdź stan kondycji usługi Azure AD DS

Aby wyświetlić stan kondycji Azure Active Directory Domain Services, wybierz pozycję **kondycja** w kategorii **Zarządzaj** . Upewnij się, że stan AD DS platformy Azure to zielony (uruchomiony), a synchronizacja została ukończona.

![Kondycja usługi Azure AD DS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

### <a name="create-and-authorize-a-managed-identity"></a>Tworzenie i Autoryzowanie tożsamości zarządzanej

Użyj *tożsamości zarządzanej przypisanej przez użytkownika* , aby uprościć bezpieczne operacje usług domenowych. Po przypisaniu roli **współautor usług domenowych** w usłudze HDInsight do zarządzanej tożsamości można odczytywać, tworzyć, modyfikować i usuwać operacje usług domenowych.

Niektóre operacje usług domenowych, takie jak tworzenie jednostek organizacyjnych i jednostki usługi, są zbędne dla pakiet Enterprise Security usługi HDInsight. Tożsamości zarządzane można tworzyć w dowolnej subskrypcji. Aby uzyskać więcej informacji na temat ogólnych tożsamości zarządzanych, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md). Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [zarządzane tożsamości w usłudze Azure HDInsight](../hdinsight-managed-identities.md).

Aby skonfigurować klastry ESP, Utwórz tożsamość zarządzaną przypisaną przez użytkownika, jeśli jeszcze jej nie masz. Zobacz [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .

Następnie przypisz rolę **współautor usług domenowych** w usłudze HDInsight do zarządzanej tożsamości w obszarze **kontroli dostępu** dla usługi Azure AD DS. Do przypisywania ról wymagane są uprawnienia administratora platformy Azure AD DS.

![Azure Active Directory Domain Services kontroli dostępu](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Przypisanie roli **współautor usług domenowych** w usłudze HDInsight zapewnia, że ta tożsamość ma odpowiedni ( `on behalf of` ) dostęp do operacji usług domenowych w domenie AD DS platformy Azure. Te operacje obejmują tworzenie i usuwanie jednostek organizacyjnych.

Po otrzymaniu roli zarządzanej przez administratora usługi Azure AD DS administrator zarządza tą osobą. Najpierw Administrator wybiera tożsamość zarządzaną w portalu. Następnie wybiera **Access Control (IAM)** w obszarze **Przegląd**. Administrator przypisuje rolę **operatora tożsamości zarządzanej** użytkownikom lub grupom, którzy chcą tworzyć klastry ESP.

Na przykład administrator usługi Azure AD DS może przypisać tę rolę do grupy **MarketingTeam** dla tożsamości zarządzanej **sjmsi** . Na poniższej ilustracji przedstawiono przykład. To przypisanie gwarantuje, że odpowiednie osoby w organizacji mogą używać tożsamości zarządzanej do tworzenia klastrów ESP.

![Przypisanie roli operatora tożsamości zarządzanego przez usługi HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

### <a name="network-configuration"></a>Konfiguracja sieci

> [!NOTE]  
> Usługę Azure AD DS należy wdrożyć w sieci wirtualnej opartej na Azure Resource Manager. Klasyczne sieci wirtualne nie są obsługiwane w przypadku usługi Azure AD DS. Aby uzyskać więcej informacji, zobacz [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Włącz usługę Azure AD DS. Następnie lokalny serwer DNS (Domain Name System) działa na maszynach wirtualnych Active Directory. Skonfiguruj sieć wirtualną AD DS platformy Azure tak, aby korzystała z tych niestandardowych serwerów DNS. Aby zlokalizować odpowiednie adresy IP, wybierz pozycję **Właściwości** w kategorii **Zarządzaj** i poszukaj w obszarze **adres IP w sieci wirtualnej**.

![Lokalizowanie adresów IP dla lokalnych serwerów DNS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Zmień konfigurację serwerów DNS w sieci wirtualnej AD DS platformy Azure. Aby użyć tych niestandardowych adresów IP, wybierz pozycję **serwery DNS** w kategorii **Ustawienia** . Następnie wybierz opcję **niestandardowa** , wprowadź pierwszy adres IP w polu tekstowym, a następnie wybierz pozycję **Zapisz**. Dodaj więcej adresów IP, wykonując te same czynności.

![Aktualizowanie konfiguracji DNS sieci wirtualnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Łatwiej jest umieścić zarówno wystąpienie usługi Azure AD DS, jak i klaster HDInsight w tej samej sieci wirtualnej platformy Azure. Jeśli planujesz używanie różnych sieci wirtualnych, musisz nawiązać połączenie równorzędne z tymi sieciami wirtualnymi, aby kontroler domeny był widoczny dla maszyn wirtualnych usługi HDInsight. Aby uzyskać więcej informacji, zobacz [wirtualne sieci równorzędne](../../virtual-network/virtual-network-peering-overview.md).

Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej, skonfiguruj sieć wirtualną usługi HDInsight tak, aby korzystała z niestandardowego serwera DNS. Wprowadź AD DS prywatnych adresów IP platformy Azure jako adresy serwerów DNS. Gdy obie sieci wirtualne używają tych samych serwerów DNS, niestandardowa nazwa domeny zostanie rozpoznana jako właściwy adres IP i będzie dostępna z usługi HDInsight. Na przykład jeśli nazwa domeny to `contoso.com` , po tym kroku `ping contoso.com` należy rozwiązać prawidłowy adres IP AD DS platformy Azure.

![Konfigurowanie niestandardowych serwerów DNS dla równorzędnej sieci wirtualnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Jeśli w podsieci usługi HDInsight są używane reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), należy zezwolić na [wymagane adresy IP](../hdinsight-management-ip-addresses.md) dla ruchu przychodzącego i wychodzącego.

Aby przetestować konfigurację sieci, Dołącz do maszyny wirtualnej z systemem Windows do sieci wirtualnej/podsieci usługi HDInsight i Wyślij polecenie ping do nazwy domeny. (Należy rozwiązać adres IP). Uruchom **ldp.exe** , aby uzyskać dostęp do domeny AD DS platformy Azure. Następnie przyłącz tę maszynę wirtualną z systemem Windows do domeny, aby upewnić się, że wszystkie wymagane wywołania RPC powiodło się między klientem a serwerem.

Użyj **polecenia nslookup** , aby potwierdzić dostęp sieciowy do konta magazynu. Lub dowolną zewnętrzną bazę danych, która może być używana (na przykład zewnętrzna magazyn metadanych Hive lub Ranger DB). Upewnij się, że [wymagane porty](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) są dozwolone w regułach sieciowej grupy zabezpieczeń podsieci AD DS platformy Azure, jeśli sieciowej grupy zabezpieczeń zabezpiecza AD DS platformy Azure. Jeśli łączenie domeny z maszyną wirtualną z systemem Windows powiedzie się, możesz przejść do następnego kroku i utworzyć klastry ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Tworzenie klastra usługi HDInsight przy użyciu protokołu ESP

Po poprawnym skonfigurowaniu poprzednich kroków następnym krokiem jest utworzenie klastra usługi HDInsight z włączoną funkcją ESP. Podczas tworzenia klastra usługi HDInsight można włączyć pakiet Enterprise Security na karcie **zabezpieczenia i sieć** . Aby zapoznać się z szablonem Azure Resource Manager na potrzeby wdrożenia, użyj środowiska portalu. Następnie Pobierz wstępnie wypełniony szablon na stronie **Recenzja + tworzenie** do użytku w przyszłości.

Można również włączyć funkcję [brokera identyfikatora usługi HDInsight](identity-broker.md) podczas tworzenia klastra. Funkcja brokera identyfikatorów umożliwia zalogowanie się do usługi Ambari przy użyciu Multi-Factor Authentication i uzyskanie wymaganych biletów Kerberos bez konieczności używania skrótów haseł w usłudze Azure AD DS.

> [!NOTE]  
> Pierwsze sześć znaków nazw klastrów ESP musi być unikatowe w danym środowisku. Jeśli na przykład masz wiele klastrów ESP w różnych sieciach wirtualnych, wybierz konwencję nazewnictwa, która zapewnia unikalność pierwszych sześciu znaków w nazwach klastra.

![Weryfikacja domeny dla usługi Azure HDInsight pakiet Enterprise Security](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Po włączeniu protokołu ESP typowe błędne konfiguracje związane z platformą Azure AD DS są automatycznie wykrywane i weryfikowane. Po naprawieniu tych błędów możesz przejść do następnego kroku.

![Weryfikacja domeny w usłudze Azure HDInsight pakiet Enterprise Security nie powiodła się](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Podczas tworzenia klastra usługi HDInsight przy użyciu protokołu ESP należy podać następujące parametry:

* **Użytkownik administrator klastra**: Wybierz administratora klastra ze zsynchronizowanego wystąpienia usługi Azure AD DS. To konto domeny musi być już zsynchronizowane i dostępne w usłudze Azure AD DS.

* **Grupy dostępu klastra**: grupy zabezpieczeń, których użytkownicy mają synchronizować i mają dostęp do klastra, powinny być dostępne w usłudze Azure AD DS. Przykładem jest Grupa HiveUsers. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy i Dodawanie członków w Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **Adres URL adresów LDAP**: przykład `ldaps://contoso.com:636` .

Utworzoną tożsamość zarządzaną można wybrać z listy rozwijanej **tożsamość zarządzana przez użytkownika** podczas tworzenia nowego klastra.

![Tożsamość zarządzana w usłudze Azure HDInsight ESP Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować zasady Hive i uruchamiać zapytania programu Hive, zobacz [Konfigurowanie zasad Apache Hive dla klastrów usługi HDInsight przy użyciu protokołu ESP](apache-domain-joined-run-hive.md).
* Aby połączyć się z klastrami usługi HDInsight przy użyciu protokołu SSH, zobacz [Używanie protokołu SSH z Apache Hadoop opartych na systemie Linux w usłudze HDInsight z systemu Linux, UNIX lub OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
