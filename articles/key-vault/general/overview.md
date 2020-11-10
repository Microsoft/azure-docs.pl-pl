---
title: Przegląd Azure Key Vault — Azure Key Vault
description: Azure Key Vault to bezpieczny Magazyn kluczy tajnych, który zapewnia zarządzanie wpisami tajnymi, kluczami i certyfikatami, które są obsługiwane przez sprzętowe moduły zabezpieczeń.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 5025c4ad81433b6eb18f5beded860625791ffce5
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445102"
---
# <a name="about-azure-key-vault"></a>Informacje o usłudze Azure Key Vault

Usługa Azure Key Vault pomaga rozwiązać następujące problemy:

- **Zarządzanie wpisami tajnymi** — usługa Azure Key Vault może służyć do bezpiecznego przechowywania i ścisłego kontrolowania dostępu do tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych.
- **Zarządzanie kluczami** — usługa Azure Key Vault może być także używana jako rozwiązanie do zarządzania kluczami. Usługa Azure Key Vault ułatwia tworzenie i kontrolowanie kluczy szyfrowania używanych do szyfrowania danych. 
- **Zarządzanie certyfikatami** — Azure Key Vault to również usługa, która umożliwia łatwe inicjowanie obsługi i wdrażanie publicznych i prywatnych certyfikatów Transport Layer Security/SSL (TLS/SSL) do użycia z platformą Azure i wewnętrznymi zasobami połączonymi.

Azure Key Vault ma dwie warstwy usług: standardowa, która szyfruje za pomocą klucza programowego i warstwy Premium, która obejmuje klucze chronione przez moduł HSM. Aby wyświetlić porównanie warstw standardowa i Premium, zobacz [stronę z cennikiem Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="why-use-azure-key-vault"></a>Jakie są zalety korzystania z usługi Azure Key Vault?

### <a name="centralize-application-secrets"></a>Scentralizowana obsługa wpisów tajnych aplikacji

Centralny magazyn wpisów tajnych aplikacji w usłudze Azure Key Vault umożliwia kontrolowanie ich dystrybucji. Usługa Key Vault znacznie ogranicza prawdopodobieństwo przypadkowego ujawnienia wpisów tajnych. Korzystając z usługi Key Vault, deweloperzy aplikacji nie muszą już przechowywać informacji zabezpieczeń w aplikacji. Ponieważ nie trzeba już przechowywać informacji o zabezpieczeniach w aplikacjach, nie ma potrzeby używania tych informacji w kodzie. Na przykład aplikacja może potrzebować połączenia z bazą danych. Zamiast przechowywać parametry połączenia w kodzie aplikacji można je przechowywać bezpiecznie w usłudze Key Vault.

Twoje aplikacje mogą bezpiecznie uzyskiwać dostęp do potrzebnych informacji za pomocą identyfikatorów URI. Te identyfikatory URI umożliwiają aplikacjom pobranie określonych wersji wpisu tajnego. Nie ma konieczności pisania niestandardowego kodu w celu ochrony poufnych informacji przechowywanych w usłudze Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Bezpieczne przechowywanie wpisów tajnych i kluczy

Udzielenie dostępu do magazynu kluczy wywołującemu użytkownikowi lub aplikacji wymaga odpowiedniego uwierzytelnienia i autoryzacji. Uwierzytelnianie ustala tożsamość elementu wywołującego, a autoryzacja określa, jakie operacje może on wykonywać.

Uwierzytelnianie jest wykonywane za pośrednictwem usługi Azure Active Directory. Autoryzację można przeprowadzić za pomocą kontroli dostępu opartej na rolach (Azure RBAC) lub Key Vault zasad dostępu. Kontrola dostępu do platformy Azure jest używana podczas rozwiązywania problemów z zarządzaniem magazynami, a przy próbie uzyskania dostępu do danych przechowywanych w magazynie jest używana.

Magazyny kluczy platformy Azure mogą być chronione przez oprogramowanie lub, w Azure Key Vault warstwy Premium, chronione sprzętowo przez sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). Klucze chronione przez oprogramowanie, wpisy tajne i certyfikaty są zabezpieczane przez platformę Azure przy użyciu standardowych algorytmów i długości kluczy.  W sytuacjach, gdy wymagana jest dodatkowa gwarancja, można importować lub generować klucze w sprzętowych modułów zabezpieczeń, które nigdy nie pozostawiają granicy modułu HSM. Azure Key Vault korzysta z oprogramowanie wspomagające nCipher sprzętowych modułów zabezpieczeń, które są zweryfikowane w trybie FIPS (Federal Information Processing Standards) 140-2 Level 2. Za pomocą narzędzi oprogramowanie wspomagające nCipher można przenieść klucz z modułu HSM do Azure Key Vault.

