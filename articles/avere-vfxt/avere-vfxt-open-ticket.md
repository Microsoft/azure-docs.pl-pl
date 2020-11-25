---
title: Jak uzyskać pomoc techniczną dotyczącą avere vFXT dla platformy Azure
description: Dowiedz się, jak rozwiązywać problemy, które mogą wystąpić podczas wdrażania lub korzystania z programu avere vFXT dla platformy Azure, tworząc bilet pomocy technicznej za pośrednictwem Azure Portal.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 8159d83624cdd474d91030d5376a3db447beffe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008433"
---
# <a name="get-help-with-your-system"></a>Uzyskaj pomoc dotyczącą systemu

Aby uzyskać pomoc dotyczącą usługi avere vFXT dla systemu Azure, poniżej przedstawiono sposoby uzyskania pomocy technicznej:

* **Problem z avere vFXT** — Użyj Azure Portal, aby otworzyć bilet pomocy technicznej dla vFXT avere, zgodnie z [poniższym](#open-a-support-ticket-for-your-avere-vfxt)opisem.
* **Przydział** — Jeśli masz problem związany z przydziałem, [Poproś o zwiększenie limitu przydziału](#request-a-quota-increase)
* **Dokumentacja i przykłady** — Jeśli znajdziesz problemy z tą dokumentacją lub przykładami, przewiń w dół strony, a następnie skorzystaj z sekcji **Opinie** , aby wyszukać istniejące problemy i w razie potrzeby zastąpić nowe rozwiązanie.

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>Otwórz bilet pomocy technicznej dla Twojego avere vFXT

Jeśli wystąpią problemy podczas wdrażania lub korzystania z avere vFXT, poproś o pomoc za pośrednictwem Azure Portal.

Wykonaj następujące kroki, aby upewnić się, że bilet pomocy technicznej jest oznaczony przy użyciu zasobu z klastra. Tagowanie biletu pomaga nam kierować go do poprawnego zasobu pomocy technicznej.

1. Z programu [https://portal.azure.com](https://portal.azure.com) Wybierz pozycję **grupy zasobów**. Przejdź do grupy zasobów zawierającej klaster vFXT, w którym wystąpił problem, a następnie kliknij jedną z maszyn wirtualnych klastra avere.

    ![zrzut ekranu przedstawiający panel "przegląd" Azure Portal grupy zasobów z określoną maszyną wirtualną](media/avere-vfxt-ticket-vm.png)

1. Na stronie VM przewiń w dół do końca lewego panelu, a następnie kliknij pozycję **nowe żądanie obsługi**.

    ![Zrzut ekranu strony maszyny wirtualnej Azure Portal dla maszyny wirtualnej z poprzedniego zrzutu ekranu. Menu po lewej stronie jest przewijane do dołu i "nowe żądanie obsługi" jest zakreślone.](media/avere-vfxt-ticket-request.png)

1. Na pierwszej stronie żądania pomocy technicznej wybierz typ problemu i upewnij się, że wybrano poprawną subskrypcję.

   W obszarze **Usługa** kliknij pozycję **wszystkie usługi** i w obszarze **Magazyn** wybierz pozycję **avere vFXT**.

   Dodaj krótkie podsumowanie i wybierz typ problemu.

    ![zrzut ekranu przedstawiający nowy ekran żądania obsługi w Azure Portal. Wybrana jest karta podstawy. Elementy ekranu obejmują typ problemu, subskrypcję, usługę, podsumowanie i typ problemu.](media/ticket-basics.png)

   Kliknij przycisk **Dalej** , aby kontynuować.

1. Druga strona formularza pomocy technicznej zawiera sugestie dotyczące rozwiązania problemu na podstawie opisu podsumowania. Kliknij przycisk **dalej** w dolnej części, jeśli nadal potrzebujesz utworzyć bilet pomocy technicznej.

   ![zrzut ekranu przedstawiający nowy ekran żądania obsługi z wybraną kartą rozwiązania. Pole tekstowe w środku ma tytuł "zalecane rozwiązanie" i objaśnia możliwe środki zaradcze.](media/ticket-solutions.png)

1. Na trzeciej stronie Podaj szczegóły — dotyczy to również informacji o klastrze, czasie wystąpienia problemu, ważności i sposobu kontaktowania się z Tobą. Wypełnij informacje i kliknij przycisk **dalej** u dołu.

   ![zrzut ekranu przedstawiający nowy ekran żądania obsługi z wybraną kartą szczegóły. Pola informacji są zorganizowane w kategorii "Szczegóły problemu", "metoda obsługi" i "informacje kontaktowe".](media/ticket-details.png)

1. Zapoznaj się z informacjami na ostatniej stronie i kliknij przycisk **Utwórz**. Potwierdzenie i numer biletu zostaną wysłane na Twój adres e-mail, a członek personelu pomocy technicznej skontaktuje się z Tobą.

## <a name="request-a-quota-increase"></a>Poproś o zwiększenie limitu przydziału

Zapoznaj się z [limitem przydziału dla klastra vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) , aby dowiedzieć się, jakie składniki są potrzebne do wdrożenia avere VFXT dla platformy Azure. Można [zażądać zwiększenia limitu przydziału](../azure-portal/supportability/resource-manager-core-quotas-request.md) z Azure Portal.