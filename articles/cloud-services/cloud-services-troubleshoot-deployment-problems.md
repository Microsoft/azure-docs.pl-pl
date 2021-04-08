---
title: Rozwiązywanie problemów z wdrażaniem usługi w chmurze (klasycznej) | Microsoft Docs
description: Istnieje kilka typowych problemów, które można napotkać podczas wdrażania usługi w chmurze na platformie Azure. Ten artykuł zawiera rozwiązania dla niektórych z nich.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7b3d7a9a674aab3976da9399f71ff4d8df08eb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741081"
---
# <a name="troubleshoot-azure-cloud-services-classic-deployment-problems"></a>Rozwiązywanie problemów z wdrażaniem w usłudze Azure Cloud Services (klasyczne)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Podczas wdrażania pakietu aplikacji usługi w chmurze na platformie Azure można uzyskać informacje dotyczące wdrożenia z okienka **Właściwości** w Azure Portal. Możesz użyć szczegółów w tym okienku, aby pomóc w rozwiązywaniu problemów z usługą w chmurze, a także podać te informacje do pomocy technicznej platformy Azure podczas otwierania nowego żądania pomocy technicznej.

Okienko **Właściwości** można znaleźć w następujący sposób:

* W Azure Portal kliknij wdrożenie usługi w chmurze, kliknij pozycję **wszystkie ustawienia**, a następnie kliknij pozycję **Właściwości**.

> [!NOTE]
> Zawartość okienka **Właściwości** można skopiować do schowka, klikając ikonę w prawym górnym rogu okienka.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problem: nie można uzyskać dostępu do witryny sieci Web, ale wdrożenie zostało uruchomione i wszystkie wystąpienia roli są gotowe
Link adresu URL witryny sieci Web wyświetlany w portalu nie zawiera portu. Domyślnym portem dla witryn sieci Web jest 80. Jeśli aplikacja jest skonfigurowana do uruchamiania w innym porcie, podczas uzyskiwania dostępu do witryny sieci Web należy dodać poprawny numer portu.

1. W Azure Portal kliknij wdrożenie usługi w chmurze.
2. W okienku **właściwości** Azure Portal Sprawdź porty wystąpień roli (w obszarze **wejściowe punkty końcowe**).
3. Jeśli port nie jest 80, Dodaj poprawną wartość portu do adresu URL podczas uzyskiwania dostępu do aplikacji. Aby określić port inny niż domyślny, wpisz adres URL, po którym następuje dwukropek (:), a następnie numer portu, bez spacji.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problem: moje wystąpienia roli są odtwarzane bez wykonywania jakichkolwiek czynności
Naprawianie usługi odbywa się automatycznie, gdy platforma Azure wykrywa węzły problemu i w związku z tym przenosi wystąpienia ról do nowych węzłów. W takim przypadku może być automatycznie wyświetlane odtwarzanie wystąpień ról. Aby dowiedzieć się, czy nastąpiła próba naprawy usługi:

1. W Azure Portal kliknij wdrożenie usługi w chmurze.
2. W okienku **właściwości** Azure Portal zapoznaj się z informacjami i określ, czy nastąpiło Naprawianie usługi w czasie, w którym zaobserwowano odtwarzanie ról.

Role również będą odtwarzane w trybie około raz miesięcznie w trakcie aktualizacji systemu operacyjnego hosta i systemu operacyjnego gościa.  
Aby uzyskać więcej informacji, zobacz wpis w blogu [Ponowne uruchamianie wystąpienia roli z powodu uaktualnień systemu operacyjnego](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problem: nie można dokonać zamiany adresu VIP i otrzymać błąd
Zamiana adresu VIP nie jest dozwolona, jeśli aktualizacja wdrożenia jest w toku. Aktualizacje wdrożenia mogą odbywać się automatycznie, gdy:

* Dostępny jest nowy system operacyjny gościa, który jest konfigurowany do aktualizacji automatycznych.
* Naprawianie usługi.

Aby dowiedzieć się, czy aktualizacja automatyczna uniemożliwia przeprowadzenie wymiany adresu VIP:

1. W Azure Portal kliknij wdrożenie usługi w chmurze.
2. W okienku **właściwości** Azure Portal Sprawdź wartość **stan**. Jeśli jest **gotowa**, sprawdź **ostatnią operację** , aby zobaczyć, czy niedawno wystąpił, co może uniemożliwić zamianę adresu VIP.
3. Powtórz kroki 1 i 2 w przypadku wdrożenia produkcyjnego.
4. Jeśli aktualizacja automatyczna jest w toku, poczekaj na jej zakończenie przed podjęciem próby wykonania zamiany adresu VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problem: wystąpienie roli zawiera pętlę między uruchomioną, inicjowanie, zajętą i zatrzymaną
Ten stan może wskazywać na problem z kodem, pakietem lub plikiem konfiguracyjnym aplikacji. W takim przypadku powinno być możliwe wyświetlenie stanu co kilka minut, a Azure Portal może powiedzieć, jak **odtwarzanie**, **zajętość** lub **Inicjowanie**. Oznacza to, że wystąpił problem z aplikacją, która uniemożliwia uruchomienie wystąpienia roli.

Aby uzyskać więcej informacji na temat rozwiązywania tego problemu, zobacz wpis na blogu [dane diagnostyczne usługi Azure PaaS COMPUTE](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data) i [typowe problemy, które powodują](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)odtworzenie ról.

## <a name="problem-my-application-stopped-working"></a>Problem: moja aplikacja przestała działać
1. W Azure Portal kliknij wystąpienie roli.
2. W okienku **właściwości** Azure Portal należy wziąć pod uwagę następujące warunki, aby rozwiązać problem:
   * Jeśli wystąpienie roli zostało ostatnio zatrzymane (można sprawdzić wartość **licznika przerwań**), wdrożenie może zostać zaktualizowane. Poczekaj, aż wystąpienie roli wznowi działanie.
   * Jeśli wystąpienie roli jest **zajęte**, sprawdź kod aplikacji, aby sprawdzić, czy zdarzenie [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) jest obsługiwane. Być może trzeba będzie dodać lub naprawić jakiś kod, który obsługuje to zdarzenie.
   * Zapoznaj się z scenariuszami dotyczącymi danych diagnostycznych i rozwiązywania problemów w blogu Publikuj [dane diagnostyki usługi Azure PaaS COMPUTE](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

> [!WARNING]
> W przypadku odtwarzania usługi w chmurze należy zresetować właściwości wdrożenia, co skutecznie wymazuje informacje dotyczące oryginalnego problemu.
>
>

## <a name="next-steps"></a>Następne kroki
Zobacz więcej [artykułów do rozwiązywania problemów](./cloud-services-allocation-failures.md) dotyczących usług Cloud Services.

Aby dowiedzieć się, jak rozwiązywać problemy z rolą usługi w chmurze, korzystając z danych diagnostycznych dotyczących komputerów z usługą Azure PaaS, zobacz [Seria blogów Piotr Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).