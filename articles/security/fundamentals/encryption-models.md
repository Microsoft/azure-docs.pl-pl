---
title: Modele szyfrowania danych w Microsoft Azure
description: Ten artykuł zawiera omówienie modeli szyfrowania danych w usługach Microsoft Azure.
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
ms.openlocfilehash: 95ab5917779a73b7221a5b431126164aef88b494
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812124"
---
# <a name="data-encryption-models"></a>Modele szyfrowania danych

Zrozumienie różnych modeli szyfrowania oraz ich zalet i wad jest niezbędne do zrozumienia, w jaki sposób różni dostawcy zasobów na platformie Azure implementują szyfrowanie w spoczynku. Definicje te są udostępniane wszystkim dostawcom zasobów na platformie Azure w celu zapewnienia wspólnego języka i taksonomii.

Istnieją trzy scenariusze szyfrowania po stronie serwera:

- Szyfrowanie po stronie serwera przy użyciu kluczy Service-Managed serwera
  - Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania
  - Firma Microsoft zarządza kluczami
  - Pełna funkcjonalność chmury

- Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w Azure Key Vault
  - Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania
  - Klient kontroluje klucze za pośrednictwem Azure Key Vault
  - Pełna funkcjonalność chmury

- Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta na sprzęcie kontrolowanym przez klienta
  - Dostawcy zasobów platformy Azure wykonują operacje szyfrowania i odszyfrowywania
  - Klient kontroluje klucze na sprzęcie kontrolowanym przez klienta
  - Pełna funkcjonalność chmury

Modele szyfrowania po stronie serwera odnoszą się do szyfrowania wykonywanego przez usługę platformy Azure. W tym modelu dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania. Na przykład usługa Azure Storage może odbierać dane w operacjach zwykłego tekstu i będzie wykonywać wewnętrzne szyfrowanie i odszyfrowywanie. Dostawca zasobów może używać kluczy szyfrowania zarządzanych przez firmę Microsoft lub klienta w zależności od podanej konfiguracji.

![Serwer](./media/encryption-models/azure-security-encryption-atrest-fig3.png)

Każdy z modeli szyfrowania w spoczynku po stronie serwera implikuje charakterystykę charakterystyczną zarządzania kluczami. Obejmuje to miejsce i sposób tworzenia i przechowywania kluczy szyfrowania, a także modele dostępu i procedury rotacji kluczy.  

W przypadku szyfrowania po stronie klienta należy wziąć pod uwagę następujące kwestie:

- Usługi platformy Azure nie widzą odszyfrowanych danych
- Klienci zarządzają kluczami i przechowują je lokalnie (lub w innych bezpiecznych magazynach). Klucze nie są dostępne dla usług platformy Azure
- Ograniczona funkcjonalność chmury

Obsługiwane modele szyfrowania na platformie Azure zostały podzielone na dwie główne grupy: "Szyfrowanie klienta" i "Szyfrowanie po stronie serwera", jak wspomniano wcześniej. Niezależnie od używanego modelu szyfrowania w spoczynku usługi platformy Azure zawsze zalecają korzystanie z bezpiecznego transportu, takiego jak protokół TLS lub HTTPS. W związku z tym szyfrowanie w transportu powinno być adresowane przez protokół transportowy i nie powinno być głównym czynnikiem określającym model szyfrowania danych w spoczynku.

## <a name="client-encryption-model"></a>Model szyfrowania klienta

Model szyfrowania klienta odnosi się do szyfrowania wykonywanego poza dostawcą zasobów lub platformą Azure przez usługę lub aplikację wywołującą. Szyfrowanie może być wykonywane przez aplikację usługi na platformie Azure lub przez aplikację uruchamianą w centrum danych klienta. W obu przypadkach podczas korzystania z tego modelu szyfrowania dostawca zasobów platformy Azure odbiera zaszyfrowany obiekt blob danych bez możliwości odszyfrowywania danych w jakikolwiek sposób ani uzyskiwania dostępu do kluczy szyfrowania. W tym modelu zarządzanie kluczami jest wykonywane przez usługę wywołującą/aplikację i jest nieprzezroczyste dla usługi platformy Azure.

