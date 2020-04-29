---
title: Microsoft Azure szyfrowanie danych — w czasie spoczynku | Microsoft Docs
description: Ten artykuł zawiera omówienie Microsoft Azure szyfrowanie danych w pamięci podręcznej, ogólne możliwości i ogólne zagadnienia.
services: security
documentationcenter: na
author: msmbaldwin
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: mbaldwin
ms.openlocfilehash: 1e08e758fbba911d3391794f5bab31aaf6a5fc73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81454683"
---
# <a name="azure-data-encryption-at-rest"></a>Szyfrowanie danych platformy Azure — w spoczynku

Microsoft Azure zawiera narzędzia do zabezpieczania danych zgodnie z potrzebami zabezpieczeń i zgodności firmy. Ten dokument koncentruje się na:

- Jak dane są chronione w spoczynku między Microsoft Azure
- Omawia różne składniki biorące udział w implementacji ochrony danych.
- Przegląda specjaliści i wady różnych metod ochrony zarządzania kluczami.

Szyfrowanie w spoczynku jest typowym wymogiem bezpieczeństwa. W systemie Azure organizacje mogą szyfrować dane bez ryzyka lub kosztów niestandardowego rozwiązania do zarządzania kluczami. W organizacjach istnieje możliwość, że platforma Azure całkowicie zarządza szyfrowaniem w stanie spoczynku. Ponadto organizacje mają różne opcje, aby dokładnie zarządzać szyfrowaniem i kluczami szyfrowania.

## <a name="what-is-encryption-at-rest"></a>Co to jest szyfrowanie w spoczynku?

Szyfrowanie w spoczynku to kodowanie (szyfrowanie) danych, gdy są utrwalane. Szyfrowanie w projektach REST na platformie Azure umożliwia korzystanie z szyfrowania symetrycznego w celu szybkiego szyfrowania i odszyfrowywania dużych ilości danych zgodnie z prostym modelem koncepcyjnym:

- Symetryczny klucz szyfrowania jest używany do szyfrowania danych podczas zapisywania w magazynie.
- Ten sam klucz szyfrowania jest używany do odszyfrowywania tych danych, ponieważ jest readied do użycia w pamięci.
- Dane mogą być partycjonowane i różne klucze mogą być używane dla każdej partycji.
- Klucze muszą być przechowywane w bezpiecznej lokalizacji z kontrolą dostępu opartą na tożsamości i zasadami inspekcji. Klucze szyfrowania danych są często szyfrowane za pomocą klucza szyfrowania kluczy w Azure Key Vault, aby dodatkowo ograniczyć dostęp.

W tym scenariuszu scenariusze zarządzania kluczami i kontroli, a także zapewnienia skalowalności i dostępności, wymagają dodatkowych konstrukcji. Poniżej opisano Microsoft Azure szyfrowanie przy założeniach i składnikach Rest.

## <a name="the-purpose-of-encryption-at-rest"></a>Cel szyfrowania w czasie spoczynku

Szyfrowanie w spoczynku zapewnia ochronę danych przechowywanych danych (w spoczynku). Ataki na dane przechowywane w usłudze REST obejmują próby uzyskania fizycznego dostępu do sprzętu, na którym dane są zapisywane, a następnie naruszenie zawartych w nim danych. W takim przypadku może dojść do nieprawidłowej obsługi dysku twardego serwera podczas konserwacji, co umożliwia osobie atakującej usunięcie dysku twardego. Później osoba atakująca umieści dysk twardy na komputerze w ramach kontroli, aby spróbować uzyskać dostęp do danych.

Szyfrowanie w spoczynku zostało zaprojektowane w taki sposób, aby uniemożliwić osobie atakującej dostęp do niezaszyfrowanych danych, zapewniając szyfrowanie danych na dysku. Jeśli osoba atakująca uzyska dysk twardy z zaszyfrowanymi danymi, ale nie klucze szyfrowania, osoba atakująca musi pokonać szyfrowanie, aby odczytywać dane. Ten atak jest znacznie bardziej skomplikowany i zużywa zasoby niż dostęp do nieszyfrowanych danych na dysku twardym. Z tego powodu szyfrowanie w stanie spoczynku jest zdecydowanie zalecane i jest to wymaganie o wysokim priorytecie dla wielu organizacji.

Szyfrowanie w spoczynku może być również wymagane przez organizację potrzebną do zarządzania danymi i ich zgodnością. Regulacje branżowe i rządowe, takie jak HIPAA, PCI i FedRAMP, stanowią szczegółowe zabezpieczenia dotyczące ochrony danych i wymagań dotyczących szyfrowania. Szyfrowanie w spoczynku jest obowiązkowym środkiem wymaganym do zgodności z niektórymi z tych rozporządzeń. Aby uzyskać więcej informacji na temat podejścia firmy Microsoft do weryfikacji zgodności ze standardem FIPS 140-2, zobacz [publikację Federal Information Processing Standard (FIPS) 140-2](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2). 

