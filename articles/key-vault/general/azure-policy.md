---
title: Integrowanie usługi Azure Key Vault z usługą Azure Policy
description: Dowiedz się, jak zintegrować Azure Key Vault z Azure Policy
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 10/15/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 6ac4d0e0744bfc82a686671234e013b2dd717146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92927757"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Integrowanie usługi Azure Key Vault z usługą Azure Policy

[Azure Policy](../../governance/policy/index.yml) to narzędzie ładu, które zapewnia użytkownikom możliwość inspekcji środowiska platformy Azure i zarządzania nim w odpowiedniej skali. Azure Policy zapewnia możliwość umieszczenia guardrails w zasobach platformy Azure, aby upewnić się, że są one zgodne z przypisanymi regułami zasad. Dzięki temu użytkownicy mogą wykonywać inspekcje, wymuszać w czasie rzeczywistym oraz korygowanie środowiska platformy Azure. Wyniki inspekcji wykonywane przez zasady będą dostępne dla użytkowników na pulpicie nawigacyjnym zgodności, gdzie będą mogli zobaczyć przechodzenie do szczegółów zasobów i składników, które nie są zgodne.  Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Policy](../../governance/policy/overview.md).

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

Key Vault utworzył zestaw zasad, które mogą służyć do zarządzania kluczami, certyfikatami i obiektami tajnymi. Te zasady są wbudowane, co oznacza, że nie wymagają pisania niestandardowego kodu JSON, aby je włączyć, i są dostępne w Azure Portal do przypisania. Można nadal dostosować niektóre parametry, aby odpowiadały potrzebom organizacji.

# <a name="certificate-policies"></a>[Zasady certyfikatów](#tab/certificates)

### <a name="certificates-should-have-the-specified-maximum-validity-period-preview"></a>Certyfikaty powinny mieć określony maksymalny okres ważności (wersja zapoznawcza)

Te zasady umożliwiają zarządzanie maksymalnym okresem ważności certyfikatów przechowywanych w magazynie kluczy. Jest to dobre rozwiązanie w zakresie zabezpieczeń, które umożliwia ograniczenie maksymalnego okresu ważności certyfikatów. Jeśli klucz prywatny certyfikatu miał zostać naruszony bez wykrycia, użycie certyfikatów krótkoterminowych minimalizuje czas ciągłego uszkodzenia i zmniejsza wartość certyfikatu do osoby atakującej.

### <a name="certificates-should-use-allowed-key-types-preview"></a>Certyfikaty powinny używać dozwolonych typów kluczy (wersja zapoznawcza)

Te zasady umożliwiają ograniczenie typu certyfikatów, które mogą znajdować się w magazynie kluczy. Za pomocą tych zasad można upewnić się, że klucze prywatne certyfikatu to RSA, ECC lub są chronione przez moduł HSM. Można wybrać spośród poniższych list, które typy certyfikatów są dozwolone.

- RSA
- RSA — HSM
- ECC
- ECC — HSM

### <a name="certificates-should-have-the-specified-lifetime-action-triggers-preview"></a>Certyfikaty powinny mieć określone wyzwalacze akcji okresu istnienia (wersja zapoznawcza)

Te zasady umożliwiają zarządzanie akcją okresu istnienia określoną dla certyfikatów, które znajdują się w ciągu określonej liczby dni ich wygaśnięcia lub osiągnęły określony odsetek ich użytkowania.

### <a name="certificates-should-be-issued-by-the-specified-integrated-certificate-authority-preview"></a>Certyfikaty powinny być wystawiane przez określony zintegrowany urząd certyfikacji (wersja zapoznawcza)

Jeśli używasz Key Vault zintegrowanego urzędu certyfikacji (DigiCert lub GlobalSign) i chcesz, aby użytkownicy używali jednego lub jednego z tych dostawców, możesz użyć tych zasad do inspekcji lub wymuszenia wyboru. Tych zasad można również użyć do inspekcji lub odrzucania tworzenia certyfikatów z podpisem własnym w magazynie kluczy.

