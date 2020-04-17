---
title: Przewodnik dołączania do analizy kodu zabezpieczeń firmy Microsoft
description: Ten artykuł zawiera opis instalowania rozszerzenia Analizy kodu zabezpieczeń firmy Microsoft
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3ef111817b6351277f975b9b7e454f9a89982451
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460209"
---
# <a name="onboarding-and-installing"></a>Dołączanie i instalowanie

Wymagania wstępne do rozpoczęcia pracy z analizą kodu zabezpieczeń firmy Microsoft:

- Kwalifikująca się oferta ujednoliconej pomocy technicznej firmy Microsoft, jak opisano w poniższej sekcji.
- Organizacja Azure DevOps.
- Uprawnienie do instalowania rozszerzeń w organizacji Azure DevOps.
- Kod źródłowy, który można zsynchronizować z potokiem devops hostowanego w chmurze.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Dołączanie rozszerzenia analizy kodu zabezpieczeń firmy Microsoft

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Chcesz kupić rozszerzenie Analizy kodu zabezpieczeń firmy Microsoft?

Jeśli masz jedną z następujących ofert pomocy technicznej, skontaktuj się z menedżerem konta technicznego, aby kupić lub zamienić istniejące godziny, aby uzyskać dostęp do rozszerzenia:

- Warstwa Zaawansowane ujednolicone wsparcie
- Warstwa ujednolicona wydajność pomocy technicznej
- Wsparcie dla programistów
- Wsparcie dla partnerów
- Premier Wsparcie dla przedsiębiorstw

Jeśli nie masz jednej z wyżej wymienionych umów wsparcia, możesz zakupić rozszerzenie od jednego z naszych Partnerów.

**Następne kroki:**

Skontaktuj się z partnerem z poniższej listy i poproś o zakup rozszerzenia Analizy kodu zabezpieczeń firmy Microsoft.

>**Partnerów:**

- Strefy - Dane kontaktowe:cloudsupport@zones.com
- Wortell – Dane kontaktowe:info@wortell.nl

### <a name="become-a-partner"></a>Zostań partnerem

Zespół analizy kodu zabezpieczeń firmy Microsoft poszukuje partnerów pokładowych z umową Premier Support for Partners. Partnerzy pomogą umożliwić klientom usługi Azure DevOps bezpieczniejsze opracowywanie, sprzedając rozszerzenie klientom, którzy chcą go kupić, ale nie mają umowy pomocy technicznej dla przedsiębiorstw z firmą Microsoft. Zainteresowani partnerzy mogą zarejestrować się [tutaj](http://www.microsoftpartnersupport.com/msrd/opin).

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Instalowanie rozszerzenia analizy kodu zabezpieczeń firmy Microsoft

1. Po udostępnieniu rozszerzenia organizacji Azure DevOps przejdź do strony organizacji programu Azure DevOps. Przykładowym adresem URL takiej `https://dev.azure.com/contoso`strony jest .
1. Wybierz ikonę torby na zakupy w prawym górnym rogu obok swojego imienia i nazwiska, a następnie wybierz pozycję **Zarządzaj rozszerzeniami**.
1. Wybierz **pozycję Udostępnione**.
1. Wybierz rozszerzenie Analiza kodu zabezpieczeń firmy Microsoft, wybierz pozycję **Zainstaluj**.
1. Z listy rozwijanej wybierz organizację Azure DevOps, aby zainstalować rozszerzenie.
1. Wybierz pozycję **Zainstaluj**. Po zakończeniu instalacji można rozpocząć korzystanie z rozszerzenia.

>[!NOTE]
> Nawet jeśli nie masz dostępu do instalacji rozszerzenia, przejdź do kroków instalacji. Podczas procesu instalacji można zażądać dostępu od administratora organizacji Azure DevOps.

Po zainstalowaniu rozszerzenia, bezpieczne zadania kompilacji rozwoju są widoczne i dostępne do dodania do usługi Azure Potoki.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Dodawanie określonych zadań kompilacji do potoku usługi Azure DevOps

1. W organizacji Azure DevOps otwórz swój projekt zespołowy.
1. Wybierz **kompilacje** > **potoków**.
1. Wybierz potok, do którego chcesz dodać zadania kompilacji rozszerzenia:
   - Nowy potok: Wybierz **nowy** i wykonaj kroki szczegółowe, aby utworzyć nowy potok.
   - Edytuj potok: wybierz istniejący potok, a następnie wybierz pozycję **Edytuj,** aby rozpocząć edycję potoku.
1. Wybierz **+** i przejdź do okienka **Dodawanie zadań.**
1. Z listy lub za pomocą pola wyszukiwania znajdź zadanie kompilacji, które chcesz dodać. Wybierz pozycję **Dodaj**.
1. Określ parametry potrzebne do wykonania zadania.
1. Kolejka nowej kompilacji.
   >[!NOTE]
   >Ścieżki plików i folderów są względem katalogu głównego repozytorium źródłowego. Jeśli określisz pliki wyjściowe i foldery jako parametry, zostaną one zastąpione wspólną lokalizacją, którą zdefiniowaliśmy w agencie kompilacji.

> [!TIP]
>
> - Aby uruchomić analizę po kompilacji, umieść zadania kompilacji analizy kodu zabezpieczeń firmy Microsoft po kroku Publikowania artefaktów kompilacji kompilacji. W ten sposób kompilacja może kończyć i publikować wyniki przed uruchomieniem narzędzi analizy statycznej.
> - Zawsze wybierz **kontynuuj na błąd** dla zadań kompilacji bezpiecznego osiągnięć. Nawet jeśli jedno narzędzie nie powiedzie się, inne mogą działać. Nie ma współzależności między narzędziami.
> - Zadania kompilacji analizy kodu zabezpieczeń firmy Microsoft nie powiedzie się tylko wtedy, gdy narzędzie nie powiedzie się pomyślnie. Ale odniosą sukces, nawet jeśli narzędzie identyfikuje problemy w kodzie. Za pomocą zadania kompilacji po analizie, można skonfigurować kompilacji, aby zakończyć się niepowodzeniem, gdy narzędzie identyfikuje problemy w kodzie.
> - Niektóre zadania kompilacji usługi Azure DevOps nie są obsługiwane podczas uruchamiania za pośrednictwem potoku wydania. W szczególności usługi Azure DevOps nie obsługuje zadań, które publikują artefakty z poziomu potoku wydania.
> - Aby uzyskać listę wstępnie zdefiniowanych zmiennych w kompilacji zespołu usługi Azure DevOps, które można określić jako parametry, zobacz [Zmienne kompilacji devops platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfigurowania zadań [kompilacji,](security-code-analysis-customize.md) zobacz nasz przewodnik konfiguracji lub [przewodnik konfiguracji YAML](yaml-configuration.md).

Jeśli masz więcej pytań na temat rozszerzenia i oferowanych narzędzi, sprawdź naszą [stronę FAQ](security-code-analysis-faq.md).
