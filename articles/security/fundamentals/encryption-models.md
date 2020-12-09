---
title: Modele szyfrowania danych w Microsoft Azure
description: Ten artykuł zawiera omówienie modeli szyfrowania danych w programie Microsoft Azure.
services: security
documentationcenter: na
author: msmbaldwin
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mbaldwin
ms.openlocfilehash: 141daa485fae5aba2db23647fada30ba5b621cd0
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854618"
---
# <a name="data-encryption-models"></a>Modele szyfrowania danych

Zrozumienie różnych modeli szyfrowania i ich zalet i wad jest niezbędne do poznania sposobu, w jaki różne dostawcy zasobów na platformie Azure implementują szyfrowanie w spoczynku. Te definicje są udostępniane wszystkim dostawcom zasobów na platformie Azure w celu zapewnienia wspólnego języka i taksonomii.

Istnieją trzy scenariusze dotyczące szyfrowania po stronie serwera:

- Szyfrowanie po stronie serwera przy użyciu kluczy Service-Managed
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

Modele szyfrowania po stronie serwera odnoszą się do szyfrowania, które jest wykonywane przez usługę platformy Azure. W tym modelu dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania. Na przykład usługa Azure Storage może odbierać dane w postaci zwykłego tekstu i przeprowadzi wewnętrznie szyfrowanie i odszyfrowywanie. Dostawca zasobów może korzystać z kluczy szyfrowania, które są zarządzane przez firmę Microsoft lub przez klienta w zależności od podanej konfiguracji.