### <a name="certificates-should-be-issued-by-the-specified-non-integrated-certificate-authority-preview"></a>Certyfikaty powinny być wystawiane przez określony niezintegrowany urząd certyfikacji (wersja zapoznawcza)

Jeśli używasz wewnętrznego urzędu certyfikacji lub urzędu certyfikacji, który nie jest zintegrowany z magazynem kluczy i chcesz, aby użytkownicy korzystali z urzędu certyfikacji z udostępnionej listy, możesz użyć tych zasad, aby utworzyć listę dozwolonych urzędów certyfikacji według nazwy wystawcy. Tych zasad można również użyć do inspekcji lub odrzucania tworzenia certyfikatów z podpisem własnym w magazynie kluczy.

### <a name="certificates-using-elliptic-curve-cryptography-should-have-allowed-curve-names-preview"></a>Certyfikaty używające kryptografii krzywej eliptycznej powinny mieć dozwolone nazwy krzywych (wersja zapoznawcza)

Jeśli używasz kryptografii krzywej eliptyczna lub certyfikatów ECC, możesz dostosować listę dozwolonych nazw krzywych z poniższej listy. Opcja domyślna zezwala na wszystkie następujące nazwy krzywych.

- P-256
- P-256 K
- P-384
- P-521

## <a name="certificates-using-rsa-cryptography-manage-minimum-key-size-for-rsa-certificates-preview"></a>Certyfikaty używające kryptografii RSA Zarządzaj minimalnym rozmiarem klucza dla certyfikatów RSA (wersja zapoznawcza)

W przypadku korzystania z certyfikatów RSA można wybrać minimalny rozmiar klucza, który musi zawierać certyfikaty. Można wybrać jedną opcję z poniższej listy.

- 2048 bit
- 3072 bit
- 4096 bit

## <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Zarządzanie certyfikatami w ciągu określonej liczby dni wygaśnięcia (wersja zapoznawcza)

Jeśli certyfikat, który nie jest odpowiednio monitorowany, nie zostanie obrócony przed jego wygaśnięciem, może wystąpić awaria usługi. Zasady te mają na celu zapewnienie, że certyfikaty przechowywane w magazynie kluczy są monitorowane. Zalecane jest stosowanie tych zasad wielokrotnie z różnymi progami wygaśnięcia, na przykład 180, 90, 60 i 30-dniowych progów. Te zasady mogą służyć do monitorowania i Klasyfikacja wygaśnięcia certyfikatów w organizacji.

# <a name="key-policies"></a>[Najważniejsze zasady](#tab/keys)

### <a name="keys-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Klucze nie powinny być aktywne dłużej niż określona liczba dni (wersja zapoznawcza)

Jeśli chcesz mieć pewność, że Twoje klucze nie były aktywne dłużej niż określona liczba dni, możesz użyć tych zasad, aby prześledzić czas aktywności klucza.

**Jeśli klucz ma ustawioną datę aktywacji**, ta zasada będzie obliczać liczbę dni, które upłynęły od **daty aktywacji** klucza do bieżącej daty. Jeśli liczba dni przekracza ustawiony próg, klucz zostanie oznaczony jako niezgodny z zasadami.

**Jeśli klucz nie ma ustawionej daty aktywacji**, ta zasada będzie obliczać liczbę dni, które upłynęły od **daty utworzenia** klucza do bieżącej daty. Jeśli liczba dni przekracza ustawiony próg, klucz zostanie oznaczony jako niezgodny z zasadami.

### <a name="keys-should-be-the-specified-cryptographic-type-rsa-or-ec-preview"></a>Klucze powinny być określonym typem kryptograficznym RSA lub EC (wersja zapoznawcza)

Te zasady umożliwiają ograniczenie typu kluczy, które mogą znajdować się w magazynie kluczy. Możesz użyć tych zasad, aby upewnić się, że klucze są oparte na kluczach RSA, ECC lub są obsługiwane przez moduł HSM. Można wybrać spośród poniższych list, które typy certyfikatów są dozwolone.

