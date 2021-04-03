---
title: Zabezpieczanie klastra Service Fabric platformy Azure
description: Dowiedz się więcej o scenariuszach zabezpieczeń dla klastra usługi Azure Service Fabric i różnych technologiach, których można użyć do ich wdrożenia.
ms.topic: conceptual
ms.date: 08/14/2018
ms.openlocfilehash: 6f7bb785184938fe5c1e20e3c915b0112c7723ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573072"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Scenariusze zabezpieczeń klastra Service Fabric

Klaster Service Fabric platformy Azure to zasób, którego jesteś członkiem. Użytkownik jest odpowiedzialny za zabezpieczanie klastrów w celu zapobiegania łączeniu się z nimi nieautoryzowanych użytkowników. Bezpieczny klaster jest szczególnie ważny w przypadku uruchamiania obciążeń produkcyjnych w klastrze. Istnieje możliwość utworzenia klastra niezabezpieczonego, jednak Jeśli klaster ujawnia punkty końcowe zarządzania do publicznej sieci Internet, anonimowi użytkownicy mogą się z nim połączyć. Niezabezpieczone klastry nie są obsługiwane w przypadku obciążeń produkcyjnych. 

Ten artykuł zawiera omówienie scenariuszy zabezpieczeń klastrów platformy Azure i klastrów autonomicznych oraz różnych technologii, których można użyć do ich wdrożenia:

* Zabezpieczenia między węzłami
* Zabezpieczenia między klientem a węzłem
* Service Fabric kontroli dostępu opartej na rolach

## <a name="node-to-node-security"></a>Zabezpieczenia między węzłami

Zabezpieczenia typu węzeł-węzeł pomagają w zabezpieczaniu komunikacji między maszynami wirtualnymi lub komputerami w klastrze. Ten scenariusz zabezpieczeń zapewnia, że tylko komputery autoryzowane do dołączenia do klastra mogą uczestniczyć w aplikacjach i usługach obsługujących klaster.

![Diagram komunikacji między węzłami][Node-to-Node]

Klastry działające na platformie Azure i autonomicznych klastrach działających w systemie Windows mogą korzystać z [zabezpieczeń certyfikatów](/previous-versions/msp-n-p/ff649801(v=pandp.10)) lub [zabezpieczeń systemu Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)) na komputerach z systemem Windows Server.

### <a name="node-to-node-certificate-security"></a>Zabezpieczenia certyfikatów między węzłami

Service Fabric używa certyfikatów serwera X. 509, które są określane jako część konfiguracji typu węzła podczas tworzenia klastra. Na końcu tego artykułu zobaczysz krótkie omówienie tego, czym są te certyfikaty i jak można je uzyskać lub utworzyć.

