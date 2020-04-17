---
title: Zabezpieczanie klastra sieci szkieletowej usług Azure
description: Dowiedz się więcej o scenariuszach zabezpieczeń klastra sieci szkieletowej usług Azure i różnych technologiach, których można użyć do ich zaimplementowania.
ms.topic: conceptual
ms.date: 08/14/2018
ms.custom: sfrev
ms.openlocfilehash: c43cfbd4468a64867d50482d9c8055622602f159
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461586"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Scenariusze zabezpieczeń klastra sieci szkieletowej usług

Klaster sieci szkieletowej usług Azure to zasób, którego jesteś właścicielem. Użytkownik jest odpowiedzialny za zabezpieczenie klastrów, aby zapobiec łączeniu się z nimi nieautoryzowanym użytkownikom. Bezpieczny klaster jest szczególnie ważne podczas uruchamiania obciążeń produkcyjnych w klastrze. Istnieje możliwość utworzenia klastra niezabezpieczonego, jednak jeśli klaster udostępnia punkty końcowe zarządzania do publicznego Internetu, anonimowi użytkownicy mogą się z nim połączyć. Klastry niezabezpieczone nie są obsługiwane dla obciążeń produkcyjnych. 

Ten artykuł zawiera omówienie scenariuszy zabezpieczeń dla klastrów platformy Azure i klastrów autonomicznych oraz różnych technologii, których można użyć do ich zaimplementowania:

* Zabezpieczenia między węzłami
* Zabezpieczenia między klientami
* Kontrola dostępu oparta na rolach (RBAC)

## <a name="node-to-node-security"></a>Zabezpieczenia między węzłami

Zabezpieczenia między węzłami pomagają zabezpieczyć komunikację między maszynami wirtualnymi lub komputerami w klastrze. Ten scenariusz zabezpieczeń gwarantuje, że tylko komputery, które są upoważnione do przyłączenia się do klastra mogą uczestniczyć w hostowania aplikacji i usług w klastrze.

![Diagram komunikacji między węzłami][Node-to-Node]