- RSA
- RSA — HSM
- ECC
- ECC — HSM

### <a name="keys-using-elliptic-curve-cryptography-should-have-the-specified-curve-names-preview"></a>Klucze używające kryptografii krzywej eliptycznej powinny mieć określone nazwy krzywych (wersja zapoznawcza)

Jeśli używasz kryptografii krzywej eliptyczna lub kluczy ECC, możesz dostosować listę dozwolonych nazw krzywych z poniższej listy. Opcja domyślna zezwala na wszystkie następujące nazwy krzywych.

- P-256
- P-256 K
- P-384
- P-521

### <a name="keys-should-have-expirations-dates-set-preview"></a>W kluczach powinny być ustawione daty wygasania (wersja zapoznawcza)

Ta zasada przeprowadza inspekcję wszystkich kluczy w magazynach kluczy i kluczy flag, które nie mają ustawionej daty wygaśnięcia jako niezgodnej. Można również użyć tych zasad, aby zablokować tworzenie kluczy, które nie mają ustawionej daty wygaśnięcia.

### <a name="keys-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Klucze powinny mieć więcej niż określoną liczbę dni przed wygaśnięciem (wersja zapoznawcza)

Jeśli klucz jest zbyt bliski wygaśnięcia, opóźnienie organizacyjne do obrócenia klucza może spowodować awarię. Klucze należy obrócić o określoną liczbę dni przed wygaśnięciem, aby zapewnić wystarczającą ilość czasu na reagowanie na awarię. Te zasady będą przeprowadzać inspekcję kluczy, które są zbyt bliskie daty wygaśnięcia, i umożliwiają ustawienie tego progu w ciągu dni. Można również użyć tych zasad, aby zapobiec tworzeniu nowych kluczy, które są zbyt bliskie daty wygaśnięcia.

### <a name="keys-should-be-backed-by-a-hardware-security-module-preview"></a>Klucze powinny być obsługiwane przez sprzętowy moduł zabezpieczeń (wersja zapoznawcza)

HSM jest sprzętowym modułem zabezpieczeń, który przechowuje klucze. Moduł HSM zapewnia fizyczną warstwę ochrony kluczy kryptograficznych. Klucz kryptograficzny nie może opuścić fizycznego modułu HSM, który zapewnia wyższy poziom zabezpieczeń niż klucz oprogramowania. Niektóre organizacje mają wymagania dotyczące zgodności, które umożliwiają korzystanie z kluczy HSM. Te zasady służą do inspekcji wszystkich kluczy przechowywanych w magazynie kluczy, które nie są objęte usługą HSM. Można również użyć tych zasad, aby zablokować tworzenie nowych kluczy, które nie są chronione przez moduł HSM. Te zasady będą stosowane do wszystkich typów kluczy, RSA i ECC.

### <a name="keys-using-rsa-cryptography-should-have-a-specified-minimum-key-size-preview"></a>Klucze używające kryptografii RSA powinny mieć określony minimalny rozmiar klucza (wersja zapoznawcza)

Korzystanie z kluczy RSA o mniejszych rozmiarach nie jest bezpiecznym sposobem projektowania. Użytkownik może być objęty normami audytu i certyfikacji, które umożliwiają użycie minimalnego rozmiaru klucza. Poniższe zasady umożliwiają ustawienie minimalnego wymaganego rozmiaru klucza w magazynie kluczy. Można przeprowadzać inspekcję kluczy, które nie spełniają tego wymagania minimalnego. Tych zasad można również użyć do blokowania tworzenia nowych kluczy, które nie spełniają wymagań dotyczących minimalnego rozmiaru klucza.

### <a name="keys-should-have-the-specified-maximum-validity-period-preview"></a>Klucze powinny mieć określony maksymalny okres ważności (wersja zapoznawcza)

Zarządzaj wymaganiami dotyczącymi zgodności organizacji, określając maksymalną ilość czasu w dniach, przez jaką klucz może być ważny w magazynie kluczy. Klucze, które są poprawne dłużej niż ustawiony próg, zostaną oznaczone jako niezgodne. Za pomocą tych zasad można także zablokować tworzenie nowych kluczy, które mają datę wygaśnięcia ustawioną dłużej niż maksymalny okres ważności określony przez użytkownika.