Konfigurowanie zabezpieczeń certyfikatów podczas tworzenia klastra, w Azure Portal, przy użyciu szablonu Azure Resource Manager lub przy użyciu autonomicznego szablonu JSON. Domyślnym zachowaniem zestawu SDK Service Fabric jest wdrożenie i zainstalowanie certyfikatu z najpóźniejszym terminem wygaśnięcia. zachowanie klasyczne może definiować certyfikaty podstawowe i pomocnicze, aby umożliwić Ręczne inicjowanie przerzucania i nie jest zalecane do użycia w ramach nowych funkcji. Certyfikaty podstawowe, które będą używane, mają najpóźniejsze daty wygaśnięcia, powinny być inne od klienta administratora i certyfikatów klienta tylko do odczytu ustawionych dla [zabezpieczeń klient-węzeł](#client-to-node-security).

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatów w klastrze dla platformy Azure, zobacz [Konfigurowanie klastra przy użyciu szablonu Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatów w klastrze dla autonomicznego klastra systemu Windows Server, zobacz temat [Zabezpieczanie klastra autonomicznego w systemie Windows za pomocą certyfikatów X. 509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Zabezpieczenia między węzłami systemu Windows

> [!NOTE]
> Uwierzytelnianie systemu Windows jest oparte na protokole Kerberos. Uwierzytelnianie NTLM nie jest obsługiwane jako typ uwierzytelniania.
>
> Za każdym razem, gdy to możliwe, Użyj uwierzytelniania certyfikatu X. 509 dla klastrów Service Fabric.

Aby dowiedzieć się, jak skonfigurować zabezpieczenia systemu Windows dla autonomicznego klastra systemu Windows Server, zobacz temat [Zabezpieczanie klastra autonomicznego w systemie Windows przy użyciu zabezpieczeń systemu Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Zabezpieczenia między klientem a węzłem

Zabezpieczenia klienta w węźle uwierzytelniają klientów i pomagają w zabezpieczaniu komunikacji między klientem a poszczególnymi węzłami w klastrze. Ten typ zabezpieczeń pomaga upewnić się, że tylko autoryzowani użytkownicy mają dostęp do klastra i aplikacji wdrożonych w klastrze. Klienci są jednoznacznie identyfikowani przy użyciu poświadczeń zabezpieczeń systemu Windows lub poświadczeń zabezpieczeń certyfikatów.

![Diagram komunikacji między klientem a węzłem][Client-to-Node]

Klastry działające na platformie Azure i w klastrach autonomicznych działających w systemie Windows mogą korzystać z [zabezpieczeń certyfikatów](/previous-versions/msp-n-p/ff649801(v=pandp.10)) lub [zabezpieczeń systemu Windows](/previous-versions/msp-n-p/ff649396(v=pandp.10)), ale zalecane jest użycie uwierzytelniania certyfikatu X. 509, jeśli to możliwe.

### <a name="client-to-node-certificate-security"></a>Zabezpieczenia certyfikatów między klientami a węzłami

Skonfiguruj zabezpieczenia certyfikatu klient-węzeł podczas tworzenia klastra, w Azure Portal, przy użyciu szablonu Menedżer zasobów lub przy użyciu autonomicznego szablonu JSON. Aby utworzyć certyfikat, Określ certyfikat klienta administratora lub certyfikat klienta użytkownika. Najlepszym rozwiązaniem jest określenie, że certyfikaty klienta i klienta administratora będą się różnić od podstawowych i pomocniczych certyfikatów określonych dla [zabezpieczeń między węzłami](#node-to-node-security). Certyfikaty klastra mają takie same prawa jak certyfikaty administratora klienta. Jednak powinny być używane tylko przez klaster, a nie przez użytkowników administracyjnych jako najlepsze rozwiązanie w zakresie zabezpieczeń.

Klienci, którzy łączą się z klastrem przy użyciu certyfikatu administratora, mają pełny dostęp do możliwości zarządzania. Klienci łączący się z klastrem przy użyciu certyfikatu klienta użytkownika tylko do odczytu mają dostęp tylko do odczytu do funkcji zarządzania. Te certyfikaty są używane dla Service Fabric RBAC, które opisano w dalszej części tego artykułu.

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatów w klastrze dla platformy Azure, zobacz [Konfigurowanie klastra przy użyciu szablonu Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatów w klastrze dla autonomicznego klastra systemu Windows Server, zobacz temat [Zabezpieczanie klastra autonomicznego w systemie Windows za pomocą certyfikatów X. 509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Zabezpieczenia Azure Active Directory na poziomie klienta na platformie Azure

Usługa Azure AD umożliwia organizacjom (znanym jako dzierżawy) zarządzanie dostępem użytkowników do aplikacji. Aplikacje są podzielone na te z interfejsem użytkownika logowania opartego na sieci Web i z natywną obsługą klienta. Jeśli dzierżawa nie została jeszcze utworzona, Zacznij od czytania, [jak uzyskać dzierżawę Azure Active Directory][active-directory-howto-tenant].

Klaster usługi Service Fabric udostępnia kilka punktów wejścia dla swoich funkcji zarządzania, w tym internetowe narzędzie [Service Fabric Explorer][service-fabric-visualizing-your-cluster] i [program Visual Studio][service-fabric-manage-application-in-visual-studio]. W efekcie utworzysz dwie aplikacje usługi Azure AD w celu kontrolowania dostępu do klastra, jednej aplikacji sieci Web i jednej aplikacji natywnej.

W przypadku klastrów działających na platformie Azure można również zabezpieczyć dostęp do punktów końcowych zarządzania przy użyciu Azure Active Directory (Azure AD). Aby dowiedzieć się, jak utworzyć wymagane artefakty usługi Azure AD i jak je wypełnić podczas tworzenia klastra, zobacz [Konfigurowanie usługi Azure AD do uwierzytelniania klientów](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

W przypadku klastrów usługi Service Fabric wdrożonych w sieci publicznej hostowanej na platformie Azure zalecenia dla wzajemnego uwierzytelniania klienta i węzła są następujące:

* Używanie usługi Azure Active Directory na potrzeby określania tożsamości klienta
* Certyfikat służący do identyfikacji serwera i szyfrowania TLS komunikacji http

W przypadku klastrów Service Fabric wdrożonych w sieci publicznej hostowanej na platformie Azure zalecenia dotyczące zabezpieczeń między węzłami polegają na uwierzytelnianiu węzłów za pomocą certyfikatu klastra.

W przypadku autonomicznych klastrów systemu Windows Server, jeśli masz systemy Windows Server 2012 R2 i Windows Active Directory, zalecamy użycie zabezpieczeń systemu Windows z kontami usług zarządzanymi przez grupę. W przeciwnym razie Użyj zabezpieczeń systemu Windows z kontami systemu Windows.

## <a name="service-fabric-role-based-access-control"></a>Service Fabric kontroli dostępu opartej na rolach

Za pomocą kontroli dostępu można ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników. Dzięki temu klaster jest bezpieczniejszy. Obsługiwane są dwa typy kontroli dostępu dla klientów łączących się z klastrem: rola administratora i rola użytkownika.

Użytkownicy, którym przypisano rolę administratora, mają pełny dostęp do możliwości zarządzania, w tym możliwości odczytu i zapisu. Użytkownicy, którym przypisano rolę użytkownika, domyślnie mają dostęp tylko do odczytu do funkcji zarządzania (na przykład możliwości zapytania). Mogą również rozpoznać aplikacje i usługi.

Podczas tworzenia klastra Ustaw role administratora i klienta użytkownika. Przypisywanie ról przez dostarczanie osobnych tożsamości (na przykład przy użyciu certyfikatów lub usługi Azure AD) dla każdego typu roli. Aby uzyskać więcej informacji o domyślnych ustawieniach kontroli dostępu i sposobach zmieniania ustawień domyślnych, zobacz [Service Fabric kontroli dostępu opartej na rolach dla klientów Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certyfikaty X. 509 i Service Fabric

Certyfikaty cyfrowe X. 509 są zwykle używane do uwierzytelniania klientów i serwerów. Są one również używane do szyfrowania i cyfrowego podpisywania wiadomości. Service Fabric używa certyfikatów X. 509 w celu zabezpieczenia klastra i zapewnienia funkcji zabezpieczeń aplikacji. Aby uzyskać więcej informacji na temat certyfikatów cyfrowych X. 509, zobacz [Praca z certyfikatami](/dotnet/framework/wcf/feature-details/working-with-certificates). [Key Vault](../key-vault/general/overview.md) służy do zarządzania certyfikatami dla klastrów Service Fabric na platformie Azure.

Niektóre ważne zagadnienia, które należy wziąć pod uwagę:

* Aby utworzyć certyfikaty dla klastrów z uruchomionymi obciążeniami produkcyjnymi, należy użyć prawidłowo skonfigurowanej usługi certyfikatów systemu Windows Server lub jednej z zatwierdzonych [urzędów certyfikacji (CA)](https://en.wikipedia.org/wiki/Certificate_authority).
* Nigdy nie należy używać żadnych certyfikatów tymczasowych ani testowych, które tworzysz przy użyciu narzędzi takich jak MakeCert.exe w środowisku produkcyjnym.
* Możesz użyć certyfikatu z podpisem własnym, ale tylko w klastrze testowym. Nie należy używać certyfikatu z podpisem własnym w środowisku produkcyjnym.
* Podczas generowania odcisku palca certyfikatu Pamiętaj o wygenerowaniu odcisku palca SHA1. Algorytm SHA1 jest używany podczas konfigurowania odcisków palca certyfikatu klienta i klastra.

### <a name="cluster-and-server-certificate-required"></a>Certyfikat klastra i serwera (wymagany)

Te certyfikaty (jeden podstawowy i opcjonalnie pomocniczy) są wymagane do zabezpieczenia klastra i uniemożliwiają nieautoryzowany dostęp do niego. Te certyfikaty zapewniają uwierzytelnianie klastra i serwera.

Uwierzytelnianie klastra uwierzytelnia komunikację między węzłami w ramach Federacji klastra. Do klastra można dołączyć tylko węzły, które mogą udowodnić swoją tożsamość za pomocą tego certyfikatu. Uwierzytelnianie serwera uwierzytelnia punkty końcowe zarządzania klastrami na kliencie zarządzania, dzięki czemu klient zarządzania wie, że jest w stanie mówić do rzeczywistego klastra, a nie jako "man in the middle". Ten certyfikat udostępnia również protokół TLS dla interfejsu API zarządzania HTTPS i dla Service Fabric Explorer za pośrednictwem protokołu HTTPS. Gdy klient lub węzeł uwierzytelnia węzeł, jeden z pierwszych testów jest wartością nazwy pospolitej w polu **podmiot** . Ta nazwa pospolita lub jedna z nazw alternatywny podmiotu (San) certyfikatów musi znajdować się na liście dozwolonych nazw wspólnych.

Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny. Te certyfikaty zazwyczaj mają rozszerzenia PFX lub PEM.  
* Należy utworzyć certyfikat dla wymiany kluczy, który można wyeksportować do pliku wymiany informacji osobistych (pfx).
* **Nazwa podmiotu certyfikatu musi być zgodna z domeną używaną do uzyskiwania dostępu do klastra Service Fabric**. To dopasowanie jest wymagane w celu udostępnienia protokołu TLS dla punktu końcowego zarządzania HTTPS klastra i Service Fabric Explorer. Nie można uzyskać certyfikatu TLS/SSL z urzędu certyfikacji dla domeny *. cloudapp.azure.com. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

Niektóre inne zagadnienia, które należy wziąć pod uwagę:

* Pole **podmiotu** może zawierać wiele wartości. Każda wartość jest poprzedzona inicjalizacją, aby wskazać typ wartości. Zazwyczaj inicjowanie jest **CN** (dla *nazwy pospolitej*); na przykład **CN = www \. contoso.com**.
* Pole **podmiotu** może być puste.
* Jeśli pole wyboru opcjonalnej **alternatywnej nazwy podmiotu** jest wypełnione, musi mieć zarówno nazwę pospolitą certyfikatu, jak i jeden wpis na sieć San. Są one wprowadzane jako wartości **nazw DNS** . Aby dowiedzieć się, jak generować certyfikaty, które mają sieci SAN, zobacz [jak dodać alternatywną nazwę podmiotu do certyfikatu bezpiecznego protokołu LDAP](https://support.microsoft.com/kb/931351).
* Wartość pola **zamierzone cele** w certyfikacie powinna zawierać odpowiednią wartość, na przykład **uwierzytelnianie serwera** lub **uwierzytelnianie klienta**.

### <a name="application-certificates-optional"></a>Certyfikaty aplikacji (opcjonalnie)

Dowolna liczba dodatkowych certyfikatów może być zainstalowana w klastrze na potrzeby zabezpieczeń aplikacji. Przed utworzeniem klastra należy wziąć pod uwagę scenariusze zabezpieczeń aplikacji, które wymagają zainstalowania certyfikatu w węzłach, na przykład:

* Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji.
* Szyfrowanie danych między węzłami podczas replikacji.

Koncepcja tworzenia bezpiecznych klastrów jest taka sama, niezależnie od tego, czy są to klastry systemu Linux czy Windows.

### <a name="client-authentication-certificates-optional"></a>Certyfikaty uwierzytelniania klienta (opcjonalnie)

Dla operacji administratora lub użytkownika można określić dowolną liczbę dodatkowych certyfikatów. Klient może używać tych certyfikatów, gdy wymagane jest wzajemne uwierzytelnianie. Certyfikaty klienta zwykle nie są wystawiane przez urząd certyfikacji innej firmy. Zamiast tego magazyn osobisty bieżącej lokalizacji użytkownika zwykle zawiera certyfikaty klienta umieszczone w ramach głównego urzędu certyfikacji. Certyfikat powinien mieć **zamierzoną** wartość w polu **uwierzytelnianie klienta**.  

Domyślnie certyfikat klastra ma uprawnienia administratora. Te dodatkowe certyfikaty klienta nie powinny być zainstalowane w klastrze, ale są określone jako dozwolone w konfiguracji klastra.  Należy jednak zainstalować certyfikaty klienta na komputerach klienckich, aby połączyć się z klastrem i wykonać wszelkie operacje.

> [!NOTE]
> Wszystkie operacje zarządzania w klastrze Service Fabric wymagają certyfikatów serwera. Nie można używać certyfikatów klienta do zarządzania.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra na platformie Azure przy użyciu szablonu Menedżer zasobów](service-fabric-cluster-creation-via-arm.md)
* [Tworzenie klastra przy użyciu witryny Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
