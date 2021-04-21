---
title: Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Service Fabric
description: Ten artykuł zawiera zestaw najlepszych rozwiązań w zakresie zabezpieczeń usługi Azure Service Fabric zabezpieczeń.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: a7d87e2496158fec8ff33ab8586c845a6207f810
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816066"
---
# <a name="azure-service-fabric-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Service Fabric
Wdrażanie aplikacji na platformie Azure jest szybkie, łatwe i ekonomiczne. Przed wdrożeniem aplikacji w chmurze w środowisku produkcyjnym zapoznaj się z naszą listą podstawowych i zalecanych najlepszych rozwiązań w zakresie implementowania bezpiecznych klastrów w aplikacji.

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Ponadto usługa Service Fabric pozwala sprostać istotnym wyzwaniom związanym z opracowywaniem aplikacji w chmurze i zarządzaniem nimi. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu.

Dla każdego najlepszego rozwiązania wyjaśnimy:

-   Jakie jest najlepsze rozwiązanie.
-   Dlaczego warto zaimplementować najlepsze rozwiązanie.
-   Co może się zdarzyć, jeśli nie zaim implementuje się najlepszego rozwiązania.
-   Jak można nauczyć się implementować najlepsze rozwiązania.

Zalecamy stosowanie następujących najlepszych rozwiązań Service Fabric azure:

-   Tworzenie bezpiecznych klastrów przy użyciu Azure Resource Manager i modułu Service Fabric PowerShell.
-   Użyj certyfikatów X.509.
-   Konfigurowanie zasad zabezpieczeń.
-   Zaim implementuj Reliable Actors konfiguracji zabezpieczeń.
-   Konfigurowanie usługi TLS dla usługi Azure Service Fabric.
-   Używaj izolacji sieci i zabezpieczeń w usłudze Azure Service Fabric.
-   Skonfiguruj Azure Key Vault zabezpieczeń.
-   Przypisywanie użytkowników do ról.


## <a name="best-practices-for-securing-your-clusters"></a>Najlepsze rozwiązania dotyczące zabezpieczania klastrów

Zawsze używaj bezpiecznego klastra:
-   Zaim implementuj zabezpieczenia klastra przy użyciu certyfikatów.
-   Zapewnianie dostępu klienta (administratora i tylko do odczytu) przy użyciu Azure Active Directory (Azure AD).

Korzystanie z wdrożeń automatycznych:
-   Użyj skryptów do generowania, wdrażania i przewłaczania wpisów tajnych.
-   Przechowuj wpisy tajne w usłudze Azure Key Vault i używaj usługi Azure AD do uzyskiwania dostępu do wszystkich innych klientów.
-   Wymagaj uwierzytelniania w celu uzyskania dostępu przez człowieka do wpisów tajnych.

Ponadto należy wziąć pod uwagę następujące opcje konfiguracji:
-   Utwórz sieci obwodowe (znane również jako strefy zdemilitaryzowane, strefy DMZ i podsieci ekranowane) przy użyciu sieciowych grup zabezpieczeń platformy Azure.
-   Uzyskiwanie dostępu do maszyn wirtualnych klastra lub zarządzanie klastrem przy użyciu serwerów przeskoków z Podłączanie pulpitu zdalnego.

Klastry muszą być zabezpieczone, aby uniemożliwić nieautoryzowanym użytkownikom nawiązywanie połączenia, zwłaszcza gdy klaster jest uruchomiony w środowisku produkcyjnym. Mimo że można utworzyć niezabezpieczony klaster, użytkownicy anonimowi mogą łączyć się z klastrem, jeśli klaster uwidacznia punkty końcowe zarządzania w publicznym Internecie.

Istnieją trzy [scenariusze wdrażania](../../service-fabric/service-fabric-cluster-security.md) zabezpieczeń klastra przy użyciu różnych technologii:

