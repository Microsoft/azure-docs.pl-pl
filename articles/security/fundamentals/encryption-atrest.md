---
title: Szyfrowanie danych platformy Microsoft Azure w spoczynku | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie szyfrowania danych platformy Microsoft Azure w spoczynku, ogólne możliwości i ogólne zagadnienia.
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454683"
---
# <a name="azure-data-encryption-at-rest"></a>Szyfrowanie danych platformy Azure w spoczynku

Platforma Microsoft Azure zawiera narzędzia do ochrony danych zgodnie z potrzebami firmy w zakresie bezpieczeństwa i zgodności. Niniejszy dokument koncentruje się na:

- Jak dane są chronione w spoczynku na platformie Microsoft Azure
- Omawia różne elementy biorące udział we wdrażaniu ochrony danych,
- Przegląda plusy i minusy różnych kluczowych metod ochrony zarządzania.

Szyfrowanie w spoczynku jest typowym wymogiem bezpieczeństwa. Na platformie Azure organizacje mogą szyfrować dane w stanie spoczynku bez ryzyka lub kosztów niestandardowego rozwiązania do zarządzania kluczami. Organizacje mają możliwość umożliwienia platformie Azure całkowitego zarządzania szyfrowaniem w spoczynku. Ponadto organizacje mają różne opcje ścisłego zarządzania kluczami szyfrowania lub szyfrowania.

## <a name="what-is-encryption-at-rest"></a>Co to jest szyfrowanie w spoczynku?

Szyfrowanie w spoczynku jest kodowanie (szyfrowanie) danych, gdy jest utrwalony. Projekty Szyfrowania w spoczynku na platformie Azure używają szyfrowania symetrycznego do szybkiego szyfrowania i odszyfrowywania dużych ilości danych zgodnie z prostym modelem koncepcyjnym:

- Symetryczny klucz szyfrowania jest używany do szyfrowania danych podczas zapisywania ich w magazynie.
- Ten sam klucz szyfrowania jest używany do odszyfrowywania tych danych, ponieważ jest on odczytywany do użycia w pamięci.
- Dane mogą być podzielone na partycje, a dla każdej partycji mogą być używane różne klucze.
- Klucze muszą być przechowywane w bezpiecznej lokalizacji z zasadami kontroli dostępu i inspekcji opartymi na tożsamości. Klucze szyfrowania danych są często szyfrowane za pomocą klucza szyfrowania klucza w usłudze Azure Key Vault w celu dalszego ograniczenia dostępu.

W praktyce kluczowe scenariusze zarządzania i kontroli, a także gwarancje skali i dostępności wymagają dodatkowych konstrukcji. Pojęcia i składniki szyfrowania platformy Microsoft Azure w spoczynku są opisane poniżej.

## <a name="the-purpose-of-encryption-at-rest"></a>Cel szyfrowania w spoczynku

Szyfrowanie w spoczynku zapewnia ochronę danych przechowywanych danych (w stanie spoczynku). Ataki na dane w spoczynku obejmują próby uzyskania fizycznego dostępu do sprzętu, na którym dane są przechowywane, a następnie naruszenia zawartych danych. W takim ataku dysk twardy serwera mógł zostać źle obsłużony podczas konserwacji, umożliwiając osobie atakującej usunięcie dysku twardego. Później osoba atakująca umieściła dysk twardy w komputerze pod kontrolą, aby spróbować uzyskać dostęp do danych.

Szyfrowanie w spoczynku ma na celu uniemożliwienie osobie atakującej dostępu do niezaszyfrowanych danych, zapewniając, że dane są szyfrowane na dysku. Jeśli osoba atakująca uzyska dysk twardy z zaszyfrowanymi danymi, ale nie z kluczami szyfrowania, osoba atakująca musi pokonać szyfrowanie, aby odczytać dane. Ten atak jest znacznie bardziej złożony i zużywa zasoby niż uzyskiwanie dostępu do niezaszyfrowanych danych na dysku twardym. Z tego powodu szyfrowanie w spoczynku jest wysoce zalecane i jest wymogiem o wysokim priorytecie dla wielu organizacji.