Oprócz spełnienia wymagań dotyczących zgodności i przepisów prawnych szyfrowanie w spoczynku zapewnia ochronę kompleksową. Microsoft Azure zapewnia zgodną platformę dla usług, aplikacji i danych. Zapewnia również kompleksowe funkcje i zabezpieczenia fizyczne, kontrolę dostępu do danych i inspekcję. Jednak ważne jest, aby zapewnić dodatkowe "nakładające się" środki zabezpieczeń w przypadku niepowodzenia jednego z pozostałych środków zabezpieczeń, a szyfrowanie w spoczynku zapewnia takie środki bezpieczeństwa.

Firma Microsoft jest zaangażowana w szyfrowanie opcji REST w ramach usług Cloud Services i zapewnia klientom kontrolę nad kluczami szyfrowania i dziennikami użycia klucza. Ponadto firma Microsoft pracuje nad szyfrowaniem domyślnie wszystkich danych klienta.

## <a name="azure-encryption-at-rest-components"></a>Szyfrowanie platformy Azure w składnikach REST

Jak opisano wcześniej, celem szyfrowania w czasie spoczynku jest to, że dane utrwalane na dysku są szyfrowane przy użyciu tajnego klucza szyfrowania. Aby osiągnąć ten cel, należy podać bezpieczny sposób tworzenia kluczy, magazynu, kontroli dostępu i zarządzania kluczami szyfrowania. Chociaż szczegółowe informacje mogą się różnić, szyfrowanie usług platformy Azure w ramach implementacji REST można znaleźć w temacie przedstawionym na poniższym diagramie.

