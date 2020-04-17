---
title: Dowiedz się więcej o zabezpieczeniach aplikacji usługi Azure Service Fabric
description: Omówienie sposobu bezpiecznego uruchamiania aplikacji mikrousług w sieci szkieletowej usług. Dowiedz się, jak uruchamiać usługi i skrypt startowy na różnych kontach zabezpieczeń, uwierzytelniać i autoryzować użytkowników, zarządzać wpisami tajnymi aplikacji, bezpieczną komunikacją usługi, używać bramy interfejsu API i zabezpieczać dane aplikacji w spoczynku.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: c97c5345a1a18cce8c44508542f12d3642d2b8f9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461433"
---
# <a name="service-fabric-application-and-service-security"></a>Zabezpieczenia aplikacji i usług sieci szkieletowej usług
Architektura mikrousług może przynieść [wiele korzyści.](service-fabric-overview-microservices.md) Zarządzanie zabezpieczeniami mikrousług jest jednak wyzwaniem i różni się od zarządzania zabezpieczeniami tradycyjnych aplikacji monolitycznych. 

W monolitach aplikacja jest zazwyczaj uruchomiona na jednym lub kilku serwerach w sieci i łatwiej jest zidentyfikować narażonych portów i interfejsów API i adres IP. Często istnieje jeden obwód lub granicy i jednej bazy danych do ochrony. Jeśli ten system zostanie naruszony z powodu naruszenia zabezpieczeń lub ataku, jest prawdopodobne, że wszystko w systemie będzie dostępne dla osoby atakującej. Z mikrousług systemu jest bardziej złożony.  Usługi są zdecentralizowane i dystrybuowane między wieloma hostami i migrują z hosta do hosta.  Dzięki odpowiedniemu bezpieczeństwu możesz ograniczyć uprawnienia, które osoba atakująca może uzyskać, oraz ilość danych dostępnych w jednym ataku, naruszając jedną usługę.  Komunikacja nie jest wewnętrzna, ale odbywa się za pośrednictwem sieci i istnieje wiele narażonych portów i interakcji między usługami. Wiedza o tym, jakie są te interakcje z usługami i kiedy się one zdarzają, ma kluczowe znaczenie dla zabezpieczeń aplikacji.

Ten artykuł nie jest przewodnikiem po zabezpieczeniach mikrousług, istnieje wiele takich zasobów dostępnych w trybie online, ale opisano, jak różne aspekty zabezpieczeń można wykonać w sieci szkieletowej usług.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
Często jest konieczne, aby zasoby i interfejsy API udostępniane przez usługę były ograniczone do niektórych zaufanych użytkowników lub klientów. Uwierzytelnianie to proces niezawodnego ustalania tożsamości użytkownika.  Autoryzacja to proces, który udostępnia interfejsy API lub usługi niektórym uwierzytelnionym użytkownikom, ale nie innym.

### <a name="authentication"></a>Authentication
Pierwszym krokiem do podejmowania decyzji zaufania na poziomie interfejsu API jest uwierzytelnianie. Uwierzytelnianie to proces niezawodnego ustalania tożsamości użytkownika.  W scenariuszach mikrousług uwierzytelnianie jest zazwyczaj obsługiwane centralnie. Jeśli używasz bramy interfejsu API, można [odciążyć uwierzytelnianie](/azure/architecture/patterns/gateway-offloading) do bramy. Jeśli używasz tej metody, upewnij się, że poszczególne usługi nie można uzyskać bezpośrednio (bez bramy interfejsu API), chyba że dodatkowe zabezpieczenia są w miejscu do uwierzytelniania wiadomości, czy pochodzą one z bramy, czy nie.

Jeśli usługi są dostępne bezpośrednio, usługa uwierzytelniania, takich jak azure active directory lub dedykowane mikrousługi uwierzytelniania działające jako usługa tokenu zabezpieczającego (STS) może służyć do uwierzytelniania użytkowników. Decyzje dotyczące zaufania są współużytkowane przez usługi za pomocą tokenów zabezpieczających lub plików cookie. 

W przypadku ASP.NET Core podstawowym mechanizmem [uwierzytelniania użytkowników](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) jest system członkostwa ASP.NET Core Identity. ASP.NET Podstawowa tożsamość przechowuje informacje o użytkowniku (w tym informacje logowania, role i oświadczenia) w magazynie danych skonfigurowanym przez dewelopera. ASP.NET Podstawowa tożsamość obsługuje uwierzytelnianie dwuskładnikowe.  Obsługiwane są również zewnętrzne dostawcy uwierzytelniania, dzięki czemu użytkownicy mogą logować się przy użyciu istniejących procesów uwierzytelniania od dostawców takich jak Microsoft, Google, Facebook lub Twitter.

