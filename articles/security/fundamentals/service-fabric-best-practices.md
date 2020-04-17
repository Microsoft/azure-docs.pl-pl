---
title: Najważniejsze wskazówki dotyczące zabezpieczeń sieci szkieletowej usług Azure
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dotyczących zabezpieczeń sieci szkieletowej usług Azure.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 4548bf77c01194802c2e6203bcbf9fbd240370a2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461654"
---
# <a name="azure-service-fabric-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Service Fabric
Wdrażanie aplikacji na platformie Azure jest szybkie, łatwe i ekonomiczne. Przed wdrożeniem aplikacji w chmurze w procesach produkcyjnych zapoznaj się z naszą listą najważniejszych i zalecanych najlepszych rozwiązań dotyczących implementowania bezpiecznych klastrów w aplikacji.

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi. Ponadto usługa Service Fabric pozwala sprostać istotnym wyzwaniom związanym z opracowywaniem aplikacji w chmurze i zarządzaniem nimi. Deweloperzy i administratorzy mogą uniknąć złożonych problemów związanych z infrastrukturą i skoncentrować się na implementowaniu wymagających obciążeń o znaczeniu strategicznym, które są skalowalne, niezawodne i łatwe w zarządzaniu.

Dla każdego najlepszego rozwiązania wyjaśniamy:

-   Jakie są najlepsze praktyki.
-   Dlaczego należy zaimplementować najlepsze rozwiązania.
-   Co może się zdarzyć, jeśli nie zaimplementujesz najlepszych rozwiązań.
-   Jak można nauczyć się zaimplementować najlepsze praktyki.

Zaleca się następujące najlepsze rozwiązania dotyczące zabezpieczeń sieci szkieletowej usług Azure:

-   Tworzenie bezpiecznych klastrów za pomocą szablonów usługi Azure Resource Manager i modułu PowerShell sieci szkieletowej usług.
-   Użyj certyfikatów X.509.
-   Konfigurowanie zasad zabezpieczeń.
-   Zaimplementuj konfigurację zabezpieczeń reliable actors.
-   Konfigurowanie protokołu TLS dla sieci szkieletowej usług Azure.
-   Użyj izolacji sieci i zabezpieczeń z siecią szkieletową usług Azure.
-   Skonfiguruj usługę Azure Key Vault pod kątem zabezpieczeń.
-   Przypisz użytkowników do ról.


## <a name="best-practices-for-securing-your-clusters"></a>Najważniejsze wskazówki dotyczące zabezpieczania klastrów

Zawsze używaj bezpiecznego klastra:
-   Zaimplementuj zabezpieczenia klastra przy użyciu certyfikatów.
-   Zapewnij dostęp klienta (administratora i tylko do odczytu) przy użyciu usługi Azure Active Directory (Azure AD).

Użyj zautomatyzowanych wdrożeń:
-   Użyj skryptów do generowania, wdrażania i przerzucenia wpisów tajnych.
-   Przechowuj wpisy tajne w usłudze Azure Key Vault i używaj usługi Azure AD dla wszystkich innych klientów.
-   Wymagaj uwierzytelniania dla ludzkiego dostępu do wpisów tajnych.

Ponadto należy wziąć pod uwagę następujące opcje konfiguracji:
-   Tworzenie sieci obwodowych (nazywanych również strefami zdemilitaryzowanymi, strefami DMZ i podsieciami ekranowanymi) przy użyciu grup zabezpieczeń sieci Azure (NSG).
-   Dostęp do maszyn wirtualnych klastra (VM) lub zarządzanie klastrem przy użyciu serwerów szybkiego dostępu z przyłączem pulpitu zdalnego.

Klastry muszą być zabezpieczone, aby uniemożliwić nieautoryzowanym użytkownikom łączenie się, szczególnie gdy klaster jest uruchomiony w produkcji. Chociaż możliwe jest utworzenie klastra niezabezpieczonego, użytkownicy anonimowi mogą łączyć się z klastrem, jeśli klaster udostępnia punkty końcowe zarządzania do publicznego Internetu.

Istnieją trzy [scenariusze](../../service-fabric/service-fabric-cluster-security.md) implementacji zabezpieczeń klastra przy użyciu różnych technologii:

