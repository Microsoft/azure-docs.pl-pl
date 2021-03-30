---
title: Dowiedz się więcej o usłudze Azure Service Fabric Application Security
description: Omówienie sposobu bezpiecznego uruchamiania aplikacji mikrousług na Service Fabric. Dowiedz się, jak uruchamiać usługi i skrypty uruchomieniowe w ramach różnych kont zabezpieczeń, uwierzytelniać i autoryzować użytkowników, zarządzać kluczami tajnymi aplikacji, zabezpieczać komunikację z usługą, korzystać z bramy interfejsu API i zabezpieczać dane aplikacji w stanie spoczynku.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: f17840f31d2a4c12a1d4618bd16e81dcc2cc8a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86256581"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric zabezpieczenia aplikacji i usługi
Architektura mikrousług może przynieść [wiele korzyści](service-fabric-overview-microservices.md). Zarządzanie zabezpieczeniami mikrousług jest jednak wyzwaniem i innym niż zarządzanie tradycyjnymi aplikacjami litymi zabezpieczeń. 

W przypadku monolitu aplikacja jest zwykle uruchomiona na co najmniej jednym serwerze w sieci i łatwiej jest identyfikować uwidocznione porty i interfejsy API oraz adres IP. Istnieje często jeden obwód lub granica i jedna baza danych do ochrony. Jeśli ten system został naruszony z powodu naruszenia zabezpieczeń lub ataku, prawdopodobnie wszystko w systemie będzie dostępne dla osoby atakującej. W przypadku mikrousług system jest bardziej skomplikowany.  Usługi są zdecentralizowane i rozproszone na wielu hostach i migrowane z hosta do hosta.  Dzięki poprawnym zabezpieczeniom można ograniczyć uprawnienia, które może uzyskać osoba atakująca i ilość danych dostępnych w ramach jednego ataku przez naruszenie jednej usługi.  Komunikacja nie jest wewnętrzna, ale jest wykonywana przez sieć i istnieje wiele dostępnych portów i interakcji między usługami. Wiedząc, co to są interakcje usługi, i kiedy ich działanie ma kluczowe znaczenie dla bezpieczeństwa aplikacji.

Ten artykuł nie jest przewodnikiem w zakresie zabezpieczeń mikrousług, istnieje wiele zasobów dostępnych w trybie online, ale opisano, jak różne aspekty zabezpieczeń można osiągnąć w Service Fabric.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
Często konieczne jest, aby zasoby i interfejsy API udostępniane przez usługę były ograniczone do określonych zaufanych użytkowników lub klientów. Uwierzytelnianie to proces niezawodnego potwierdzania tożsamości użytkownika.  Autoryzacja to proces, w którym interfejsy API lub usługi są dostępne dla niektórych uwierzytelnionych użytkowników, ale nie do innych.

### <a name="authentication"></a>Authentication
Pierwszym krokiem podejmowania decyzji zaufania na poziomie interfejsu API jest uwierzytelnianie. Uwierzytelnianie to proces niezawodnego potwierdzania tożsamości użytkownika.  W scenariuszach mikrousług uwierzytelnianie jest zazwyczaj obsługiwane centralnie. W przypadku korzystania z bramy interfejsu API można [odciążyć uwierzytelnianie](/azure/architecture/patterns/gateway-offloading) do bramy. W przypadku korzystania z tej metody upewnij się, że poszczególne usługi nie są dostępne bezpośrednio (bez bramy interfejsu API), chyba że dodatkowe zabezpieczenia są używane do uwierzytelniania komunikatów niezależnie od tego, czy pochodzą z bramy, czy nie.

W przypadku uzyskiwania dostępu do usług można korzystać z usługi uwierzytelniania, takiej jak Azure Active Directory lub dedykowanej mikrousługi uwierzytelniania działającej jako usługa tokenu zabezpieczającego (STS) do uwierzytelniania użytkowników. Decyzje dotyczące zaufania są współużytkowane przez usługi z tokenami zabezpieczeń lub plikami cookie. 

W przypadku ASP.NET Core podstawowy mechanizm [uwierzytelniania użytkowników](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) to ASP.NET Core system członkostwa tożsamości. ASP.NET Core Identity przechowuje informacje o użytkowniku (w tym informacje dotyczące logowania, role i oświadczenia) w magazynie danych skonfigurowanym przez dewelopera. Tożsamość ASP.NET Core obsługuje uwierzytelnianie dwuskładnikowe.  Dostawcy uwierzytelniania zewnętrznego są również obsługiwani, aby użytkownicy mogli logować się przy użyciu istniejących procesów uwierzytelniania od dostawców takich jak Microsoft, Google, Facebook lub Twitter.

### <a name="authorization"></a>Autoryzacja
Po uwierzytelnieniu usługi muszą autoryzować dostęp użytkowników lub określić, co użytkownik może zrobić. Ten proces umożliwia usłudze udostępnianie interfejsów API niektórym uwierzytelnionym użytkownikom, ale nie wszystkim. Autoryzacja jest prostopadła i niezależna od uwierzytelniania, która jest procesem upewniania się, kto jest użytkownikiem. Uwierzytelnianie może utworzyć jedną lub więcej tożsamości dla bieżącego użytkownika.