### <a name="authorization"></a>Autoryzacja
Po uwierzytelnieniu usługi muszą autoryzować dostęp użytkownika lub określić, co użytkownik jest w stanie zrobić. Ten proces umożliwia usłudze udostępnianie interfejsów API niektórym uwierzytelnionym użytkownikom, ale nie wszystkim. Autoryzacja jest ortogonalna i niezależna od uwierzytelniania, czyli procesu określania, kim jest użytkownik. Uwierzytelnianie może utworzyć jedną lub więcej tożsamości dla bieżącego użytkownika.

[ASP.NET Autoryzacja podstawowa](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) może być wykonywana na podstawie ról użytkowników lub na podstawie zasad niestandardowych, które mogą obejmować inspekcję oświadczeń lub innych heurystyki.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Ograniczanie i bezpieczne uzyskiwanie dostępu przy użyciu bramy interfejsu API
Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. [Brama interfejsu API](/azure/architecture/microservices/gateway) znajduje się między klientami i usługami i jest punktem wejścia do wszystkich usług, które aplikacja jest dostarczana. Działa jako odwrotny serwer proxy, routingu żądań od klientów do usług. Może również wykonywać różne zadania przekrojowe, takie jak uwierzytelnianie i autoryzacja, zakończenie TLS i ograniczanie szybkości. Jeśli nie wdrożysz bramy, klienci muszą wysyłać żądania bezpośrednio do usług front-end.

