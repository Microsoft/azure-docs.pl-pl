---
title: Zabezpieczenia przedsiębiorstwa z usługą Azure AD DS — usługa Azure HDInsight
description: Dowiedz się, jak skonfigurować i skonfigurować klaster pakietów zabezpieczeń hdinsight dla przedsiębiorstw przy użyciu usług domenowych Active Directory platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/17/2020
ms.openlocfilehash: c045378b6e69a9bb1b696d3390dadf84a50bd3b7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687277"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Konfiguracje pakietów zabezpieczeń przedsiębiorstwa z usługami domenowymi Active Directory platformy Azure w usłudze HDInsight

Klastry pakietów zabezpieczeń przedsiębiorstwa (ESP) zapewniają dostęp dla wielu administratorów w klastrach usługi Azure HDInsight. Klastry HDInsight z usługą ESP są połączone z domeną. To połączenie umożliwia użytkownikom domeny używanie poświadczeń domeny do uwierzytelniania za pomocą klastrów i uruchamiania zadań dużych zbiorów danych.

W tym artykule dowiesz się, jak skonfigurować klaster USŁUGI HDInsight przy użyciu usług domenowych Active Directory platformy Azure (Usługi Azure AD DS).

> [!NOTE]  
> Protokół ESP jest ogólnie dostępny w programach HDInsight 3.6 i 4.0 dla tych typów klastrów: Apache Spark, Interactive, Hadoop i HBase. Protokół ESP dla typu klastra Apache Kafka jest w wersji zapoznawczej tylko z obsługą najlepszych starań. Klastry ESP utworzone przed datą GA ESP (1 października 2018 r.) nie są obsługiwane.

## <a name="enable-azure-ad-ds"></a>Włączanie usług Azure AD DS

> [!NOTE]  
> Tylko administratorzy dzierżawy mają uprawnienia do włączania usług Azure AD DS. Jeśli magazyn klastra to Usługa Azure Data Lake Storage Gen1 lub Gen2, należy wyłączyć uwierzytelnianie wieloskładnikowe platformy Azure tylko dla użytkowników, którzy będą musieli uzyskać dostęp do klastra przy użyciu podstawowego uwierzytelniania Kerberos.
>
> [Zaufane adresy IP](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) lub [dostęp warunkowy](../../active-directory/conditional-access/overview.md) umożliwiają wyłączenie uwierzytelniania wieloskładnikowego dla określonych użytkowników *tylko wtedy,* gdy uzyskują dostęp do zakresu adresów IP w sieci wirtualnej klastra HDInsight. Jeśli używasz dostępu warunkowego, upewnij się, że punkt końcowy usługi Active Directory jest włączony w sieci wirtualnej USŁUGI HDInsight.
>
> Jeśli magazyn klastra jest magazyn obiektów Blob platformy Azure, nie należy wyłączać uwierzytelniania wieloskładnikowego.

Włączenie usługi Azure AD DS jest warunkiem wstępnym, zanim będzie można utworzyć klaster HDInsight za pomocą usługi ESP. Aby uzyskać więcej informacji, zobacz [Włączanie usług domenowych Active Directory platformy Azure przy użyciu witryny Azure portal](../../active-directory-domain-services/tutorial-create-instance.md).

Gdy usługa Azure AD DS jest włączona, wszyscy użytkownicy i obiekty domyślnie rozpoczynają synchronizację z usługi Azure Active Directory (Azure AD) do usług Azure AD DS. Długość operacji synchronizacji zależy od liczby obiektów w usłudze Azure AD. Synchronizacja może potrwać kilka dni dla setek tysięcy obiektów.

Nazwa domeny używana z usługą Azure AD DS musi mieć 39 znaków lub mniej, aby pracować z usługą HDInsight.

Można wybrać synchronizację tylko grup, które potrzebują dostępu do klastrów HDInsight. Ta opcja synchronizacji tylko niektórych grup jest nazywana *synchronizacją o określonym zakresie*. Aby uzyskać [instrukcje, zobacz Konfigurowanie synchronizacji o określonym zakresie z usługi Azure AD do domeny zarządzanej](../../active-directory-domain-services/scoped-synchronization.md).

Gdy włączasz bezpieczną ldap, umieść nazwę domeny w nazwie podmiotu. I temat alternatywna nazwa w certyfikacie. Jeśli nazwa domeny jest *contoso100.onmicrosoft.com,* upewnij się, że dokładna nazwa istnieje w nazwie podmiotu certyfikatu i alternatywnej nazwie podmiotu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej usług Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md).

Poniższy przykład tworzy certyfikat z podpisem własnym. Nazwa domeny *contoso100.onmicrosoft.com* jest zarówno `Subject` (nazwa podmiotu), jak i `DnsName` (nazwa alternatywna podmiotu).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Sprawdzanie stanu kondycji usług Azure AD DS

