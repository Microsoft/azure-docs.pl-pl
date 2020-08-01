---
title: Wyświetl oceny aktualizacji Azure Automation
description: W tym artykule opisano sposób wyświetlania ocen aktualizacji dla wdrożeń Update Management.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 92861304a946e357b2b265cd825eceb8e22f7d2d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450404"
---
# <a name="view-update-assessments"></a>Wyświetlanie ocen aktualizacji

W Update Management można wyświetlić informacje o maszynach, braku aktualizacji, wdrożeniach aktualizacji i zaplanowanych wdrożeniach aktualizacji.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

W Update Management można wyświetlić informacje o maszynach, braku aktualizacji, wdrożeniach aktualizacji i zaplanowanych wdrożeniach aktualizacji.

[![Update Management widoku domyślnego](./media/update-mgmt-overview/update-management-view.png)](./media/update-mgmt-overview/update-management-view-expanded.png#lightbox)

Aby wyświetlić ocenę aktualizacji, wykonaj następujące czynności.

1. W Azure Portal przejdź do **konta usługi Automation** i wybierz konto usługi Automation z włączoną opcją Update Management z listy.

2. Na koncie usługi Automation wybierz pozycję **Update Management** w okienku po lewej stronie.

3. Aktualizacje środowiska są wyświetlane na stronie **Update Management** . Jeśli jakieś aktualizacje zostały zidentyfikowane jako brakujące, lista ich zostanie wyświetlona na karcie **brakujące aktualizacje** .

   W kolumnie **zgodność** można zobaczyć czas ostatniego oceny maszyny. W kolumnie **Aktualizacja gotowości agenta** można sprawdzić kondycję agenta aktualizacji. Jeśli wystąpił problem, wybierz link, aby przejść do dokumentacji dotyczącej rozwiązywania problemów, która może pomóc w rozwiązaniu problemu.

4. W obszarze **link do informacji**wybierz link do aktualizacji, aby otworzyć artykuł pomocy technicznej, który zawiera ważne informacje o aktualizacji.

     [![Wyświetl stan aktualizacji](./media/update-mgmt-view-update-assessments/missing-updates.png)](./media/update-mgmt-view-update-assessments/missing-updates-expanded.png#lightbox)

5. Kliknij w dowolnym miejscu w obszarze aktualizacji, aby otworzyć okienko przeszukiwanie dzienników. Zapytanie dotyczące przeszukiwania dzienników jest wstępnie zdefiniowane dla tej określonej aktualizacji. Możesz zmodyfikować to zapytanie lub utworzyć własne zapytanie, aby wyświetlić szczegółowe informacje.

    [![Wyświetl wyniki zapytania dziennika](./media/update-mgmt-view-update-assessments/logsearch-results.png)](./media/update-mgmt-view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Wyświetl brakujące aktualizacje

Wybierz pozycję **brakujące aktualizacje** , aby wyświetlić listę aktualizacji, których brakuje na Twoich komputerach. Każda aktualizacja jest wyświetlana i można ją wybrać. Wyświetlane są informacje o liczbie maszyn, które wymagają aktualizacji, szczegółów systemu operacyjnego i linku do większej ilości informacji. W okienku przeszukiwania dzienników są również wyświetlane szczegółowe informacje o aktualizacjach.

![Brakujące aktualizacje](./media/update-mgmt-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Współpraca z klasyfikacjami aktualizacji

W poniższej tabeli wymieniono obsługiwane klasyfikacje aktualizacji w Update Management z definicją dla każdej klasyfikacji.

### <a name="windows"></a>Windows

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne     | Aktualizacje dotyczące określonych problemów, które dotyczą krytycznych błędów niezwiązanych z zabezpieczeniami.        |
|Aktualizacje zabezpieczeń     | Aktualizacje dotyczące specyficznych dla produktu problemów związanych z zabezpieczeniami.        |
|Pakiety zbiorcze aktualizacji     | Zestawy poprawek, które są pakowane razem w celu łatwiejszego wdrażania.        |
|Pakiety funkcji     | Nowe funkcje produktu dystrybuowane poza wydaniem produktu.        |
|Dodatki Service Pack     | Zestawy poprawek, które są stosowane do aplikacji.        |
|Aktualizacje definicji     | Aktualizacje definicji wirusów lub innych plików definicji.        |
|narzędzia     | Narzędzia lub funkcje, które pomagają wykonać jedno lub więcej zadań.        |
|Aktualizacje     | Aktualizacje aplikacji lub plików, które są obecnie zainstalowane.        |

### <a name="linux"></a>Linux

|Klasyfikacja  |Opis  |
|---------|---------|
|Aktualizacje krytyczne i zabezpieczeń     | Aktualizacje dotyczące konkretnego problemu lub problemu związanego z zabezpieczeniami.         |
|Inne aktualizacje     | Wszystkie inne aktualizacje, których charakter nie ma znaczenia ani aktualizacje zabezpieczeń.        |

W przypadku systemu Linux Update Management może rozróżnić aktualizacje krytyczne i aktualizacje zabezpieczeń w chmurze, a dane oceny są wyświetlane. (Stopień szczegółowości jest możliwy ze względu na Wzbogacanie danych w chmurze). W przypadku stosowania poprawek Update Management opiera się na danych klasyfikacji dostępnych na komputerze. W przeciwieństwie do innych dystrybucji, CentOS nie ma informacji dostępnych w wersji RTM produktu. Jeśli masz maszyny CentOS skonfigurowane do zwracania danych zabezpieczeń dla poniższego polecenia, Update Management może zostać poprawione na podstawie klasyfikacji:

```bash
sudo yum -q --security check-update
```

Obecnie nie jest obsługiwana metoda umożliwiająca natywną klasyfikację — dostępność danych w systemie CentOS. W tej chwili tylko Najlepsza pomoc techniczna jest świadczona klientom, którzy włączyli tę funkcję samodzielnie.

Aby sklasyfikować aktualizacje w systemie Red Hat Enterprise w wersji 6, należy zainstalować wtyczkę yum-Security. W Red Hat Enterprise Linux 7 wtyczka jest już częścią yum, nie ma potrzeby instalowania żadnych elementów. Aby uzyskać więcej informacji, zobacz następujący [artykuł merytoryczny](https://access.redhat.com/solutions/10021)firmy Red Hat.

## <a name="next-steps"></a>Następne kroki

Następnym etapem procesu jest [wdrożenie aktualizacji](update-mgmt-deploy-updates.md) na niezgodnych maszynach i przejrzenie wyników wdrożenia.
