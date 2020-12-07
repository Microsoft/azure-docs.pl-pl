---
title: Najlepsze rozwiązania dotyczące Azure AD B2C
titleSuffix: Azure AD B2C
description: Zalecenia i najlepsze rozwiązania, które należy wziąć pod uwagę podczas pracy z Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: 1c3c3d38ac0d8334f70f681d8ef86c0d6f86ecfa
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750224"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Zalecenia i najlepsze rozwiązania dotyczące Azure Active Directory B2C

Poniższe najlepsze rozwiązania i zalecenia dotyczą niektórych podstawowych aspektów integrowania Azure Active Directory (Azure AD) B2C z istniejącymi lub nowymi środowiskami aplikacji.

## <a name="fundamentals"></a>Podstawy

| Najlepsze rozwiązanie | Opis |
|--|--|
| Wybieranie przepływów użytkownika dla większości scenariuszy | Platforma obsługi tożsamości Azure AD B2C jest podstawową siłą usługi. Zasady w pełni opisują środowiska tożsamości, takie jak rejestrowanie, logowanie lub edytowanie profilów. Aby ułatwić skonfigurowanie najczęstszych zadań związanych z tożsamościami, Portal Azure AD B2C obejmuje wstępnie zdefiniowane, konfigurowalne zasady o nazwie przepływy użytkownika. Korzystając z przepływów użytkowników, możesz tworzyć wspaniałe środowiska użytkownika w kilka minut za pomocą zaledwie kilku kliknięć. [Dowiedz się, kiedy używać przepływów użytkowników a zasad niestandardowych](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Rejestracje aplikacji | Wszystkie aplikacje (w sieci Web, natywny) i interfejs API, które są zabezpieczane, muszą być zarejestrowane w Azure AD B2C. Jeśli aplikacja ma zarówno wersję sieci Web, jak i natywną systemu iOS i Android, można zarejestrować je jako jedną aplikację w Azure AD B2C z tym samym IDENTYFIKATORem klienta. Dowiedz się [, jak zarejestrować aplikacje OIDC, SAML, Web i Native](./tutorial-register-applications.md?tabs=applications). Dowiedz się więcej o [typach aplikacji, których można używać w Azure AD B2C](./application-types.md). |
| Przechodzenie do rozliczania miesięcznie aktywnych użytkowników | Azure AD B2C została przeniesiona z comiesięcznych aktywnych uwierzytelnień na miesięczne rozliczenia aktywnych użytkowników (MAU). Większość klientów znajdzie ten model ekonomicznie. [Dowiedz się więcej o comiesięcznych rozliczeniach aktywnych użytkowników](https://azure.microsoft.com/updates/mau-billing/). [Zapisz ten link](b2clogin.md) |

## <a name="planning-and-design"></a>Planowanie i projektowanie

Zdefiniuj architekturę aplikacji i usług, bieżące systemy spisu i zaplanuj migrację do Azure AD B2C.

| Najlepsze rozwiązanie | Opis |
|--|--|
| Tworzenie kompleksowego rozwiązania | Uwzględnij wszystkie zależności aplikacji podczas planowania integracji Azure AD B2C. Należy wziąć pod uwagę wszystkie usługi i produkty, które znajdują się w danym środowisku lub które mogą być konieczne do dodania do rozwiązania, na przykład Azure Functions, systemy zarządzania relacjami z klientami (CRM), Azure API Management Gateway i Storage. Weź pod uwagę bezpieczeństwo i skalowalność wszystkich usług. |
| Udokumentowanie środowisk użytkowników | Szczegółowe informacje o tym, jak wszyscy użytkownicy mogą napotkać użytkownicy w Twojej aplikacji. Uwzględnij każdy ekran i wszystkie przepływy rozgałęzienia, które mogą wystąpić podczas korzystania z aspektów tożsamości i profilu aplikacji. Uwzględnij użyteczność, dostępność i lokalizację w planowaniu. |
| Wybieranie odpowiedniego protokołu uwierzytelniania |  Aby zapoznać się z różnymi scenariuszami aplikacji i zalecanymi przepływami uwierzytelniania, zobacz [scenariusze i obsługiwane przepływy uwierzytelniania](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilotażowe środowisko użytkownika — Weryfikacja koncepcji (ZK) | Zacznij od naszych [przykładów kodu firmy Microsoft](code-samples.md) i [przykładów społecznościowych](https://github.com/azure-ad-b2c/samples). |
| Utwórz plan migracji |Planowanie z wyprzedzeniem może sprawiać, że migracja przebiega bardziej płynnie. Dowiedz się więcej o [migracji użytkowników](user-migration.md).|
| Użyteczność a zabezpieczenia | Twoje rozwiązanie musi mieć możliwość osiągnięcia odpowiedniej równowagi między użytecznością aplikacji a akceptowalnym poziomem ryzyka w organizacji. |
| Przenoszenie lokalnych zależności do chmury | Aby zapewnić odporne rozwiązanie, rozważ przeniesienie istniejących zależności aplikacji do chmury. |
| Migrowanie istniejących aplikacji do b2clogin.com | Zaniechanie login.microsoftonline.com będzie obowiązywać dla wszystkich dzierżawców Azure AD B2C 4 grudnia 2020. [Dowiedz się więcej](b2clogin.md). |
| Korzystanie z ochrony tożsamości i dostępu warunkowego | Te funkcje umożliwiają znacznie większą kontrolę nad ryzykownymi uwierzytelnieniami i zasadami dostępu. Wymagany jest Azure AD B2C Premium P2. [Dowiedz się więcej](conditional-access-identity-protection-overview.md). |

## <a name="implementation"></a>Implementacja

W fazie wdrażania należy wziąć pod uwagę następujące zalecenia.

| Najlepsze rozwiązanie | Opis |
|--|--|
| Edytuj zasady niestandardowe z rozszerzeniem Azure AD B2C dla Visual Studio Code | Pobierz Visual Studio Code i to [rozszerzenie utworzone przez społeczność z witryny Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). Chociaż nie jest to oficjalny produkt firmy Microsoft, rozszerzenie Azure AD B2C dla Visual Studio Code zawiera kilka funkcji, które ułatwiają pracę z zasadami niestandardowymi. |
| Dowiedz się, jak rozwiązywać problemy Azure AD B2C | Dowiedz się, jak [rozwiązywać problemy z zasadami niestandardowymi](./troubleshoot-custom-policies.md?tabs=applications) podczas projektowania. Dowiedz się, jak wygląda normalny przepływ uwierzytelniania i korzystaj z narzędzi do wykrywania anomalii i błędów. Na przykład użyj [Application Insights](troubleshoot-with-application-insights.md) , aby przejrzeć dzienniki wyjściowe podróży użytkownika. |
| Skorzystaj z naszej biblioteki sprawdzonych wzorców zasad niestandardowych | Znajdź [przykłady](https://github.com/azure-ad-b2c/samples) kilku ulepszonych Azure AD B2C użytkowników zarządzania tożsamościami i dostępem klientów (CIAM). |

## <a name="testing"></a>Testowanie

Przetestuj i automatyzuj implementację Azure AD B2C.

| Najlepsze rozwiązanie | Opis |
|--|--|
| Konto dla ruchu globalnego | Użyj źródeł ruchu z różnych adresów globalnych, aby przetestować wymagania dotyczące wydajności i lokalizacji. Upewnij się, że wszystkie pliki HTML, CSS i zależności mogą spełniać wymagania dotyczące wydajności. |
| Testowanie funkcjonalne i interfejsu użytkownika | Przetestuj przepływy użytkowników na całym końcu. Dodaj testy syntetyczne co kilka minut przy użyciu protokołu selen, test sieci Web programu VS itd. |
| Testowanie piórem | Przed rozpoczęciem korzystania z rozwiązania należy wykonać ćwiczenia testowania penetracji w celu sprawdzenia, czy wszystkie składniki są bezpieczne, w tym wszystkich zależności innych firm. Upewnij się, że interfejsy API zostały zabezpieczone przy użyciu tokenów dostępu i używasz właściwego protokołu uwierzytelniania dla scenariusza aplikacji. Dowiedz się więcej o [testowaniu penetracji](../security/fundamentals/pen-testing.md) i [Microsoft Cloud ujednolicone reguły testowania penetracji](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| Testowanie A/B | Przed przeprowadzeniem całego wypełniania, aby przetworzyć nowe funkcje przy użyciu małego, losowego zestawu użytkowników. Dzięki włączeniu języka JavaScript w Azure AD B2C można zintegrować z narzędziami do testowania/B, takimi jak optymalizacja, przejrzystość i inne. |
| Testowanie obciążeniowe | Azure AD B2C można skalować, ale aplikacja może skalować się tylko wtedy, gdy wszystkie jej zależności można skalować. Przetestuj testowanie interfejsów API i sieci CDN. |
| Ograniczanie przepływności |  Azure AD B2C ogranicza ruch, jeśli zbyt wiele żądań jest wysyłanych z tego samego źródła w krótkim czasie. Używaj kilku źródeł ruchu podczas testowania obciążenia i `AADB2C90229` łagodnie Obsługuj kod błędu w aplikacjach. |
| Automatyzacja | Korzystaj z potoków ciągłej integracji i dostarczania (CI/CD), aby zautomatyzować testowanie i wdrożenia, na przykład [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operacje

Zarządzaj środowiskiem Azure AD B2C.

| Najlepsze rozwiązanie | Opis |
|--|--|
| Tworzenie wielu środowisk | Aby ułatwić wykonywanie operacji i wdrażania, Utwórz osobne środowiska na potrzeby opracowywania, testowania, przedprodukcyjnego i produkcyjnego. Utwórz Azure AD B2C dzierżawców dla każdej z nich. |
| Używanie kontroli wersji dla zasad niestandardowych | Należy rozważyć użycie usługi GitHub, Azure Repos lub innego systemu kontroli wersji opartej na chmurze dla zasad niestandardowych Azure AD B2C. |
| Użyj interfejsu API Microsoft Graph, aby zautomatyzować zarządzanie dzierżawcami usługi B2C | Interfejsy API Microsoft Graph:<br/>Zarządzanie [platformą obsługi tożsamości](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) (zasady niestandardowe)<br/>[Klucze](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[Przepływy użytkowników](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Integracja z usługą Azure DevOps | [Potok](deploy-custom-policies-devops.md) ciągłej integracji i ciągłego wdrażania umożliwia łatwe i szybkie przemieszczanie kodu między różnymi środowiskami.   |
| Integracja z usługą Azure Monitor | [Zdarzenia dziennika inspekcji](view-audit-logs.md) są przechowywane tylko przez siedem dni. [Integracja z usługą Azure monitor](azure-monitor.md) w celu przechowywania dzienników do długoterminowego użytku lub integracji z narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń innych firm (Siem) w celu uzyskania szczegółowych informacji o środowisku. |
| Skonfiguruj aktywne alerty i monitorowanie | [Śledź zachowanie użytkowników](./analytics-with-application-insights.md) w Azure AD B2C przy użyciu Application Insights. |

## <a name="support-and-status-updates"></a>Aktualizacje pomocy technicznej i stanu
## <a name="todays-support-and-status-updates"></a>Dzisiejsze aktualizacje pomocy technicznej i stanu
Bądź na bieżąco z stanem usługi i Znajdź opcje pomocy technicznej.

| Najlepsze rozwiązanie | Opis |
|--|--|
| [Aktualizacje usług](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Bądź na bieżąco z Azure AD B2Cmi aktualizacjami i ogłoszeniami o produkcie. |
| [Pomoc techniczna firmy Microsoft](support-options.md) | Prześlij żądanie pomocy technicznej dotyczące Azure AD B2C problemów technicznych. Pomoc dotycząca rozliczeń i subskrypcji jest świadczona bezpłatnie. |
| [Stan platformy Azure](https://status.azure.com/status) | Wyświetl bieżący stan kondycji wszystkich usług platformy Azure. |