![Klient](./media/encryption-models/azure-security-encryption-atrest-fig2.png)

## <a name="server-side-encryption-using-service-managed-keys"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę

Dla wielu klientów podstawowym wymaganiem jest zapewnienie, że dane są szyfrowane zawsze, gdy są przechowywane. Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę umożliwia ten model dzięki umożliwieniu klientom oznaczania określonego zasobu (konta magazynu, bazy danych SQL itp.) na potrzeby szyfrowania i pozostawienia firmie Microsoft wszystkich aspektów zarządzania kluczami, takich jak wystawianie kluczy, rotacja i tworzenie kopii zapasowych. Większość usług platformy Azure, które obsługują szyfrowanie magazynowany, zwykle obsługuje ten model odciążania zarządzania kluczami szyfrowania na platformę Azure. Dostawca zasobów platformy Azure tworzy klucze, umieszcza je w bezpiecznym magazynie i pobiera je w razie potrzeby. Oznacza to, że usługa ma pełny dostęp do kluczy i ma pełną kontrolę nad zarządzaniem cyklem życia poświadczeń.

![Zarządzane](./media/encryption-models/azure-security-encryption-atrest-fig4.png)

W związku z tym szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę szybko rozwiązuje problem konieczności stosowania szyfrowania w spoczynku przy niskim narzucie na klienta. Gdy klient jest dostępny, zwykle otwiera Azure Portal subskrypcji docelowej i dostawcy zasobów i sprawdza pole wskazujące, że chce, aby dane były szyfrowane. W przypadku niektórych menedżerów zasobów szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez usługę jest domyślnie włączone.

Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez firmę Microsoft oznacza, że usługa ma pełny dostęp do przechowywania kluczy i zarządzania nimi. Niektórzy klienci mogą chcieć zarządzać kluczami, ponieważ uważają, że zyskują większe bezpieczeństwo, jednak podczas oceny tego modelu należy wziąć pod uwagę koszty i ryzyko związane z niestandardowym rozwiązaniem magazynu kluczy. W wielu przypadkach organizacja może określić, że ograniczenia zasobów lub ryzyko związane z rozwiązaniem lokalnym mogą być większe niż ryzyko związane z zarządzaniem w chmurze kluczami magazynowymi.  Jednak ten model może nie być wystarczający dla organizacji, które mają wymagania dotyczące kontrolowania tworzenia lub cyklu życia kluczy szyfrowania lub zarządzania kluczami szyfrowania usługi przez inny personel niż osoby zarządzające usługą (czyli rozdzielenie zarządzania kluczami od ogólnego modelu zarządzania usługą).

### <a name="key-access"></a>Dostęp do klucza

Gdy jest używane szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez usługę, usługa zarządza tworzeniem klucza, magazynem i dostępem do usługi. Zazwyczaj podstawowe dostawcy zasobów platformy Azure przechowują klucze szyfrowania danych w magazynie, który znajduje się blisko danych i jest szybko dostępny, a klucze szyfrowania kluczy są przechowywane w bezpiecznym magazynie wewnętrznym.

**Zalety**

- Prosta konfiguracja
- Firma Microsoft zarządza rotacją kluczy, tworzeniem kopii zapasowych i nadmiarowością
- Klient nie ma kosztów związanych z implementacją ani ryzykiem związanego z niestandardowym schematem zarządzania kluczami.

**Wady**

- Brak kontroli klienta nad kluczami szyfrowania (specyfikacja klucza, cykl życia, odwołanie itp.)
- Brak możliwości odseregowania zarządzania kluczami od ogólnego modelu zarządzania usługą

## <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta w Azure Key Vault

W scenariuszach, w których wymagane jest szyfrowanie danych w spoczynku i kontrolowanie kluczy szyfrowania, klienci mogą używać szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta w Key Vault. Niektóre usługi mogą przechowywać tylko główny klucz szyfrowania klucza Azure Key Vault i przechowywać zaszyfrowany klucz szyfrowania danych w lokalizacji wewnętrznej bliżej danych. W tym scenariuszu klienci mogą używać własnych kluczy do usługi Key Vault (BYOK — Bring Your Own Key) lub generować nowe klucze i używać ich do szyfrowania żądanych zasobów. Dostawca zasobów wykonuje operacje szyfrowania i odszyfrowywania, ale używa skonfigurowanego klucza szyfrowania klucza jako klucza głównego dla wszystkich operacji szyfrowania.