-   Zabezpieczenia między węzłami: ten scenariusz zabezpiecza komunikację między maszynami wirtualnymi a komputerami w klastrze. Ta forma zabezpieczeń gwarantuje, że tylko te komputery, które są upoważnione do przyłączenia się do klastra, mogą obsługiwać aplikacje i usługi w klastrze.
W tym scenariuszu klastry uruchamiane na platformie Azure lub autonomiczne klastry uruchamiane w systemie Windows mogą używać [zabezpieczeń certyfikatów](../../service-fabric/service-fabric-windows-cluster-x509-security.md) lub [zabezpieczeń systemu Windows](../../service-fabric/service-fabric-windows-cluster-windows-security.md) dla komputerów z systemem Windows Server.
-   Zabezpieczenia klient-węzeł: Ten scenariusz zabezpiecza komunikację między klientem sieci szkieletowej usług a poszczególnymi węzłami w klastrze.
-   Kontrola dostępu oparta na rolach (RBAC): W tym scenariuszu używa oddzielnych tożsamości (certyfikaty, usługa Azure AD i tak dalej) dla każdego administratora i roli klienta użytkownika, który uzyskuje dostęp do klastra. Podczas tworzenia klastra należy określić tożsamości roli.

>[!NOTE]
>**Zalecenia dotyczące zabezpieczeń dla klastrów platformy Azure:** Zabezpieczenia usługi Azure AD można używać do uwierzytelniania klientów i certyfikatów dla zabezpieczeń między węzłami.

Aby skonfigurować samodzielny klaster systemu Windows, zobacz [Konfigurowanie ustawień autonomicznego klastra systemu Windows](../../service-fabric/service-fabric-cluster-manifest.md).

