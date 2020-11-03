---
title: Konfiguracja aplikacji platformy Azure — często zadawane pytania
description: Przeczytaj odpowiedzi na często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure, takich jak różnice między Azure Key Vault.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 5d74b17bdd9c264a983bfdd2e374001dd4a0e2c0
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242113"
---
# <a name="azure-app-configuration-faq"></a>Konfiguracja aplikacji platformy Azure — często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Czym różni się konfiguracja aplikacji od Azure Key Vault?

Konfiguracja aplikacji ułatwia deweloperom zarządzanie ustawieniami aplikacji i dostępnością funkcji kontroli. Celem jest uproszczenie wielu zadań związanych z pracą ze złożonymi danymi konfiguracyjnymi.

Konfiguracja aplikacji obsługuje:

- Hierarchiczne przestrzenie nazw
- Etykietowania
- Obszerne zapytania
- Pobieranie wsadowe
- Wyspecjalizowane operacje zarządzania
- Interfejs użytkownika zarządzania funkcjami

Konfiguracja aplikacji uzupełnia Key Vault, a dwa powinny być używane obok siebie w większości wdrożeń aplikacji.

## <a name="should-i-store-secrets-in-app-configuration"></a>Czy należy przechowywać wpisy tajne w konfiguracji aplikacji?

Mimo że konfiguracja aplikacji zapewnia zabezpieczenia z ograniczeniami, Key Vault jest nadal najlepszym miejscem do przechowywania wpisów tajnych aplikacji. Key Vault zapewnia szyfrowanie na poziomie sprzętu, szczegółowe zasady dostępu oraz operacje zarządzania, takie jak rotacja certyfikatów.

