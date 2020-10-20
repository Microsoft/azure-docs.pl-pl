---
title: Wyświetl oceny aktualizacji Azure Automation
description: W tym artykule opisano sposób wyświetlania ocen aktualizacji dla wdrożeń Update Management.
services: automation
ms.subservice: update-management
ms.date: 09/17/2020
ms.topic: conceptual
ms.openlocfilehash: 39df5888a330a92ae043e34c3043da5b1f566345
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222525"
---
# <a name="view-update-assessments-in-update-management"></a>Wyświetlanie ocen aktualizacji w Update Management

W Update Management można wyświetlić informacje o maszynach, braku aktualizacji, wdrożeniach aktualizacji i zaplanowanych wdrożeniach aktualizacji. Można wyświetlić informacje o ocenie z zakresu wybranej maszyny wirtualnej platformy Azure, z wybranego serwera z obsługą łuku lub z konta usługi Automation na wszystkich skonfigurowanych maszynach i serwerach.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

## <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Aby wyświetlić ocenę aktualizacji z poziomu maszyny wirtualnej platformy Azure, przejdź do **Virtual Machines** i wybierz maszynę wirtualną z listy. W menu po lewej stronie wybierz pozycję **gość + aktualizacje hosta**, a następnie wybierz pozycję **Przejdź do Update Management** na stronę **gość + aktualizacje hosta** .

W Update Management można wyświetlić informacje o komputerze, brakujące aktualizacje, wdrożenia aktualizacji i zaplanowane wdrożenia aktualizacji.

[![Widok oceny Update Management dla maszyny wirtualnej platformy Azure](./media/view-update-assessments/update-assessment-azure-vm.png)](./media/view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

Aby wyświetlić ocenę aktualizacji z serwera z obsługą łuku, przejdź do opcji **serwery — Azure Arc** i wybierz serwer z listy. Z menu po lewej stronie wybierz pozycję **gość i aktualizacje hosta**. Na stronie **gość + aktualizacje hosta** wybierz pozycję **Przejdź do Update Management**.

W Update Management można wyświetlić informacje o komputerze z obsługą łuku, brakujących aktualizacji, wdrożeniach aktualizacji i zaplanowanych wdrożeniach aktualizacji.

[![Widok oceny Update Management dla serwerów z obsługą łuku](./media/view-update-assessments/update-assessment-arc-server.png)](./media/view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

Aby wyświetlić ocenę aktualizacji na wszystkich komputerach, w tym na serwerach z obsługą ARC z poziomu konta usługi Automation, przejdź do **konta usługi Automation** i wybierz konto usługi Automation z włączonym Update Management z listy. Na koncie usługi Automation wybierz pozycję **Update Management** w menu po lewej stronie.

Aktualizacje środowiska są wyświetlane na stronie **Update Management** . Jeśli jakieś aktualizacje zostały zidentyfikowane jako brakujące, lista ich zostanie wyświetlona na karcie **brakujące aktualizacje** .

[![Update Management widoku domyślnego](./media/overview/update-management-view.png)](./media/overview/update-management-view-expanded.png#lightbox)

W kolumnie **zgodność** można zobaczyć czas ostatniego oceny maszyny. W kolumnie **Aktualizacja gotowości agenta** można sprawdzić kondycję agenta aktualizacji. Jeśli wystąpił problem, wybierz link, aby przejść do dokumentacji dotyczącej rozwiązywania problemów, która może pomóc w rozwiązaniu problemu.

W obszarze **link do informacji**wybierz link do aktualizacji, aby otworzyć artykuł pomocy technicznej, który zawiera ważne informacje o aktualizacji.

[![Wyświetl stan aktualizacji](./media/view-update-assessments/missing-updates.png)](./media/view-update-assessments/missing-updates-expanded.png#lightbox)

Kliknij w dowolnym miejscu w obszarze aktualizacji, aby otworzyć okienko przeszukiwanie dzienników. Zapytanie dotyczące przeszukiwania dzienników jest wstępnie zdefiniowane dla tej określonej aktualizacji. Możesz zmodyfikować to zapytanie lub utworzyć własne zapytanie, aby wyświetlić szczegółowe informacje.

[![Wyświetl wyniki zapytania dziennika](./media/view-update-assessments/logsearch-results.png)](./media/view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Wyświetl brakujące aktualizacje

Wybierz pozycję **brakujące aktualizacje** , aby wyświetlić listę aktualizacji, których brakuje na Twoich komputerach. Każda aktualizacja jest wyświetlana i można ją wybrać. Wyświetlane są informacje o liczbie maszyn, które wymagają aktualizacji, szczegółów systemu operacyjnego i linku do większej ilości informacji. W okienku przeszukiwania dzienników są również wyświetlane szczegółowe informacje o aktualizacjach.

![Brakujące aktualizacje](./media/view-update-assessments/automation-view-update-assessments-missing-updates.png)

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

Następnym etapem procesu jest [wdrożenie aktualizacji](deploy-updates.md) na niezgodnych maszynach i przejrzenie wyników wdrożenia.