Tworzenie bezpiecznego klastra za pomocą szablonów usługi Azure Resource Manager i modułu PowerShell sieci szkieletowej usług.
Aby uzyskać instrukcje krok po kroku dotyczące tworzenia bezpiecznego klastra sieci szkieletowej usług przy użyciu szablonów usługi Azure Resource Manager, zobacz [Tworzenie klastra sieci szkieletowej usług](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Użyj szablonu Usługi Azure Resource Manager:
-   Dostosuj klaster za pomocą szablonu do konfigurowania zarządzanego magazynu dla wirtualnych dysków twardych (VHD).
-   Korzystaj z szablonu w celu łatwego zarządzania konfiguracją i inspekcji.

Potraktuj konfigurację klastra jako kod:
-   Należy dokładnie sprawdzić konfiguracje wdrażania.
-   Należy unikać używania niejawnych poleceń, aby bezpośrednio modyfikować zasoby.

Wiele aspektów [cyklu życia aplikacji sieci szkieletowej usług](../../service-fabric/service-fabric-application-lifecycle.md) można zautomatyzować. [Moduł PowerShell sieci szkieletowej usług](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatyzuje typowe zadania do wdrażania, uaktualniania, usuwania i testowania aplikacji sieci szkieletowej usług Azure. Dostępne są również zarządzane interfejsy API i interfejsy API HTTP do zarządzania aplikacjami.

## <a name="use-x509-certificates"></a>Używanie certyfikatów X.509
Zawsze zabezpieczaj klastry przy użyciu certyfikatów X.509 lub zabezpieczeń systemu Windows. Zabezpieczenia są konfigurowane tylko w czasie tworzenia klastra. Nie można włączyć zabezpieczeń po utworzeniu klastra.

Aby określić [certyfikat klastra,](../../service-fabric/service-fabric-windows-cluster-x509-security.md)należy ustawić wartość właściwości **ClusterCredentialType** na X509. Aby określić certyfikat serwera dla połączeń zewnętrznych, należy ustawić właściwość **ServerCredentialType** na X509.

Ponadto należy postępować zgodnie z tymi praktykami:
-   Tworzenie certyfikatów dla klastrów produkcyjnych przy użyciu poprawnie skonfigurowaną usługą certyfikatów systemu Windows Server. Certyfikaty można również uzyskać od zatwierdzonego urzędu certyfikacji.
-   Nigdy nie używaj certyfikatu tymczasowego lub testowego dla klastrów produkcyjnych, jeśli certyfikat został utworzony przy użyciu pliku MakeCert.exe lub podobnego narzędzia.
-   Użyj certyfikatu z podpisem własnym dla klastrów testowych, ale nie dla klastrów produkcyjnych.

Jeśli klaster jest niezabezpieczony, każdy może anonimowo połączyć się z klastrem i wykonywać operacje zarządzania. Z tego powodu zawsze zabezpieczaj klastry produkcyjne przy użyciu certyfikatów X.509 lub zabezpieczeń systemu Windows.

Aby dowiedzieć się więcej na temat używania certyfikatów X.509, zobacz [Dodawanie lub usuwanie certyfikatów dla klastra sieci szkieletowej usług](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Konfigurowanie zasad zabezpieczeń
Sieci szkieletowej usług zabezpiecza również zasoby, które są używane przez aplikacje. Zasoby, takie jak pliki, katalogi i certyfikaty są przechowywane na kontach użytkowników podczas wdrażania aplikacji. Ta funkcja sprawia, że uruchamianie aplikacji jest bezpieczniejsze od siebie, nawet w udostępnionym środowisku hostowanym.

-   Użyj grupy domen lub użytkownika usługi Active Directory: Uruchom usługę pod poświadczeniami dla użytkownika lub konta grupy usługi Active Directory. Pamiętaj, aby używać usługi Active Directory lokalnie w domenie, a nie w usłudze Azure Active Directory. Uzyskaj dostęp do innych zasobów w domenie, którym przyznano uprawnienia przy użyciu użytkownika lub grupy domeny. Na przykład zasoby, takie jak udziały plików.

-   Przypisywanie zasad dostępu do zabezpieczeń dla punktów końcowych HTTP i HTTPS: Określ właściwość **SecurityAccessPolicy,** aby zastosować zasady **RunAs** do usługi, gdy manifest usługi deklaruje zasoby punktu końcowego za pomocą protokołu HTTP. Porty przydzielone do punktów końcowych HTTP są poprawnie kontrolowane przez dostęp listy dla konta użytkownika RunAs, w których działa usługa. Gdy zasady nie jest ustawiona, http.sys nie ma dostępu do usługi i można uzyskać błędy z wywołania z klienta.

Aby dowiedzieć się, jak używać zasad zabezpieczeń w klastrze sieci szkieletowej usług, zobacz [Konfigurowanie zasad zabezpieczeń dla aplikacji](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Implementowanie konfiguracji zabezpieczeń Reliable Actors
Usługa Fabric Reliable Actors jest implementacją wzorca projektowania aktora. Podobnie jak w przypadku każdego wzorca projektowania oprogramowania, decyzja o użyciu określonego wzorca opiera się na tym, czy problem z oprogramowaniem pasuje do wzorca.

Ogólnie rzecz biorąc, użyj wzorzec projektowania aktora, aby pomóc w modelowaniu rozwiązań dla następujących problemów z oprogramowaniem lub scenariuszy zabezpieczeń:
-   Przestrzeń problemowa obejmuje dużą liczbę (tysiące lub więcej) małych, niezależnych i odizolowanych jednostek stanu i logiki.
-   Pracujesz z obiektami jednowątkowymi, które nie wymagają znaczącej interakcji ze składnikami zewnętrznymi, w tym wykonywania zapytań o stan w zestawie aktorów.
-   Wystąpienia aktora nie blokują wywołujących z nieprzewidywalnymi opóźnieniami przez wystawianie operacji we/wy.

W sieci szkieletowej usług podmioty są implementowane w ramach aplikacji reliable actors. Ta struktura jest oparta na wzorze aktora i opiera się na [bazie usług niezawodnych sieci szkieletowej usług.](../../service-fabric/service-fabric-reliable-services-introduction.md) Każda usługa niezawodnego aktora, który piszesz jest partycjonowane stanowe niezawodnej usługi.

Każdy aktor jest zdefiniowany jako wystąpienie typu aktora, identyczne ze sposobem obiektu .NET jest wystąpieniem typu .NET. Na przykład **typ aktora,** który implementuje funkcjonalność kalkulatora może mieć wiele podmiotów tego typu, które są dystrybuowane w różnych węzłach w klastrze. Każdy z rozproszonych aktorów charakteryzuje się jednoznacznie identyfikatorem aktora.

[Konfiguracje zabezpieczeń replikatora](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) służą do zabezpieczania kanału komunikacyjnego używanego podczas replikacji. Ta konfiguracja uniemożliwia usługom wyświetlanie wzajemnego ruchu replikacji i zapewnia bezpieczeństwo danych o wysokiej dostępności. Domyślnie pusta sekcja konfiguracji zabezpieczeń uniemożliwia bezpieczeństwo replikacji.
Konfiguracje replikatora skonfigurować replikatora, który jest odpowiedzialny za uczynienie stan dostawcy stanu aktora wysoce niezawodne.

## <a name="configure-tls-for-azure-service-fabric"></a>Konfigurowanie sieci szkieletowej usługi TLS dla usługi Azure
Proces uwierzytelniania serwera [uwierzytelnia](../../service-fabric/service-fabric-cluster-creation-via-arm.md) punkty końcowe zarządzania klastrem do klienta zarządzania. Klient zarządzania następnie rozpoznaje, że rozmawia z prawdziwym klastrem. Ten certyfikat zapewnia również [protokół TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) dla interfejsu API zarządzania HTTPS i Eksploratora sieci szkieletowej usług za pośrednictwem protokołu HTTPS.
Musisz uzyskać niestandardową nazwę domeny dla klastra. Podczas żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardową nazwą domeny używaną dla klastra.

Aby skonfigurować TLS dla aplikacji, należy najpierw uzyskać certyfikat SSL/TLS, który został podpisany przez urząd certyfikacji. Urząd certyfikacji jest zaufaną stroną trzecią, która wystawia certyfikaty dla celów zabezpieczeń TLS. Jeśli nie masz jeszcze certyfikatu SSL/TLS, musisz go uzyskać od firmy, która sprzedaje certyfikaty SSL/TLS.

Certyfikat musi spełniać następujące wymagania dotyczące certyfikatów SSL/TLS na platformie Azure:
-   Certyfikat musi zawierać klucz prywatny.

-   Certyfikat musi zostać utworzony w celu wymiany kluczy i można go wyeksportować do pliku wymiany informacji osobistych (.pfx).

-   Nazwa podmiotu certyfikatu musi być zgodna z nazwą domeny używaną do uzyskiwania dostępu do usługi w chmurze.

    - Uzyskaj niestandardową nazwę domeny do użycia w celu uzyskania dostępu do usługi w chmurze.
    - Zażądaj certyfikatu od urzędu certyfikacji o nazwie podmiotu zgodnej z niestandardową nazwą domeny usługi. Jeśli na przykład niestandardową nazwą domeny jest __contoso__**.com,** certyfikat urzędu certyfikacji powinien mieć nazwę podmiotu **.contoso.com** lub __www__**.contoso.com**.

    >[!NOTE]
    >Nie można uzyskać certyfikatu SSL/TLS od urzędu certyfikacji dla domeny **.net** __aplikacji cloudapp.__

-   Certyfikat musi używać szyfrowania 2048-bitowego.

Protokół HTTP jest niezabezpieczony i podlega atakom podsłuchowym. Dane przesyłane za pośrednictwem protokołu HTTP są wysyłane jako zwykły tekst z przeglądarki sieci web do serwera sieci web lub między innymi punktami końcowymi. Osoby atakujące mogą przechwytywać i wyświetlać poufne dane przesyłane za pośrednictwem protokołu HTTP, takie jak dane karty kredytowej i loginy do kont. Gdy dane są wysyłane lub publikowane za pośrednictwem przeglądarki za pośrednictwem protokołu HTTPS, protokół SSL zapewnia szyfrowanie poufnych informacji i zabezpieczenie ich przed przechwyceniem.

Aby dowiedzieć się więcej na temat używania certyfikatów SSL/TLS, zobacz [Konfigurowanie protokołu TLS dla aplikacji na platformie Azure](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Korzystanie z izolacji i zabezpieczeń sieci w sieci szkieletowej usług Azure
Skonfiguruj bezpieczny klaster typu 3 węzłów przy użyciu [szablonu usługi Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) jako przykładu. Kontroluj przychodzący i wychodzący ruch sieciowy za pomocą szablonu i grup zabezpieczeń sieci.

Szablon ma sieciowej grupy sieciowej dla każdego zestawu skalowania maszyny wirtualnej i służy do kontrolowania ruchu do i z zestawu. Reguły są domyślnie skonfigurowane, aby umożliwić cały ruch niezbędny dla usług systemowych i portów aplikacji określonych w szablonie. Przejrzyj te reguły i wprowadzać wszelkie zmiany dopasowane do twoich potrzeb, w tym dodawanie nowych reguł dla aplikacji.

Aby uzyskać więcej informacji, zobacz [Typowe scenariusze sieci dla sieci szkieletowej usług Azure](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Konfigurowanie usługi Azure Key Vault dla bezpieczeństwa
Sieci szkieletowej usług używa certyfikatów do zapewnienia uwierzytelniania i szyfrowania do zabezpieczania klastra i jego aplikacji.

Usługa Service Fabric używa certyfikatów X.509 do zabezpieczenia klastra i zapewnienia funkcji zabezpieczeń aplikacji. Usługa Azure Key Vault służy do [zarządzania certyfikatami](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) dla klastrów sieci szkieletowej usług na platformie Azure. Dostawca zasobów platformy Azure, który tworzy klastry pobiera certyfikaty z magazynu kluczy. Dostawca następnie instaluje certyfikaty na maszynach wirtualnych, gdy klaster jest wdrażany na platformie Azure.

Istnieje relacja certyfikatów między [usługą Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), klastrem sieci szkieletowej usług a dostawcą zasobów, który używa certyfikatów. Podczas tworzenia klastra informacje o relacji certyfikatów są przechowywane w magazynie kluczy.

Istnieją dwa podstawowe kroki konfigurowania magazynu kluczy:
1. Utwórz grupę zasobów specjalnie dla magazynu kluczy.

    Zaleca się umieszczenie magazynu kluczy w własnej grupie zasobów. Ta akcja pomaga zapobiec utracie kluczy i wpisów tajnych, jeśli zostaną usunięte inne grupy zasobów, takie jak magazyn, obliczenia lub grupa zawierająca klaster. Grupa zasobów zawierająca magazyn kluczy musi znajdować się w tym samym regionie co klaster, który go używa.

2. Utwórz magazyn kluczy w nowej grupie zasobów.

    Magazyn kluczy musi być włączony do wdrożenia. Dostawca zasobów obliczeniowych może następnie uzyskać certyfikaty z magazynu i zainstalować je w wystąpieniach maszyny Wirtualnej.

Aby dowiedzieć się więcej o konfigurowaniu magazynu kluczy, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="assign-users-to-roles"></a>Przypisywanie użytkowników do ról
Po utworzeniu aplikacji do reprezentowania klastra, przypisać użytkowników do ról, które są obsługiwane przez sieci szkieletowej usług: tylko do odczytu i administratora. Te role można przypisać przy użyciu witryny Azure portal.

>[!NOTE]
> Aby uzyskać więcej informacji na temat używania ról w sieci szkieletowej usług, zobacz [Kontrola dostępu oparta na rolach dla klientów sieci szkieletowej usług](../../service-fabric/service-fabric-cluster-security-roles.md).

Usługa Azure Service Fabric obsługuje dwa typy kontroli dostępu dla klientów, którzy są połączeni z [klastrem sieci szkieletowej usług:](../../service-fabric/service-fabric-cluster-creation-via-arm.md)administrator i użytkownik. Administrator klastra może używać kontroli dostępu do ograniczania dostępu do niektórych operacji klastra dla różnych grup użytkowników. Kontrola dostępu sprawia, że klaster jest bezpieczniejszy.

## <a name="next-steps"></a>Następne kroki

- [Lista kontrolna zabezpieczeń sieci szkieletowej usług](service-fabric-checklist.md)
- Skonfiguruj [środowisko programistyczne](../../service-fabric/service-fabric-get-started.md)sieci szkieletowej usług .
- Dowiedz się więcej o [opcjach pomocy technicznej sieci szkieletowej](../../service-fabric/service-fabric-support.md)usług .