Usługa Azure Key Vault została zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje dane ani nie mogła ich wyodrębnić.

### <a name="monitor-access-and-use"></a>Monitorowanie dostępu i użycia

Po utworzeniu kilku magazynów usługi Key Vault może zajść potrzeba monitorowania sposobu oraz czasu dostępu do kluczy i wpisów tajnych. Działanie można monitorować przez włączenie rejestrowania dla magazynów. Usługę Azure Key Vault można skonfigurować w następujących celach:

- Archiwizowanie na koncie magazynu.
- Przesyłanie strumieniowe do centrum zdarzeń.
- Wyślij dzienniki do Azure Monitor dzienników.

Kontrolujesz dzienniki i możesz je zabezpieczyć przez ograniczenie dostępu, a także możesz usunąć dzienniki, które nie są już potrzebne.

### <a name="simplified-administration-of-application-secrets"></a>Uproszczone administrowanie wpisami tajnymi aplikacji

W przypadku przechowywania cennych danych należy wykonać kilka czynności. Informacje o zabezpieczeniach muszą być zabezpieczone, muszą być zgodne z cyklem życia i muszą być wysoce dostępne. Usługa Azure Key Vault upraszcza proces spełnienia tych wymagań przez:

- Usunięcie potrzeby znajomości sprzętowych modułów zabezpieczeń.
- Skalowanie w górę w krótkiej postaci, aby sprostać wzrostom użycia w organizacji.
- Replikowanie zawartości usługi Key Vault w regionie do regionu pomocniczego. Replikacja danych zapewnia wysoką dostępność i eliminuje konieczność wykonywania jakichkolwiek czynności przez administratora w celu wyzwolenia trybu failover.
- Udostępnianie standardowych opcji administrowania platformą Azure za pośrednictwem portalu, interfejsu wiersza polecenia platformy Azure i programu PowerShell.
- Zautomatyzowanie pewnych zadań związanych z certyfikatami kupowanymi od publicznych urzędów certyfikacji, na przykład ich rejestracji i odnawiania.

Usługa Azure Key Vault umożliwia także rozdzielenie wpisów tajnych aplikacji. Aplikacje mają dostęp tylko do magazynu, do którego zezwolono im na dostęp, i mogą zostać ograniczone do wykonywania tylko określonych operacji. Istnieje możliwość utworzenia usługi Azure Key Vault dla aplikacji i ograniczenia użycia wpisów tajnych przechowywanych w usłudze Key Vault do konkretnej aplikacji i konkretnego zespołu deweloperów.

### <a name="integrate-with-other-azure-services"></a>Integracja z innymi usługami platformy Azure

Jako bezpieczny magazyn na platformie Azure usługa Key Vault jest używana do upraszczania takich scenariuszy jak:
-  [Usługa Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)
-  Funkcja [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) i [transparent Data Encryption]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15) w programie SQL Server i Azure SQL Database
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

Samą usługę Key Vault można zintegrować z kontami magazynu, centrami zdarzeń i analizą dzienników.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md)
- [Szybki start: tworzenie usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia](../secrets/quick-create-cli.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