Utrata kluczy szyfrowania kluczy oznacza utratę danych. Z tego powodu nie należy usuwać kluczy. Kopię zapasową kluczy należy tworzyć przy każdym utworzeniu lub obróceniu. [Usuwanie nie soft-delete](../../key-vault/general/soft-delete-overview.md) powinno być włączone w dowolnym magazynie przechowującym klucze szyfrowania kluczy. Zamiast usuwać klucz, ustaw wartość enabled na false lub ustaw datę wygaśnięcia.

### <a name="key-access"></a>Dostęp do klucza

Model szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta w usłudze Azure Key Vault obejmuje usługę, która uzyskuje dostęp do kluczy w celu szyfrowania i odszyfrowywania zgodnie z potrzebami. Klucze szyfrowania danych spoczynku są udostępniane usłudze za pośrednictwem zasad kontroli dostępu. Te zasady udziela tożsamości usługi dostępu do odbierania klucza. Usługę platformy Azure uruchamianą w imieniu skojarzonej subskrypcji można skonfigurować przy użyciu tożsamości w tej subskrypcji. Usługa może przeprowadzić uwierzytelnianie Azure Active Directory i odbierać token uwierzytelniania identyfikujący się jako usługa działająca w imieniu subskrypcji. Token ten można następnie zaprezentować Key Vault uzyskać klucz, do których udzielono mu dostępu.

W przypadku operacji korzystających z kluczy szyfrowania tożsamości usługi można przyznać dostęp do dowolnej z następujących operacji: decrypt, encrypt, unwrapKey, wrapKey, verify, sign, get, list, update, create, import, delete, backup, and restore.

Aby uzyskać klucz do użycia podczas szyfrowania lub odszyfrowywania danych magazynowane, tożsamość usługi, która będzie działać jako wystąpienie usługi Resource Manager musi mieć klucz UnwrapKey (w celu uzyskania klucza do odszyfrowania) i WrapKey (aby wstawić klucz do magazynu kluczy podczas tworzenia nowego klucza).

>[!NOTE]
>Aby uzyskać więcej informacji na Key Vault autoryzacji, zobacz stronę zabezpieczania magazynu kluczy w Azure Key Vault [dokumentacji usługi](../../key-vault/general/security-features.md).

**Zalety**

- Pełna kontrola nad używanymi kluczami — klucze szyfrowania są zarządzane w Key Vault klienta pod kontrolą klienta.
- Możliwość szyfrowania wielu usług do jednego wzorca
- Może oddzielić zarządzanie kluczami od ogólnego modelu zarządzania usługą
- Może definiować usługę i lokalizację klucza między regionami

**Wady**

- Klient ponosi pełną odpowiedzialność za zarządzanie dostępem do kluczy
- Klient ponosi pełną odpowiedzialność za zarządzanie cyklem życia klucza
- Dodatkowe koszty & konfiguracji

## <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta na sprzęcie kontrolowanym przez klienta

Niektóre usługi platformy Azure umożliwiają model zarządzania kluczami "Hostuj własny klucz" (HYOK). Ten tryb zarządzania jest przydatny w scenariuszach, w których istnieje potrzeba szyfrowania danych magazynowych i zarządzania kluczami w repozytorium własnościowym poza kontrolą firmy Microsoft. W tym modelu usługa musi pobrać klucz z lokacji zewnętrznej. Ma to wpływ na gwarancje wydajności i dostępności, a konfiguracja jest bardziej złożona. Ponadto, ponieważ usługa ma dostęp do klucza szyfrowania danych podczas operacji szyfrowania i odszyfrowywania, ogólne gwarancje bezpieczeństwa tego modelu są podobne do tych, w których klucze są zarządzane przez klienta w Azure Key Vault.  W związku z tym ten model nie jest odpowiedni dla większości organizacji, chyba że mają określone wymagania dotyczące zarządzania kluczami. Ze względu na te ograniczenia większość usług platformy Azure nie obsługuje szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez serwer na sprzęcie kontrolowanym przez klienta.

