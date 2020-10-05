---
title: Integrowanie usługi Azure Key Vault z usługą Azure Policy
description: Dowiedz się, jak zintegrować Azure Key Vault z Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 62ea64f6f0ccf3a9f4ceabc64a97e1e5570b92b4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88586073"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrowanie usługi Azure Key Vault z usługą Azure Policy

[Azure Policy](../../governance/policy/index.yml) to narzędzie ładu, które zapewnia użytkownikom możliwość inspekcji środowiska platformy Azure i zarządzania nim w odpowiedniej skali. Azure Policy zapewnia możliwość umieszczenia guardrails w zasobach platformy Azure, aby upewnić się, że są one zgodne z przypisanymi regułami zasad. Dzięki temu użytkownicy mogą wykonywać inspekcje, wymuszać w czasie rzeczywistym oraz korygowanie środowiska platformy Azure. Wyniki inspekcji wykonywane przez zasady będą dostępne dla użytkowników na pulpicie nawigacyjnym zgodności, gdzie będą mogli zobaczyć, które zasoby i składniki są zgodne, a które nie.  Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Policy](../../governance/policy/overview.md).

Przykładowe scenariusze użycia:

- Chcesz zwiększyć bezpieczeństwo stan firmy, implementując wymagania dotyczące minimalnych rozmiarów kluczy i maksymalnych okresów ważności certyfikatów w magazynach kluczy firmy, ale nie wiesz, które zespoły będą zgodne, a które nie. 
- Obecnie nie masz rozwiązania do przeprowadzania inspekcji w całej organizacji lub przeprowadzasz ręczne inspekcje środowiska przez zaproszenie poszczególnych zespołów w organizacji, aby zgłaszać ich zgodność. Szukasz sposobu automatyzowania tego zadania, wykonywania inspekcji w czasie rzeczywistym i zagwarantowania dokładności inspekcji.
- Chcesz wymusić firmowe zasady zabezpieczeń i uniemożliwić użytkownikom tworzenie certyfikatów z podpisem własnym, ale nie masz zautomatyzowanego sposobu blokowania ich tworzenia. 
- Chcesz osłabić niektóre wymagania dla zespołów testowych, ale chcesz zachować ścisłe kontrole w środowisku produkcyjnym. Potrzebny jest prosty zautomatyzowany sposób oddzielenia wymuszania zasobów. 
- Chcesz mieć pewność, że w przypadku problemu z witryną sieciową można wymuszać wycofanie nowych zasad. Aby wyłączyć wymuszanie zasad, potrzebujesz jednego kliknięcia. 
- Korzystasz z rozwiązania innej firmy w celu przeprowadzania inspekcji środowiska i chcesz użyć wewnętrznej oferty Microsoft. 

## <a name="types-of-policy-effects-and-guidance"></a>Typy efektów i wskazówek dotyczących zasad

**Inspekcja**: Jeśli efekt zasad jest ustawiony na inspekcje, zasady nie spowodują żadnych istotnych zmian w środowisku. Spowoduje to wyświetlenie alertu tylko dla składników, takich jak certyfikaty, które nie są zgodne z definicjami zasad w określonym zakresie, poprzez oznaczenie tych składników jako niezgodnych na pulpicie nawigacyjnym zgodności zasad. Inspekcja jest domyślna, jeśli nie wybrano żadnego efektu zasad. 

**Odmów**: Jeśli efekt zasad jest ustawiony na Odmów, zasady będą blokować tworzenie nowych składników, takich jak certyfikaty, a także zablokować nowe wersje istniejących składników, które nie są zgodne z definicją zasad. Nie dotyczy istniejących niezgodnych zasobów w magazynie kluczy. Funkcje "Audit" będą nadal działać.

## <a name="available-built-in-policy-definitions"></a>Dostępne "wbudowane" definicje zasad

Key Vault utworzył zestaw zasad, które można przypisać do typowych scenariuszy zarządzania certyfikatami. Te zasady są wbudowane, co oznacza, że nie wymagają pisania niestandardowego kodu JSON, aby je włączyć, i są dostępne w Azure Portal do przypisania. Można nadal dostosować niektóre parametry, aby odpowiadały potrzebom organizacji. 

Poniżej znajdują się osiem zasad wersji zapoznawczej.

### <a name="manage-certificate-validity-period-preview"></a>Zarządzanie okresem ważności certyfikatu (wersja zapoznawcza)

