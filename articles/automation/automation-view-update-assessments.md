---
title: Wyświetl oceny aktualizacji Azure Automation
description: W tym artykule opisano sposób wyświetlania ocen aktualizacji dla wdrożeń Update Management.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 321146364897d46a403bdfd6789fcb219179d88c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830603"
---
# <a name="view-update-assessments"></a>Wyświetlanie ocen aktualizacji

Na koncie Azure Automation wybierz pozycję **Update Management** , aby wyświetlić stan maszyn.

Ten widok zawiera informacje dotyczące maszyn, brakujących aktualizacji, wdrożeń aktualizacji i zaplanowanych wdrożeń aktualizacji. W kolumnie **zgodność** można zobaczyć czas ostatniego oceny maszyny. W kolumnie **Aktualizowanie gotowości agenta** można sprawdzić kondycję agenta aktualizacji. Jeśli wystąpił problem, wybierz link, aby przejść do dokumentacji dotyczącej rozwiązywania problemów, która może pomóc w rozwiązaniu problemu.

Aby uruchomić wyszukiwanie w dzienniku, które zwraca informacje o komputerze, aktualizacji lub wdrożeniu, wybierz odpowiedni element na liście. Zostanie otwarte okienko przeszukiwania dzienników z zapytaniem dotyczącym wybranego elementu.

![Update Management widoku domyślnego](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Wyświetl brakujące aktualizacje

Wybierz pozycję **brakujące aktualizacje** , aby wyświetlić listę aktualizacji, których brakuje na Twoich komputerach. Każda aktualizacja jest wyświetlana i można ją wybrać. Wyświetlane są informacje o liczbie maszyn, które wymagają aktualizacji, szczegółów systemu operacyjnego i linku do większej ilości informacji. W okienku przeszukiwania dzienników są również wyświetlane szczegółowe informacje o aktualizacjach.

![Brakujące aktualizacje](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

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

### <a name="linux"></a><a name="linux-2"></a>Linux

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

Aby uzyskać ogólne informacje, zobacz [Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure](automation-tutorial-update-management.md).