W sieci szkieletowej usług brama może być dowolną usługą bezstanową, taką jak [aplikacja ASP.NET Core,](service-fabric-reliable-services-communication-aspnetcore.md)lub inną usługą przeznaczoną do transferu danych przychodzących, taką jak [Traefik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)lub Azure API [Management](https://docs.microsoft.com/azure/api-management).

Usługa API Management integruje się bezpośrednio z siecią szkieletową usług, umożliwiając publikowanie interfejsów API z bogatym zestawem reguł routingu do usług szkieletowej usług zaplecza.  Można zabezpieczyć dostęp do usług wewnętrznej bazy danych, zapobiegać atakom DOS przy użyciu ograniczania przepustowości lub weryfikować klucze interfejsu API, tokeny JWT, certyfikaty i inne poświadczenia. Aby dowiedzieć się więcej, przeczytaj [omówienie usługi Sieci szkieletowej usług w usłudze Azure API Management](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Zarządzanie wpisami tajnymi aplikacji
Wpisy tajne mogą być wszelkie poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu. W tym artykule używa usługi Azure Key Vault do zarządzania kluczami i wpisami tajnymi. Jednak *przy użyciu* wpisów tajnych w aplikacji jest niezależny od platformy w chmurze, aby umożliwić aplikacje, które mają być wdrażane w klastrze hostowane w dowolnym miejscu.

Zalecanym sposobem zarządzania ustawieniami konfiguracji usługi są [pakiety konfiguracji usługi][config-package]. Pakiety konfiguracyjne są wersjonowane i aktualizowane za pośrednictwem zarządzanych uaktualnień stopniowych z sprawdzaniem kondycji i automatycznym wycofywaniem. Jest to preferowane w przypadku konfiguracji globalnej, ponieważ zmniejsza szanse na globalną awarię usługi. Zaszyfrowane wpisy tajne nie są wyjątkiem. Sieć szkieletowa usług ma wbudowane funkcje szyfrowania i odszyfrowywania wartości w pliku Settings.xml pakietu konfiguracyjnego przy użyciu szyfrowania certyfikatów.

Na poniższym diagramie przedstawiono podstawowy przepływ do zarządzania kluczem tajnym w aplikacji sieci szkieletowej usług:

![omówienie tajnego zarządzania][overview]

Istnieją cztery główne kroki w tym przepływie:

1. Uzyskaj certyfikat szyfrowania danych.
2. Zainstaluj certyfikat w klastrze.
3. Szyfruj tajne wartości podczas wdrażania aplikacji z certyfikatem i wstrzykuje je do pliku konfiguracji Settings.xml usługi.
4. Odczytuj zaszyfrowane wartości z pliku Settings.xml, odszyfrowywanie przy tym samym certyfikacie. 

[Usługa Azure Key Vault][key-vault-get-started] jest tutaj używana jako bezpieczna lokalizacja magazynu dla certyfikatów i jako sposób na zainstalowanie certyfikatów w klastrach sieci szkieletowej usług na platformie Azure. Jeśli nie wdrażasz na platformie Azure, nie trzeba używać usługi Key Vault do zarządzania wpisami tajnymi w aplikacjach sieci szkieletowej usług.

Na przykład zobacz [Zarządzanie wpisami tajnymi aplikacji](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Zabezpiecz środowisko hostingowe
Za pomocą usługi Azure Service Fabric, można zabezpieczyć aplikacje, które są uruchomione w klastrze w ramach różnych kont użytkowników. Sieci szkieletowej usług pomaga również zabezpieczyć zasoby, które są używane przez aplikacje w czasie wdrażania w ramach kont użytkowników — na przykład pliki, katalogi i certyfikaty. Dzięki temu uruchamianie aplikacji, nawet w środowisku hostowanym udostępnionego, jest bezpieczniejsze od siebie nawzajem.

Manifest aplikacji deklaruje, że podmioty zabezpieczeń (użytkownicy i grupy) wymagane uruchomić usługi i bezpieczne zasoby.  Te podmioty zabezpieczeń są odwoływane w zasadach, na przykład run-as, powiązania punktu końcowego, udostępnianie pakietu lub zasady dostępu do zabezpieczeń.  Zasady są następnie stosowane do zasobów usługi w **ServiceManifestImport** sekcji manifestu aplikacji.

Podczas deklarowania podmiotów, można również zdefiniować i utworzyć grupy użytkowników, dzięki czemu można dodać jednego lub więcej użytkowników do każdej grupy, które mają być zarządzane razem. Jest to przydatne, gdy istnieje wielu użytkowników dla różnych punktów wejścia usługi i muszą mieć pewne typowe uprawnienia, które są dostępne na poziomie grupy.

Domyślnie aplikacje sieci szkieletowej usług są uruchamiane na koncie, pod które działa proces Fabric.exe. Sieć szkieletowa usług zapewnia również możliwość uruchamiania aplikacji w ramach konta użytkownika lokalnego lub konta systemu lokalnego, który jest określony w manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [Uruchamianie usługi jako konta użytkownika lokalnego lub lokalnego konta systemowego](service-fabric-application-runas-security.md).  Można również [uruchomić skrypt startowy usługi jako konto użytkownika lokalnego lub systemowego](service-fabric-run-script-at-service-startup.md).

Podczas uruchamiania sieci szkieletowej usług w klastrze autonomicznym systemu Windows można uruchomić usługę w obszarze [Konta domeny usługi Active Directory](service-fabric-run-service-as-ad-user-or-group.md) lub grupy [zarządzanych kont usług](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Zabezpieczanie kontenerów
Sieci szkieletowej usług udostępnia mechanizm dla usług wewnątrz kontenera, aby uzyskać dostęp do certyfikatu, który jest zainstalowany w węzłach w klastrze systemu Windows lub Linux (wersja 5.7 lub nowsza). Ten certyfikat PFX może służyć do uwierzytelniania aplikacji lub usługi lub bezpiecznej komunikacji z innymi usługami. Aby uzyskać więcej informacji, zobacz [Importowanie certyfikatu do kontenera](service-fabric-securing-containers.md).

Ponadto sieci szkieletowej usług obsługuje również gMSA (grupy zarządzanych kont usług) dla kontenerów systemu Windows. Aby uzyskać więcej informacji, zobacz [Konfigurowanie gMSA dla kontenerów systemu Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Bezpieczna komunikacja serwisowa
W sieci szkieletowej usług usługa działa gdzieś w klastrze sieci szkieletowej usług, zazwyczaj rozproszone między wieloma maszynami wirtualnymi. Sieć szkieletowa usług udostępnia kilka opcji zabezpieczania komunikacji usługi.

Punkty końcowe HTTPS można włączyć w usługach sieci web [ASP.NET Core lub Java.](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service)

Można ustanowić bezpieczne połączenie między odwrotnym serwerem proxy a usługami, umożliwiając w ten sposób kanał bezpieczny od końca do końca. Łączenie się z usługami bezpiecznymi jest obsługiwane tylko wtedy, gdy serwer proxy odwrotny jest skonfigurowany do nasłuchiwania na https. Aby uzyskać informacje na temat konfigurowania odwrotnego serwera proxy, przeczytaj [pozycję Wsteczny serwer proxy w sieci szkieletowej usług Azure](service-fabric-reverseproxy.md).  [Połącz się z bezpieczną usługą](service-fabric-reverseproxy-configure-secure-communication.md) opisuje sposób ustanawiania bezpiecznego połączenia między odwrotnym serwerem proxy a usługami.

Struktura aplikacji niezawodne usługi zawiera kilka wstępnie utworzonych stosów komunikacji i narzędzi, których można użyć w celu zwiększenia bezpieczeństwa. Dowiedz się, jak poprawić bezpieczeństwo podczas korzystania z komunikacji zdalnej usługi (w [języku C#](service-fabric-reliable-services-secure-communication.md) lub [Java)](service-fabric-reliable-services-secure-communication-java.md)lub przy użyciu [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Szyfrowanie danych aplikacji w stanie spoczynku
Każdy [typ węzła](service-fabric-cluster-nodetypes.md) w klastrze sieci szkieletowej usług uruchomiony na platformie Azure jest wspierany przez [zestaw skalowania maszyny wirtualnej.](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) Za pomocą szablonu usługi Azure Resource Manager możesz dołączać dyski z danymi do zestawów skalowania tworzących klaster usługi Service Fabric.  Jeśli usługi zapisują dane na dołączonym dysku danych, można [zaszyfrować te dyski danych](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) w celu ochrony danych aplikacji.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki
* [Uruchamianie skryptu konfiguracji podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)
* [Dowiedz się więcej o zabezpieczeniach klastra](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png