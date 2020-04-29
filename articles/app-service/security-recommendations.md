---
title: Zalecenia dotyczące zabezpieczeń
description: Zaimplementuj zalecenia dotyczące zabezpieczeń, aby pomóc w spełnieniu obowiązków bezpieczeństwa określonych w naszym wspólnym modelu odpowiedzialności. Zwiększ bezpieczeństwo aplikacji.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 50e2666aa533a5111055a095c612b58bfe6f9db4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546697"
---
# <a name="security-recommendations-for-app-service"></a>Zalecenia dotyczące zabezpieczeń App Service

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń Azure App Service. Wdrożenie tych zaleceń pomoże Ci zrealizować swoje zobowiązania w zakresie zabezpieczeń zgodnie z opisem w naszym wspólnym modelu odpowiedzialności i poprawi ogólne zabezpieczenia rozwiązań aplikacji sieci Web. Aby uzyskać więcej informacji na temat tego, co firma Microsoft może spełnić obowiązki dostawcy usług, przeczytaj artykuł [zabezpieczenia infrastruktury platformy Azure](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Ogólne

| Zalecenie | Komentarze |
|-|-|----|
| Bądź na bieżąco | Używaj najnowszych wersji obsługiwanych platform, języków programowania, protokołów i struktur. |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze |
|-|----|
| Wyłącz dostęp anonimowy | Jeśli nie musisz obsługiwać żądań anonimowych, wyłącz dostęp anonimowy. Aby uzyskać więcej informacji na temat opcji uwierzytelniania Azure App Service, zobacz [uwierzytelnianie i autoryzacja w Azure App Service](overview-authentication-authorization.md).|
| Wymagaj uwierzytelniania | Jeśli to możliwe, użyj modułu uwierzytelniania App Service zamiast pisania kodu do obsługi uwierzytelniania i autoryzacji. Zobacz [uwierzytelnianie i autoryzacja w Azure App Service](overview-authentication-authorization.md). |
| Ochrona zasobów zaplecza przy użyciu dostępu uwierzytelnionego | Możesz użyć tożsamości użytkownika lub użyć tożsamości aplikacji w celu uwierzytelnienia w zasobie zaplecza. Gdy zdecydujesz się użyć tożsamości aplikacji, użyj [tożsamości zarządzanej](overview-managed-identity.md).
| Wymagaj uwierzytelniania certyfikatu klienta | Uwierzytelnianie za pomocą certyfikatu klienta zwiększa bezpieczeństwo przez umożliwienie połączeń z klientami, którzy mogą uwierzytelniać się przy użyciu dostarczanych certyfikatów. |

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze |
|-|-|
| Przekierowywanie protokołu HTTP do protokołu HTTPs | Domyślnie klienci mogą łączyć się z aplikacjami sieci Web przy użyciu protokołu HTTP lub HTTPS. Zalecamy przekierowanie protokołu HTTP do protokołu HTTPs, ponieważ protokół HTTPS używa protokołu SSL/TLS w celu zapewnienia bezpiecznego połączenia, które jest szyfrowane i uwierzytelniane. |
| Szyfruj komunikację z zasobami platformy Azure | Gdy aplikacja nawiązuje połączenie z zasobami platformy Azure, takimi jak [SQL Database](https://azure.microsoft.com/services/sql-database/) lub [Azure Storage](/azure/storage/), połączenie pozostaje na platformie Azure. Ponieważ połączenie odbywa się za pomocą udostępnionej sieci na platformie Azure, zawsze należy zaszyfrować całą komunikację. |
| Wymagaj najnowszej wersji protokołu TLS | Ponieważ 2018 nowe aplikacje Azure App Service używają protokołu TLS 1,2. Nowsze wersje protokołu TLS obejmują ulepszenia zabezpieczeń w porównaniu ze starszymi wersjami protokołów. |
| Użyj FTPS | App Service obsługuje zarówno protokół FTP, jak i FTPS do wdrażania plików. Jeśli to możliwe, użyj FTPS zamiast FTP. Jeśli jeden lub oba te protokoły nie są używane, należy [je wyłączyć](deploy-ftp.md#enforce-ftps). |
| Zabezpieczanie danych aplikacji | Nie przechowuj wpisów tajnych aplikacji, takich jak poświadczenia bazy danych, tokeny interfejsu API lub klucze prywatne w plikach kodu lub konfiguracji. Powszechnie akceptowanym podejściem jest uzyskanie dostępu do nich jako [zmiennych środowiskowych](https://wikipedia.org/wiki/Environment_variable) przy użyciu standardowego wzorca w wybranym języku. W Azure App Service można definiować zmienne środowiskowe za poorednictwem [ustawień aplikacji](web-sites-configure.md) i [parametrów połączenia](web-sites-configure.md). Ustawienia aplikacji i parametry połączenia są przechowywane na platformie Azure. Ustawienia aplikacji są odszyfrowywane dopiero przed wprowadzeniem ich do pamięci procesu aplikacji podczas uruchamiania aplikacji. Klucze szyfrowania są regularnie obracane. Alternatywnie możesz zintegrować swoją aplikację Azure App Service z [Azure Key Vault](/azure/key-vault/) do zarządzania zaawansowanymi kluczami tajnymi. Dzięki [dostępowi do Key Vault za pomocą tożsamości zarządzanej](../key-vault/tutorial-web-application-keyvault.md)aplikacja App Service może bezpiecznie uzyskać dostęp do potrzebnych wpisów tajnych. |

## <a name="networking"></a>Networking

| Zalecenie | Komentarze |
|-|-|
| Użyj ograniczeń statycznych adresów IP | Azure App Service w systemie Windows umożliwia zdefiniowanie listy adresów IP, które mogą uzyskiwać dostęp do aplikacji. Lista dozwolonych może obejmować pojedyncze adresy IP lub zakres adresów IP definiowanych przez maskę podsieci. Aby uzyskać więcej informacji, zobacz [Azure App Service ograniczeń statycznych adresów IP](app-service-ip-restrictions.md).  |
| Użyj warstwy cenowej izolowanej | Z wyjątkiem warstwy cenowej izolowanej wszystkie warstwy uruchamiają aplikacje w udostępnionej infrastrukturze sieci w Azure App Service. Warstwa izolowana zapewnia pełną izolację sieci, uruchamiając aplikacje w dedykowanym [środowisku App Service](environment/intro.md). Środowisko App Service działa we własnym wystąpieniu [platformy Azure Virtual Network](/azure/virtual-network/).|
| Użyj bezpiecznych połączeń podczas uzyskiwania dostępu do zasobów lokalnych | Za pomocą [połączeń hybrydowych](app-service-hybrid-connections.md), [integracji Virtual Network](web-sites-integrate-with-vnet.md)lub [środowiska App Service](environment/intro.md) można nawiązać połączenie z zasobami lokalnymi. |
| Ograniczanie ekspozycji na przychodzący ruch sieciowy | Sieciowe grupy zabezpieczeń pozwalają ograniczyć dostęp do sieci i kontrolować liczbę narażonych punktów końcowych. Aby uzyskać więcej informacji, zobacz [jak kontrolować ruch przychodzący do App Service Environment](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitorowanie

| Zalecenie | Komentarze |
|-|-|
|Użyj Azure Security Center warstwy Standardowa | [Azure Security Center](../security-center/security-center-app-services.md) jest natywnie zintegrowany z Azure App Service. Można uruchamiać oceny i zapewniać zalecenia dotyczące zabezpieczeń. |

## <a name="next-steps"></a>Następne kroki

Skontaktuj się z dostawcą aplikacji, aby sprawdzić, czy istnieją dodatkowe wymagania dotyczące zabezpieczeń. Aby uzyskać więcej informacji na temat tworzenia bezpiecznych aplikacji, zobacz temat [bezpieczna programowanie dokumentacji](../security/fundamentals/abstract-develop-secure-apps.md).