-   Zabezpieczenia między węzłami: ten scenariusz zabezpiecza komunikację między maszynami wirtualnych i komputerami w klastrze. Ta forma zabezpieczeń zapewnia, że tylko te komputery, które są autoryzowane do dołączenia do klastra, mogą hostować aplikacje i usługi w klastrze.
W tym scenariuszu klastry uruchomione na platformie Azure lub klastry [](../../service-fabric/service-fabric-windows-cluster-x509-security.md) autonomiczne, które działają w systemie Windows, mogą używać zabezpieczeń certyfikatów lub zabezpieczeń systemu [Windows](../../service-fabric/service-fabric-windows-cluster-windows-security.md) dla maszyn z systemem Windows Server.
-   Zabezpieczenia klient-węzeł: ten scenariusz zabezpiecza komunikację między klientem usługi Service Fabric a poszczególnymi węzłami w klastrze.
-   Service Fabric kontroli dostępu opartej na rolach (Service Fabric RBAC): w tym scenariuszu są używane oddzielne tożsamości (certyfikaty, usługa Azure AD itp.) dla każdego administratora i roli klienta użytkownika, która uzyskuje dostęp do klastra. Tożsamości ról określa się podczas tworzenia klastra.

>[!NOTE]
>**Zalecenie dotyczące zabezpieczeń klastrów platformy Azure:** Użyj zabezpieczeń usługi Azure AD do uwierzytelniania klientów i certyfikatów na potrzeby zabezpieczeń między węzłami.

Aby skonfigurować autonomiczny klaster systemu Windows, zobacz Configure settings for a standalone Windows cluster (Konfigurowanie [ustawień dla autonomicznego klastra systemu Windows).](../../service-fabric/service-fabric-cluster-manifest.md)