### <a name="key-access"></a>Dostęp do klucza

Gdy używane jest szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez usługę na sprzęcie kontrolowanym przez klienta, klucze są utrzymywane w systemie skonfigurowanym przez klienta. Usługi platformy Azure, które obsługują ten model, zapewniają sposób nawiązywania bezpiecznego połączenia z magazynem kluczy dostarczonym przez klienta.

**Zalety**

- Pełna kontrola nad używanym kluczem głównym — klucze szyfrowania są zarządzane przez magazyn dostarczony przez klienta
- Możliwość szyfrowania wielu usług do jednego wzorca
- Może oddzielić zarządzanie kluczami od ogólnego modelu zarządzania dla usługi
- Może definiować usługę i lokalizację klucza między regionami

**Wady**

- Pełna odpowiedzialność za magazyn kluczy, zabezpieczenia, wydajność i dostępność
- Pełna odpowiedzialność za zarządzanie dostępem do kluczy
- Pełna odpowiedzialność za zarządzanie cyklem życia klucza
- Znacząca instalacja, konfiguracja i bieżące koszty konserwacji
- Zwiększona zależność od dostępności sieci między centrum danych klienta a centrami danych platformy Azure.

## <a name="supporting-services"></a>Usługi obsługi
Usługi platformy Azure, które obsługują każdy model szyfrowania:

| Produkt, funkcja lub usługa | Server-Side użyciu klucza Service-Managed   | Server-Side użyciu klucza Customer-Managed | Client-Side użyciu klucza Client-Managed  |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
| **Sztuczna inteligencja i uczenie maszynowe**      |                    |                    |                    |
| Azure Cognitive Search           | Tak                | Tak                | -                  |
| Azure Cognitive Services         | Tak                | Tak                | -                  |
| Azure Machine Learning           | Tak                | Tak                | -                  |
| Azure Machine Learning Studio (klasyczne) | Tak         | Wersja zapoznawcza, RSA 2048-bitowa | -               |
| Content Moderator                | Tak                | Tak                | -                  |
| Rozpoznawanie twarzy                             | Tak                | Tak                | -                  |
| Language Understanding           | Tak                | Tak                | -                  |
| Personalizacja                     | Tak                | Tak                | -                  |
| QnA Maker                        | Tak                | Tak                | -                  |
| Usługi mowy                  | Tak                | Tak                | -                  |
| Tłumaczenie tekstu w usłudze Translator                  | Tak                | Tak                | -                  |
| Power BI                         | Tak                | Tak, 4096-bitowe RSA  | -                  |
| **Analiza**                    |                    |                    |                    |
| Usługa Azure Stream Analytics           | Tak                | Tak\*\*            | -                  |
| Event Hubs                       | Tak                | Tak                | -                  |
| Funkcje                        | Tak                | Tak                | -                  |
| Azure Analysis Services          | Tak                | -                  | -                  |
| Azure Data Catalog               | Tak                | -                  | -                  |
| Azure HDInsight                  | Tak                | Wszystko                | -                  |
| Azure Monitor Application Insights | Tak                | Tak                | -                  |
| Azure Monitor Log Analytics      | Tak                | Tak                | -                  |
| Azure Data Explorer              | Tak                | Tak                | -                  |
| Azure Data Factory               | Tak                | Tak                | -                  |
| Azure Data Lake Store            | Tak                | Tak, RSA 2048-bitowy  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Tak                | Tak                | -                  |
| Container Instances              | Tak                | Tak                | -                  |
| Container Registry               | Tak                | Tak                | -                  |
| **Środowisko obliczeniowe**                      |                    |                    |                    |
| Virtual Machines                 | Tak                | Tak                | -                  |
| Zestaw skalowania maszyn wirtualnych        | Tak                | Tak                | -                  |
| SAP HANA                         | Tak                | Tak                | -                  |
| App Service                      | Tak                | Tak\*\*            | -                  |
| Automation                       | Tak                | Tak\*\*            | -                  |
| Azure Functions                  | Tak                | Tak\*\*            | -                  |
| Azure Portal                     | Tak                | Tak\*\*            | -                  |
| Logic Apps                       | Tak                | Tak                | -                  |
| Aplikacje zarządzane przez platformę Azure       | Tak                | Tak\*\*            | -                  |
| Service Bus                      | Tak                | Tak                | -                  |
| Site Recovery                    | Tak                | Tak                | -                  |
| **Bazy danych**                    |                    |                    |                    |
| Program SQL Server w usłudze Virtual Machines   | Tak                | Tak                | Tak                |
| Azure SQL Database               | Tak                | Tak, RSA 3072-bitowa  | Tak                |
| Azure SQL Database for MariaDB   | Tak                | -                  | -                  |
| Azure SQL Database for MySQL     | Tak                | Tak                | -                  |
| Azure SQL Database for PostgreSQL | Tak               | Tak                | -                  |
| Azure Synapse Analytics          | Tak                | Tak, RSA 3072-bitowa  | -                  |
| SQL Server Stretch Database      | Tak                | Tak, RSA 3072-bitowa  | Tak                |
| Table Storage                    | Tak                | Tak                | Tak                |
| Azure Cosmos DB                  | Tak                | Tak                | -                  |
| Azure Databricks                 | Tak                | Tak                | -                  |
| Usługa Azure Database Migration Service | Tak                | N/a\*              | -                  |
| **DevOps**                       |                    |                    |                    |
| Usługa Azure DevOps Services            | Tak                | -                  | -                  |
| Azure Repos                      | Tak                | -                  | -                  |
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
| **Zarządzanie + ład**    |                    |                    |                    |
| Azure Site Recovery              | Tak                | -                  | -                  |
| Azure Migrate                    | Tak                | Tak                | -                  |
| **Media**                        |                    |                    |                    |
| Media Services                   | Tak                | Tak                | Tak                |
| **Bezpieczeństwo**                     |                    |                    |                    |
| Azure Security Center dla IoT    | Tak                | Tak                | -                  |
| Usługa Azure Sentinel                   | Tak                | Tak                | -                  |
| **Storage**                      |                    |                    |                    |
| Blob Storage                     | Yes                | Tak                | Tak                |
| Premium Blob Storage             | Tak                | Tak                | Tak                |
| Disk Storage                     | Tak                | Tak                | -                  |
| Ultra Disk Storage               | Tak                | Tak                | -                  |
| Zarządzane Disk Storage             | Tak                | Tak                | -                  |
| File Storage                     | Yes                | Tak                | -                  |
| Plik Premium Storage             | Tak                | Tak                | -                  |
| Usługa File Sync                        | Tak                | Tak                | -                  |
| Queue Storage                    | Tak                | Tak                | Tak                |
| Avere vFXT                       | Tak                | -                  | -                  |
| Azure Cache for Redis            | Tak                | N/a\*              | -                  |
| Azure NetApp Files               | Tak                | Tak                | -                  |
| Archive Storage                  | Tak                | Tak                | -                  |
| StorSimple                       | Tak                | Tak                | Tak                |
| Azure Backup                     | Tak                | Tak                | Tak                |
| Data Box                         | Tak                | -                  | Tak                |
| Data Box Edge                    | Tak                | Tak                | -                  |

\* Ta usługa nie utrwala danych. Przejściowe pamięci podręczne, jeśli istnieją, są szyfrowane przy użyciu klucza firmy Microsoft.

\*\* Ta usługa obsługuje przechowywanie danych we własnym Key Vault, koncie magazynu lub innej usłudze utrwalania danych, która już obsługuje szyfrowanie Server-Side przy użyciu Customer-Managed Key.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak jest używane szyfrowanie na platformie Azure.](encryption-overview.md)
- Dowiedz się, jak platforma Azure [używa podwójnego szyfrowania,](double-encryption.md) aby ograniczyć zagrożenia związane z szyfrowaniem danych.