Te zasady umożliwiają zarządzanie maksymalnym okresem ważności certyfikatów przechowywanych w magazynie kluczy. Jest to dobre rozwiązanie w zakresie zabezpieczeń, które umożliwia ograniczenie maksymalnego okresu ważności certyfikatów. Jeśli klucz prywatny certyfikatu miał zostać naruszony bez wykrycia, użycie certyfikatów krótkoterminowych minimalizuje czas ciągłego uszkodzenia i zmniejsza wartość certyfikatu do osoby atakującej. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Zarządzaj dozwolonymi typami kluczy certyfikatów (wersja zapoznawcza)
Te zasady umożliwiają ograniczenie typu certyfikatów, które mogą znajdować się w magazynie kluczy. Za pomocą tych zasad można upewnić się, że klucze prywatne certyfikatu to RSA, ECC lub są chronione przez moduł HSM. Można wybrać spośród poniższych list, które typy certyfikatów są dozwolone.
- RSA
- RSA — HSM
- ECC 
- ECC — HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Zarządzanie wyzwalaczami akcji okresu istnienia certyfikatu (wersja zapoznawcza)

Te zasady umożliwiają zarządzanie akcją okresu istnienia określoną dla certyfikatów, które znajdują się w ciągu określonej liczby dni ich wygaśnięcia lub osiągnęły określony odsetek ich użytkowania. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Zarządzanie certyfikatami wystawionymi przez zintegrowany urząd certyfikacji (wersja zapoznawcza)

Jeśli używasz Key Vault zintegrowanego urzędu certyfikacji (DigiCert lub GlobalSign) i chcesz, aby użytkownicy używali jednego lub jednego z tych dostawców, możesz użyć tych zasad do inspekcji lub wymuszenia wyboru. Tych zasad można również użyć do inspekcji lub odrzucania tworzenia certyfikatów z podpisem własnym w magazynie kluczy. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Zarządzanie certyfikatami wystawionymi przez zintegrowany urząd certyfikacji (wersja zapoznawcza)

Jeśli używasz wewnętrznego urzędu certyfikacji lub urzędu certyfikacji, który nie jest zintegrowany z magazynem kluczy i chcesz, aby użytkownicy korzystali z urzędu certyfikacji z udostępnionej listy, możesz użyć tych zasad, aby utworzyć listę dozwolonych urzędów certyfikacji według nazwy wystawcy. Tych zasad można również użyć do inspekcji lub odrzucania tworzenia certyfikatów z podpisem własnym w magazynie kluczy. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Zarządzaj dozwolonymi nazwami krzywych dla certyfikatów kryptograficznych krzywej eliptycznej (wersja zapoznawcza)
Jeśli używasz kryptografii krzywej eliptyczna lub certyfikatów ECC, możesz dostosować listę dozwolonych nazw krzywych z poniższej listy. Opcja domyślna zezwala na wszystkie następujące nazwy krzywych. 
- P-256
- P-256 K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Zarządzaj minimalnym rozmiarem klucza dla certyfikatów RSA (wersja zapoznawcza)
W przypadku korzystania z certyfikatów RSA można wybrać minimalny rozmiar klucza, który musi zawierać certyfikaty. Można wybrać jedną opcję z poniższej listy. 
- 2048 bit
- 3072 bit
- 4096 bit

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Zarządzanie certyfikatami w ciągu określonej liczby dni wygaśnięcia (wersja zapoznawcza)
Jeśli certyfikat, który nie jest odpowiednio monitorowany, nie zostanie obrócony przed jego wygaśnięciem, może wystąpić awaria usługi. Zasady te mają na celu zapewnienie, że certyfikaty przechowywane w magazynie kluczy są monitorowane. Zalecane jest stosowanie tych zasad wielokrotnie z różnymi progami wygaśnięcia, na przykład 180, 90, 60 i 30-dniowych progów. Te zasady mogą służyć do monitorowania i Klasyfikacja wygaśnięcia certyfikatów w organizacji. 

## <a name="example-scenario"></a>Przykładowy scenariusz

Zarządzasz magazynem kluczy używanym przez wiele zespołów, które zawierają certyfikaty 100 i chcesz upewnić się, że żaden z certyfikatów w magazynie kluczy nie jest ważny przez dłużej niż 2 lata.

1. Należy przypisać zasady [zarządzania okresem ważności certyfikatu](#manage-certificate-validity-period-preview) , określić, że maksymalny okres ważności certyfikatu wynosi 24 miesiące, i ustawić wpływ zasad na "inspekcję". 
1. [Raport zgodności można wyświetlić na Azure Portal](#view-compliance-results)i sprawdzić, czy 20 certyfikatów jest niezgodnych i prawidłowych dla > 2 lat, a pozostałe certyfikaty są zgodne. 
1. Skontaktuj się z właścicielami tych certyfikatów i przekaż nowe wymagania dotyczące zabezpieczeń, których certyfikaty nie mogą być ważne przez dłużej niż 2 lata. Niektóre zespoły odpowiadają i 15 certyfikatów zostały odnowione z maksymalnym okresem ważności wynoszącym 2 lata lub mniej. Inne zespoły nie odpowiadają, a nadal masz 5 niezgodnych certyfikatów w magazynie kluczy.
1. Zmienisz efekt przypisanych zasad do "Odmów". 5 niezgodnych certyfikatów nie jest odwołanych i nadal działają. Nie można jednak przedłużyć okresu ważności, który jest dłuższy niż 2 lata. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Włączanie zasad Key Vault i zarządzanie nimi za pomocą Azure Portal

### <a name="select-a-policy-definition"></a>Wybierz definicję zasad

1. Zaloguj się w witrynie Azure Portal. 
1. Wyszukaj ciąg "Policy" na pasku wyszukiwania i wybierz pozycję **zasady**.

    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img1.png)

1. W oknie zasady wybierz pozycję **definicje**.

    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img2.png)