# <a name="secret-policies"></a>[Zasady tajności](#tab/secrets)

### <a name="secrets-should-not-be-active-for-longer-than-the-specified-number-of-days-preview"></a>Wpisy tajne nie powinny być aktywne dłużej niż określona liczba dni (wersja zapoznawcza)

Jeśli chcesz mieć pewność, że Twoje wpisy tajne nie były aktywne dłużej niż określona liczba dni, możesz użyć tych zasad, aby przeprowadzić inspekcję okresu aktywności klucza tajnego.

**Jeśli klucz tajny ma ustawioną datę aktywacji**, ta zasada będzie obliczać liczbę dni, które upłynęły od **daty aktywacji** wpisu tajnego do bieżącej daty. Jeśli liczba dni przekracza ustawiony próg, wpis tajny zostanie oznaczony jako niezgodny z zasadami.

**Jeśli klucz tajny nie ma ustawionej daty aktywacji**, ta zasada będzie obliczać liczbę dni, które upłynęły od **daty utworzenia** klucza tajnego do bieżącej daty. Jeśli liczba dni przekracza ustawiony próg, wpis tajny zostanie oznaczony jako niezgodny z zasadami.

### <a name="secrets-should-have-content-type-set-preview"></a>Wpisy tajne powinny mieć ustawiony typ zawartości (wersja zapoznawcza)

Każdy zwykły tekst lub zaszyfrowany plik można przechowywać jako klucz tajny magazynu kluczy. Jednak organizacja może chcieć ustawić różne zasady rotacji i ograniczenia dotyczące haseł, parametrów połączenia lub certyfikatów przechowywanych jako klucze. Tag typu zawartości może pomóc użytkownikowi zobaczyć, co jest przechowywane w obiekcie tajnym bez odczytywania wartości klucza tajnego. Te zasady służą do inspekcji wpisów tajnych, które nie mają zestawu tagów typu zawartości. Można również użyć tych zasad, aby zapobiec tworzeniu nowych wpisów tajnych, jeśli nie mają ustawionego znacznika typu zawartości.

### <a name="secrets-should-have-expiration-date-set-preview"></a>Wpisy tajne powinny mieć ustawioną datę wygaśnięcia (wersja zapoznawcza)

Te zasady przeprowadzają inspekcję wszystkich wpisów tajnych w magazynie kluczy i flag tajnych, które nie mają daty wygaśnięcia ustawionej jako niezgodne. Za pomocą tych zasad można także zablokować tworzenie wpisów tajnych, które nie mają ustawionej daty wygaśnięcia.

### <a name="secrets-should-have-more-than-the-specified-number-of-days-before-expiration-preview"></a>Wpisy tajne powinny mieć więcej niż określoną liczbę dni przed wygaśnięciem (wersja zapoznawcza)

Jeśli klucz tajny jest zbyt bliski wygaśnięcia, opóźnienie organizacyjne do obrócenia klucza tajnego może spowodować awarię. Wpisy tajne powinny zostać obrócone o określoną liczbę dni przed wygaśnięciem, aby zapewnić wystarczającą ilość czasu na reagowanie na awarię. Te zasady będą przeprowadzać inspekcję wpisów tajnych, które są zbyt bliskie daty wygaśnięcia, i umożliwiają ustawienie tego progu w ciągu dni. Możesz również użyć tych zasad, aby zapobiec tworzeniu nowych wpisów tajnych, które są zbyt bliskie daty wygaśnięcia.

### <a name="secrets-should-have-the-specified-maximum-validity-period-preview"></a>Wpisy tajne powinny mieć określony maksymalny okres ważności (wersja zapoznawcza)

