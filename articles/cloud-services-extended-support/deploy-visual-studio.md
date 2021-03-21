---
title: Użyj Cloud Services (obsługa rozszerzona) (wersja zapoznawcza)
description: Dowiedz się teraz, jak utworzyć i wdrożyć usługę w chmurze platformy Azure przy użyciu Azure Resource Manager z programem Visual Studio
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722674"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Tworzenie i wdrażanie usługi w chmurze platformy Azure (obsługa rozszerzona) przy użyciu programu Visual Studio

Począwszy od [programu Visual Studio 2019 w wersji 16,9](https://visualstudio.microsoft.com/vs/preview/) (obecnie dostępnego w wersji zapoznawczej), możesz współpracować z usługami w chmurze za pomocą Azure Resource Manager (ARM), co znacznie upraszcza i modernizacj konserwację zasobów platformy Azure oraz zarządzanie nimi. Jest to możliwe dzięki nowej usłudze platformy Azure, która jest nazywana Cloud Services (obsługa rozszerzona). Istnieje możliwość opublikowania istniejącej usługi w chmurze w celu Cloud Services (obsługa rozszerzona). Aby uzyskać informacje na temat tej usługi platformy Azure, zobacz [dokumentację dotyczącą Cloud Services (obsługa rozszerzona)](overview.md).

> [!IMPORTANT]
> Cloud Services (obsługa rozszerzona) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Rejestrowanie funkcji dla subskrypcji
Cloud Services (obsługa rozszerzona) jest obecnie w wersji zapoznawczej. Zarejestruj funkcję subskrypcji w następujący sposób:

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
Aby uzyskać więcej informacji, zobacz [wymagania wstępne dotyczące wdrażania Cloud Services (obsługa rozszerzona)](deploy-prerequisite.md)

## <a name="create-a-project"></a>Tworzenie projektu

Program Visual Studio udostępnia szablon projektu, który umożliwia tworzenie usługi w chmurze platformy Azure z rozszerzoną obsługą o nazwie **Usługa w chmurze platformy Azure (obsługa rozszerzona)**. Usługa w chmurze jest prostą usługą platformy Azure ogólnego przeznaczenia. Po utworzeniu projektu program Visual Studio umożliwia skonfigurowanie, debugowanie i wdrożenie usługi w chmurze na platformie Azure.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Aby utworzyć projekt usługi w chmurze platformy Azure (obsługa rozszerzona) w programie Visual Studio

W tej sekcji omówiono tworzenie projektu usługi w chmurze platformy Azure w programie Visual Studio z co najmniej jedną rolą sieci Web.

1. W oknie uruchamiania wybierz pozycję **Utwórz nowy projekt**.

1. W polu wyszukiwania wpisz ciąg w *chmurze*, a następnie wybierz pozycję **Usługa w chmurze Azure (obsługa rozszerzona)**.

   ![Nowa usługa w chmurze platformy Azure z rozszerzoną obsługą](./media/choose-project-template.png)

1. Nadaj projektowi nazwę i wybierz pozycję **Utwórz**.

   ![Nadaj nazwę projektowi](./media/configure-new-project.png)

1. W oknie dialogowym **Nowa usługa w chmurze Microsoft Azure** wybierz role, które chcesz dodać, a następnie wybierz przycisk strzałki w prawo, aby dodać je do rozwiązania.

    ![Wybierz nowe role usługi w chmurze platformy Azure](./media/choose-roles.png)

1. Aby zmienić nazwę dodanej roli, umieść wskaźnik myszy na roli w oknie dialogowym **nowa Microsoft Azure usługa w chmurze** , a następnie z menu kontekstowego wybierz polecenie **Zmień nazwę**. Możesz również zmienić nazwę roli w rozwiązaniu (w **Eksplorator rozwiązań**) po dodaniu.

    ![Zmień nazwę roli usługi w chmurze platformy Azure](./media/new-cloud-service-rename.png)

Projekt programu Visual Studio Azure ma skojarzenia z projektami ról w rozwiązaniu. Projekt zawiera również plik *definicji usługi* i *plik konfiguracji usługi*:

- **Plik definicji usługi** — definiuje ustawienia czasu wykonywania dla aplikacji, w tym informacje o wymaganych rolach, punktach końcowych i rozmiarze maszyny wirtualnej.
- **Plik konfiguracji usługi** — określa, ile wystąpień roli jest uruchomionych i wartości ustawień zdefiniowanych dla roli.

Aby uzyskać więcej informacji o tych plikach, zobacz [Konfigurowanie ról dla usługi w chmurze platformy Azure za pomocą programu Visual Studio](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service).

## <a name="publish-a-cloud-service"></a>Publikowanie usługi w chmurze

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie z menu kontekstowego wybierz pozycję **Publikuj**.

   ![Strona logowania](./media/publish-step-1.png)

1. **Konto** — wybierz konto lub wybierz pozycję **Dodaj konto** z listy rozwijanej konto.

1. **Wybierz subskrypcję** — wybierz subskrypcję, która ma być używana dla danego wdrożenia. Subskrypcja używana do wdrażania Cloud Services (obsługa rozszerzona) musi mieć przypisane role właściciela lub współautora za pośrednictwem kontroli dostępu opartej na rolach (RBAC). Jeśli Twoja subskrypcja nie ma żadnej z tych ról, zapoznaj [się z instrukcjami, aby dodać przypisanie roli](../role-based-access-control/role-assignments-steps.md) w celu dodania go przed dalszym kontynuowaniem.

1. Wybierz przycisk **dalej** , aby przejść do strony **ustawień** .

   ![Typowe ustawienia](./media/publish-settings.png)

1. **Usługa w chmurze** — Użyj listy rozwijanej, wybierz istniejącą usługę w chmurze lub wybierz pozycję **Utwórz nową**, a następnie Utwórz usługę w chmurze. Centrum danych wyświetla w nawiasach dla każdej usługi w chmurze. Zaleca się, aby lokalizacja centrum danych dla usługi w chmurze była taka sama, jak lokalizacja centrum danych dla konta magazynu.

   Jeśli zdecydujesz się utworzyć nową usługę w chmurze, zostanie wyświetlone okno dialogowe **Tworzenie usługi w chmurze (obsługa rozszerzona)** . Określ lokalizację i grupę zasobów, której chcesz użyć dla usługi w chmurze.

   ![Tworzenie usługi w chmurze z obsługą rozszerzoną](./media/extended-support-dialog.png)

1. **Konfiguracja kompilacji** — wybierz opcję **Debuguj** lub **Zwolnij**.

1. **Konfiguracja usługi** — wybierz **chmurę** lub **lokalną**.

1. **Konto magazynu** — wybierz konto magazynu do użycia w ramach tego wdrożenia lub **Utwórz nowe** , aby utworzyć konto magazynu. Region jest wyświetlany w nawiasach dla każdego konta magazynu. Zalecane jest, aby lokalizacja centrum danych dla konta magazynu była taka sama, jak lokalizacja centrum danych dla usługi w chmurze (typowe ustawienia).

   Konto usługi Azure Storage przechowuje pakiet dla wdrożenia aplikacji. Po wdrożeniu aplikacji pakiet zostanie usunięty z konta magazynu.

1. **Key Vault** — Określ Key Vault, który zawiera wpisy tajne dla tej usługi w chmurze. Ta funkcja jest włączona, jeśli pulpit zdalny jest włączony lub do konfiguracji są dodawane certyfikaty.

1. **Włącz pulpit zdalny dla wszystkich ról** — wybierz tę opcję, jeśli chcesz mieć możliwość zdalnego łączenia się z usługą. Zostanie wyświetlony monit o podanie poświadczeń.

   ![Ustawienia pulpitu zdalnego](./media/remote-desktop-configuration.png)

1. Wybierz przycisk **dalej** , aby przejść do strony **Ustawienia diagnostyki** .

   ![Ustawienia diagnostyki](./media/diagnostics-settings.png)

   Diagnostyka umożliwia rozwiązywanie problemów z usługą w chmurze platformy Azure (lub maszyną wirtualną platformy Azure). Aby uzyskać informacje na temat diagnostyki, zobacz [Konfigurowanie diagnostyki dla usług Azure Cloud Services i Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines). Aby uzyskać informacje na temat Application Insights, zobacz [co to jest Application Insights?](../azure-monitor/app/app-insights-overview.md).

1. Wybierz przycisk **dalej** , aby przejść do strony **Podsumowanie** .

   ![Podsumowanie](./media/publish-summary.png)

1. **Profil docelowy** — możesz wybrać opcję utworzenia profilu publikowania na podstawie ustawień, które zostały wybrane. Na przykład można utworzyć jeden profil dla środowiska testowego i drugi dla produkcji. Aby zapisać ten profil, wybierz ikonę **Zapisz** . Kreator utworzy profil i zapisze go w projekcie programu Visual Studio. Aby zmodyfikować nazwę profilu, Otwórz listę **profil docelowy** , a następnie wybierz pozycję **Zarządzaj.**

   > [!Note]
   > Profil publikowania zostanie wyświetlony w Eksplorator rozwiązań w programie Visual Studio, a ustawienia profilu są zapisywane w pliku z rozszerzeniem. azurePubxml. Ustawienia są zapisywane jako atrybuty tagów XML.

1. Po skonfigurowaniu wszystkich ustawień wdrożenia projektu wybierz pozycję **Publikuj** w dolnej części okna dialogowego. Możesz monitorować stan procesu w oknie danych wyjściowych **dziennika aktywności platformy Azure** w programie Visual Studio.

Gratulacje! Projekt usługi w chmurze o rozszerzonym obsłudze został opublikowany na platformie Azure. Aby ponownie opublikować z tymi samymi ustawieniami, możesz użyć ponownie profilu publikowania lub Powtórz te kroki, aby utworzyć nowy.

## <a name="clean-up-azure-resources"></a>Czyszczenie zasobów platformy Azure

Aby wyczyścić zasoby platformy Azure utworzone w ramach tego samouczka, przejdź do [Azure Portal](https://portal.azure.com), wybierz pozycję **grupy zasobów**, Znajdź i otwórz grupę zasobów, która została użyta do utworzenia usługi, a następnie wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

Skonfiguruj ciągłą integrację (CI) za pomocą przycisku **Konfiguruj** na ekranie **publikowania** . Aby uzyskać więcej informacji, zobacz [dokumentację Azure Pipelines](/azure/devops/pipelines).