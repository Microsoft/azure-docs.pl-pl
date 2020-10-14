---
title: Tworzenie laboratorium w usłudze Azure DevTest Labs | Microsoft Docs
description: W tym artykule omówiono proces tworzenia laboratorium przy użyciu Azure Portal i Azure DevTest Labs.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 962997bcc66188c66fd9db856fe44e4926f8e70c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019651"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Tworzenie laboratorium w usłudze Azure DevTest Labs

Azure DevTest Labs to infrastruktura, która obejmuje wiele różnych zasobów (np. maszyn wirtualnych) i umożliwia lepsze zarządzanie tymi zasobami przez określenie ograniczeń i przydziałów. W tym artykule szczegółowo omówiono proces tworzenia laboratorium przy użyciu witryny Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć laboratorium, potrzebne są:

* Subskrypcja platformy Azure. Aby zapoznać się z opcjami zakupu platformy Azure, zobacz [Jak kupić system Azure](https://azure.microsoft.com/pricing/purchase-options/) lub [Bezpłatna miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/). Aby utworzyć laboratorium, trzeba być właścicielem subskrypcji.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Rozpoczynanie pracy z usługą Azure DevTest Labs w zaledwie kilka minut

Kliknięcie następującego linku spowoduje przeniesienie do strony Azure Portal, która umożliwia rozpoczęcie tworzenia nowego laboratorium w Azure DevTest Labs.

[Rozpoczynanie pracy z usługą Azure DevTest Labs w zaledwie kilka minut](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Wypełnij ustawienia dla nowego konta

Na stronie **Tworzenie DevTest Labs** Wypełnij poniższe ustawienia.

> [!TIP]
> W dolnej części każdej strony znajduje się link umożliwiający **pobranie szablonu do automatyzacji**.

### <a name="basic-settings"></a>Ustawienia podstawowe

Domyślnie zostanie wyświetlona karta **Ustawienia podstawowe** . Wypełnij te wartości:

|Nazwa|Opis|
|---|---|
|**Subskrypcja** | Wymagane. W pozycji **Subskrypcja** wybierz subskrypcję do skojarzenia z laboratorium.|
|**Grupa zasobów**| Wymagane. Wprowadź **nazwę grupy zasobów** dla laboratorium. Utwórz nowy, jeśli taki nie istnieje.|
|**Nazwa laboratorium**| Wymagane. Wprowadź **nazwę** laboratorium.|
|**Lokalizacja**|Wymagane. Wybierz lokalizację, w której ma zostać zapisane laboratorium.|
|**Środowiska publiczne**| Zobacz [Konfigurowanie i używanie środowisk publicznych](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Ustawienia automatycznego zamykania

Przejdź do strony **automatycznego zamykania** , aby wyświetlić jej ustawienia. Automatyczne zamknięcie umożliwia automatyczne zamknięcie wszystkich maszyn w laboratorium w zaplanowanym terminie każdego dnia.

Na stronie można włączyć **automatyczne zamykanie** i zdefiniować parametry automatycznego zamykania wszystkich maszyn wirtualnych laboratorium... Funkcja automatycznego zamykania służy głównie do osiągnięcia oszczędności. Pozwala określić, kiedy ma dojść do automatycznego zamknięcia maszyny wirtualnej. Ustawienia automatycznego zamykania można zmienić po utworzeniu laboratorium, wykonując czynności opisane w artykule [Zarządzanie wszystkimi zasadami dla laboratorium w Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

### <a name="networking"></a>Networking

Podczas tworzenia laboratorium zostanie utworzona domyślna sieć.

Przejdź do karty **Sieć** , aby zmienić lub skonfigurować odpowiednie ustawienie. Na przykład wybierz istniejącą sieć wirtualną.

### <a name="tags"></a>Tags

Wprowadź informacje w polach **NAZWA** i **WARTOŚĆ** w obszarze **Tagi**, jeśli chcesz utworzyć niestandardowe tagowanie, które będzie dodawane do każdego zasobu utworzonego w laboratorium. Tagi są przydatne do porządkowania zasobów laboratorium według kategorii i zarządzania nimi. Aby uzyskać więcej informacji na temat tagów, w tym na temat sposobu dodawania tagów po utworzeniu laboratorium, zobacz [Dodawanie tagów do laboratorium](devtest-lab-add-tag.md).

### <a name="review-and-create"></a>Przegląd i tworzenie

Po zakończeniu wybierz pozycję **Utwórz**. Stan procesu tworzenia laboratorium można monitorować, obserwując obszar **powiadomienia** w prawym górnym rogu strony portalu. 

## <a name="completed-the-creation"></a>Ukończono tworzenie

Po ukończeniu przycisk **Przejdź do zasobu** pojawia się u dołu strony i w oknie powiadomień. Alternatywnie możesz odświeżyć stronę **DevTest Labs** , aby zobaczyć nowo utworzone laboratorium na liście laboratoriów.  

Naciśnij przycisk **Przejdź do zasobu** i nastąpi przejście do strony głównej nowego konta w usłudze DevTest Labs.

Możesz również wyszukać **DevTest Labs** w Azure Portal. Wybierz nowe konto z listy i uzyskaj dostęp do strony głównej. 

## <a name="next-steps"></a>Następne kroki

Po utworzeniu laboratorium warto rozważyć poniższe kroki:

* [Zabezpieczanie dostępu do laboratoriów](devtest-lab-add-devtest-user.md)
* [Ustawianie zasad laboratorium](devtest-lab-set-lab-policy.md)
* [Utwórz szablon laboratorium](devtest-lab-create-template.md)
* [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych](devtest-lab-artifact-author.md)
* [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)