Szyfrowanie w spoczynku może być również wymagane przez organizację wymaga zarządzania danymi i wysiłków na rzecz zgodności. Przepisy branżowe i rządowe, takie jak HIPAA, PCI i FedRAMP, określają szczególne zabezpieczenia dotyczące ochrony danych i wymogów szyfrowania. Szyfrowanie w spoczynku jest obowiązkowym środkiem wymaganym do przestrzegania niektórych z tych przepisów. Aby uzyskać więcej informacji na temat podejścia firmy Microsoft do weryfikacji FIPS 140-2, zobacz [Publikacja 140-2 federalnego standardu przetwarzania informacji (FIPS).](https://docs.microsoft.com/microsoft-365/compliance/offering-fips-140-2) 

Oprócz spełnienia wymagań dotyczących zgodności i przepisów, szyfrowanie w spoczynku zapewnia ochronę w szczegółowej ochrony. Platforma Microsoft Azure zapewnia zgodną platformę dla usług, aplikacji i danych. Zapewnia również kompleksowe zabezpieczenia i zabezpieczenia fizyczne, kontrolę dostępu do danych i inspekcję. Jednak ważne jest, aby zapewnić dodatkowe "nakładające się" środki bezpieczeństwa w przypadku, gdy jeden z innych środków bezpieczeństwa nie powiedzie się, a szyfrowanie w spoczynku zapewnia taki środek bezpieczeństwa.

Firma Microsoft zobowiązuje się do szyfrowania w spoczynku opcji w usługach w chmurze i dając klientom kontrolę nad kluczami szyfrowania i dziennikami użycia klucza. Ponadto firma Microsoft domyślnie pracuje nad szyfrowaniem wszystkich danych klientów w stanie spoczynku.

## <a name="azure-encryption-at-rest-components"></a>Szyfrowanie platformy Azure w składnikach spoczynku

Jak opisano wcześniej, celem szyfrowania w spoczynku jest to, że dane, które są utrwalone na dysku, są szyfrowane za pomocą tajnego klucza szyfrowania. Aby osiągnąć ten cel, należy zapewnić bezpieczne tworzenie kluczy, przechowywanie, kontrolę dostępu i zarządzanie kluczami szyfrowania. Chociaż szczegóły mogą się różnić, implementacje szyfrowania usług platformy Azure w spoczynku można opisać w kategoriach zilustrowanych na poniższym diagramie.

![Składniki](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Lokalizacja przechowywania kluczy szyfrowania i kontroli dostępu do tych kluczy jest centralnym elementem modelu szyfrowania w spoczynku. Klucze muszą być wysoce zabezpieczone, ale zarządzane przez określonych użytkowników i dostępne dla określonych usług. W przypadku usług platformy Azure usługa Azure Key Vault jest zalecanym rozwiązaniem magazynu kluczy i zapewnia wspólne środowisko zarządzania w różnych usługach. Klucze są przechowywane i zarządzane w magazynach kluczy, a dostęp do magazynu kluczy może być przyznany użytkownikom lub usługom. Usługa Azure Key Vault obsługuje tworzenie kluczy przez klientów lub importowanie kluczy klientów do użycia w scenariuszach kluczy szyfrowania zarządzanego przez klienta.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Uprawnienia do używania kluczy przechowywanych w usłudze Azure Key Vault, do zarządzania lub dostępu do nich do szyfrowania w spoczynku szyfrowania i odszyfrowywania, mogą być przyznawane do kont usługi Azure Active Directory.

### <a name="key-hierarchy"></a>Hierarchia kluczy

Więcej niż jeden klucz szyfrowania jest używany w implementacji szyfrowania w spoczynku. Przechowywanie klucza szyfrowania w usłudze Azure Key Vault zapewnia bezpieczny dostęp do kluczy i centralne zarządzanie kluczami. Jednak lokalny dostęp do kluczy szyfrowania usługi jest bardziej wydajny w przypadku szyfrowania zbiorczego i odszyfrowywania niż interakcja z magazynem kluczy dla każdej operacji danych, co pozwala na silniejsze szyfrowanie i lepszą wydajność. Ograniczenie użycia pojedynczego klucza szyfrowania zmniejsza ryzyko naruszenia zabezpieczeń klucza i koszt ponownego szyfrowania, gdy klucz musi zostać zastąpiony. Szyfry platformy Azure w spoczynku modele używają hierarchii kluczy składa się z następujących typów kluczy w celu zaspokojenia wszystkich tych potrzeb:

- **Klucz szyfrowania danych (DEK)** — symetryczny klucz AES256 używany do szyfrowania partycji lub bloku danych.  Pojedynczy zasób może mieć wiele partycji i wiele kluczy szyfrowania danych. Szyfrowanie każdego bloku danych za pomocą innego klucza utrudnia ataki analizy kryptograficznej. Dostęp do deks jest potrzebny przez dostawcę zasobów lub wystąpienie aplikacji, który szyfruje i odszyfrowuje określony blok. Gdy DEK jest zastępowany nowym kluczem, tylko dane w skojarzonym z nim bloku muszą być ponownie zaszyfrowane za pomocą nowego klucza.
- **Key Encryption Key (KEK)** — klucz szyfrowania używany do szyfrowania kluczy szyfrowania danych. Użycie klucza szyfrowania klucza, który nigdy nie opuszcza usługi Key Vault, umożliwia szyfrowanie i kontrolowanie kluczy szyfrowania danych. Jednostka, która ma dostęp do KEK może być inna niż jednostka, która wymaga DEK. Jednostka może pośredniczyć dostęp do DEK, aby ograniczyć dostęp każdego identyfikatora DEK do określonej partycji. Ponieważ KEK jest wymagane do odszyfrowania DEKs, KEK jest skutecznie pojedynczy punkt, przez który DEKs mogą być skutecznie usunięte przez usunięcie KEK.

Klucze szyfrowania danych zaszyfrowane za pomocą kluczy szyfrowania kluczy są przechowywane oddzielnie i tylko jednostka z dostępem do klucza szyfrowania klucza może odszyfrować te klucze szyfrowania danych. Obsługiwane są różne modele przechowywania kluczy. Omówimy każdy model bardziej szczegółowo w dalszej części następnej sekcji.

## <a name="data-encryption-models"></a>Modele szyfrowania danych

Zrozumienie różnych modeli szyfrowania i ich zalet i wad jest niezbędne do zrozumienia, jak różni dostawcy zasobów na platformie Azure implementują szyfrowanie w rest. Definicje te są współużytkowane przez wszystkich dostawców zasobów na platformie Azure, aby zapewnić wspólny język i taksonomię.

Istnieją trzy scenariusze szyfrowania po stronie serwera:

- Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę
  - Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania
  - Microsoft zarządza kluczami
  - Pełna funkcjonalność chmury

- Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault
  - Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania
  - Klient steruje kluczami za pośrednictwem usługi Azure Key Vault
  - Pełna funkcjonalność chmury

- Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta na sprzęcie kontrolowanym przez klienta
  - Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania
  - Klient steruje kluczami na sprzęcie sterowanym przez klienta
  - Pełna funkcjonalność chmury

W przypadku szyfrowania po stronie klienta należy wziąć pod uwagę następujące kwestie:

- Usługi platformy Azure nie widzą odszyfrowanych danych
- Klienci zarządzają kluczami lokalnymi i przechowują je lokalnie (lub w innych bezpiecznych sklepach). Klucze nie są dostępne dla usług platformy Azure
- Zmniejszona funkcjonalność chmury

Obsługiwane modele szyfrowania na platformie Azure podzielone na dwie główne grupy: "Szyfrowanie klienta" i "Szyfrowanie po stronie serwera", jak wspomniano wcześniej. Niezależnie od używanego modelu szyfrowania w spoczynku usługi platformy Azure zawsze zalecają korzystanie z bezpiecznego transportu, takiego jak TLS lub HTTPS. W związku z tym szyfrowanie w transporcie powinny być kierowane przez protokół transportu i nie powinny być głównym czynnikiem przy określaniu, który model szyfrowania w spoczynku do użycia.

### <a name="client-encryption-model"></a>Model szyfrowania klienta

Model szyfrowania klienta odnosi się do szyfrowania, które jest wykonywane poza dostawcą zasobów lub platformy Azure przez usługę lub aplikację wywołującą. Szyfrowanie może być wykonywane przez aplikację usługi na platformie Azure lub przez aplikację działającą w centrum danych klienta. W obu przypadkach podczas korzystać z tego modelu szyfrowania dostawcy zasobów platformy Azure odbiera zaszyfrowany obiekt blob danych bez możliwości odszyfrowania danych w jakikolwiek sposób lub mają dostęp do kluczy szyfrowania. W tym modelu zarządzanie kluczami odbywa się przez usługę wywołującą/aplikację i jest nieprzezroczyste dla usługi Platformy Azure.

![Klient](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Model szyfrowania po stronie serwera

Modele szyfrowania po stronie serwera odnoszą się do szyfrowania wykonywanego przez usługę Platformy Azure. W tym modelu dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania. Na przykład usługa Azure Storage może odbierać dane w operacjach zwykłego tekstu i będzie wykonywać szyfrowanie i odszyfrowywanie wewnętrznie. Dostawca zasobów może używać kluczy szyfrowania zarządzanych przez firmę Microsoft lub klienta w zależności od dostarczonej konfiguracji.

![Serwer](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modele zarządzania kluczami szyfrowania po stronie serwera

Każdy z modeli szyfrowania po stronie serwera w spoczynku implikuje charakterystyczne cechy zarządzania kluczami. Obejmuje to, gdzie i jak klucze szyfrowania są tworzone i przechowywane, a także modele dostępu i procedury rotacji kluczy.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę

Dla wielu klientów podstawowym wymogiem jest zapewnienie, że dane są szyfrowane, gdy są w spoczynku. Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę umożliwia ten model, umożliwiając klientom oznaczenie określonego zasobu (konto magazynu, bazy danych SQL itp.) do szyfrowania i pozostawienie wszystkich aspektów zarządzania kluczami, takich jak wydawanie kluczy, rotacja i tworzenie kopii zapasowych w firmie Microsoft. Większość usług platformy Azure, które obsługują szyfrowanie w spoczynku zazwyczaj obsługuje ten model odciążania zarządzania kluczami szyfrowania na platformie Azure. Dostawca zasobów platformy Azure tworzy klucze, umieszcza je w bezpiecznym magazynie i pobiera je w razie potrzeby. Oznacza to, że usługa ma pełny dostęp do kluczy i usługa ma pełną kontrolę nad zarządzaniem cyklem życia poświadczeń.

![Zarządzane](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę w związku z tym szybko rozwiązuje potrzebę szyfrowania w spoczynku z niskim obciążeniem dla klienta. Gdy klient jest dostępny, zazwyczaj otwiera witrynę Azure portal dla docelowego dostawcy subskrypcji i zasobów i sprawdza pole wskazujące, że chcą, aby dane były szyfrowane. W niektórych Menedżerów zasobów szyfrowanie po stronie serwera z kluczami zarządzanymi usługami jest domyślnie włączone.

Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez firmę Microsoft oznacza, że usługa ma pełny dostęp do przechowywania kluczy i zarządzania nimi. Podczas gdy niektórzy klienci mogą chcieć zarządzać kluczami, ponieważ czują, że zyskują większe bezpieczeństwo, koszt i ryzyko związane z niestandardowym rozwiązaniem magazynu kluczy należy wziąć pod uwagę podczas oceny tego modelu. W wielu przypadkach organizacja może określić, że ograniczenia zasobów lub ryzyko rozwiązania lokalnego może być większe niż ryzyko zarządzania szyfrowaniem w chmurze kluczy spoczynku.  Jednak ten model może nie być wystarczający dla organizacji, które mają wymagania do kontrolowania tworzenia lub cyklu życia kluczy szyfrowania lub mieć inny personel zarządzać kluczami szyfrowania usługi niż osoby zarządzające usługą (czyli oddzielenie zarządzania kluczami od ogólnego modelu zarządzania usługą).

##### <a name="key-access"></a>Dostęp do klucza

Gdy używane jest szyfrowanie po stronie serwera z kluczami zarządzanymi usługami, usługa jest zarządzana przez usługę. Zazwyczaj dostawcy zasobów platformy Azure podstawowe będą przechowywać klucze szyfrowania danych w magazynie, który jest blisko danych i szybko dostępne i dostępne, gdy klucze szyfrowania kluczy są przechowywane w bezpiecznym magazynie wewnętrznym.

**Zalety**

- Prosta konfiguracja
- Firma Microsoft zarządza rotacją kluczy, tworzeniem kopii zapasowych i nadmiarowością
- Klient nie ma kosztów związanych z implementacją ani ryzykiem niestandardowego schematu zarządzania kluczami.

**Wady**

- Brak kontroli klienta nad kluczami szyfrowania (specyfikacja klucza, cykl życia, odwołanie itp.)
- Brak możliwości oddzielenia zarządzania kluczami od ogólnego modelu zarządzania usługą

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault

W scenariuszach, w których wymaganiem jest szyfrowanie danych w spoczynku i kontrolowanie kluczy szyfrowania, klienci mogą używać szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta w magazynie kluczy. Niektóre usługi mogą przechowywać tylko główny klucz szyfrowania klucza w usłudze Azure Key Vault i przechowywać zaszyfrowany klucz szyfrowania danych w lokalizacji wewnętrznej bliżej danych. W tym scenariuszu klienci mogą przynieść własne klucze do usługi Key Vault (BYOK — Bring Your Own Key) lub wygenerować nowe i użyć ich do szyfrowania żądanych zasobów. Dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania, ale używa skonfigurowany klucz szyfrowania klucza jako klucza głównego dla wszystkich operacji szyfrowania.

Utrata kluczy szyfrowania oznacza utratę danych. Z tego powodu klucze nie powinny być usuwane. Kopie zapasowe klawiszy powinny być tworzone przy każdym utworzeniu lub obrócenie. [Usuwanie programowe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) powinno być włączone w każdym magazynie przechowując klucze szyfrowania kluczy. Zamiast usuwać klucz, ustaw włączono fałsz lub ustaw datę wygaśnięcia.

##### <a name="key-access"></a>Dostęp do klucza

Model szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta w usłudze Azure Key Vault obejmuje usługę uzyskującą dostęp do kluczy w celu zaszyfrowania i odszyfrowania w razie potrzeby. Szyfrowanie w spoczynku klucze są dostępne dla usługi za pośrednictwem zasad kontroli dostępu. Ta zasada udziela dostępu tożsamości usługi, aby otrzymać klucz. Usługa platformy Azure uruchomiona w imieniu skojarzonej subskrypcji można skonfigurować z tożsamością w tej subskrypcji. Usługa może wykonywać uwierzytelnianie usługi Azure Active Directory i odbierać token uwierzytelniania identyfikujący się jako usługa działająca w imieniu subskrypcji. Token ten można następnie przedstawić do magazynu kluczy, aby uzyskać klucz, do który otrzymał dostęp.

W przypadku operacji przy użyciu kluczy szyfrowania tożsamość usługi może uzyskać dostęp do dowolnej z następujących operacji: odszyfrowywanie, szyfrowanie, rozpakowanie klucza, wrapKey, weryfikowanie, podpisywanie, uzyskiwanie, wyświetlanie, aktualizowanie, tworzenie, importowanie, usuwanie, tworzenie kopii zapasowych i przywracanie.

Aby uzyskać klucz do użycia w szyfrowaniu lub odszyfrowywania danych w spoczynku tożsamości usługi, że wystąpienie usługi Resource Manager będzie działać jako musi mieć UnwrapKey (aby uzyskać klucz do odszyfrowywania) i WrapKey (wstawić klucz do magazynu kluczy podczas tworzenia nowego klucza).

>[!NOTE]
>Aby uzyskać więcej informacji na temat autoryzacji usługi Key Vault, zobacz stronę bezpiecznego magazynu kluczy w [dokumentacji usługi Azure Key Vault](../../key-vault/general/secure-your-key-vault.md).

**Zalety**

- Pełna kontrola nad używanymi kluczami — klucze szyfrowania są zarządzane w magazynie kluczy klienta pod kontrolą klienta.
- Możliwość szyfrowania wielu usług do jednego wzorca
- Może segregować zarządzanie kluczami od ogólnego modelu zarządzania usługą
- Może definiować usługę i kluczową lokalizację w różnych regionach

**Wady**

- Klient ponosi pełną odpowiedzialność za zarządzanie dostępem do kluczy
- Klient ponosi pełną odpowiedzialność za kluczowe zarządzanie cyklem życia
- Dodatkowe obciążenie & konfiguracji instalatora

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w sprzęcie kontrolowanym przez klienta

Niektóre usługi platformy Azure umożliwiają model zarządzania kluczami hosta własnego klucza (HYOK). Ten tryb zarządzania jest przydatny w scenariuszach, w których istnieje potrzeba szyfrowania danych w spoczynku i zarządzania kluczami w zastrzeżonym repozytorium poza kontrolą firmy Microsoft. W tym modelu usługa musi pobrać klucz z lokacji zewnętrznej. Gwarancje wydajności i dostępności mają wpływ, a konfiguracja jest bardziej złożona. Ponadto ponieważ usługa ma dostęp do usługi DEK podczas operacji szyfrowania i odszyfrowywania ogólne gwarancje zabezpieczeń tego modelu są podobne do gdy klucze są zarządzane przez klienta w usłudze Azure Key Vault.  W rezultacie ten model nie jest odpowiedni dla większości organizacji, chyba że mają określone wymagania dotyczące zarządzania kluczami. Z powodu tych ograniczeń większość usług platformy Azure nie obsługuje szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez serwer w sprzęcie kontrolowanym przez klienta.

##### <a name="key-access"></a>Dostęp do klucza

Gdy szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę w sprzęcie kontrolowanym przez klienta są używane, klucze są obsługiwane w systemie skonfigurowanym przez klienta. Usługi platformy Azure, które obsługują ten model zapewniają możliwość ustanowienia bezpiecznego połączenia z magazynem kluczy dostarczonym przez klienta.

**Zalety**

- Pełna kontrola nad używanym kluczem głównym – klucze szyfrowania są zarządzane przez magazyn dostarczony przez klienta
- Możliwość szyfrowania wielu usług do jednego wzorca
- Może segregować zarządzanie kluczami od ogólnego modelu zarządzania usługą
- Może definiować usługę i kluczową lokalizację w różnych regionach

**Wady**

- Pełna odpowiedzialność za przechowywanie kluczy, bezpieczeństwo, wydajność i dostępność
- Pełna odpowiedzialność za zarządzanie dostępem do kluczy
- Pełna odpowiedzialność za kluczowe zarządzanie cyklem życia
- Znaczne koszty konfiguracji, konfiguracji i bieżącej konserwacji
- Zwiększona zależność od dostępności sieci między centrum danych klienta i centrów danych platformy Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Szyfrowanie w spoczynku w usługach w chmurze firmy Microsoft

Usługi Microsoft Cloud są używane we wszystkich trzech modelach chmury: IaaS, PaaS, SaaS. Poniżej znajdują się przykłady, jak pasują do każdego modelu:

- Usługi oprogramowania, określane jako Oprogramowanie jako serwer lub SaaS, które mają aplikację dostarczoną przez chmurę, taką jak Office 365.
- Usługi platformy, które klienci korzystają z chmury w swoich aplikacjach, używając chmury do takich rzeczy jak pamięć masowa, analizy i funkcje magistrali usług.
- Usługi infrastrukturalne lub Infrastruktura jako usługa (IaaS), w których klient wdraża systemy operacyjne i aplikacje hostowane w chmurze i ewentualnie wykorzystuje inne usługi w chmurze.

### <a name="encryption-at-rest-for-saas-customers"></a>Szyfrowanie w spoczynku dla klientów SaaS

Klienci oprogramowania jako usługi (SaaS) zazwyczaj mają włączone lub dostępne szyfrowanie w każdej usłudze. Usługa Office 365 ma kilka opcji weryfikacji lub włączania szyfrowania w spoczynku. Aby uzyskać informacje o usługach Office 365, zobacz [Szyfrowanie w usłudze Office 365](https://docs.microsoft.com/office365/securitycompliance/encryption).

### <a name="encryption-at-rest-for-paas-customers"></a>Szyfrowanie w spoczynku dla klientów PaaS

Dane klienta platformy jako usługi (PaaS) zazwyczaj znajdują się w usłudze magazynu, takiej jak Magazyn obiektów Blob, ale mogą być również buforowane lub przechowywane w środowisku wykonywania aplikacji, takim jak maszyna wirtualna. Aby wyświetlić opcje szyfrowania w spoczynku dostępne dla Ciebie, sprawdź poniższą tabelę dla platform magazynu i aplikacji, których używasz.

### <a name="encryption-at-rest-for-iaas-customers"></a>Szyfrowanie w spoczynku dla klientów IaaS

Klienci infrastruktury jako usługi (IaaS) mogą mieć wiele usług i aplikacji w użyciu. Usługi IaaS mogą włączyć szyfrowanie w spoczynku na obsługiwanych maszynach wirtualnych platformy Azure i dyskach wirtualnych przy użyciu szyfrowania dysków platformy Azure.

#### <a name="encrypted-storage"></a>Szyfrowana pamięć masowa

Podobnie jak PaaS, rozwiązania IaaS mogą korzystać z innych usług platformy Azure, które przechowują dane zaszyfrowane w spoczynku. W takich przypadkach można włączyć obsługę szyfrowania w spoczynku, zgodnie z każdą zużytą usługą platformy Azure. Poniższa tabela wylicza główne platformy magazynu, usług i aplikacji oraz model szyfrowania w spoczynku obsługiwane. 

#### <a name="encrypted-compute"></a>Zaszyfrowane obliczenia

Wszystkie dyski zarządzane, migawki i obrazy są szyfrowane przy użyciu szyfrowania usługi magazynu przy użyciu klucza zarządzanego przez usługę. Bardziej kompletne rozwiązanie szyfrowania w spoczynku gwarantuje, że dane nigdy nie są utrwalone w postaci niezaszyfrowanej. Podczas przetwarzania danych na maszynie wirtualnej dane mogą być utrwalane w pliku stronicowanym systemu Windows lub pliku wymiany systemu Linux, zrzucie awaryjnym lub w dzienniku aplikacji. Aby upewnić się, że te dane są szyfrowane w spoczynku, aplikacje IaaS mogą używać szyfrowania dysków platformy Azure na maszynie wirtualnej usługi Azure IaaS (Windows lub Linux) i dysku wirtualnym.

#### <a name="custom-encryption-at-rest"></a>Szyfrowanie niestandardowe w spoczynku

Zaleca się, aby w miarę możliwości aplikacje IaaS korzystać z opcji szyfrowania i szyfrowania dysków platformy Azure w spoczynku dostępnych przez wszystkie używane usługi platformy Azure. W niektórych przypadkach, takich jak nieregularne wymagania szyfrowania lub magazynu opartego na platformie Azure, deweloper aplikacji IaaS może być konieczne wdrożenie szyfrowania w spoczynku siebie. Deweloperzy rozwiązań IaaS mogą lepiej integrować się z zarządzaniem platformą Azure i oczekiwaniami klientów, wykorzystując niektóre składniki platformy Azure. W szczególności deweloperzy powinni używać usługi Azure Key Vault, aby zapewnić bezpieczne przechowywanie kluczy, a także zapewnić swoim klientom spójne opcje zarządzania kluczami z większością usług platformy Azure. Ponadto rozwiązania niestandardowe powinny używać tożsamości usługi zarządzanej przez platformę Azure, aby umożliwić kontom usługi dostęp do kluczy szyfrowania. Aby uzyskać informacje o deweloperach dotyczących tożsamości usługi Azure Key Vault i managed service, zobacz ich odpowiednie zestawy SDK.

## <a name="azure-resource-providers-encryption-model-support"></a>Obsługa modeli szyfrowania dostawców zasobów platformy Azure

Usługi Platformy Microsoft Azure obsługują co najmniej jeden model szyfrowania w spoczynku. Jednak w przypadku niektórych usług jeden lub więcej modeli szyfrowania może nie mieć zastosowania. W przypadku usług obsługujących kluczowe scenariusze zarządzane przez klienta mogą obsługiwać tylko podzbiór typów kluczy obsługiwanych przez usługę Azure Key Vault dla kluczy szyfrowania kluczy. Ponadto usługi mogą zwolnić obsługę tych scenariuszy i typów kluczy w różnych harmonogramach. W tej sekcji opisano obsługę szyfrowania w spoczynku w momencie pisania tego zapisu dla każdej z głównych usług magazynu danych platformy Azure.

### <a name="azure-disk-encryption"></a>Szyfrowanie dysków platformy Azure

Każdy klient korzystający z funkcji infrastruktury platformy Azure jako usługi (IaaS) może uzyskać szyfrowanie w spoczynku dla swoich maszyn wirtualnych i dysków IaaS za pośrednictwem szyfrowania dysków azure. Aby uzyskać więcej informacji na temat szyfrowania na dysku Azure, zobacz [dokumentację szyfrowania dysków platformy Azure](../azure-security-disk-encryption-overview.md).

#### <a name="azure-storage"></a>Azure Storage

Wszystkie usługi usługi Azure Storage (magazyn obiektów Blob, magazyn kolejek, magazyn tabel i pliki platformy Azure) obsługują szyfrowanie po stronie serwera w spoczynku; niektóre usługi dodatkowo obsługują klucze zarządzane przez klienta i szyfrowanie po stronie klienta. 

- Po stronie serwera: wszystkie usługi Azure Storage Services domyślnie umożliwiają szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę, która jest przezroczysta dla aplikacji. Aby uzyskać więcej informacji, zobacz [Szyfrowanie usługi Azure Storage Service dla danych w spoczynku](../../storage/common/storage-service-encryption.md). Usługa Azure Blob storage i usługi Azure Files obsługują również 2048-bitowe klucze zarządzane przez klienta rsa w usłudze Azure Key Vault. Aby uzyskać więcej informacji, zobacz [Szyfrowanie usługi magazynu przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](../../storage/common/storage-encryption-keys-portal.md).
- Po stronie klienta: obiekty Blobs platformy Azure, tabele i kolejki obsługują szyfrowanie po stronie klienta. Podczas korzystania z szyfrowania po stronie klienta klienci szyfrują dane i przekazują dane jako zaszyfrowany obiekt blob. Zarządzanie kluczami jest wykonywane przez klienta. Aby uzyskać więcej informacji, zobacz [Szyfrowanie po stronie klienta i usługa Azure Key Vault dla usługi Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md).

#### <a name="azure-sql-database"></a>Azure SQL Database

Usługa Azure SQL Database obsługuje obecnie szyfrowanie w spoczynku dla scenariuszy szyfrowania po stronie usługi zarządzanej przez firmę Microsoft i po stronie klienta.

Obsługa szyfrowania serwera jest obecnie świadczona za pośrednictwem funkcji SQL o nazwie Transparent Data Encryption. Gdy klient usługi Azure SQL Database włącza klucz TDE są automatycznie tworzone i zarządzane dla nich. Szyfrowanie w spoczynku można włączyć na poziomie bazy danych i serwera. Od czerwca 2017 r. [przezroczyste szyfrowanie danych (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) jest domyślnie włączone w nowo utworzonych bazach danych. Usługa Azure SQL Database obsługuje 2048-bitowe klucze zarządzane przez klienta rsa w usłudze Azure Key Vault. Aby uzyskać więcej informacji, zobacz [Przezroczyste szyfrowanie danych z obsługą przynieś własny klucz dla bazy danych SQL Azure i magazynu danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current).

Szyfrowanie po stronie klienta danych usługi Azure SQL Database jest obsługiwane za pośrednictwem funkcji [Zawsze szyfrowane.](https://msdn.microsoft.com/library/mt163865.aspx) Zawsze zaszyfrowane używa klucza, który został utworzony i przechowywany przez klienta. Klienci mogą przechowywać klucz główny w magazynie certyfikatów systemu Windows, usłudze Azure Key Vault lub lokalnym module zabezpieczeń sprzętu. Korzystając z programu SQL Server Management Studio, użytkownicy SQL wybierają klucz, którego chcieliby użyć do szyfrowania kolumny.

#### <a name="encryption-model-and-key-management-table"></a>Tabela zarządzania modelem szyfrowania i kluczami

|                                  |                    | **Zarządzanie modelami szyfrowania i kluczami** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Po stronie serwera przy użyciu klucza zarządzanego przez usługę**     | **Po stronie serwera przy użyciu klucza zarządzanego przez klienta**             | **Po stronie klienta przy użyciu klucza zarządzanego przez klienta**      |
| **AI i uczenie maszynowe**      |                    |                    |                    |
| Azure Cognitive Search           | Tak                | Tak                | -                  |
| Azure Cognitive Services         | Tak                | Tak                | -                  |
| Azure Machine Learning           | Tak                | Tak                | -                  |
| Azure Machine Learning Studio    | Tak                | Podgląd, RSA 2048-bit | -               |
| Content Moderator                | Tak                | Tak                | -                  |
| Rozpoznawanie twarzy                             | Tak                | Tak                | -                  |
| Language Understanding           | Tak                | Tak                | -                  |
| Personalizacja                     | Tak                | Tak                | -                  |
| QnA Maker                        | Tak                | Tak                | -                  |
| Usługi mowy                  | Tak                | Tak                | -                  |
| Tekst usługi Translator                  | Tak                | Tak                | -                  |
| Power BI                         | Tak                | Podgląd, RSA 2048-bit | -                  |
| **Analiza**                    |                    |                    |                    |
| Usługa Azure Stream Analytics           | Tak                | Nie dotyczy\*            | -                  |
| Event Hubs                       | Tak                | Tak, wszystkie długości RSA. | -                  |
| Funkcje                        | Tak                | Tak, wszystkie długości RSA. | -                  |
| Azure Analysis Services          | Tak                | -                  | -                  |
| Azure Data Catalog               | Tak                | -                  | -                  |
| Azure HDInsight                  | Tak                | Wszystkie                | -                  |
| Azure Monitor Application Insights | Tak                | Tak                | -                  |
| Analiza dzienników monitora platformy Azure      | Tak                | Tak                | -                  |
| Azure Data Explorer              | Tak                | Tak                | -                  |
| Azure Data Factory               | Tak                | Tak                | -                  |
| Azure Data Lake Store            | Tak                | Tak, RSA 2048-bit  | -                  |
| **Kontenery**                   |                    |                    |                    |
| Azure Kubernetes Service         | Tak                | Tak                | -                  |
| Container Instances              | Tak                | Tak                | -                  |
| Container Registry               | Tak                | Tak                | -                  |
| **Wystąpienia obliczeniowe**                      |                    |                    |                    |
| Maszyny wirtualne                 | Tak                | Tak, RSA 2048-bit  | -                  |
| Zestaw skalowania maszyny wirtualnej        | Tak                | Tak, RSA 2048-bit  | -                  |
| SAP HANA                         | Tak                | Tak, RSA 2048-bit  | -                  |
| App Service                      | Tak                | Tak\*\*            | -                  |
| Automatyzacja                       | Tak                | Tak\*\*            | -                  |
| Azure Functions                  | Tak                | Tak\*\*            | -                  |
| Azure Portal                     | Tak                | Tak\*\*            | -                  |
| Logic Apps                       | Tak                | Tak                | -                  |
| Azure Managed Applications       | Tak                | Tak\*\*            | -                  |
| Service Bus                      | Tak                | Tak                | -                  |
| Site Recovery                    | Tak                | Tak                | -                  |
| **Bazy danych**                    |                    |                    |                    |
| Program SQL Server w usłudze Virtual Machines   | Tak                | Tak, RSA 2048-bit  | Tak                |
| Azure SQL Database               | Tak                | Tak, RSA 2048-bit  | Tak                |
| Azure SQL Database for MariaDB   | Tak                | -                  | -                  |
| Azure SQL Database for MySQL     | Tak                | Tak                | -                  |
| Azure SQL Database for PostgreSQL | Tak               | Tak                | -                  |
| Azure Synapse Analytics          | Tak                | Tak, RSA 2048-bit  | -                  |
| SQL Server Stretch Database      | Tak                | Tak, RSA 2048-bit  | Tak                |
| Table Storage                    | Tak                | Tak                | Tak                |
| Azure Cosmos DB                  | Tak                | Tak                | -                  |
| Azure Databricks                 | Tak                | Tak                | -                  |
| Azure Database Migration Service | Tak                | Nie dotyczy\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Usługa Azure DevOps Services            | Tak                | -                  | Tak                |
| Azure Repos                      | Tak                | -                  | Tak                |
| **Tożsamość**                     |                    |                    |                    |
| Usługa Azure Active Directory           | Tak                | -                  | -                  |
| Azure Active Directory Domain Services | Tak          | Tak, RSA 2048-bit  | -                  |
| **Integracja**                  |                    |                    |                    |
| Service Bus                      | Tak                | Tak                | Tak                |
| Event Grid                       | Tak                | -                  | -                  |
| API Management                   | Tak                | -                  | -                  |
| **Usługi IoT**                 |                    |                    |                    |
| Usługa IoT Hub                          | Tak                | Tak                | Tak                |
| Inicjowanie obsługi administracyjnej urządzeń usługi IoT Hub      | Tak                | Tak                | -                  |
| **Zarządzanie i ład**    |                    |                    |                    |
| Azure Site Recovery              | Tak                | -                  | -                  |
| Azure Migrate                    | Tak                | Tak                | -                  |
| **Media**                        |                    |                    |                    |
| Media Services                   | Tak                | -                  | Tak                |
| **Zabezpieczenia**                     |                    |                    |                    |
| Azure Security Center dla IoT    | Tak                | Tak                | -                  |
| Usługa Azure Sentinel                   | Tak                | Tak                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Tak                | Tak, RSA 2048-bit  | Tak                |
| Magazyn obiektów blob premium             | Tak                | Tak, RSA 2048-bit  | Tak                |
| Disk Storage                     | Tak                | Tak                | -                  |
| Pamięć dyskowa ultra dysków               | Tak                | Tak                | -                  |
| Magazyn dysku zarządzanego             | Tak                | Tak                | -                  |
| File Storage                     | Tak                | Tak, RSA 2048-bit  | -                  |
| Magazyn w wersji premium plików             | Tak                | Tak, RSA 2048-bit  | -                  |
| Usługa File Sync                        | Tak                | Tak, RSA 2048-bit  | -                  |
| Queue Storage                    | Tak                | Tak                | Tak                |
| Avere vFXT                       | Tak                | -                  | -                  |
| Azure Cache for Redis            | Tak                | Nie dotyczy\*              | -                  |
| Azure NetApp Files               | Tak                | Tak                | -                  |
| Archive Storage                  | Tak                | Tak, RSA 2048-bit  | -                  |
| Magazyn StorSimple                       | Tak                | Tak, RSA 2048-bit  | Tak                |
| Azure Backup                     | Tak                | Tak                | Tak                |
| Data Box                         | Tak                | -                  | Tak                |
| Data Box Edge                    | Tak                | Tak                | -                  |

\*Ta usługa nie utrwala danych. Przejściowe pamięci podręczne, jeśli istnieją, są szyfrowane za pomocą klucza firmy Microsoft.

\*\*Ta usługa obsługuje przechowywanie danych we własnej przechowalni kluczy, koncie magazynu lub innej utrwaleniu danych usługi, która obsługuje już szyfrowanie po stronie serwera za pomocą klucza zarządzanego przez klienta.

## <a name="conclusion"></a>Podsumowanie

Ochrona danych klientów przechowywanych w usługach platformy Azure ma ogromne znaczenie dla firmy Microsoft. Wszystkie usługi hostowane platformy Azure są zobowiązane do zapewnienia szyfrowania w spoczynku opcji. Usługi platformy Azure obsługują klucze zarządzane usługami, klucze zarządzane przez klienta lub szyfrowanie po stronie klienta. Usługi platformy Azure znacznie zwiększają dostępność szyfrowania w spoczynku, a w nadchodzących miesiącach planowane są nowe opcje w wersji zapoznawczej i ogólnej dostępności.