Wyświetl stan kondycji Usług domenowych Active Directory platformy Azure, wybierając pozycję **Kondycja** w kategorii **Zarządzanie.** Upewnij się, że stan usług Azure AD DS jest zielony (uruchomiony) i synchronizacja została zakończona.

![Kondycja usług Ad DS platformy Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Tworzenie i autoryzowanie tożsamości zarządzanej

Użyj *tożsamości zarządzanej przypisanej przez użytkownika,* aby uprościć operacje bezpiecznych usług domenowych. Po przypisaniu roli **współautora usług domenowych HDInsight** do tożsamości zarządzanej może on odczytywać, tworzyć, modyfikować i usuwać operacje usług domenowych.

Niektóre operacje usług domenowych, takie jak tworzenie jednostki organizacyjne i jednostki usługi, są potrzebne dla pakietu zabezpieczeń hdinsight enterprise security. Można tworzyć tożsamości zarządzane w dowolnej subskrypcji. Aby uzyskać więcej informacji na temat tożsamości zarządzanych w ogóle, zobacz [Tożsamości zarządzane dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych w usłudze Azure HDInsight, zobacz [Tożsamości zarządzane w usłudze Azure HDInsight.](../hdinsight-managed-identities.md)

Aby skonfigurować klastry ESP, utwórz tożsamość zarządzaną przypisaną przez użytkownika, jeśli jeszcze jej nie masz. Zobacz [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Następnie przypisz rolę **współautora usług domenowych HDInsight** do tożsamości zarządzanej w **formancie dostępu** dla usług Azure AD DS. Aby dokonać przypisania tej roli, potrzebujesz uprawnień administratora usług Ad DS platformy Azure.

![Kontrola dostępu do usług domenowych active directory platformy Azure](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Przypisywanie roli **współautora usług domenowych HDInsight** gwarantuje, że ta tożsamość ma prawidłowy (`on behalf of`) dostęp do wykonywania operacji usług domenowych w domenie usług Azure AD DS. Operacje te obejmują tworzenie i usuwanie UI.

Po podaniem tożsamości zarządzanej administrator usług AD DS zarządza, kto jej używa. Najpierw administrator wybiera tożsamość zarządzana w portalu. Następnie wybiera **kontrolę dostępu (IAM)** w obszarze **Przegląd**. Administrator przypisuje rolę **Operatora tożsamości zarządzanej** do użytkowników lub grup, które chcą utworzyć klastry ESP.

Na przykład administrator usług Ad DS platformy Azure można przypisać tę rolę do grupy **MarketingTeam** dla tożsamości zarządzanej **sjmsi.** Przykład jest pokazany na poniższej ilustracji. To przypisanie zapewnia, że odpowiednie osoby w organizacji mogą używać tożsamości zarządzanej do tworzenia klastrów ESP.

![Przypisanie roli operatora zarządzanego tożsamości hdinsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Kwestie dotyczące sieci

> [!NOTE]  
> Usługi Azure AD DS muszą być wdrażane w sieci wirtualnej opartej na usłudze Azure Resource Manager. Klasyczne sieci wirtualne nie są obsługiwane dla usług Azure AD DS. Aby uzyskać więcej informacji, zobacz [Włączanie usług domenowych Active Directory platformy Azure przy użyciu witryny Azure portal](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Włącz usługi Azure AD DS. Następnie na maszynach wirtualnych usługi Active Directory uruchamia się lokalny serwer DNS. Skonfiguruj sieć wirtualną usług Ad DS platformy Azure do używania tych niestandardowych serwerów DNS. Aby zlokalizować odpowiednie adresy IP, wybierz **pozycję Właściwości** w kategorii **Zarządzaj** i poszukaj w obszarze **Adres IP w sieci wirtualnej**.

![Lokalizowanie adresów IP dla lokalnych serwerów DNS](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Zmień konfigurację serwerów DNS w sieci wirtualnej usługi Azure AD DS. Aby użyć tych niestandardowych serwerów IP, wybierz **serwery DNS** w kategorii **Ustawienia.** Następnie wybierz opcję **Niestandardowe,** wprowadź pierwszy adres IP w polu tekstowym i wybierz pozycję **Zapisz**. Dodaj więcej adresów IP, wykonując te same kroki.

![Aktualizowanie konfiguracji dns sieci wirtualnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

Łatwiej jest umieścić zarówno wystąpienie usługi Azure AD DS, jak i klaster HDInsight w tej samej sieci wirtualnej platformy Azure. Jeśli planujesz używać różnych sieci wirtualnych, należy równorzędnie tych sieci wirtualnych, tak aby kontroler domeny jest widoczny dla maszyn wirtualnych HDInsight. Aby uzyskać więcej informacji, zobacz [Komunikacja równorzędna sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md).

Po równorzędnych sieciach wirtualnych skonfiguruj sieć wirtualną HDInsight tak, aby używała niestandardowego serwera DNS. Wprowadź prywatne adresy IP usług Ad DS platformy Azure jako adresy serwera DNS. Gdy obie sieci wirtualne używają tych samych serwerów DNS, niestandardowa nazwa domeny zostanie rozpoznana na właściwym adresie IP i będzie osiągalna z usługi HDInsight. Na przykład jeśli nazwa `contoso.com`domeny jest , `ping contoso.com` a następnie po tym kroku, należy rozwiązać do prawej usługi Azure AD DS IP.

![Konfigurowanie niestandardowych serwerów DNS dla sieci wirtualnej równorzędnej](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Jeśli używasz reguł sieciowej grupy zabezpieczeń (NSG) w podsieci HDInsight, należy zezwolić na [wymagane ustawienia IP](../hdinsight-management-ip-addresses.md) zarówno dla ruchu przychodzącego, jak i wychodzącego.

Aby przetestować konfigurację sieci, dołącz maszynę wirtualną systemu Windows do sieci wirtualnej/podsieci HDInsight i pinguje nazwę domeny. (Należy rozwiązać do ip.) Uruchom **program ldp.exe,** aby uzyskać dostęp do domeny usług Azure AD DS. Następnie dołącz tę maszynę wirtualną systemu Windows do domeny, aby potwierdzić, że wszystkie wymagane wywołania RPC powiedzą się między klientem a serwerem.

Użyj **nslookup,** aby potwierdzić dostęp sieciowy do konta magazynu. Lub dowolnej zewnętrznej bazy danych, które można użyć (na przykład zewnętrzny hive metastore lub Ranger DB). Upewnij [się,](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) że wymagane porty są dozwolone w regułach nsg podsieci usług Azure AD DS, jeśli grupa sieciowa zabezpiecza usługi Azure AD DS. Jeśli dołączenie do domeny tej maszyny Wirtualnej systemu Windows zakończy się pomyślnie, można przejść do następnego kroku i utworzyć klastry ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Tworzenie klastra HDInsight za pomocą usługi ESP

Po prawidłowym skonfigurowaniu poprzednich kroków następnym krokiem jest utworzenie klastra HDInsight z włączoną funkcją ESP. Podczas tworzenia klastra HDInsight można włączyć pakiet zabezpieczeń przedsiębiorstwa na karcie **Zabezpieczenia + sieć.** W przypadku szablonu usługi Azure Resource Manager do wdrożenia należy użyć środowiska portalu raz. Następnie pobierz wstępnie wypełniony szablon na stronie **Recenzja + utwórz** do ponownego użycia w przyszłości.

Można również włączyć funkcję [HDInsight ID Broker](identity-broker.md) podczas tworzenia klastra. Funkcja Broker identyfikatorów umożliwia logowanie się do ambari przy użyciu uwierzytelniania wieloskładnikowego i uzyskanie wymaganych biletów Protokołu Kerberos bez konieczności używania skrótów haseł w usługach Azure AD DS.

> [!NOTE]  
> Pierwsze sześć znaków nazw klastra ESP musi być unikatowe w twoim środowisku. Na przykład jeśli masz wiele klastrów ESP w różnych sieciach wirtualnych, wybierz konwencję nazewnictwa, która zapewnia, że pierwsze sześć znaków w nazwach klastra są unikatowe.

![Sprawdzanie poprawności domeny pakietu zabezpieczeń usługi Azure HDInsight enterprise](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Po włączeniu esp typowe błędy konfiguracji związane z usługą Azure AD DS są automatycznie wykrywane i weryfikowane. Po naprawieniu tych błędów można kontynuować następny krok.

![Nie można zawładnić poprawności domeny przez pakiet zabezpieczeń usługi Azure HDInsight dla przedsiębiorstw](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Podczas tworzenia klastra HDInsight z esp, należy podać następujące parametry:

* **Użytkownik administratora klastra:** Wybierz administratora klastra z zsynchronizowanego wystąpienia usług Azure AD DS. To konto domeny musi być już zsynchronizowane i dostępne w usługach Azure AD DS.

* **Grupy dostępu**do klastra: grupy zabezpieczeń, których użytkownicy mają synchronizować i mają dostęp do klastra, powinny być dostępne w usługach Azure AD DS. Przykładem jest hiveusers grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy i dodawanie członków usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **Adres URL LDAPS** `ldaps://contoso.com:636`: Przykładem jest .

Utworzoną tożsamość zarządzaną można wybrać z listy **rozwijanej tożsamości zarządzanej przypisanej przez użytkownika** podczas tworzenia nowego klastra.

![Tożsamość zarządzana usługami domenowymi usługi Active Directory usługi Active Director usługi Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować zasady gałęzi i uruchomić kwerendy gałęzi, zobacz [Konfigurowanie zasad gałęzi apache dla klastrów usługi HDInsight za pomocą usługi ESP](apache-domain-joined-run-hive.md).
* Aby połączyć się z klastrami HDInsight za pomocą systemu [SSH z systemem Apache Hadoop opartym na systemie Linux,](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)można połączyć się z klastrami HDInsight z systemem Windows, Unix lub OS X .
