---
title: Rozwiązywanie problemów z analizą zmian aplikacji — Azure Monitor
description: Dowiedz się, jak rozwiązywać problemy podczas analizy zmian aplikacji.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521084"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>Rozwiązywanie problemów z analizą zmian aplikacji (wersja zapoznawcza)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>Problemy z rejestracją firmy Microsoft. Zmień dostawcę zasobów analizy z karty Historia zmian

Jeśli po raz pierwszy przeglądasz historię zmian po jej zintegrowaniu z analizą zmian aplikacji, zobaczysz, że automatycznie zarejestrowano dostawcę zasobów **Microsoft. ChangeAnalysis**. W rzadkich przypadkach może się nie powieść z następujących powodów:

- **Nie masz wystarczających uprawnień do zarejestrowania dostawcy zasobów Microsoft. ChangeAnalysis**. Ten komunikat o błędzie oznacza, że rola w bieżącej subskrypcji nie ma skojarzonej z nią zakresu **Microsoft. Support/Register/Action** . Taka sytuacja może wystąpić, jeśli nie jesteś właścicielem subskrypcji i masz uprawnienia dostępu współdzielonego za pomocą współpracownika (czyli wyświetlania dostępu do grupy zasobów). Aby rozwiązać ten problem, możesz skontaktować się z właścicielem subskrypcji w celu zarejestrowania dostawcy zasobów **Microsoft. ChangeAnalysis** . Można to zrobić w Azure Portal za poorednictwem **subskrypcji | Dostawcy zasobów** oraz wyszukiwanie ```Microsoft.ChangeAnalysis``` i rejestrowanie w interfejsie użytkownika, a także za pomocą Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

    Rejestrowanie dostawcy zasobów przy użyciu programu PowerShell:
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Nie można zarejestrować dostawcy zasobów Microsoft. ChangeAnalysis**. Ten komunikat oznacza, że coś nie powiodło się natychmiast, gdy interfejs użytkownika wysłał żądanie zarejestrowania dostawcy zasobów i nie jest powiązany z uprawnieniami. Prawdopodobnie może to być tymczasowy problem z połączeniem z Internetem. Spróbuj odświeżyć stronę i sprawdzić połączenie internetowe. Jeśli błąd będzie się powtarzać, skontaktuj się z changeanalysishelp@microsoft.com

- Trwa **to dłużej niż oczekiwano**. Ten komunikat oznacza, że rejestracja trwa dłużej niż 2 minuty. Jest to nietypowe, ale niekoniecznie oznacza coś, co poszło źle. Możesz przejść do pozycji **subskrypcje | Dostawca zasobów** do sprawdzania stanu rejestracji dostawcy zasobów **Microsoft. ChangeAnalysis** . Możesz spróbować użyć interfejsu użytkownika, aby wyrejestrować, ponownie zarejestrować lub odświeżyć, aby zobaczyć, czy to pomoże. Jeśli problem będzie się powtarzać, skontaktuj się z changeanalysishelp@microsoft.com pomocą techniczną.
    ![Rozwiązywanie problemów z rejestracją RP trwa zbyt długo](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Zrzut ekranu przedstawiający narzędzie diagnozowania i rozwiązywania problemów dla maszyny wirtualnej z wybranymi narzędziami do rozwiązywania problemów.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Zrzut ekranu przedstawiający kafelek dla narzędzia do rozwiązywania problemów z analizą ostatnich zmian dla maszyny wirtualnej.](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Subskrypcja usługi Azure Lighthouse nie jest obsługiwana

- Nie **można wykonać zapytania dotyczącego dostawcy zasobów Microsoft. ChangeAnalysis** z komunikatem *, że subskrypcja usługi Azure Lighthouse nie jest obsługiwana, zmiany są dostępne tylko w dzierżawie głównej subskrypcji*. Obecnie istnieje ograniczenie dotyczące rejestracji dostawcy zasobów analizy zmian za pomocą subskrypcji usługi Azure Lighthouse dla użytkowników, którzy nie znajdują się w dzierżawie głównej. Pracujemy nad tym ograniczeniem. Jeśli jest to problem z blokowaniem, istnieje obejście polegające na utworzeniu jednostki usługi i jawnie przypisaniu roli w celu zezwolenia na dostęp.  Skontaktuj się z nami changeanalysishelp@microsoft.com , aby dowiedzieć się więcej.

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Wystąpił błąd podczas pobierania zmian. Odśwież Tę stronę lub wróć później, aby wyświetlić zmiany

Jest to ogólny komunikat o błędzie przedstawiony przez usługę analizy zmian aplikacji, gdy nie można załadować zmian. Oto kilka znanych przyczyn:

- Błąd połączenia z Internetem z urządzenia klienckiego
- Zmiana usługi analizy jest tymczasowo niedostępna po kilku minutach zwykle rozwiązuje ten problem. Jeśli błąd będzie się powtarzać, skontaktuj się z changeanalysishelp@micorosoft.com

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Nie masz wystarczających uprawnień do wyświetlania niektórych zmian. Skontaktuj się z administratorem subskrypcji platformy Azure

Jest to ogólny komunikat o błędzie nieautoryzowany, co oznacza, że bieżący użytkownik nie ma wystarczających uprawnień do wyświetlenia zmiany. Aby wyświetlić zmiany infrastruktury zwrócone przez usługę Azure Resource Graph i Azure Resource Manager, wymagany jest co najmniej dostęp z czytnika. W przypadku zmian w pliku gościa aplikacji internetowej i zmian konfiguracji wymagana jest co najmniej rola współautor.

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Nie można zarejestrować dostawcy zasobów Microsoft. ChangeAnalysis

Ten komunikat oznacza, że coś nie powiodło się natychmiast, gdy interfejs użytkownika wysłał żądanie zarejestrowania dostawcy zasobów i nie jest powiązany z uprawnieniami. Prawdopodobnie może to być tymczasowy problem z połączeniem z Internetem. Spróbuj odświeżyć stronę i sprawdzić połączenie internetowe. Jeśli błąd będzie się powtarzać, skontaktuj się z changeanalysishelp@microsoft.com

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Nie masz wystarczających uprawnień do zarejestrowania dostawcy zasobów Microsoft. ChangeAnalysis. Skontaktuj się z administratorem subskrypcji platformy Azure

Ten komunikat o błędzie oznacza, że rola w bieżącej subskrypcji nie ma skojarzonej z nią zakresu **Microsoft. Support/Register/Action** . Taka sytuacja może wystąpić, jeśli nie jesteś właścicielem subskrypcji i masz uprawnienia dostępu współdzielonego za pomocą współpracownika (czyli wyświetlania dostępu do grupy zasobów). Aby rozwiązać ten problem, możesz skontaktować się z właścicielem subskrypcji w celu zarejestrowania dostawcy zasobów **Microsoft. ChangeAnalysis** . Można to zrobić w Azure Portal za poorednictwem **subskrypcji | Dostawcy zasobów** oraz wyszukiwanie ```Microsoft.ChangeAnalysis``` i rejestrowanie w interfejsie użytkownika, a także za pomocą Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Rejestrowanie dostawcy zasobów przy użyciu programu PowerShell:

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Resource Graph](../../governance/resource-graph/overview.md), która ułatwia analizę zmian.