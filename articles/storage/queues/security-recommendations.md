---
title: Zalecenia dotyczące zabezpieczeń Queue Storage
titleSuffix: Azure Storage
description: Zapoznaj się z zaleceniami dotyczącymi zabezpieczeń Queue Storage. Wdrożenie tych wskazówek pomoże Ci zrealizować swoje zobowiązania w zakresie zabezpieczeń zgodnie z opisem w naszym wspólnym modelu odpowiedzialności.
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: db0e033adf553c25c6b7b401f8d0df1a2cd5995f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592164"
---
# <a name="security-recommendations-for-queue-storage"></a>Zalecenia dotyczące zabezpieczeń Queue Storage

Ten artykuł zawiera zalecenia dotyczące zabezpieczeń Queue Storage. Wdrożenie tych zaleceń pomoże Ci zrealizować swoje zobowiązania w zakresie zabezpieczeń zgodnie z opisem w naszym wspólnym modelu odpowiedzialności. Aby uzyskać więcej informacji na temat tego, co firma Microsoft może spełnić obowiązki dostawcy usług, zobacz [udostępnianie obowiązków w chmurze obliczeniowej](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Niektóre zalecenia zawarte w tym artykule mogą być automatycznie monitorowane przez Azure Security Center. Azure Security Center to pierwszy wiersz obrony w ochronie zasobów na platformie Azure. Aby uzyskać informacje na temat Azure Security Center, zobacz [co to jest Azure Security Center?](../../security-center/security-center-introduction.md).

Azure Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure w celu zidentyfikowania potencjalnych luk w zabezpieczeniach. Następnie zawiera zalecenia dotyczące sposobu ich rozwiązywania. Aby uzyskać więcej informacji o Azure Security Center zaleceniach, zapoznaj się [z zaleceniami dotyczącymi zabezpieczeń w programie Azure Security Center](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Ochrona danych

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Korzystanie z modelu wdrażania Azure Resource Manager | Utwórz nowe konta magazynu przy użyciu modelu wdrażania Azure Resource Manager, aby uzyskać ważne ulepszenia zabezpieczeń, w tym najwyższą kontrolę dostępu opartą na rolach (Azure RBAC) oraz inspekcję i uwierzytelnianie oparte na Menedżer zasobówach, dostęp do zarządzanych tożsamości, dostęp do Azure Key Vault dla wpisów tajnych, a także uwierzytelniania i autoryzacji na podstawie usługi Azure AD w celu uzyskania dostępu do danych i zasobów usługi Azure Storage. Jeśli to możliwe, Przeprowadź migrację istniejących kont magazynu, które używają klasycznego modelu wdrażania, aby użyć Azure Resource Manager. Aby uzyskać więcej informacji na temat Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../../azure-resource-manager/management/overview.md). | - |
| Włącz zaawansowaną ochronę przed zagrożeniami dla wszystkich kont magazynu | Zaawansowana ochrona przed zagrożeniami dla usługi Azure Storage stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do kont magazynu lub korzystania z nich. Alerty zabezpieczeń są wyzwalane w Azure Security Center, gdy wystąpią anomalie działania i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniach dotyczących sposobu badania i korygowania zagrożeń. Aby uzyskać więcej informacji, zobacz [Advanced Threat Protection for Azure Storage](../common/azure-defender-storage-configure.md). | [Tak](../../security-center/security-center-remediate-recommendations.md) |
| Ogranicz tokeny sygnatury dostępu współdzielonego (SAS) tylko do połączeń HTTPS | Wymaganie protokołu HTTPS, gdy klient korzysta z tokenu sygnatury dostępu współdzielonego, aby uzyskać dostęp do danych w kolejce, aby zminimalizować ryzyko podsłuchiwania. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Użyj Azure Active Directory (Azure AD), aby autoryzować dostęp do danych kolejki | Usługa Azure AD zapewnia znakomite zabezpieczenia i łatwość użycia w porównaniu do autoryzacji klucza współużytkowanego w celu autoryzowania żądań do Queue Storage. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../common/storage-auth-aad.md). | - |
| Podczas przypisywania uprawnień do podmiotu zabezpieczeń usługi Azure AD za pomocą funkcji RBAC platformy Azure należy pamiętać o najniższych uprawnieniach. | Podczas przypisywania roli do użytkownika, grupy lub aplikacji Przyznaj podmiotowi zabezpieczeń tylko te uprawnienia, które są niezbędne do wykonywania swoich zadań. Ograniczanie dostępu do zasobów pomaga zapobiegać przypadkowemu i złośliwemu wykorzystaniu danych. | - |
| Zabezpiecz klucze dostępu do konta za pomocą Azure Key Vault | Firma Microsoft zaleca używanie usługi Azure AD do autoryzowania żądań do usługi Azure Storage. Jeśli jednak musisz użyć autoryzacji klucza współużytkowanego, Zabezpiecz klucze konta za pomocą Azure Key Vault. Możesz pobrać klucze z magazynu kluczy w czasie wykonywania, zamiast zapisywać je w aplikacji. | - |
| Okresowe ponowne generowanie kluczy konta | Okresowe obracanie kluczy konta zmniejsza ryzyko ujawnienia danych do złośliwych aktorów. | - |
| Należy pamiętać o najniższych uprawnieniach podczas przypisywania uprawnień do sygnatury dostępu współdzielonego | Podczas tworzenia sygnatury dostępu współdzielonego należy określić tylko te uprawnienia, które są wymagane przez klienta do wykonywania funkcji. Ograniczanie dostępu do zasobów pomaga zapobiegać przypadkowemu i złośliwemu wykorzystaniu danych. | - |
| Zaplanuj odwołanie dla dowolnego sygnatury dostępu współdzielonego, które wystawiasz klientom | W przypadku naruszenia zabezpieczeń sygnatury dostępu współdzielonego należy odwołać te sygnatury dostępu współdzielonego tak szybko, jak to możliwe. Aby odwołać sygnaturę dostępu współdzielonego delegowania użytkownika, odwołaj klucz delegowania użytkownika, aby szybko unieważniać wszystkie podpisy skojarzone z tym kluczem. Aby odwołać sygnaturę dostępu współdzielonego usługi, która jest skojarzona z przechowywanymi zasadami dostępu, można usunąć zachowane zasady, zmienić nazwę zasad lub zmienić jej czas wygaśnięcia na czas w przeszłości. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md).  | - |
| Jeśli sygnatura dostępu współdzielonego usługi nie jest skojarzona z przechowywanymi zasadami, ustaw czas wygaśnięcia na jedną godzinę lub mniejszą | Nie można odwołać skojarzenia zabezpieczeń usługi, która nie jest skojarzona z przechowywanymi zasadami dostępu. Z tego powodu ograniczenie czasu wygaśnięcia w taki sposób, aby sygnatura dostępu współdzielonego była ważna przez jedną godzinę lub mniej. | - |