1. W filtr kategorii Usuń zaznaczenie opcji **Zaznacz wszystko** i wybierz **Key Vault**. 

    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img3.png)

1. Teraz powinno być możliwe wyświetlenie wszystkich zasad dostępnych dla publicznej wersji zapoznawczej w celu Azure Key Vault. Upewnij się, że znasz i rozumiesz powyższe wskazówki dotyczące zasad i wybierz zasady, które chcesz przypisać do zakresu.  

    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Przypisywanie zasad do zakresu 

1. Wybierz zasady, które chcesz zastosować. w tym przykładzie są wyświetlane zasady **Zarządzaj okresem ważności certyfikatu** . Kliknij przycisk Przypisz w lewym górnym rogu.

    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img5.png)
  
1. Wybierz subskrypcję, w której chcesz zastosować zasady. Możesz ograniczyć zakres tylko do jednej grupy zasobów w ramach subskrypcji. Jeśli chcesz zastosować zasady do całej subskrypcji i wykluczyć niektóre grupy zasobów, możesz również skonfigurować listę wykluczeń. Ustaw opcję selektora wymuszania zasad na **włączone** , jeśli chcesz, aby efekt zasad (inspekcja lub odmowa) został wyrzucony lub **wyłączony** w celu wyłączenia efektu (inspekcja lub odmowa). 

    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img6.png)

1. Kliknij kartę parametry w górnej części ekranu, aby określić maksymalny okres ważności w miesiącach. Wybierz pozycję **Inspekcja** lub **Odmów** dla efektu zasad zgodnie ze wskazówkami podanych w powyższych sekcjach. Następnie wybierz przycisk Recenzja + tworzenie. 

    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Wyświetl wyniki zgodności

1. Wróć do bloku zasad i wybierz kartę zgodność. Kliknij przypisanie zasad, dla którego chcesz wyświetlić wyniki sprawdzania zgodności.

    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img8.png)

1. Na tej stronie można filtrować wyniki według zgodnych lub niezgodnych magazynów. W tym miejscu można wyświetlić listę niezgodnych magazynów kluczy w zakresie przypisania zasad. Magazyn jest uznawany za niezgodny, jeśli którykolwiek ze składników (certyfikatów) w magazynie nie jest zgodny. Możesz wybrać pojedynczy magazyn, aby wyświetlić poszczególne niezgodne składniki (certyfikaty). 


    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img9.png)

1. Wyświetlanie nazw składników w magazynie, które nie są zgodne


    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img10.png)

1. Jeśli chcesz sprawdzić, czy użytkownicy nie mają możliwości tworzenia zasobów w ramach magazynu kluczy, możesz kliknąć kartę **zdarzenia składników (wersja zapoznawcza)** , aby wyświetlić podsumowanie niedozwolonych operacji certyfikatu z obiektem żądającym i sygnaturami czasowymi żądań. 


    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img11.png)

## <a name="feature-limitations"></a>Ograniczenia funkcji

Przypisanie zasad z efektem "odmowa" może potrwać do 30 minut (średni przypadek) i 1 godzina (najgorszy przypadek), aby rozpocząć odmowa tworzenia niezgodnych zasobów. Ocena zasad istniejących składników w magazynie może potrwać do 1 godziny (średnia wielkość liter) i 2 godziny (najgorszy przypadek), zanim wyniki zgodności będą widoczne w interfejsie użytkownika portalu. Jeśli wyniki zgodności są wyświetlane jako "nie uruchomiono", może to być spowodowane następującymi przyczynami:
- Wycena nie została jeszcze ukończona. Opóźnienie wstępnej oceny może potrwać do 2 godzin w scenariuszu najgorszego przypadku. 
- W zakresie przypisania zasad nie ma magazynów kluczy.
- Brak magazynów kluczy z certyfikatami w zakresie przypisania zasad. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [usługi Azure Policy](../../governance/policy/overview.md)
- Zobacz przykłady Key Vault: [Key Vault wbudowane definicje zasad](../../governance/policy/samples/built-in-policies.md#key-vault)