Użyj Azure Resource Manager i modułu Service Fabric PowerShell, aby utworzyć bezpieczny klaster.
Aby uzyskać instrukcje krok po kroku dotyczące tworzenia bezpiecznego klastra Service Fabric przy użyciu szablonów Azure Resource Manager, zobacz Tworzenie klastra [Service Fabric klastra](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Użyj szablonu Azure Resource Manager:
-   Dostosuj klaster przy użyciu szablonu, aby skonfigurować magazyn zarządzany dla wirtualnych dysków twardych (VHD) maszyny wirtualnej.
-   Przekieruj zmiany w grupie zasobów przy użyciu szablonu w celu łatwego zarządzania konfiguracją i inspekcji.

Traktuj konfigurację klastra jako kod:
-   Należy dokładnie sprawdzić konfiguracje wdrożenia.
-   Unikaj używania niejawnych poleceń do bezpośredniego modyfikowania zasobów.

Wiele aspektów cyklu życia [Service Fabric można](../../service-fabric/service-fabric-application-lifecycle.md) zautomatyzować. Moduł [Service Fabric PowerShell](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatyzuje typowe zadania dotyczące wdrażania, uaktualniania, usuwania i testowania aplikacji Service Fabric Azure. Dostępne są również zarządzane interfejsy API i interfejsy API protokołu HTTP do zarządzania aplikacją.

## <a name="use-x509-certificates"></a>Korzystanie z certyfikatów X.509
Zawsze zabezpieczaj klastry przy użyciu certyfikatów X.509 lub zabezpieczeń systemu Windows. Zabezpieczenia są konfigurowane tylko podczas tworzenia klastra. Nie można włączyć zabezpieczeń po utworzeniu klastra.

Aby określić [certyfikat klastra,](../../service-fabric/service-fabric-windows-cluster-x509-security.md)ustaw wartość właściwości **ClusterCredentialType** na X509. Aby określić certyfikat serwera dla połączeń zewnętrznych, ustaw właściwość **ServerCredentialType** na X509.

Ponadto postępuj zgodnie z tymi rozwiązaniami:
-   Utwórz certyfikaty dla klastrów produkcyjnych przy użyciu prawidłowo skonfigurowanej usługi certyfikatów systemu Windows Server. Certyfikaty można również uzyskać z zatwierdzonego urzędu certyfikacji.
-   Nigdy nie używaj tymczasowego lub testowego certyfikatu dla klastrów produkcyjnych, jeśli certyfikat został utworzony przy użyciu MakeCert.exe lub podobnego narzędzia.
-   Użyj certyfikatu z podpisem własnym dla klastrów testowych, ale nie dla klastrów produkcyjnych.

Jeśli klaster jest niezabezpieczone, każdy może połączyć się z klastrem anonimowo i wykonywać operacje zarządzania. Z tego powodu zawsze zabezpieczaj klastry produkcyjne przy użyciu certyfikatów X.509 lub zabezpieczeń systemu Windows.

Aby dowiedzieć się więcej na temat używania certyfikatów X.509, zobacz Dodawanie lub usuwanie certyfikatów [dla klastra Service Fabric klastra.](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)

## <a name="configure-security-policies"></a>Konfigurowanie zasad zabezpieczeń
Service Fabric zabezpiecza również zasoby używane przez aplikacje. Zasoby, takie jak pliki, katalogi i certyfikaty, są przechowywane na kontach użytkowników podczas wdrażania aplikacji. Ta funkcja sprawia, że uruchamianie aplikacji jest bezpieczniejsze od siebie, nawet w udostępnionym środowisku hostowym.

-   Użyj grupy domeny lub użytkownika usługi Active Directory: uruchom usługę przy użyciu poświadczeń dla konta użytkownika lub grupy usługi Active Directory. Pamiętaj, aby używać lokalnej usługi Active Directory w domenie, a nie Azure Active Directory. Uzyskaj dostęp do innych zasobów w domenie, do których udzielono uprawnień, przy użyciu użytkownika lub grupy domeny. Na przykład zasoby, takie jak udziały plików.

-   Przypisz zasady dostępu zabezpieczeń dla punktów końcowych HTTP i HTTPS: określ właściwość **SecurityAccessPolicy,** aby zastosować zasady **Uruchom** jako do usługi, gdy manifest usługi deklaruje zasoby punktu końcowego za pomocą protokołu HTTP. Porty przydzielone do punktów końcowych HTTP to prawidłowo kontrolowane listy dla konta użytkownika Uruchom jako, w ramach których działa usługa. Jeśli zasady nie są ustawione, http.sys nie ma dostępu do usługi i można uzyskać błędy z wywołaniami od klienta.

Aby dowiedzieć się, jak używać zasad zabezpieczeń w klastrze Service Fabric, zobacz [Konfigurowanie zasad zabezpieczeń dla aplikacji.](../../service-fabric/service-fabric-application-runas-security.md)

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementowanie konfiguracji Reliable Actors zabezpieczeń
Service Fabric Reliable Actors to implementacja wzorca projektowego aktora. Podobnie jak w przypadku każdego wzorca projektowego oprogramowania, decyzja o użyciu określonego wzorca zależy od tego, czy problem z oprogramowaniem pasuje do wzorca.

Ogólnie rzecz biorąc, wzorzec projektowy aktora pomaga modelować rozwiązania następujących problemów z oprogramowaniem lub scenariuszy zabezpieczeń:
-   Obszar problemu obejmuje dużą liczbę (tysiące lub więcej) małych, niezależnych i izolowanych jednostek stanu i logiki.
-   Pracujesz z obiektami jednowątkowych, które nie wymagają znaczącej interakcji ze składnikami zewnętrznymi, w tym ze stanem zapytania w zestawie aktorów.
-   Wystąpienia aktora nie blokują wywołań z nieprzewidywalnymi opóźnieniami, wystawiając operacje we/wy.

W Service Fabric aktorzy są implementowane w Reliable Actors aplikacji. Ta framework jest oparta na wzorcu aktora i oparta na Service Fabric Reliable Services [.](../../service-fabric/service-fabric-reliable-services-introduction.md) Każda zapisywana niezawodna usługa aktora jest partycjonowana stanową usługą niezawodną.

Każdy aktor jest definiowany jako wystąpienie typu aktora, identyczne ze sposobem, w jaki obiekt .NET jest wystąpieniem typu .NET. Na przykład typ **aktora,** który implementuje funkcjonalność kalkulatora, może mieć wielu aktorów tego typu, które są dystrybuowane w różnych węzłach w klastrze. Każdy z rozproszonych aktorów jest unikatowo scharakteryzowany przez identyfikator aktora.

[Konfiguracje zabezpieczeń replikatora](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) są używane do zabezpieczania kanału komunikacyjnego używanego podczas replikacji. Ta konfiguracja uniemożliwia usługom wyświetlanie ruchu replikacji między sobą i zapewnia, że dane o wysokiej dostępie są bezpieczne. Domyślnie pusta sekcja konfiguracji zabezpieczeń uniemożliwia zabezpieczenia replikacji.
Konfiguracje replikatora konfigurują replikatora, który jest odpowiedzialny za zapewnianie wysokiej niezawodności stanu dostawcy stanu aktora.

## <a name="configure-tls-for-azure-service-fabric"></a>Konfigurowanie usługi TLS dla usługi Azure Service Fabric
Proces uwierzytelniania serwera [uwierzytelnia](../../service-fabric/service-fabric-cluster-creation-via-arm.md) punkty końcowe zarządzania klastrem na kliencie zarządzania. Następnie klient zarządzania rozpoznaje, że rozmawia z rzeczywistym klastrem. Ten certyfikat udostępnia również protokół [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) dla interfejsu API zarządzania protokołem HTTPS i na Service Fabric Explorer za pośrednictwem protokołu HTTPS.
Musisz uzyskać niestandardową nazwę domeny dla klastra. W przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być taka sama jak nazwa domeny niestandardowej, która jest używana dla klastra.

Aby skonfigurować protokół TLS dla aplikacji, należy najpierw uzyskać certyfikat SSL/TLS, który został podpisany przez urząd certyfikacji. Urząd certyfikacji jest zaufaną stroną trzecią, która wydaje certyfikaty na potrzeby zabezpieczeń TLS. Jeśli nie masz jeszcze certyfikatu SSL/TLS, musisz uzyskać certyfikat od firmy, która sprzedaje certyfikaty SSL/TLS.

Certyfikat musi spełniać następujące wymagania dotyczące certyfikatów SSL/TLS na platformie Azure:
-   Certyfikat musi zawierać klucz prywatny.

-   Certyfikat musi zostać utworzony do wymiany kluczy i można go wyeksportować do pliku wymiany informacji osobistych (pfx).

-   Nazwa podmiotu certyfikatu musi być taka sama jak nazwa domeny używana do uzyskiwania dostępu do usługi w chmurze.

    - Uzyskaj niestandardową nazwę domeny do użycia w celu uzyskania dostępu do usługi w chmurze.
    - Zażądaj certyfikatu od urzędu certyfikacji z nazwą podmiotu, która odpowiada niestandardowej nazwie domeny usługi. Jeśli na przykład niestandardowa nazwa domeny to __contoso__**.com,** certyfikat z urzędu certyfikacji powinien mieć nazwę podmiotu **.contoso.com** __lub www__**.contoso.com**.

    >[!NOTE]
    >Nie można uzyskać certyfikatu SSL/TLS z urzędu certyfikacji dla __domeny cloudapp__**.net.**

-   Certyfikat musi używać szyfrowania co najmniej 2048-bitowego.

Protokół HTTP jest niezabezpieczone i podlega atakom podsłuchiwu. Dane przesyłane za pośrednictwem protokołu HTTP są wysyłane jako zwykły tekst z przeglądarki internetowej do serwera internetowego lub między innymi punktami końcowymi. Osoby atakujące mogą przechwytywać i wyświetlać poufne dane wysyłane za pośrednictwem protokołu HTTP, takie jak szczegóły karty kredytowej i dane logowania do konta. Gdy dane są wysyłane lub publikowane za pośrednictwem przeglądarki za pośrednictwem protokołu HTTPS, protokół SSL zapewnia, że poufne informacje są szyfrowane i zabezpieczane przed przechwyceniem.

Aby dowiedzieć się więcej na temat używania certyfikatów SSL/TLS, zobacz [Konfigurowanie protokołu TLS dla aplikacji na platformie Azure.](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md)

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Używanie izolacji sieci i zabezpieczeń w usłudze Azure Service Fabric
Skonfiguruj bezpieczny klaster typu 3 przy użyciu szablonu [Azure Resource Manager jako](../../azure-resource-manager/templates/template-syntax.md) przykładu. Kontroluj przychodzący i wychodzący ruch sieciowy przy użyciu szablonu i sieciowych grup zabezpieczeń.

Szablon zawiera sieciową nr 5 dla każdego zestawu skalowania maszyn wirtualnych i służy do kontrolowania ruchu do i z zestawu. Reguły są konfigurowane domyślnie tak, aby zezwalać na cały ruch niezbędny dla usług systemowych i portów aplikacji określonych w szablonie. Przejrzyj te reguły i wdaj wszelkie zmiany zgodnie z potrzebami, w tym dodaj nowe reguły dla aplikacji.

Aby uzyskać więcej informacji, zobacz [Typowe scenariusze sieciowe dla usługi Azure Service Fabric](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurowanie Azure Key Vault zabezpieczeń
Service Fabric używa certyfikatów do zapewnienia uwierzytelniania i szyfrowania w celu zabezpieczenia klastra i jego aplikacji.

Service Fabric używa certyfikatów X.509 do zabezpieczenia klastra i zapewnienia funkcji zabezpieczeń aplikacji. Za pomocą Azure Key Vault [zarządzać certyfikatami dla](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) klastrów Service Fabric na platformie Azure. Dostawca zasobów platformy Azure, który tworzy klastry, ściąga certyfikaty z magazynu kluczy. Następnie dostawca instaluje certyfikaty na maszynach wirtualnych podczas wdrażania klastra na platformie Azure.

Istnieje relacja certyfikatu [między Azure Key Vault](../../key-vault/general/security-features.md), klastrem Service Fabric i dostawcą zasobów, który korzysta z certyfikatów. Po utworzeniu klastra informacje o relacji certyfikatu są przechowywane w magazynie kluczy.

Aby skonfigurować magazyn kluczy, należy wykonać dwa podstawowe kroki:
1. Utwórz grupę zasobów specjalnie dla magazynu kluczy.

    Zalecamy, aby magazyn kluczy był umieszczany we własnej grupie zasobów. Ta akcja pomaga zapobiec utracie kluczy i wpisów tajnych w przypadku usunięcia innych grup zasobów, takich jak magazyn, obliczenia lub grupa zawierająca klaster. Grupa zasobów zawierająca magazyn kluczy musi znajdować się w tym samym regionie co klaster, który go używa.

2. Utwórz magazyn kluczy w nowej grupie zasobów.

    Magazyn kluczy musi być włączony do wdrożenia. Dostawca zasobów obliczeniowych może następnie pobrać certyfikaty z magazynu i zainstalować je na wystąpieniach maszyn wirtualnych.

Aby dowiedzieć się więcej na temat sposobu skonfigurowania magazynu kluczy, zobacz Co to [jest Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról
Po utworzeniu aplikacji reprezentujących klaster przypisz użytkowników do ról obsługiwanych przez Service Fabric: tylko do odczytu i administratora. Te role można przypisać przy użyciu Azure Portal.

>[!NOTE]
> Aby uzyskać więcej informacji na temat używania ról w Service Fabric, zobacz Service Fabric kontroli dostępu opartej na rolach [dla Service Fabric klientów](../../service-fabric/service-fabric-cluster-security-roles.md)programu .

Usługa Azure Service Fabric obsługuje dwa typy kontroli dostępu dla klientów połączonych z [klastrem Service Fabric klastra:](../../service-fabric/service-fabric-cluster-creation-via-arm.md)administrator i użytkownik. Administrator klastra może użyć kontroli dostępu, aby ograniczyć dostęp do niektórych operacji klastra dla różnych grup użytkowników. Kontrola dostępu sprawia, że klaster jest bezpieczniejszy.

## <a name="next-steps"></a>Następne kroki

- [Service Fabric listy kontrolnej zabezpieczeń](../../service-fabric/service-fabric-best-practices-security.md)
- Skonfiguruj środowisko Service Fabric [dewelopera.](../../service-fabric/service-fabric-get-started.md)
- Dowiedz się [więcej Service Fabric pomocy technicznej.](../../service-fabric/service-fabric-support.md)