## <a name="networking"></a>Networking

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Skonfiguruj minimalną wymaganą wersję Transport Layer Security (TLS) dla konta magazynu.  | Wymagaj, aby klienci używali bezpieczniejszej wersji protokołu TLS do żądania konta usługi Azure Storage przez skonfigurowanie minimalnej wersji protokołu TLS dla tego konta. Aby uzyskać więcej informacji, zobacz [Konfigurowanie minimalnej wymaganej wersji Transport Layer Security (TLS) dla konta magazynu](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)| - |
| Włącz opcję **wymagany bezpieczny transfer** na wszystkich kontach magazynu | Po włączeniu opcji **wymagany bezpieczny transfer** wszystkie żądania dotyczące konta magazynu muszą odbywać się za pośrednictwem bezpiecznych połączeń. Wszystkie żądania wysyłane za pośrednictwem protokołu HTTP zakończą się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Wymagaj bezpiecznego transferu w usłudze Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | [Tak](../../security-center/security-center-remediate-recommendations.md) |
| Włączanie reguł zapory | Skonfiguruj reguły zapory, aby ograniczyć dostęp do konta magazynu do żądań pochodzących z określonych adresów IP lub zakresów lub z listy podsieci w sieci wirtualnej platformy Azure. Więcej informacji o konfigurowaniu reguł zapory znajduje się w temacie [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | - |
| Zezwalaj zaufanym usługom firmy Microsoft na dostęp do konta magazynu | Włączenie reguł zapory dla konta magazynu domyślnie blokuje przychodzące żądania danych, chyba że żądania pochodzą z usługi działającej w sieci wirtualnej platformy Azure lub z dozwolonych publicznych adresów IP. Zablokowane żądania obejmują te z innych usług platformy Azure, z Azure Portal z usług rejestrowania i metryk i tak dalej. Możesz zezwolić na żądania z innych usług platformy Azure, dodając wyjątek, aby zezwolić zaufanym usługom firmy Microsoft na dostęp do konta magazynu. Aby uzyskać więcej informacji na temat dodawania wyjątku dla zaufanych usług firmy Microsoft, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).| - |
| Używanie prywatnych punktów końcowych | Prywatny punkt końcowy przypisuje prywatny adres IP z sieci wirtualnej platformy Azure do konta magazynu. Zabezpiecza cały ruch między siecią wirtualną a kontem magazynu za pośrednictwem prywatnego linku. Aby uzyskać więcej informacji o prywatnych punktach końcowych, zobacz [nawiązywanie połączenia prywatnego z kontem magazynu przy użyciu prywatnego punktu końcowego platformy Azure](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| Korzystanie z tagów usługi sieci wirtualnej | Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy. Aby uzyskać więcej informacji na temat tagów usług obsługiwanych przez usługę Azure Storage, zobacz [Omówienie tagów usługi platformy Azure](../../virtual-network/service-tags-overview.md). Samouczek pokazujący, jak używać tagów usługi do tworzenia reguł sieci wychodzącej, zobacz [ograniczanie dostępu do zasobów PaaS](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Ograniczanie dostępu sieciowego do określonych sieci | Ograniczanie dostępu sieciowego do sieci obsługujących klientów wymagających dostępu zmniejsza narażenie zasobów na ataki sieciowe. | [Tak](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

| Zalecenie | Komentarze | Security Center |
|-|----|--|
| Śledzenie sposobu autoryzacji żądań | Włącz rejestrowanie usługi Azure Storage, aby śledzić, w jaki sposób każde żądanie skierowane do usługi Azure Storage zostało autoryzowane. Dzienniki wskazują, czy żądanie zostało wykonane anonimowo, przy użyciu tokenu OAuth 2,0 przy użyciu klucza współużytkowanego lub przy użyciu sygnatury dostępu współdzielonego (SAS). Aby uzyskać więcej informacji, zobacz [Rejestrowanie analizy usługi Azure Storage](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Następne kroki

- [Dokumentacja zabezpieczeń platformy Azure](../../security/index.yml)
- [Bezpieczna dokumentacja dotycząca opracowywania](../../security/develop/index.yml)oprogramowania.