![Składniki](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Lokalizacją przechowywania kluczy szyfrowania i kontroli dostępu do tych kluczy jest centralne szyfrowanie w modelu Rest. Klucze muszą być wysoce zabezpieczone, ale mogą być zarządzane przez określonych użytkowników i dostępne dla określonych usług. W przypadku usług Azure Azure Key Vault jest zalecanym rozwiązaniem magazynu kluczy i oferuje wspólne środowisko zarządzania w ramach usług. Klucze są przechowywane i zarządzane w magazynach kluczy, a dostęp do magazynu kluczy można udzielić użytkownikom lub usługom. Azure Key Vault obsługuje tworzenie kluczy lub importowanie kluczy klienta do użytku w scenariuszach z kluczami szyfrowania zarządzanymi przez klienta.

### <a name="azure-active-directory"></a>Azure Active Directory

Uprawnienia do korzystania z kluczy przechowywanych w Azure Key Vault w celu zarządzania nimi lub uzyskiwania do nich dostępu w celu szyfrowania podczas szyfrowania i odszyfrowywania w czasie spoczynku można nadać Azure Active Directory kontom.

### <a name="key-hierarchy"></a>Hierarchia kluczy

W implementacji REST jest używany więcej niż jeden klucz szyfrowania. Przechowywanie klucza szyfrowania w Azure Key Vault zapewnia bezpieczny dostęp do klucza i centralne zarządzanie kluczami. Jednak lokalny dostęp usługi do kluczy szyfrowania jest bardziej wydajny w przypadku szyfrowania zbiorczego i odszyfrowywania niż korzystanie z Key Vault dla każdej operacji na danych, co pozwala na silniejsze szyfrowanie i lepszą wydajność. Ograniczenie użycia jednego klucza szyfrowania zmniejsza ryzyko naruszenia klucza i koszt ponownego szyfrowania, gdy klucz musi zostać zastąpiony. Funkcje szyfrowania platformy Azure w modelach REST używają hierarchii kluczy składającej się z następujących typów kluczy, aby rozwiązać wszystkie te wymagania:

- **Klucz szyfrowania danych (undek)** — symetryczny klucz AES256 używany do szyfrowania partycji lub bloku danych.  Pojedynczy zasób może mieć wiele partycji i wiele kluczy szyfrowania danych. Szyfrowanie każdego bloku danych przy użyciu innego klucza sprawia, że ataki analizy kryptograficznej są trudniejsze. Dostęp do DEKs jest wymagany przez dostawcę zasobów lub wystąpienie aplikacji, które szyfruje i odszyfrowuje określony blok. Gdy klucz szyfrowania danych jest zastępowany nowym kluczem, tylko dane w skojarzonym bloku muszą zostać ponownie zaszyfrowane przy użyciu nowego klucza.
- **Klucz szyfrowania klucza (KEK)** — klucz szyfrowania służący do szyfrowania kluczy szyfrowania danych. Użycie klucza szyfrowania klucza, który nigdy nie opuszcza Key Vault pozwala na szyfrowanie kluczy szyfrowania danych i sterowanie nimi. Jednostka, która ma dostęp do elementu KEK, może być różna od jednostki, która wymaga tego elementu. Jednostka może przetworzyć dostęp do tego klucza, aby ograniczyć dostęp do każdego szyfrowania danych do określonej partycji. Ponieważ KEK jest wymagany do odszyfrowania DEKs, KEK jest efektywnie jednym punktem, przez który DEKs może być skutecznie usunięty przez usunięcie KEK.

Klucze szyfrowania danych szyfrowane za pomocą kluczy szyfrowania kluczy są przechowywane oddzielnie, a tylko jednostka mająca dostęp do klucza szyfrowania klucza może odszyfrować te klucze szyfrowania danych. Obsługiwane są różne modele magazynu kluczy. Szczegółowo omówiono każdy model w dalszej części następnej sekcji.

## <a name="data-encryption-models"></a>Modele szyfrowania danych

Zrozumienie różnych modeli szyfrowania i ich zalet i wad jest niezbędne do poznania sposobu, w jaki różne dostawcy zasobów na platformie Azure implementują szyfrowanie w spoczynku. Te definicje są udostępniane wszystkim dostawcom zasobów na platformie Azure w celu zapewnienia wspólnego języka i taksonomii.

Istnieją trzy scenariusze dotyczące szyfrowania po stronie serwera:

- Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę
  - Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania
  - Firma Microsoft zarządza kluczami
  - Pełna funkcjonalność chmury

- Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault
  - Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania
  - Klucze kontroli klienta za pośrednictwem Azure Key Vault
  - Pełna funkcjonalność chmury

- Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta na sprzęcie kontrolowanym przez klienta
  - Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania
  - Klucze kontroli klienta na sprzęcie kontrolowanym przez klienta
  - Pełna funkcjonalność chmury

W przypadku szyfrowania po stronie klienta należy wziąć pod uwagę następujące kwestie:

- Usługi platformy Azure nie mogą zobaczyć odszyfrowanych danych
- Klienci zarządzają kluczami lokalnymi (lub w innych magazynach bezpiecznych) i przechowują je. Klucze nie są dostępne dla usług platformy Azure
- Zmniejszona funkcjonalność chmury

Obsługiwane modele szyfrowania na platformie Azure dzielą się na dwie główne grupy: "szyfrowanie klienta" i "szyfrowanie po stronie serwera" wymienione wcześniej. Niezależnie od szyfrowania w używanym modelu REST usługi platformy Azure zawsze zalecają korzystanie z bezpiecznego transportu, takiego jak TLS lub HTTPS. W związku z tym szyfrowanie w transporcie powinno być rozkierowane przez protokół transportowy i nie powinno być głównym czynnikiem określającym, które szyfrowanie w modelu REST ma być używane.

### <a name="client-encryption-model"></a>Model szyfrowania klienta

Model szyfrowania klienta odnosi się do szyfrowania, które jest wykonywane poza dostawcą zasobów lub na platformie Azure przez usługę lub aplikację wywołującą. Szyfrowanie może być wykonywane przez aplikację usługi na platformie Azure lub przez aplikację działającą w centrum danych klienta. W obu przypadkach, gdy korzystasz z tego modelu szyfrowania, dostawca zasobów platformy Azure odbiera zaszyfrowany obiekt BLOB danych bez możliwości odszyfrowania danych w dowolny sposób lub mieć dostęp do kluczy szyfrowania. W tym modelu zarządzanie kluczami odbywa się przez usługę wywołującą/aplikację i nieprzezroczystą dla usługi platformy Azure.

![Klient](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Model szyfrowania po stronie serwera

Modele szyfrowania po stronie serwera odnoszą się do szyfrowania, które jest wykonywane przez usługę platformy Azure. W tym modelu dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania. Na przykład usługa Azure Storage może odbierać dane w postaci zwykłego tekstu i przeprowadzi wewnętrznie szyfrowanie i odszyfrowywanie. Dostawca zasobów może korzystać z kluczy szyfrowania, które są zarządzane przez firmę Microsoft lub przez klienta w zależności od podanej konfiguracji.

![Serwer](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modele zarządzania kluczami szyfrowania po stronie serwera

Każde szyfrowanie po stronie serwera w modelach REST implikuje charakterystyczne charakterystyki zarządzania kluczami. Obejmuje to miejsce i sposób tworzenia kluczy szyfrowania oraz ich przechowywanie oraz modeli dostępu oraz procedur rotacji kluczy.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę

Dla wielu klientów zasadniczy wymóg polega na zapewnieniu, że dane są szyfrowane w każdym miejscu. Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę umożliwia klientom oznaczenie określonego zasobu (konta magazynu, bazy danych SQL itp.) w celu szyfrowania i pozostawienie wszystkich aspektów zarządzania kluczami, takich jak wystawianie kluczy, rotacja i wykonywanie kopii zapasowych w firmie Microsoft. Większość usług platformy Azure, które obsługują szyfrowanie w spoczynku, zazwyczaj obsługuje ten model odciążający zarządzanie kluczami szyfrowania na platformie Azure. Dostawca zasobów platformy Azure tworzy klucze, umieszcza je w bezpiecznym magazynie i pobiera je w razie konieczności. Oznacza to, że usługa ma pełny dostęp do kluczy, a usługa ma pełną kontrolę nad zarządzaniem cyklem życia poświadczeń.

![zarządzanych](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę w związku z tym szybko eliminuje konieczność szyfrowania przy niskim obciążeniu klienta. Gdy klient jest dostępny, zwykle otwiera Azure Portal dla docelowej subskrypcji i dostawcy zasobów i sprawdza pole wskazujące, że dane mają być szyfrowane. W przypadku niektórych menedżerów zasobów szyfrowanie po stronie serwera z kluczami zarządzanymi przez usługę jest domyślnie włączone.

Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez firmę Microsoft oznacza, że usługa ma pełny dostęp do przechowywania kluczy i zarządzania nimi. Niektórzy klienci mogą chcieć zarządzać kluczami, ponieważ mogą one mieć większe bezpieczeństwo, ale koszt i ryzyko związane z niestandardowym rozwiązaniem magazynu kluczy należy wziąć pod uwagę podczas oceniania tego modelu. W wielu przypadkach organizacja może określić, że ograniczenia zasobów lub ryzyko związane z rozwiązaniem lokalnym mogą być większe niż ryzyko związane z zarządzaniem przez chmurę szyfrowania w kluczach Rest.  Jednak ten model może być niewystarczający dla organizacji, które mają wymagania umożliwiające kontrolę tworzenia lub cyklu życia kluczy szyfrowania lub posiadania innego personelu do zarządzania kluczami szyfrowania usługi, niż w przypadku zarządzania usługą (to znaczy segregowanie klucza zarządzania z ogólnego modelu zarządzania usługi).

##### <a name="key-access"></a>Dostęp do klucza

Gdy używane jest szyfrowanie po stronie serwera z kluczami zarządzanymi przez usługę, zarządzanie kluczami, magazynem i dostępem do usługi jest zarządzane przez usługę. Zazwyczaj podstawowi dostawcy zasobów platformy Azure będą przechowywać klucze szyfrowania danych w magazynie, który jest blisko danych i szybko dostępne i dostępne, gdy klucze szyfrowania kluczy są przechowywane w bezpiecznym magazynie wewnętrznym.

**Zalety**

- Prosta konfiguracja
- Firma Microsoft zarządza rotacją, tworzeniem kopii zapasowej i nadmiarowością.
- Klient nie ma kosztu związanego z implementacją lub ryzykiem niestandardowego schematu zarządzania kluczami.

**Wady**

- Brak kontroli klienta nad kluczami szyfrowania (Specyfikacja klucza, cykl życia, odwołanie itp.)
- Brak możliwości oddzielenia zarządzania kluczami od ogólnego modelu zarządzania dla usługi

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault

W przypadku scenariuszy, w których wymagane jest szyfrowanie danych w stanie spoczynku i sterowanie kluczami szyfrowania, klienci mogą korzystać z szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta w Key Vault. Niektóre usługi mogą przechowywać tylko klucz szyfrowania klucza głównego w Azure Key Vault i przechowywać zaszyfrowany klucz szyfrowania danych w wewnętrznej lokalizacji zbliżonej do danych. W tym scenariuszu klienci mogą przenosić własne klucze do Key Vault (BYOK – Bring Your Own Key) lub generować nowe i używać ich do szyfrowania żądanych zasobów. Mimo że dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania, używa skonfigurowanego klucza szyfrowania klucza jako klucza głównego dla wszystkich operacji szyfrowania.

Utrata kluczy szyfrowania kluczy oznacza utratę danych. Z tego powodu klucze nie powinny być usuwane. Należy utworzyć kopię zapasową kluczy, gdy zostały utworzone lub obrócone. [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) powinno być włączone na dowolnym magazynie przechowującym klucze szyfrowania kluczy. Zamiast usuwać klucz, ustaw wartość włączone na false lub ustaw datę wygaśnięcia.

##### <a name="key-access"></a>Dostęp do klucza

Model szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta w Azure Key Vault obejmuje usługę, która uzyskuje dostęp do kluczy do szyfrowania i odszyfrowywania w razie potrzeby. Szyfrowanie w kluczach REST jest dostępne dla usługi za pomocą zasad kontroli dostępu. Te zasady udzielą dostępu tożsamości usługi do odbioru klucza. Usługę platformy Azure działającą w imieniu skojarzonej subskrypcji można skonfigurować przy użyciu tożsamości w tej subskrypcji. Usługa może wykonać Azure Active Directory uwierzytelnianie i otrzymać token uwierzytelniania identyfikujący siebie jako usługę działającą w imieniu subskrypcji. Token ten można następnie przedstawić Key Vault, aby uzyskać klucz, do którego miał dostęp.

W przypadku operacji przy użyciu kluczy szyfrowania można udzielić tożsamości usługi dostępu do dowolnej z następujących operacji: odszyfrowywanie, szyfrowanie, unwrapKey, wrapKey, weryfikowanie, podpisywanie, pobieranie, wyświetlanie, aktualizowanie, tworzenie, importowanie, usuwanie, wykonywanie kopii zapasowych i przywracanie.

Aby uzyskać klucz służący do szyfrowania lub odszyfrowywania danych w spoczynku, tożsamość usługi, która będzie uruchamiana Menedżer zasobów wystąpienia usługi, musi mieć UnwrapKey (Aby uzyskać klucz do odszyfrowywania) i WrapKey (aby wstawić klucz do magazynu kluczy podczas tworzenia nowego klucza).

>[!NOTE]
>Aby uzyskać więcej szczegółowych informacji na temat Key Vault autoryzacji, zobacz stronę Zabezpieczanie magazynu kluczy w [dokumentacji Azure Key Vault](../../key-vault/general/secure-your-key-vault.md).

**Zalety**

- Pełna kontrola nad używanymi kluczami — klucze szyfrowania są zarządzane w Key Vault klienta pod kontrolą klienta.
- Możliwość szyfrowania wielu usług na jednym wzorcu
- Może oddzielić zarządzanie kluczami od ogólnego modelu zarządzania dla usługi
- Może definiować lokalizację usługi i klucza w różnych regionach

**Wady**

- Klient ma pełną odpowiedzialność za zarządzanie dostępem do kluczy
- Klient ma pełną odpowiedzialność za zarządzanie cyklem życia kluczy
- Dodatkowe ustawienia & konfiguracji

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w sprzęcie kontrolowanym przez klienta

Niektóre usługi platformy Azure zapewniają własny model zarządzania kluczami (HYOK) na hoście. Ten tryb zarządzania jest użyteczny w scenariuszach, w których istnieje potrzeba zaszyfrowania danych przechowywanych w pamięci i zarządzania kluczami w repozytorium zastrzeżonym poza kontrolą firmy Microsoft. W tym modelu usługa musi pobrać klucz z zewnętrznej lokacji. Wpływ na gwarancje dotyczące wydajności i dostępności, a konfiguracja jest bardziej skomplikowana. Ponadto, ponieważ usługa ma dostęp do klucza szyfrowania danych w trakcie operacji odszyfrowywania i deszyfrowania, ogólne gwarancje bezpieczeństwa tego modelu są podobne do tego, kiedy klucze są zarządzane przez klienta w Azure Key Vault.  W związku z tym ten model nie jest odpowiedni dla większości organizacji, chyba że mają określone wymagania dotyczące zarządzania kluczami. Ze względu na te ograniczenia większość usług platformy Azure nie obsługuje szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta.

##### <a name="key-access"></a>Dostęp do klucza

Gdy używane jest szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta, klucze są utrzymywane w systemie skonfigurowanym przez klienta. Usługi platformy Azure obsługujące ten model umożliwiają ustanowienie bezpiecznego połączenia z magazynem kluczy dostarczonym przez klienta.

**Zalety**

- Pełna kontrola nad używanym kluczem głównym — klucze szyfrowania są zarządzane przez sklep dostarczony przez klienta.
- Możliwość szyfrowania wielu usług na jednym wzorcu
- Może oddzielić zarządzanie kluczami od ogólnego modelu zarządzania dla usługi
- Może definiować lokalizację usługi i klucza w różnych regionach

**Wady**

- Pełna odpowiedzialność za magazyn kluczy, bezpieczeństwo, wydajność i dostępność
- Pełna odpowiedzialność za zarządzanie dostępem do kluczy
- Pełna odpowiedzialność za zarządzanie cyklem życia kluczy
- Znacząca konfiguracja, konfiguracja i bieżące koszty konserwacji
- Zwiększona zależność od dostępności sieci między centrum danych klienta a centrami danych platformy Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Szyfrowanie w usługach w chmurze firmy Microsoft

Usługi Microsoft Cloud są używane we wszystkich trzech modelach chmur: IaaS, PaaS, SaaS. Poniżej znajdują się przykłady ich dopasowania do poszczególnych modeli:

- Usługi oprogramowania, określane jako oprogramowanie jako serwer lub SaaS, które mają aplikację dostarczoną przez chmurę, taką jak Office 365.
- Usługi platformy, do których klienci wykorzystują chmurę w swoich aplikacjach, przy użyciu chmury dla takich elementów jak Storage, Analytics i Service Bus.
- Usługi infrastruktury lub infrastruktura jako usługa (IaaS), w której klienci wdrażają systemy operacyjne i aplikacje hostowane w chmurze i mogą korzystać z innych usług w chmurze.

### <a name="encryption-at-rest-for-saas-customers"></a>Szyfrowanie w spoczynku dla klientów SaaS

Klienci korzystający z oprogramowania jako usługi (SaaS) zazwyczaj mają szyfrowanie włączone lub dostępne w każdej usłudze. Pakiet Office 365 ma kilka opcji umożliwiających klientom Weryfikowanie lub Włączanie szyfrowania w stanie spoczynku. Aby uzyskać informacje o usługach Office 365, zobacz [szyfrowanie w pakiecie Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Szyfrowanie w spoczynku dla klientów PaaS

Dane klienta platformy jako usługi (PaaS) są zwykle przechowywane w usłudze magazynu, takiej jak Blob Storage, ale mogą być również buforowane lub przechowywane w środowisku wykonywania aplikacji, na przykład na maszynie wirtualnej. Aby wyświetlić dostępne dla Ciebie opcje szyfrowania, zapoznaj się z poniższą tabelą dotyczącą używanych platform magazynu i aplikacji.

### <a name="encryption-at-rest-for-iaas-customers"></a>Szyfrowanie w spoczynku dla klientów IaaS

Klienci korzystający z infrastruktury jako usługi (IaaS) mogą mieć różne usługi i aplikacje. Usługi IaaS umożliwiają szyfrowanie przechowywanych maszyn wirtualnych i wirtualnych dysków twardych platformy Azure przy użyciu Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Zaszyfrowany magazyn

Podobnie jak w przypadku PaaS, rozwiązania IaaS mogą korzystać z innych usług platformy Azure, które przechowują dane szyfrowane w spoczynku. W takich przypadkach można włączyć szyfrowanie w obsłudze REST, zgodnie z poszczególnymi wykorzystanymi usługami platformy Azure. W poniższej tabeli wymieniono główne magazyny, usługi i platformy aplikacji oraz model szyfrowania obsługiwanego w spoczynku. 

#### <a name="encrypted-compute"></a>Zaszyfrowane obliczenia

Wszystkie Managed Disks, migawki i obrazy są szyfrowane przy użyciu szyfrowanie usługi Storage przy użyciu klucza zarządzanego przez usługę. Pełniejsze szyfrowanie w rozwiązaniu REST gwarantuje, że dane nigdy nie są utrwalane w postaci niezaszyfrowanej. Podczas przetwarzania danych na maszynie wirtualnej dane mogą być utrwalane w pliku stronicowania systemu Windows lub w systemie Linux, zrzucie awaryjnego lub w dzienniku aplikacji. Aby zapewnić szyfrowanie danych w spoczynku, aplikacje IaaS mogą używać Azure Disk Encryption na maszynie wirtualnej platformy Azure IaaS (Windows lub Linux) i na dysku wirtualnym.

#### <a name="custom-encryption-at-rest"></a>Niestandardowe szyfrowanie w spoczynku

Zaleca się, aby zawsze, gdy to możliwe, IaaS aplikacje wykorzystują Azure Disk Encryption i szyfrowanie w opcjach REST zapewnianych przez wszystkie wykorzystane usługi platformy Azure. W niektórych przypadkach, takich jak nietypowe wymagania dotyczące szyfrowania lub magazyn oparty na systemie innym niż Azure, Deweloper aplikacji IaaS może wymagać zaimplementowania szyfrowania w samym czasie. Deweloperzy rozwiązań IaaS można lepiej zintegrować z usługą Azure Management i oczekiwaniami klientów, wykorzystując pewne składniki platformy Azure. W szczególności deweloperzy powinni używać usługi Azure Key Vault, aby zapewnić bezpieczny Magazyn kluczy, a także zapewnić klientom spójne opcje zarządzania kluczami w przypadku większości usług platformy Azure. Dodatkowo rozwiązania niestandardowe powinny używać tożsamości usługi zarządzanej przez platformę Azure, aby umożliwić kontom usługi dostęp do kluczy szyfrowania. Informacje dla deweloperów dotyczące Azure Key Vault i tożsamości usługi zarządzanej znajdują się w odpowiednich zestawach SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Obsługa modelu szyfrowania dostawców zasobów platformy Azure

Usługi Microsoft Azure Each obsługują co najmniej jedno szyfrowanie w modelach Rest. W przypadku niektórych usług jednak może nie być stosowane co najmniej jeden model szyfrowania. W przypadku usług obsługujących scenariusze związane z kluczami zarządzanymi przez klienta mogą być obsługiwane tylko podzbiór typów kluczy, które Azure Key Vault obsługiwane przez klucze szyfrowania kluczy. Ponadto usługi mogą wydać wsparcie dla tych scenariuszy i typów kluczy w różnych harmonogramach. W tej sekcji opisano szyfrowanie w czasie trwania tego zapisu dla każdej głównej usługi Azure Data Storage.

### <a name="azure-disk-encryption"></a>Szyfrowanie dysków Azure

Każdy klient korzystający z funkcji infrastruktury jako usługi (IaaS) platformy Azure może uzyskać szyfrowanie w spoczynku dla maszyn wirtualnych IaaS i dysków za pośrednictwem Azure Disk Encryption. Więcej informacji na temat usługi Azure Disk Encryption można znaleźć w [dokumentacji Azure Disk Encryption](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Usługa Azure Storage

Wszystkie usługi Azure Storage (BLOB Storage, queue storage, Table Storage i Azure Files) obsługują szyfrowanie po stronie serwera. Niektóre usługi obsługują dodatkowo klucze zarządzane przez klienta i szyfrowanie po stronie klienta. 

- Po stronie serwera: wszystkie usługi magazynu platformy Azure domyślnie umożliwiają szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę, które są niewidoczne dla aplikacji. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku](../../storage/common/storage-service-encryption.md). Usługa Azure Blob Storage i Azure Files obsługują również klucze zarządzane przez klienta RSA 2048-bitowe w programie Azure Key Vault. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md).
- Po stronie klienta: obiekty blob, tabele i kolejki platformy Azure obsługują szyfrowanie po stronie klienta. W przypadku korzystania z szyfrowania po stronie klienta program szyfruje dane i przekazuje je jako zaszyfrowany obiekt BLOB. Zarządzanie kluczami jest wykonywane przez klienta. Aby uzyskać więcej informacji, zobacz [szyfrowanie po stronie klienta i Azure Key Vault dla Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database obecnie obsługuje szyfrowanie w stanie spoczynku dla usług zarządzanych przez firmę Microsoft i scenariuszy szyfrowania po stronie klienta.

Obsługa szyfrowania serwera jest obecnie udostępniana za pomocą funkcji SQL o nazwie Transparent Data Encryption. Gdy Azure SQL Database klient włącza klucz TDE, są automatycznie tworzone i zarządzane dla nich. Szyfrowanie w spoczynku można włączyć na poziomie bazy danych i serwera. Od czerwca 2017 [transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) jest domyślnie włączone dla nowo utworzonych baz danych. Azure SQL Database obsługuje RSA 2048-bitowe klucze zarządzane przez klienta w Azure Key Vault. Aby uzyskać więcej informacji, zobacz [transparent Data Encryption z obsługą Bring Your Own Key w przypadku Azure SQL Database i magazynu danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Szyfrowanie po stronie klienta Azure SQL Database danych jest obsługiwane za pomocą funkcji [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) . Always Encrypted używa klucza utworzonego i przechowywanego przez klienta. Klienci mogą przechowywać klucz główny w magazynie certyfikatów systemu Windows, Azure Key Vault lub lokalnych sprzętowych modułach zabezpieczeń. Za pomocą SQL Server Management Studio użytkownicy SQL wybierają klucz, którego chcesz użyć do zaszyfrowania kolumny.

#### <a name="encryption-model-and-key-management-table"></a>Model szyfrowania i tabela zarządzania kluczami

|                                  |                    | **Model szyfrowania i zarządzanie kluczami** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Po stronie serwera przy użyciu klucza zarządzanego przez usługę**     | **Po stronie serwera przy użyciu klucza zarządzanego przez klienta**             | **Po stronie klienta przy użyciu klucza zarządzanego przez klienta**      |
| **AI i Machine Learning**      |                    |                    |                    |
| Azure Cognitive Search           | Tak                | Tak                | -                  |
| Azure Cognitive Services         | Tak                | Tak                | -                  |
| Azure Machine Learning           | Tak                | Tak                | -                  |
| Azure Machine Learning Studio    | Tak                | Wersja zapoznawcza, RSA 2048-bit | -               |
| Content Moderator                | Tak                | Tak                | -                  |
| Rozpoznawanie twarzy                             | Tak                | Tak                | -                  |
| Language Understanding           | Tak                | Tak                | -                  |
| Personalizacja                     | Tak                | Tak                | -                  |
| QnA Maker                        | Tak                | Tak                | -                  |
| Usługi mowy                  | Tak                | Tak                | -                  |
| Tekst usługi Translator                  | Tak                | Tak                | -                  |
| Power BI                         | Tak                | Wersja zapoznawcza, RSA 2048-bit | -                  |
| **Analiza**                    |                    |                    |                    |
| Usługa Azure Stream Analytics           | Tak                | NIE DOTYCZY\*            | -                  |
| Event Hubs                       | Tak                | Tak, wszystkie długości RSA. | -                  |
| Funkcje                        | Tak                | Tak, wszystkie długości RSA. | -                  |
| Azure Analysis Services          | Tak                | -                  | -                  |
| Azure Data Catalog               | Tak                | -                  | -                  |
| Azure HDInsight                  | Tak                | Wszystkie                | -                  |
| Azure Monitor Application Insights | Tak                | Tak                | -                  |
| Azure Monitor Log Analytics      | Tak                | Tak                | -                  |
| Azure Data Explorer              | Tak                | Tak                | -                  |
| Azure Data Factory               | Tak                | Tak                | -                  |
| Azure Data Lake Store            | Tak                | Tak, RSA 2048-bit  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Tak                | Tak                | -                  |
| Container Instances              | Tak                | Tak                | -                  |
| Container Registry               | Tak                | Tak                | -                  |
| **Wystąpienia obliczeniowe**                      |                    |                    |                    |
| Maszyny wirtualne                 | Tak                | Tak, RSA 2048-bit  | -                  |
| Zestaw skalowania maszyn wirtualnych        | Tak                | Tak, RSA 2048-bit  | -                  |
| SAP HANA                         | Tak                | Tak, RSA 2048-bit  | -                  |
| App Service                      | Tak                | Opcję\*\*            | -                  |
| Automatyzacja                       | Tak                | Opcję\*\*            | -                  |
| Azure Functions                  | Tak                | Opcję\*\*            | -                  |
| Azure Portal                     | Tak                | Opcję\*\*            | -                  |
| Logic Apps                       | Tak                | Tak                | -                  |
| Azure Managed Applications       | Tak                | Opcję\*\*            | -                  |
| Service Bus                      | Tak                | Tak                | -                  |
| Site Recovery                    | Tak                | Tak                | -                  |
| **Bazy danych**                    |                    |                    |                    |
| Program SQL Server w usłudze Virtual Machines   | Tak                | Tak, RSA 2048-bit  | Tak                |
| Azure SQL Database               | Tak                | Tak, RSA 2048-bit  | Tak                |
| Azure SQL Database MariaDB   | Tak                | -                  | -                  |
| Azure SQL Database dla programu MySQL     | Tak                | Tak                | -                  |
| Azure SQL Database PostgreSQL | Tak               | Tak                | -                  |
| Azure Synapse Analytics          | Tak                | Tak, RSA 2048-bit  | -                  |
| SQL Server Stretch Database      | Tak                | Tak, RSA 2048-bit  | Tak                |
| Table Storage                    | Tak                | Tak                | Tak                |
| Azure Cosmos DB                  | Tak                | Tak                | -                  |
| Azure Databricks                 | Tak                | Tak                | -                  |
| Azure Database Migration Service | Tak                | NIE DOTYCZY\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Usługa Azure DevOps Services            | Tak                | -                  | Tak                |
| Azure Repos                      | Tak                | -                  | Tak                |
| **Tożsamość**                     |                    |                    |                    |
| Azure Active Directory           | Tak                | -                  | -                  |
| Azure Active Directory Domain Services | Tak          | Tak, RSA 2048-bit  | -                  |
| **Integracja**                  |                    |                    |                    |
| Service Bus                      | Tak                | Tak                | Tak                |
| Event Grid                       | Tak                | -                  | -                  |
| API Management                   | Tak                | -                  | -                  |
| **Usługi IoT**                 |                    |                    |                    |
| Usługa IoT Hub                          | Tak                | Tak                | Tak                |
| IoT Hub Device Provisioning      | Tak                | Tak                | -                  |
| **Zarządzanie i ład**    |                    |                    |                    |
| Azure Site Recovery              | Tak                | -                  | -                  |
| Azure Migrate                    | Tak                | Tak                | -                  |
| **Media**                        |                    |                    |                    |
| Media Services                   | Tak                | -                  | Tak                |
| **Bezpieczeństwo**                     |                    |                    |                    |
| Azure Security Center dla IoT    | Tak                | Tak                | -                  |
| Usługa Azure Sentinel                   | Tak                | Tak                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Tak                | Tak, RSA 2048-bit  | Tak                |
| Blob Storage Premium             | Tak                | Tak, RSA 2048-bit  | Tak                |
| Disk Storage                     | Tak                | Tak                | -                  |
| Ultra Disk Storage               | Tak                | Tak                | -                  |
| Zarządzana Disk Storage             | Tak                | Tak                | -                  |
| File Storage                     | Tak                | Tak, RSA 2048-bit  | -                  |
| Premium Storage pliku             | Tak                | Tak, RSA 2048-bit  | -                  |
| Usługa File Sync                        | Tak                | Tak, RSA 2048-bit  | -                  |
| Queue Storage                    | Tak                | Tak                | Tak                |
| Avere vFXT                       | Tak                | -                  | -                  |
| Azure Cache for Redis            | Tak                | NIE DOTYCZY\*              | -                  |
| Azure NetApp Files               | Tak                | Tak                | -                  |
| Archive Storage                  | Tak                | Tak, RSA 2048-bit  | -                  |
| Magazyn StorSimple                       | Tak                | Tak, RSA 2048-bit  | Tak                |
| Azure Backup                     | Tak                | Tak                | Tak                |
| Data Box                         | Tak                | -                  | Tak                |
| Data Box Edge                    | Tak                | Tak                | -                  |

\*Ta usługa nie utrwala danych. Przejściowe pamięci podręczne (jeśli istnieją) są szyfrowane za pomocą klucza firmy Microsoft.

\*\*Ta usługa obsługuje przechowywanie danych we własnym Key Vault, koncie magazynu lub innej usłudze utrwalania danych, która obsługuje już szyfrowanie po stronie serwera z kluczem zarządzanym przez klienta.

## <a name="conclusion"></a>Podsumowanie

Ochrona danych klienta przechowywanych w ramach usług platformy Azure ma najważniejsze znaczenie dla firmy Microsoft. Wszystkie usługi hostowane na platformie Azure są zobowiązane do zapewniania szyfrowania w opcjach Rest. Usługi platformy Azure obsługują klucze zarządzane przez usługę, klucze zarządzane przez klienta lub szyfrowanie po stronie klienta. Usługi platformy Azure w szerokim zakresie rozszerzają szyfrowanie w stanie spoczynku i w nadchodzących miesiącach są planowane nowe opcje dotyczące wersji zapoznawczej i ogólnej dostępności.