Klastry uruchomione na platformie Azure i autonomiczne klastry uruchomione w systemie Windows mogą używać [zabezpieczeń certyfikatów](https://msdn.microsoft.com/library/ff649801.aspx) lub [zabezpieczeń systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx) dla komputerów z systemem Windows Server.

### <a name="node-to-node-certificate-security"></a>Zabezpieczenia certyfikatu węzeł-węzeł

Sieć szkieletowa usług używa certyfikatów serwera X.509 określonych jako część konfiguracji typu węzła podczas tworzenia klastra. Na końcu tego artykułu można zobaczyć krótki przegląd tego, czym są te certyfikaty i jak można je nabyć lub utworzyć.

Konfigurowanie zabezpieczeń certyfikatów podczas tworzenia klastra w witrynie Azure Portal przy użyciu szablonu usługi Azure Resource Manager lub przy użyciu autonomicznego szablonu JSON. Domyślnym zachowaniem sdk sieci szkieletowej usług jest wdrożenie i zainstalowanie certyfikatu z najdalej w przyszłym wygasającym certyfikacie; klasyczne zachowanie pozwoliło na zdefiniowanie certyfikatów podstawowych i pomocniczych, aby umożliwić ręczne inicjowane najazdy i nie jest zalecane do użycia nad nowymi funkcjami. Certyfikaty podstawowe, które będą używane ma najdalej do przyszłej daty wygaśnięcia, powinny różnić się od klienta administracyjnego i tylko do odczytu certyfikatów klienta, które można ustawić dla [zabezpieczeń klient-węzeł](#client-to-node-security).

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatów w klastrze na platformie Azure, zobacz [Konfigurowanie klastra przy użyciu szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatów w klastrze dla autonomicznego klastra systemu Windows Server, zobacz [Zabezpieczanie autonomicznego klastra w systemie Windows przy użyciu certyfikatów X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Zabezpieczenia systemu Windows typu węzeł-węzeł

Aby dowiedzieć się, jak skonfigurować zabezpieczenia systemu Windows dla autonomicznego klastra systemu Windows Server, zobacz [Zabezpieczanie autonomicznego klastra w systemie Windows przy użyciu zabezpieczeń systemu Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Zabezpieczenia między klientami

Zabezpieczenia klient-węzeł uwierzytelniają klientów i pomagają w zabezpieczeniu komunikacji między klientem a poszczególnymi węzłami w klastrze. Ten typ zabezpieczeń pomaga zapewnić, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i aplikacji wdrożonych w klastrze. Klienci są jednoznacznie identyfikowani za pomocą poświadczeń zabezpieczeń systemu Windows lub poświadczeń zabezpieczeń certyfikatów.

![Diagram komunikacji klient-węzeł][Client-to-Node]

Klastry działające na platformie Azure i autonomiczne klastry działające w systemie Windows mogą używać [zabezpieczeń certyfikatów](https://msdn.microsoft.com/library/ff649801.aspx) lub [zabezpieczeń systemu Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Zabezpieczenia certyfikatu klient-węzeł

Skonfiguruj zabezpieczenia certyfikatu klient-węzeł podczas tworzenia klastra w witrynie Azure portal przy użyciu szablonu Menedżera zasobów lub przy użyciu autonomicznego szablonu JSON. Aby utworzyć certyfikat, określ certyfikat klienta administratora lub certyfikat klienta użytkownika. Najlepszym rozwiązaniem jest, że określone certyfikaty klienta administratora i klienta użytkownika powinny się różnić od certyfikatów podstawowych i pomocniczych określonych dla [zabezpieczeń węzła do węzła](#node-to-node-security). Certyfikaty klastra mają takie same prawa jak certyfikaty administratora klienta. Jednak powinny one być używane tylko przez klaster, a nie przez użytkowników administracyjnych jako najlepsze rozwiązanie w zakresie zabezpieczeń.

Klienci, którzy łączą się z klastrem przy użyciu certyfikatu administratora mają pełny dostęp do możliwości zarządzania. Klienci, którzy łączą się z klastrem przy użyciu certyfikatu klienta tylko do odczytu, mają dostęp do odczytu tylko do możliwości zarządzania. Certyfikaty te są używane dla RBAC, który jest opisany w dalszej części tego artykułu.

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatów w klastrze na platformie Azure, zobacz [Konfigurowanie klastra przy użyciu szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Aby dowiedzieć się, jak skonfigurować zabezpieczenia certyfikatów w klastrze dla autonomicznego klastra systemu Windows Server, zobacz [Zabezpieczanie autonomicznego klastra w systemie Windows przy użyciu certyfikatów X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Zabezpieczenia usługi Azure Active Directory między klientami na platformie Azure na platformie Azure

Usługa Azure AD umożliwia organizacjom (znanym jako dzierżawy) zarządzanie dostępem użytkowników do aplikacji. Aplikacje są podzielone na te z interfejsem użytkownika logowania opartego na sieci Web i te z natywnym doświadczeniem klienta. Jeśli nie utworzono jeszcze dzierżawy, zacznij od przeczytania [jak uzyskać dzierżawy usługi Azure Active Directory.][active-directory-howto-tenant]

Klaster usługi Service Fabric udostępnia kilka punktów wejścia dla swoich funkcji zarządzania, w tym internetowe narzędzie [Service Fabric Explorer][service-fabric-visualizing-your-cluster] i [program Visual Studio][service-fabric-manage-application-in-visual-studio]. W rezultacie utworzysz dwie aplikacje usługi Azure AD, aby kontrolować dostęp do klastra, jedną aplikację sieci web i jedną aplikację macierzystą.

W przypadku klastrów uruchomionych na platformie Azure można również zabezpieczyć dostęp do punktów końcowych zarządzania przy użyciu usługi Azure Active Directory (Azure AD). Aby dowiedzieć się, jak utworzyć wymagane artefakty usługi Azure AD i jak je wypełnić podczas tworzenia klastra, zobacz [Konfigurowanie usługi Azure AD w celu uwierzytelnienia klientów.](service-fabric-cluster-creation-setup-aad.md)

## <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

W przypadku klastrów usługi Service Fabric wdrożonych w sieci publicznej hostowanej na platformie Azure zalecenia dla wzajemnego uwierzytelniania klienta i węzła są następujące:

* Używanie usługi Azure Active Directory na potrzeby określania tożsamości klienta
* Certyfikat dla tożsamości serwera i szyfrowania TLS komunikacji http

W przypadku klastrów sieci szkieletowej usług wdrożonych w sieci publicznej hostowanych na platformie Azure zalecenie dotyczące zabezpieczeń między węzłami polega na użyciu certyfikatu klastra do uwierzytelniania węzłów.

W przypadku autonomicznych klastrów systemu Windows Server, jeśli masz systemy Windows Server 2012 R2 i Windows Active Directory, zaleca się używanie zabezpieczeń systemu Windows z grupowymi kontami usług zarządzanych. W przeciwnym razie należy użyć zabezpieczeń systemu Windows z kontami systemu Windows.

## <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

Za pomocą kontroli dostępu można ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników. Pomaga to uczynić klaster bardziej bezpieczne. Dwa typy kontroli dostępu są obsługiwane dla klientów, którzy łączą się z klastrem: rola administratora i rola użytkownika.

Użytkownicy, którym przypisano rolę Administrator, mają pełny dostęp do funkcji zarządzania, w tym funkcji odczytu i zapisu. Użytkownicy, którym domyślnie przypisano rolę Użytkownik, mają tylko dostęp do odczytu do funkcji zarządzania (na przykład możliwości zapytań). Mogą również rozwiązywać problemy z aplikacjami i usługami.

Ustaw role klienta administratora i użytkownika podczas tworzenia klastra. Przypisz role, udostępniając oddzielne tożsamości (na przykład przy użyciu certyfikatów lub usługi Azure AD) dla każdego typu roli. Aby uzyskać więcej informacji na temat domyślnych ustawień kontroli dostępu i sposobu zmieniania ustawień [domyślnych, zobacz Kontrola dostępu oparta na rolach dla klientów sieci szkieletowej usług](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certyfikaty X.509 i sieci szkieletowej usług

Certyfikaty cyfrowe X.509 są często używane do uwierzytelniania klientów i serwerów. Są one również używane do szyfrowania i cyfrowego podpisywania wiadomości. Usługa Service Fabric używa certyfikatów X.509 do zabezpieczenia klastra i zapewnienia funkcji zabezpieczeń aplikacji. Aby uzyskać więcej informacji na temat certyfikatów cyfrowych X.509, zobacz [Praca z certyfikatami](https://msdn.microsoft.com/library/ms731899.aspx). Usługa [Key Vault](../key-vault/general/overview.md) służy do zarządzania certyfikatami klastrów sieci szkieletowej usług na platformie Azure.

Kilka ważnych rzeczy do rozważenia:

* Aby utworzyć certyfikaty dla klastrów z uruchomionymi obciążeniami produkcyjnymi, należy użyć poprawnie skonfigurowanej usługi certyfikatów systemu Windows Server lub certyfikatu zatwierdzonego [urzędu certyfikacji .](https://en.wikipedia.org/wiki/Certificate_authority)
* Nigdy nie używaj żadnych tymczasowych lub testowych certyfikatów utworzonych przy użyciu narzędzi, takich jak MakeCert.exe w środowisku produkcyjnym.
* Certyfikatu z podpisem własnym można używać, ale tylko w klastrze testowym. Nie należy używać certyfikatu z podpisem własnym w produkcji.
* Podczas generowania odcisku palca certyfikatu, należy wygenerować odcisk palca SHA1. SHA1 jest to, co jest używane podczas konfigurowania odcisków palców certyfikatu klienta i klastra.

### <a name="cluster-and-server-certificate-required"></a>Certyfikat klastra i serwera (wymagany)

Te certyfikaty (jeden podstawowy i opcjonalnie pomocniczy) są wymagane do zabezpieczenia klastra i zapobiegania nieautoryzowanemu dostępowi do niego. Certyfikaty te zapewniają uwierzytelnianie klastra i serwera.

Uwierzytelnianie klastra uwierzytelnia komunikację między węzłami dla federacji klastra. Tylko węzły, które mogą udowodnić swoją tożsamość za pomocą tego certyfikatu, mogą dołączyć do klastra. Uwierzytelnianie serwera uwierzytelnia punkty końcowe zarządzania klastrem do klienta zarządzania, dzięki czemu klient zarządzania wie, że rozmawia z prawdziwym klastrem, a nie z "człowiekiem w środku". Ten certyfikat zapewnia również protokół TLS dla interfejsu API zarządzania HTTPS i Eksploratora sieci szkieletowej usług za pośrednictwem protokołu HTTPS. Gdy klient lub węzeł uwierzytelnia węzeł, jednym z początkowych kontroli jest wartość nazwy pospolitej w polu **Temat.** Ta nazwa pospolita lub jedna z alternatywnych nazw przedmiotów certyfikatów (SAN) musi znajdować się na liście dozwolonych nazw pospolitych.

Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny. Certyfikaty te zazwyczaj mają rozszerzenia .pfx lub .pem  
* Certyfikat musi zostać utworzony w celu wymiany kluczy, który można wyeksportować do pliku wymiany informacji osobistych (pfx).
* **Nazwa podmiotu certyfikatu musi być zgodna z domeną używaną do uzyskiwania dostępu do klastra sieci szkieletowej usług**. To dopasowanie jest wymagane do zapewnienia protokołu TLS dla punktu końcowego zarządzania HTTPS klastra i Eksploratora sieci szkieletowej usług. Nie można uzyskać certyfikatu TLS/SSL od urzędu certyfikacji (CA) dla domeny *.cloudapp.azure.com. Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla danego klastra.

Kilka innych rzeczy do rozważenia:

* Pole **Temat** może mieć wiele wartości. Każda wartość jest poprzedzony inicjacją, aby wskazać typ wartości. Zwykle inicjalizacja jest **CN** (dla *nazwy pospolitej);* na przykład **CN\.= www contoso.com**.
* Pole **Temat** może być puste.
* Jeśli opcjonalne pole **Nazwa alternatywna podmiotu** jest wypełniane, musi ono mieć zarówno wspólną nazwę certyfikatu, jak i jeden wpis na san. Są one wprowadzane jako wartości **nazwy DNS.** Aby dowiedzieć się, jak generować certyfikaty z sieciami SAN, zobacz [Jak dodać alternatywną nazwę podmiotu do bezpiecznego certyfikatu LDAP](https://support.microsoft.com/kb/931351).
* Wartość pola **Zamierzone cele** certyfikatu powinna zawierać odpowiednią wartość, taką jak **uwierzytelnianie serwera** lub **uwierzytelnianie klienta.**

### <a name="application-certificates-optional"></a>Certyfikaty aplikacji (opcjonalnie)

Dowolna liczba dodatkowych certyfikatów może być zainstalowana w klastrze ze względów bezpieczeństwa aplikacji. Przed utworzeniem klastra należy wziąć pod uwagę scenariusze zabezpieczeń aplikacji, które wymagają zainstalowania certyfikatu w węzłach, takie jak:

* Szyfrowanie i odszyfrowywanie wartości konfiguracji aplikacji.
* Szyfrowanie danych między węzłami podczas replikacji.

Koncepcja tworzenia bezpiecznych klastrów jest taka sama, niezależnie od tego, czy są to klastry systemu Linux czy Windows.

### <a name="client-authentication-certificates-optional"></a>Certyfikaty uwierzytelniania klienta (opcjonalnie)

Można określić dowolną liczbę dodatkowych certyfikatów dla operacji administratora lub klienta użytkownika. Klient może używać tego certyfikatu, gdy wymagane jest uwierzytelnianie wzajemne. Certyfikaty klienta zazwyczaj nie są wystawiane przez urząd certyfikacji innej firmy. Zamiast tego magazyn osobisty bieżącej lokalizacji użytkownika zazwyczaj zawiera certyfikaty klienta umieszczone tam przez główny urząd. Certyfikat powinien mieć wartość **zamierzonego celu** **uwierzytelniania klienta**.  

Domyślnie certyfikat klastra ma uprawnienia klienta administratora. Te dodatkowe certyfikaty klienta nie powinny być instalowane w klastrze, ale są określone jako dozwolone w konfiguracji klastra.  Jednak certyfikaty klienta muszą być zainstalowane na komputerach klienckich, aby połączyć się z klastrem i wykonać wszelkie operacje.

> [!NOTE]
> Wszystkie operacje zarządzania w klastrze sieci szkieletowej usług wymagają certyfikatów serwera. Certyfikatów klienta nie można używać do zarządzania.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra na platformie Azure przy użyciu szablonu Usługi Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Tworzenie klastra przy użyciu witryny Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