![Serwer](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Każde szyfrowanie po stronie serwera w modelach REST implikuje charakterystyczne charakterystyki zarządzania kluczami. Obejmuje to miejsce i sposób tworzenia kluczy szyfrowania oraz ich przechowywanie oraz modeli dostępu oraz procedur rotacji kluczy.  

W przypadku szyfrowania po stronie klienta należy wziąć pod uwagę następujące kwestie:

- Usługi platformy Azure nie mogą zobaczyć odszyfrowanych danych
- Klienci zarządzają kluczami lokalnymi (lub w innych magazynach bezpiecznych) i przechowują je. Klucze nie są dostępne dla usług platformy Azure
- Zmniejszona funkcjonalność chmury

Obsługiwane modele szyfrowania na platformie Azure dzielą się na dwie główne grupy: "szyfrowanie klienta" i "szyfrowanie po stronie serwera" wymienione wcześniej. Niezależnie od szyfrowania w używanym modelu REST usługi platformy Azure zawsze zalecają korzystanie z bezpiecznego transportu, takiego jak TLS lub HTTPS. W związku z tym szyfrowanie w transporcie powinno być rozkierowane przez protokół transportowy i nie powinno być głównym czynnikiem określającym, które szyfrowanie w modelu REST ma być używane.

## <a name="client-encryption-model"></a>Model szyfrowania klienta

Model szyfrowania klienta odnosi się do szyfrowania, które jest wykonywane poza dostawcą zasobów lub na platformie Azure przez usługę lub aplikację wywołującą. Szyfrowanie może być wykonywane przez aplikację usługi na platformie Azure lub przez aplikację działającą w centrum danych klienta. W obu przypadkach, gdy korzystasz z tego modelu szyfrowania, dostawca zasobów platformy Azure odbiera zaszyfrowany obiekt BLOB danych bez możliwości odszyfrowania danych w dowolny sposób lub mieć dostęp do kluczy szyfrowania. W tym modelu zarządzanie kluczami odbywa się przez usługę wywołującą/aplikację i nieprzezroczystą dla usługi platformy Azure.

![Klient](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę

Dla wielu klientów zasadniczy wymóg polega na zapewnieniu, że dane są szyfrowane w każdym miejscu. Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę umożliwia klientom oznaczenie określonego zasobu (konta magazynu, bazy danych SQL itp.) w celu szyfrowania i pozostawienie wszystkich aspektów zarządzania kluczami, takich jak wystawianie kluczy, rotacja i wykonywanie kopii zapasowych w firmie Microsoft. Większość usług platformy Azure, które obsługują szyfrowanie w spoczynku, zazwyczaj obsługuje ten model odciążający zarządzanie kluczami szyfrowania na platformie Azure. Dostawca zasobów platformy Azure tworzy klucze, umieszcza je w bezpiecznym magazynie i pobiera je w razie konieczności. Oznacza to, że usługa ma pełny dostęp do kluczy, a usługa ma pełną kontrolę nad zarządzaniem cyklem życia poświadczeń.

![zarządzanych](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę w związku z tym szybko eliminuje konieczność szyfrowania przy niskim obciążeniu klienta. Gdy klient jest dostępny, zwykle otwiera Azure Portal dla docelowej subskrypcji i dostawcy zasobów i sprawdza pole wskazujące, że dane mają być szyfrowane. W przypadku niektórych menedżerów zasobów szyfrowanie po stronie serwera z kluczami zarządzanymi przez usługę jest domyślnie włączone.

Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez firmę Microsoft oznacza, że usługa ma pełny dostęp do przechowywania kluczy i zarządzania nimi. Niektórzy klienci mogą chcieć zarządzać kluczami, ponieważ mogą one mieć większe bezpieczeństwo, ale koszt i ryzyko związane z niestandardowym rozwiązaniem magazynu kluczy należy wziąć pod uwagę podczas oceniania tego modelu. W wielu przypadkach organizacja może określić, że ograniczenia zasobów lub ryzyko związane z rozwiązaniem lokalnym mogą być większe niż ryzyko związane z zarządzaniem przez chmurę szyfrowania w kluczach Rest.  Jednak ten model może być niewystarczający dla organizacji, które mają wymagania umożliwiające kontrolę tworzenia lub cyklu życia kluczy szyfrowania lub posiadania innego personelu do zarządzania kluczami szyfrowania usługi, niż w przypadku zarządzania usługą (to znaczy segregowanie klucza zarządzania z ogólnego modelu zarządzania usługi).

### <a name="key-access"></a>Dostęp do klucza

Gdy używane jest szyfrowanie po stronie serwera z kluczami zarządzanymi przez usługę, zarządzanie kluczami, magazynem i dostępem do usługi jest zarządzane przez usługę. Zazwyczaj podstawowi dostawcy zasobów platformy Azure będą przechowywać klucze szyfrowania danych w magazynie, który jest blisko danych i szybko dostępne i dostępne, gdy klucze szyfrowania kluczy są przechowywane w bezpiecznym magazynie wewnętrznym.

**Zalety**

- Prosta konfiguracja
- Firma Microsoft zarządza rotacją, tworzeniem kopii zapasowej i nadmiarowością.
- Klient nie ma kosztu związanego z implementacją lub ryzykiem niestandardowego schematu zarządzania kluczami.

**Wady**

- Brak kontroli klienta nad kluczami szyfrowania (Specyfikacja klucza, cykl życia, odwołanie itp.)
- Brak możliwości oddzielenia zarządzania kluczami od ogólnego modelu zarządzania dla usługi

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault

W przypadku scenariuszy, w których wymagane jest szyfrowanie danych w stanie spoczynku i sterowanie kluczami szyfrowania, klienci mogą korzystać z szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta w Key Vault. Niektóre usługi mogą przechowywać tylko klucz szyfrowania klucza głównego w Azure Key Vault i przechowywać zaszyfrowany klucz szyfrowania danych w wewnętrznej lokalizacji zbliżonej do danych. W tym scenariuszu klienci mogą przenosić własne klucze do Key Vault (BYOK – Bring Your Own Key) lub generować nowe i używać ich do szyfrowania żądanych zasobów. Mimo że dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania, używa skonfigurowanego klucza szyfrowania klucza jako klucza głównego dla wszystkich operacji szyfrowania.

Utrata kluczy szyfrowania kluczy oznacza utratę danych. Z tego powodu klucze nie powinny być usuwane. Należy utworzyć kopię zapasową kluczy, gdy zostały utworzone lub obrócone. [Usuwanie nietrwałe](../../key-vault/general/soft-delete-overview.md) powinno być włączone na dowolnym magazynie przechowującym klucze szyfrowania kluczy. Zamiast usuwać klucz, ustaw wartość włączone na false lub ustaw datę wygaśnięcia.

### <a name="key-access"></a>Dostęp do klucza

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

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w sprzęcie kontrolowanym przez klienta

Niektóre usługi platformy Azure zapewniają własny model zarządzania kluczami (HYOK) na hoście. Ten tryb zarządzania jest użyteczny w scenariuszach, w których istnieje potrzeba zaszyfrowania danych przechowywanych w pamięci i zarządzania kluczami w repozytorium zastrzeżonym poza kontrolą firmy Microsoft. W tym modelu usługa musi pobrać klucz z zewnętrznej lokacji. Wpływ na gwarancje dotyczące wydajności i dostępności, a konfiguracja jest bardziej skomplikowana. Ponadto, ponieważ usługa ma dostęp do klucza szyfrowania danych w trakcie operacji odszyfrowywania i deszyfrowania, ogólne gwarancje bezpieczeństwa tego modelu są podobne do tego, kiedy klucze są zarządzane przez klienta w Azure Key Vault.  W związku z tym ten model nie jest odpowiedni dla większości organizacji, chyba że mają określone wymagania dotyczące zarządzania kluczami. Ze względu na te ograniczenia większość usług platformy Azure nie obsługuje szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta.

### <a name="key-access"></a>Dostęp do klucza

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

## <a name="supporting-services"></a>Usługi pomocnicze
Usługi platformy Azure, które obsługują każdy model szyfrowania:

| Produkt, funkcja lub usługa | Server-Side przy użyciu klucza Service-Managed   | Server-Side przy użyciu klucza Customer-Managed | Client-Side przy użyciu klucza Client-Managed  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **Sztuczna inteligencja i uczenie maszynowe**      |                    |                    |                    |
| Azure Cognitive Search           | Tak                | Tak                | -                  |
| Azure Cognitive Services         | Tak                | Tak                | -                  |
| Azure Machine Learning           | Tak                | Tak                | -                  |
| Azure Machine Learning Studio (klasyczny) | Tak         | Wersja zapoznawcza, RSA 2048-bit | -               |
| Content Moderator                | Tak                | Tak                | -                  |
| Rozpoznawanie twarzy                             | Tak                | Tak                | -                  |
| Language Understanding           | Tak                | Tak                | -                  |
| Personalizacja                     | Tak                | Tak                | -                  |
| QnA Maker                        | Tak                | Tak                | -                  |
| Usługi mowy                  | Tak                | Tak                | -                  |
| Tłumaczenie tekstu w usłudze Translator                  | Tak                | Tak                | -                  |
| Power BI                         | Tak                | Tak, RSA 4096-bit  | -                  |
| **Analiza**                    |                    |                    |                    |
| Usługa Azure Stream Analytics           | Tak                | Opcję\*\*            | -                  |
| Event Hubs                       | Tak                | Tak                | -                  |
| Funkcje                        | Tak                | Tak                | -                  |
| Azure Analysis Services          | Tak                | -                  | -                  |
| Azure Data Catalog               | Tak                | -                  | -                  |
| Azure HDInsight                  | Tak                | Wszystko                | -                  |
| Azure Monitor Application Insights | Tak                | Tak                | -                  |
| Azure Monitor Log Analytics      | Tak                | Tak                | -                  |
| Azure Data Explorer              | Tak                | Tak                | -                  |
| Azure Data Factory               | Tak                | Tak                | -                  |
| Azure Data Lake Store            | Tak                | Tak, RSA 2048-bit  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Tak                | Tak                | -                  |
| Container Instances              | Tak                | Tak                | -                  |
| Container Registry               | Tak                | Tak                | -                  |
| **Środowisko obliczeniowe**                      |                    |                    |                    |
| Virtual Machines                 | Tak                | Tak                | -                  |
| Zestaw skalowania maszyn wirtualnych        | Tak                | Tak                | -                  |
| SAP HANA                         | Tak                | Tak                | -                  |
| App Service                      | Tak                | Opcję\*\*            | -                  |
| Automation                       | Tak                | Opcję\*\*            | -                  |
| Azure Functions                  | Tak                | Opcję\*\*            | -                  |
| Azure Portal                     | Tak                | Opcję\*\*            | -                  |
| Logic Apps                       | Tak                | Tak                | -                  |
| Aplikacje zarządzane przez platformę Azure       | Tak                | Opcję\*\*            | -                  |
| Service Bus                      | Tak                | Tak                | -                  |
| Site Recovery                    | Tak                | Tak                | -                  |
| **Bazy danych**                    |                    |                    |                    |
| Program SQL Server w usłudze Virtual Machines   | Tak                | Tak                | Tak                |
| Azure SQL Database               | Tak                | Tak, RSA 3072-bit  | Tak                |
| Azure SQL Database MariaDB   | Tak                | -                  | -                  |
| Azure SQL Database dla programu MySQL     | Tak                | Tak                | -                  |
| Azure SQL Database PostgreSQL | Tak               | Tak                | -                  |
| Azure Synapse Analytics          | Tak                | Tak, RSA 3072-bit  | -                  |
| SQL Server Stretch Database      | Tak                | Tak, RSA 3072-bit  | Tak                |
| Table Storage                    | Tak                | Tak                | Tak                |
| Azure Cosmos DB                  | Tak                | Tak                | -                  |
| Azure Databricks                 | Tak                | Tak                | -                  |
| Azure Database Migration Service | Tak                | NIE DOTYCZY\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Usługa Azure DevOps Services            | Tak                | -                  | Tak                |
| Azure Repos                      | Tak                | -                  | Tak                |
| **Tożsamość**                     |                    |                    |                    |
| Azure Active Directory           | Tak                | -                  | -                  |
| Azure Active Directory Domain Services | Tak          | Tak                | -                  |
| **Integracja**                  |                    |                    |                    |
| Service Bus                      | Tak                | Tak                | Tak                |
| Event Grid                       | Tak                | -                  | -                  |
| API Management                   | Tak                | -                  | -                  |
| **Usługi IoT**                 |                    |                    |                    |
| Usługa IoT Hub                          | Tak                | Tak                | Tak                |
| IoT Hub Device Provisioning      | Tak                | Tak                | -                  |
| **Zarządzanie i nadzór**    |                    |                    |                    |
| Azure Site Recovery              | Tak                | -                  | -                  |
| Azure Migrate                    | Tak                | Tak                | -                  |
| **Media**                        |                    |                    |                    |
| Media Services                   | Tak                | Tak                | Tak                |
| **Bezpieczeństwo**                     |                    |                    |                    |
| Azure Security Center dla IoT    | Tak                | Tak                | -                  |
| Usługa Azure Sentinel                   | Tak                | Tak                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Yes                | Tak                | Tak                |
| Blob Storage Premium             | Tak                | Tak                | Tak                |
| Disk Storage                     | Tak                | Tak                | -                  |
| Ultra Disk Storage               | Tak                | Tak                | -                  |
| Zarządzana Disk Storage             | Tak                | Tak                | -                  |
| File Storage                     | Yes                | Tak                | -                  |
| Premium Storage pliku             | Tak                | Tak                | -                  |
| Usługa File Sync                        | Tak                | Tak                | -                  |
| Queue Storage                    | Tak                | Tak                | Tak                |
| Avere vFXT                       | Tak                | -                  | -                  |
| Azure Cache for Redis            | Tak                | NIE DOTYCZY\*              | -                  |
| Azure NetApp Files               | Tak                | Tak                | -                  |
| Archive Storage                  | Tak                | Tak                | -                  |
| StorSimple                       | Tak                | Tak                | Tak                |
| Azure Backup                     | Tak                | Tak                | Tak                |
| Data Box                         | Tak                | -                  | Tak                |
| Data Box Edge                    | Tak                | Tak                | -                  |

\* Ta usługa nie utrwala danych. Przejściowe pamięci podręczne (jeśli istnieją) są szyfrowane za pomocą klucza firmy Microsoft.

\*\* Ta usługa obsługuje przechowywanie danych we własnym Key Vault, koncie magazynu lub innej usłudze utrwalania danych, która obsługuje już Server-Side szyfrowanie z kluczem Customer-Managed.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [szyfrowanie jest używane na platformie Azure](encryption-overview.md).
- Dowiedz się, w jaki sposób platforma Azure używa [podwójnego szyfrowania](double-encryption.md) , aby ograniczyć zagrożenia związane z szyfrowaniem danych.