Można utworzyć wartości konfiguracji aplikacji, które odwołują się do wpisów tajnych przechowywanych w Key Vault. Aby uzyskać więcej informacji, zobacz [Korzystanie z odwołań Key Vault w aplikacji ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Czy konfiguracja aplikacji szyfruje moje dane?

Tak. Konfiguracja aplikacji szyfruje wszystkie przechowywane wartości klucza i szyfruje komunikację sieciową. Nazwy kluczy i etykiety są używane jako indeksy do pobierania danych konfiguracji i nie są szyfrowane.

## <a name="where-does-data-stored-in-app-configuration-reside"></a>Gdzie znajdują się dane przechowywane w konfiguracji aplikacji? 

Dane klienta przechowywane w konfiguracji aplikacji znajdują się w regionie, w którym utworzono magazyn konfiguracji aplikacji klienta. Konfiguracja aplikacji może replikować dane do [sparowanych regionów](../best-practices-availability-paired-regions.md) pod kątem odporności danych, ale nie replikuje ani nie przenosi danych klienta poza ich geograficzną, zgodnie z definicją [zamieszkania danych na platformie Azure](https://azure.microsoft.com/global-infrastructure/data-residency/). Klienci i użytkownicy końcowi mogą w całości przenosić, kopiować lub uzyskiwać dostęp do danych klienta z lokalizacji.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Czym różni się konfiguracja aplikacji od ustawień Azure App Service?

Azure App Service umożliwia zdefiniowanie ustawień aplikacji dla każdego wystąpienia App Service. Te ustawienia są przesyłane jako zmienne środowiskowe do kodu aplikacji. Jeśli chcesz, możesz skojarzyć ustawienie z określonym miejscem wdrożenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień aplikacji](../app-service/configure-common.md#configure-app-settings).

W przeciwieństwie do konfiguracji aplikacji platformy Azure można definiować ustawienia, które mogą być współużytkowane przez wiele aplikacji. Obejmuje to aplikacje działające w App Service, a także inne platformy. Kod aplikacji uzyskuje dostęp do tych ustawień za pośrednictwem dostawców konfiguracji dla platform .NET i Java, za pomocą zestawu Azure SDK lub bezpośrednio za pośrednictwem interfejsów API REST.

Można również importować i eksportować ustawienia między App Service i konfiguracji aplikacji. Ta funkcja umożliwia szybkie skonfigurowanie nowego magazynu konfiguracji aplikacji na podstawie istniejących ustawień App Service. Możesz również udostępnić konfigurację z istniejącą aplikacją, która opiera się na App Service ustawieniach.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Czy istnieją jakieś ograniczenia rozmiaru kluczy i wartości przechowywanych w konfiguracji aplikacji?

Dla pojedynczego elementu klucz-wartość istnieje limit 10 KB.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak należy przechowywać konfiguracje dla wielu środowisk (testów, przemieszczania, produkcji itp.)?

Użytkownik kontroluje, kto może uzyskać dostęp do konfiguracji aplikacji na poziomie magazynu. Użyj oddzielnego magazynu dla każdego środowiska, które wymaga innych uprawnień. Takie podejście zapewnia najlepszą izolację zabezpieczeń.

Jeśli nie potrzebujesz izolacji zabezpieczeń między środowiskami, możesz użyć etykiet, aby rozróżnić wartości konfiguracyjne. [Aby włączyć różne konfiguracje dla różnych środowisk, należy użyć etykiet](./howto-labels-aspnet-core.md) .

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Jakie są zalecane metody korzystania z konfiguracji aplikacji?

Zobacz [najlepsze rozwiązania](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Ile jest koszt konfiguracji aplikacji?

Istnieją dwie warstwy cenowe:

- Warstwa Bezpłatna
- Warstwa standardowa.

Jeśli sklep został utworzony przed wprowadzeniem warstwy Standardowa, jest on automatycznie przenoszony do warstwy Bezpłatna po ogólnej dostępności. Możesz wybrać opcję uaktualnienia do warstwy Standardowa lub pozostać w warstwie Bezpłatna.

Nie można obniżyć poziomu sklepu z warstwy Standardowa do warstwy Bezpłatna. W warstwie Bezpłatna można utworzyć nowy magazyn, a następnie zaimportować dane konfiguracji do tego magazynu.

## <a name="which-app-configuration-tier-should-i-use"></a>Której warstwy konfiguracji aplikacji należy użyć?

Obie warstwy konfiguracji aplikacji oferują podstawowe funkcje, w tym ustawienia konfiguracji, flagi funkcji, odwołania Key Vault, podstawowe operacje zarządzania, metryki i dzienniki.

Poniżej przedstawiono zagadnienia dotyczące wybierania warstwy.

- **Zasoby na subskrypcję** : zasób składa się z pojedynczego magazynu konfiguracji. Każda subskrypcja jest ograniczona do jednego magazynu konfiguracji w warstwie Bezpłatna. Subskrypcje mogą mieć nieograniczoną liczbę magazynów konfiguracji w warstwie Standardowa.
- **Magazyn na zasób** : w warstwie Bezpłatna każdy magazyn konfiguracji jest ograniczony do 10 MB pamięci masowej. W warstwie Standardowa każdy magazyn konfiguracji może korzystać z maksymalnie 1 GB miejsca w magazynie.
- **Historia poprawek** : Konfiguracja aplikacji przechowuje historię wszystkich zmian wprowadzonych do kluczy. W warstwie Bezpłatna ta historia jest przechowywana przez siedem dni. W warstwie Standardowa ta historia jest przechowywana przez 30 dni.
- **Przydział żądań** : magazyny w warstwie Bezpłatna są ograniczone do 1 000 żądań dziennie. Gdy sklep osiągnie 1 000 żądań, zwraca kod stanu HTTP 429 dla wszystkich żądań do północy czasu UTC.

    Magazyny w warstwie Standardowa są ograniczone do 20 000 żądań na godzinę. Po wyczerpaniu przydziału kod stanu HTTP 429 jest zwracany dla wszystkich żądań do końca godziny.

- **Umowa dotycząca poziomu usług** : warstwa standardowa ma umowę SLA na 99,9% czasu dostępności. Warstwa Bezpłatna nie ma umowy SLA.
- **Funkcje zabezpieczeń** : w obu warstwach są dostępne podstawowe funkcje zabezpieczeń, w tym szyfrowanie za pomocą kluczy zarządzanych przez firmę Microsoft, uwierzytelnianie za pośrednictwem algorytmu HMAC lub Azure Active Directory, obsługa usługi Azure RBAC, tożsamość zarządzana i Tagi usług. Warstwa standardowa oferuje bardziej zaawansowane funkcje zabezpieczeń, w tym obsługę linków prywatnych i szyfrowanie za pomocą kluczy zarządzanych przez klienta.
- **Koszt** : magazyny w warstwie Standardowa mają dzienną opłatą za użycie. Pierwsze 200 000 żądań dziennie są uwzględniane w dziennej opłacie. Jest również opłata za nadwyżkowe żądania w porównaniu do codziennej alokacji. Nie ma kosztu korzystania z magazynu warstwy Bezpłatna.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Czy mogę uaktualnić sklep z warstwy Bezpłatna do warstwy Standardowa? Czy mogę obniżyć poziom sklepu z warstwy Standardowa do warstwy Bezpłatna?

W dowolnym momencie możesz przeprowadzić uaktualnienie z warstwy Bezpłatna do warstwy Standardowa.

Nie można obniżyć poziomu sklepu z warstwy Standardowa do warstwy Bezpłatna. W warstwie Bezpłatna można utworzyć nowy magazyn, a następnie [zaimportować dane konfiguracji do tego magazynu](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Czy istnieją jakieś ograniczenia dotyczące liczby żądań dotyczących konfiguracji aplikacji?

Magazyny konfiguracji w warstwie Bezpłatna są ograniczone do 1 000 żądań dziennie. W przypadku magazynów konfiguracji w warstwie Standardowa może wystąpić tymczasowe ograniczenie przepustowości, gdy częstotliwość żądań przekracza 20 000 żądań na godzinę.

Po osiągnięciu limitu magazyn zwróci kod stanu HTTP 429 dla wszystkich żądań zrealizowanych do czasu wygaśnięcia okresu. `retry-after-ms`Nagłówek w odpowiedzi zawiera sugerowany czas oczekiwania (w milisekundach) przed podjęciem próby wykonania żądania.

Jeśli aplikacja regularnie korzysta z kodu stanu HTTP 429 odpowiedzi, rozważ przeprojektowanie go w celu zmniejszenia liczby wykonanych żądań. Aby uzyskać więcej informacji, zobacz [ograniczanie żądań do konfiguracji aplikacji](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Moja aplikacja otrzymuje kod stanu HTTP 429 odpowiedzi. Dlaczego?

W następujących okolicznościach zostanie wyświetlony kod stanu HTTP 429 odpowiedź:

* Przekraczanie dziennego limitu żądań dla sklepu w warstwie Bezpłatna.
* Tymczasowe ograniczenie wydajności ze względu na dużą stawkę żądania dla sklepu w warstwie Standardowa.
* Nadmierne użycie przepustowości.
* Podjęto próbę utworzenia lub zmodyfikowania klucza w przypadku przekroczenia oferty magazynu.

Sprawdź treść odpowiedzi 429 z określonego powodu, dlaczego żądanie nie powiodło się.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Jak mogę otrzymywać anonse dotyczące nowych wersji i innych informacji związanych z konfiguracją aplikacji?

Subskrybowanie [repozytorium anonsów usługi GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak zgłosić problem lub przekazać sugestię?

Możesz skontaktować się bezpośrednio z usługą [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Następne kroki

* [Usługa Azure App Configuration — informacje](./overview.md)