[Autoryzację ASP.NET Core](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) można wykonać na podstawie ról użytkowników lub zasad niestandardowych, które mogą obejmować sprawdzanie oświadczeń lub innych algorytmów heurystycznych.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Ograniczanie i bezpieczny dostęp przy użyciu bramy interfejsu API
Aplikacje w chmurze zwykle potrzebują bramy frontonu, aby udostępniać pojedynczy punkt danych przychodzących dla użytkowników, urządzeń lub innych aplikacji. [Brama interfejsu API](/azure/architecture/microservices/gateway) znajduje się między klientami a usługami i jest punktem wejścia do wszystkich usług udostępnianych przez aplikację. Działa jako zwrotny serwer proxy, żądania routingu od klientów do usług. Może również wykonywać różne zadania wycinania, takie jak uwierzytelnianie i autoryzacja, zakończenie protokołu TLS i ograniczanie szybkości. Jeśli Brama nie zostanie wdrożona, klienci muszą wysyłać żądania bezpośrednio do usług frontonu.

W Service Fabric Brama może być dowolną usługą bezstanową, taką jak [aplikacja ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)lub inna usługa zaprojektowana na potrzeby ruchu przychodzącego, na przykład [Traefik](https://docs.traefik.io/), [Event Hubs](../event-hubs/index.yml), [IoT Hub](../iot-hub/index.yml)lub [Azure API Management](../api-management/index.yml).

API Management integruje się bezpośrednio z Service Fabric, co pozwala na publikowanie interfejsów API z bogatym zestawem reguł routingu do usług Service Fabric zaplecza.  Można zabezpieczyć dostęp do usług zaplecza, zapobiegać atakom na system DOS przy użyciu ograniczania lub weryfikować klucze interfejsu API, tokeny JWT, certyfikaty i inne poświadczenia. Aby dowiedzieć się więcej, Przeczytaj [Service Fabric z usługą Azure API Management — Omówienie](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Zarządzanie wpisami tajnymi aplikacji
Wpisy tajne mogą być dowolnymi informacjami poufnymi, takimi jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu. W tym artykule jest używane Azure Key Vault do zarządzania kluczami i wpisami tajnymi. Jednak *Używanie* wpisów tajnych w aplikacji to Cloud Platform-niezależny od, aby umożliwić wdrażanie aplikacji w klastrze hostowanym w dowolnym miejscu.

Zalecaną metodą zarządzania ustawieniami konfiguracji usługi jest użycie [pakietów konfiguracji usługi][config-package]. Pakiety konfiguracyjne są obsługiwane i aktualizowalne przez zarządzane uaktualnienia stopniowe z weryfikacją kondycji i autowycofywanie. Jest to preferowana konfiguracja globalna, ponieważ zmniejsza to szanse na awarię usługi globalnej. Zaszyfrowane klucze tajne nie są wyjątkiem. Service Fabric ma wbudowane funkcje szyfrowania i odszyfrowywania wartości w pakiecie konfiguracyjnym Settings.xml pliku przy użyciu szyfrowania certyfikatów.

Na poniższym diagramie przedstawiono podstawowy przepływ dla zarządzania kluczami tajnymi w aplikacji Service Fabric:

![Zarządzanie kluczami tajnymi — Omówienie][overview]

W tym przepływie istnieją cztery główne kroki:

1. Uzyskaj certyfikat szyfrowania danych.
2. Zainstaluj certyfikat w klastrze.
3. Szyfruj wartości tajne podczas wdrażania aplikacji z certyfikatem i wstrzyknąć je do pliku konfiguracji Settings.xml usługi.
4. Odczytaj zaszyfrowane wartości z Settings.xml przez odszyfrowanie przy użyciu tego samego certyfikatu szyfrowania. 

[Azure Key Vault][key-vault-get-started] jest używany w tym miejscu jako bezpieczna lokalizacja magazynu dla certyfikatów i jako sposób uzyskiwania certyfikatów zainstalowanych w klastrach Service Fabric na platformie Azure. Jeśli nie planujesz wdrożenia na platformie Azure, nie musisz używać Key Vault do zarządzania wpisami tajnymi w aplikacjach Service Fabric.

Aby zapoznać się z przykładem, zobacz Zarządzanie wpisami [tajnymi aplikacji](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Zabezpieczanie środowiska hostingu
Za pomocą usługi Azure Service Fabric można zabezpieczyć aplikacje działające w klastrze pod różnymi kontami użytkowników. Service Fabric również zabezpiecza zasoby, które są używane przez aplikacje w czasie wdrażania w ramach kont użytkowników — na przykład pliki, katalogi i certyfikaty. Dzięki temu uruchomione aplikacje, nawet w udostępnianym środowisku hostowanym, są bezpieczniejsze od siebie nawzajem.

Manifest aplikacji deklaruje podmioty zabezpieczeń (Użytkownicy i grupy) wymagane uruchomienie usług i zabezpieczonych zasobów.  Do tych podmiotów zabezpieczeń odwołują się zasady, na przykład powiązanie "Uruchom jako", "udostępnianie pakietu" lub zasady dostępu zabezpieczeń.  Zasady są następnie stosowane do zasobów usługi w sekcji **ServiceManifestImport** manifestu aplikacji.

W przypadku deklarowania podmiotów zabezpieczeń można także zdefiniować i utworzyć grupy użytkowników, aby co najmniej jeden użytkownik może zostać dodany do każdej grupy, która ma być zarządzana wspólnie. Jest to przydatne, jeśli istnieje wielu użytkowników dla różnych punktów wejścia usługi i że muszą mieć pewne typowe uprawnienia, które są dostępne na poziomie grupy.

Domyślnie Service Fabric aplikacje są uruchamiane w ramach konta, w ramach którego działa proces Fabric.exe. Service Fabric oferuje również możliwość uruchamiania aplikacji na koncie użytkownika lokalnego lub na lokalnym koncie systemowym określonym w manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [Uruchamianie usługi jako konta użytkownika lokalnego lub konta System lokalny](service-fabric-application-runas-security.md).  Możesz również [uruchomić skrypt uruchamiania usługi jako konto użytkownika lokalnego lub systemu](service-fabric-run-script-at-service-startup.md).

W przypadku uruchamiania Service Fabric w klastrze autonomicznym systemu Windows można uruchomić usługę w obszarze [Active Directory konta domeny](service-fabric-run-service-as-ad-user-or-group.md) lub [konta usług zarządzane przez grupę](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Zabezpieczanie kontenerów
Service Fabric udostępnia mechanizm dla usług wewnątrz kontenera w celu uzyskania dostępu do certyfikatu zainstalowanego na węzłach w klastrze z systemem Windows lub Linux (wersja 5,7 lub nowsza). Ten certyfikat PFX może służyć do uwierzytelniania aplikacji lub usługi lub bezpiecznej komunikacji z innymi usługami. Aby uzyskać więcej informacji, zobacz [Importowanie certyfikatu do kontenera](service-fabric-securing-containers.md).

Ponadto Service Fabric obsługuje także gMSA (konta usług zarządzane przez grupę) dla kontenerów systemu Windows. Aby uzyskać więcej informacji, zobacz [Konfigurowanie gMSA dla kontenerów systemu Windows](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Bezpieczna komunikacja z usługą
W Service Fabric usługa działa w dowolnym miejscu w klastrze Service Fabric, zazwyczaj rozmieszczona na wielu maszynach wirtualnych. Service Fabric oferuje kilka opcji zabezpieczania komunikacji z usługą.

Punkty końcowe HTTPS można włączyć w usługach sieci Web [ASP.NET Core lub Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) .

Można nawiązać bezpieczne połączenie między zwrotnym serwerem proxy a usługami, umożliwiając w ten sposób zakończenie pełnego bezpiecznego kanału. Łączenie z usługami zabezpieczonymi jest obsługiwane tylko wtedy, gdy serwer proxy odwrotny jest skonfigurowany do nasłuchiwania przy użyciu protokołu HTTPS. Aby uzyskać informacje na temat konfigurowania zwrotnego serwera proxy, Odczytaj [zwrotny serwer proxy na platformie Azure Service Fabric](service-fabric-reverseproxy.md).  [Nawiąż połączenie z bezpieczną usługą](service-fabric-reverseproxy-configure-secure-communication.md) w celu ustalenia bezpiecznego połączenia między zwrotnym serwerem proxy a usługami.

Platforma aplikacji Reliable Services zawiera kilka wstępnie utworzonych stosów i narzędzi do komunikacji, których można użyć w celu zwiększenia bezpieczeństwa. Dowiedz się, jak poprawić zabezpieczenia podczas korzystania z usług zdalnych (w [języku C#](service-fabric-reliable-services-secure-communication.md) lub [Java](service-fabric-reliable-services-secure-communication-java.md)) lub przy użyciu programu [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Szyfruj dane aplikacji w stanie spoczynku
Każdy [Typ węzła](service-fabric-cluster-nodetypes.md) w klastrze Service Fabric uruchomionym na platformie Azure jest obsługiwany przez [zestaw skalowania maszyn wirtualnych](../virtual-machine-scale-sets/overview.md). Za pomocą szablonu usługi Azure Resource Manager możesz dołączać dyski z danymi do zestawów skalowania tworzących klaster usługi Service Fabric.  Jeśli usługi zapisują dane na dołączonym dysku danych, można je [zaszyfrować](../virtual-machine-scale-sets/disk-encryption-powershell.md) , aby chronić dane aplikacji.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki
* [Uruchom skrypt instalacyjny podczas uruchamiania usługi](service-fabric-run-script-at-service-startup.md)
* [Określanie zasobów w manifeście usługi](service-fabric-service-manifest-resources.md)
* [Wdrażanie aplikacji](service-fabric-deploy-remove-applications.md)
* [Informacje o zabezpieczeniach klastra](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