Zarządzaj wymaganiami dotyczącymi zgodności organizacji, określając maksymalną ilość czasu w dniach, w ciągu którego wpis tajny może być prawidłowy w magazynie kluczy. Wpisy tajne, które są poprawne dłużej niż ustawiony próg, zostaną oznaczone jako niezgodne. Za pomocą tych zasad można także zablokować tworzenie nowych wpisów tajnych, które mają datę wygaśnięcia ustawioną dłużej niż maksymalny okres ważności określony przez użytkownika.

---

## <a name="example-scenario"></a>Przykładowy scenariusz

Zarządzasz magazynem kluczy używanym przez wiele zespołów, które zawierają certyfikaty 100 i chcesz upewnić się, że żaden z certyfikatów w magazynie kluczy nie jest ważny przez dłużej niż 2 lata.

1. **Certyfikaty należy przypisać do określonych zasad maksymalnego okresu ważności** , określić, że maksymalny okres ważności certyfikatu wynosi 24 miesiące, i ustawić wpływ zasad na "inspekcję". 
1. [Raport zgodności można wyświetlić na Azure Portal](#view-compliance-results)i sprawdzić, czy 20 certyfikatów jest niezgodnych i prawidłowych dla > 2 lat, a pozostałe certyfikaty są zgodne. 
1. Skontaktuj się z właścicielami tych certyfikatów i przekaż nowe wymagania dotyczące zabezpieczeń, których certyfikaty nie mogą być ważne przez dłużej niż 2 lata. Niektóre zespoły odpowiadają i 15 certyfikatów zostały odnowione z maksymalnym okresem ważności wynoszącym 2 lata lub mniej. Inne zespoły nie odpowiadają, a nadal masz 5 niezgodnych certyfikatów w magazynie kluczy.
1. Zmienisz efekt przypisanych zasad do "Odmów". 5 niezgodnych certyfikatów nie jest odwołanych i nadal działają. Nie można jednak przedłużyć okresu ważności, który jest dłuższy niż 2 lata. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Włączanie zasad Key Vault i zarządzanie nimi za pomocą Azure Portal

### <a name="select-a-policy-definition"></a>Wybierz definicję zasad

1. Zaloguj się w witrynie Azure Portal. 
1. Wyszukaj ciąg "Policy" na pasku wyszukiwania i wybierz pozycję **zasady**.

    ![Zrzut ekranu pokazujący pasek wyszukiwania.](../media/policy-img1.png)

1. W oknie zasady wybierz pozycję **definicje**.

    ![Zrzut ekranu, który podświetla opcję definicje.](../media/policy-img2.png)

1. W filtr kategorii Usuń zaznaczenie opcji **Zaznacz wszystko** i wybierz **Key Vault**. 

    ![Zrzut ekranu pokazujący filtr kategorii i wybraną kategorię Key Vault.](../media/policy-img3.png)

1. Teraz powinno być możliwe wyświetlenie wszystkich zasad dostępnych dla publicznej wersji zapoznawczej w celu Azure Key Vault. Upewnij się, że znasz i rozumiesz powyższe wskazówki dotyczące zasad i wybierz zasady, które chcesz przypisać do zakresu.  

    ![Zrzut ekranu pokazujący zasady, które są dostępne dla publicznej wersji zapoznawczej.](../media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Przypisywanie zasad do zakresu 

1. Wybierz zasady, które chcesz zastosować. w tym przykładzie są wyświetlane zasady **Zarządzaj okresem ważności certyfikatu** . Kliknij przycisk Przypisz w lewym górnym rogu.

    ![Zrzut ekranu przedstawiający zasady zarządzania okresem ważności certyfikatu.](../media/policy-img5.png)
  
1. Wybierz subskrypcję, w której chcesz zastosować zasady. Możesz ograniczyć zakres tylko do jednej grupy zasobów w ramach subskrypcji. Jeśli chcesz zastosować zasady do całej subskrypcji i wykluczyć niektóre grupy zasobów, możesz również skonfigurować listę wykluczeń. Ustaw opcję selektora wymuszania zasad na **włączone** , jeśli chcesz, aby efekt zasad (inspekcja lub odmowa) został wyrzucony lub **wyłączony** w celu wyłączenia efektu (inspekcja lub odmowa). 

    ![Zrzut ekranu pokazujący, gdzie można wybrać ograniczenie zakresu tylko do jednej grupy zasobów w ramach subskrypcji.](../media/policy-img6.png)

1. Kliknij kartę parametry w górnej części ekranu, aby określić maksymalny okres ważności w miesiącach. Wybierz pozycję **Inspekcja** lub **Odmów** dla efektu zasad zgodnie ze wskazówkami podanych w powyższych sekcjach. Następnie wybierz przycisk Recenzja + tworzenie. 

    ![Zrzut ekranu pokazujący kartę parametry, w której można określić maksymalny okres ważności w miesiącach.](../media/policy-img7.png)

### <a name="view-compliance-results"></a>Wyświetl wyniki zgodności

1. Wróć do bloku zasad i wybierz kartę zgodność. Kliknij przypisanie zasad, dla którego chcesz wyświetlić wyniki sprawdzania zgodności.

    ![Zrzut ekranu przedstawiający kartę zgodność, w której można wybrać przypisanie zasad, dla którego chcesz wyświetlić wyniki sprawdzania zgodności.](../media/policy-img8.png)

1. Na tej stronie można filtrować wyniki według zgodnych lub niezgodnych magazynów. W tym miejscu można wyświetlić listę niezgodnych magazynów kluczy w zakresie przypisania zasad. Magazyn jest uznawany za niezgodny, jeśli którykolwiek ze składników (certyfikatów) w magazynie nie jest zgodny. Możesz wybrać pojedynczy magazyn, aby wyświetlić poszczególne niezgodne składniki (certyfikaty). 


    ![Zrzut ekranu przedstawiający listę niezgodnych magazynów kluczy w zakresie przypisania zasad.](../media/policy-img9.png)

1. Wyświetlanie nazw składników w magazynie, które nie są zgodne


    ![Zrzut ekranu pokazujący, gdzie można wyświetlić nazwy składników w magazynie, które nie są zgodne.](../media/policy-img10.png)

1. Jeśli chcesz sprawdzić, czy użytkownicy nie mają możliwości tworzenia zasobów w ramach magazynu kluczy, możesz kliknąć kartę **zdarzenia składników (wersja zapoznawcza)** , aby wyświetlić podsumowanie niedozwolonych operacji certyfikatu z obiektem żądającym i sygnaturami czasowymi żądań. 


    ![Przegląd sposobu działania Azure Key Vault](../media/policy-img11.png)

## <a name="feature-limitations"></a>Ograniczenia funkcji

Przypisanie zasad z efektem "odmowa" może potrwać do 30 minut (średni przypadek) i 1 godzina (najgorszy przypadek), aby rozpocząć odmowa tworzenia niezgodnych zasobów. Ocena zasad istniejących składników w magazynie może potrwać do 1 godziny (średnia wielkość liter) i 2 godziny (najgorszy przypadek), zanim wyniki zgodności będą widoczne w interfejsie użytkownika portalu. Jeśli wyniki zgodności są wyświetlane jako "nie uruchomiono", może to być spowodowane następującymi przyczynami:
- Wycena nie została jeszcze ukończona. Opóźnienie wstępnej oceny może potrwać do 2 godzin w scenariuszu najgorszego przypadku. 
- W zakresie przypisania zasad nie ma magazynów kluczy.
- Brak magazynów kluczy z certyfikatami w zakresie przypisania zasad.

> [!NOTE]
> Azure Policy [tryby dostawcy zasobów](../../governance/policy/concepts/definition-structure.md#resource-provider-modes), takie jak te dla Azure Key Vault, zawierają informacje o zgodności na stronie [zgodność składników](../../governance/policy/how-to/get-compliance-data.md#component-compliance) .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [usługi Azure Policy](../../governance/policy/overview.md)
- Zobacz przykłady Key Vault: [Key Vault wbudowane definicje zasad](../../governance/policy/samples/built-in-policies.md#key-vault)
