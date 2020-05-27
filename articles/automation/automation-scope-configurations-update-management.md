---
title: Pracuj z konfiguracjami zakresu dla Azure Automation Update Management
description: W tym artykule opisano sposób pracy z konfiguracjami zakresu podczas korzystania z Update Management.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 23ec49f2d68cf376ef0beb118d8bf69ada7bc0de
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832031"
---
# <a name="work-with-scope-configurations-for-update-management"></a>Pracuj z konfiguracjami zakresu dla Update Management

W tym artykule opisano, jak można korzystać z konfiguracji zakresu podczas korzystania z funkcji [Update Management](automation-update-management.md) na maszynach wirtualnych. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Sprawdź konfigurację zakresu

Update Management używa konfiguracji zakresu w obszarze roboczym Log Analytics, aby określić komputery, które mają być włączone dla tej funkcji. Konfiguracja zakresu jest grupą co najmniej jednego zapisanego wyszukiwania, które jest używane do ograniczania zakresu funkcji do określonych komputerów. Aby uzyskać dostęp do konfiguracji zakresu:

1. Na koncie usługi Automation w obszarze **powiązane zasoby**wybierz pozycję **obszar roboczy**. 

2. Wybierz obszar roboczy w obszarze **źródła danych obszaru roboczego**, a następnie wybierz pozycję **konfiguracje zakresów**.

3. Jeśli w wybranym obszarze roboczym nie jest jeszcze włączona funkcja Update Management, zostanie utworzona `MicrosoftDefaultScopeConfig-Updates` Konfiguracja zakresu. 

4. Jeśli w wybranym obszarze roboczym jest już włączona funkcja, nie zostanie ona wdrożona ponownie i nie zostanie do niej dodana konfiguracja zakresu. 

5. Wybierz wielokropek w dowolnej konfiguracji zakresu, a następnie kliknij przycisk **Edytuj**. 

6. W okienku Edycja wybierz pozycję **Wybierz grupy komputerów**. W okienku grupy komputerów zostaną wyświetlone zapisane wyszukiwania, które są używane do tworzenia konfiguracji zakresu.

## <a name="view-a-saved-search"></a>Wyświetl zapisane wyszukiwanie

Gdy komputer zostanie dodany do Update Management, zostanie również dodany do zapisanego wyszukiwania w obszarze roboczym. Zapisane wyszukiwanie jest kwerendą zawierającą komputery przeznaczone do tworzenia.

1. Przejdź do obszaru roboczego Log Analytics i wybierz pozycję **zapisane wyszukiwania** w obszarze **Ogólne**. Zapisane wyszukiwanie używane przez Update Management to:

|Nazwa     |Kategoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | Aktualizacje        | Updates__MicrosoftDefaultComputerGroup         |

2. Wybierz zapisane wyszukiwanie, aby wyświetlić zapytanie używane do wypełniania grupy. Na poniższej ilustracji przedstawiono zapytanie i jego wyniki:

    ![Zapisane wyszukiwania](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Następne kroki

* Aby korzystać z tej funkcji, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
* Aby rozwiązać problemy z błędami funkcji, zobacz [Rozwiązywanie problemów Update Management](troubleshoot/update-management.md).
* Rozwiązywanie problemów z błędami programu Windows Update Agent można znaleźć w temacie [Rozwiązywanie problemów z usługą Windows Update Agent](troubleshoot/update-agent-issues.md).
* Aby rozwiązać problemy z błędami agenta aktualizacji systemu Linux, zobacz [Rozwiązywanie problemów z usługą Linux Update Agent](troubleshoot/update-agent-issues-linux.md).