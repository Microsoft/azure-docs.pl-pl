---
title: Samouczek — Tworzenie zestawu replik w Azure AD Domain Services | Microsoft Docs
description: Informacje na temat tworzenia i używania zestawów replik w Azure Portal na potrzeby odporności usługi z Azure AD Domain Services
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: joflore
ms.openlocfilehash: 8cd78a98e47468f103459fd7aa40b145d53db4fd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967039"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services-preview"></a>Samouczek: Tworzenie i używanie zestawów replik dla odporności lub geolokalizacji w Azure Active Directory Domain Services (wersja zapoznawcza)

Aby zwiększyć odporność domeny zarządzanej Azure Active Directory Domain Services (Azure AD DS) lub wdrożyć w dodatkowych lokalizacjach geograficznych blisko aplikacji, można użyć *zestawów replik*. Każda przestrzeń nazw domeny zarządzanej platformy Azure AD DS, na przykład *aaddscontoso.com*, zawiera jeden zestaw replik początkowych. Możliwość tworzenia dodatkowych zestawów replik w innych regionach platformy Azure zapewnia odporność geograficzną dla domeny zarządzanej.

Zestaw replik można dodać do dowolnej równorzędnej sieci wirtualnej w dowolnym regionie świadczenia usługi Azure, który obsługuje usługę Azure AD DS.

Zestawy replik są funkcją publicznej wersji zapoznawczej w Azure AD Domain Services. Zapoznaj się z różnicami w zakresie pomocy technicznej, które istnieją dla funkcji w wersji zapoznawczej. Aby uzyskać więcej informacji o wersjach zapoznawczych, [Azure Active Directory wersji zapoznawczej umowy SLA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Informacje o wymaganiach dotyczących sieci wirtualnej
> * Tworzenie zestawu replik
> * Usuń zestaw replik

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena utworzona przy użyciu Azure Resource Manager modelu wdrażania i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie konieczności [Utwórz i skonfiguruj Azure Active Directory Domain Services domenę zarządzaną][tutorial-create-instance].

    > [!IMPORTANT]
    > W przypadku domen zarządzanych utworzonych przy użyciu klasycznego modelu wdrażania nie można używać zestawów replik. Należy również użyć minimalnej jednostki SKU *przedsiębiorstwa* dla domeny zarządzanej. W razie potrzeby [Zmień jednostkę SKU dla domeny zarządzanej][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W tym samouczku utworzysz zestawy replik i zarządzasz je przy użyciu Azure Portal. Aby rozpocząć, najpierw Zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="networking-considerations"></a>Zagadnienia dotyczące pracy w sieci

Sieci wirtualne, które obsługują zestawy replik, muszą być w stanie komunikować się ze sobą. Aplikacje i usługi, które są zależne od usługi Azure AD DS, wymagają także łączności sieciowej z sieciami wirtualnymi hostującym zestawy replik. Komunikacja równorzędna usługi Azure Virtual Network powinna być skonfigurowana między wszystkimi sieciami wirtualnymi, aby utworzyć w pełni siatkę sieci. Te Komunikacja równorzędna umożliwiają skuteczną replikację między zestawami replik.

Aby można było używać zestawów replik w usłudze Azure AD DS, należy zapoznać się z następującymi wymaganiami dotyczącymi sieci wirtualnej platformy Azure:

* Unikaj nakładających się przestrzeni adresów IP, aby umożliwić komunikację równorzędną i routing sieci wirtualnych.
* Utwórz podsieci z wystarczającą liczbą adresów IP, aby obsłużyć twój scenariusz.
* Upewnij się, że usługa Azure AD DS ma własną podsieć. Nie udostępniaj tej podsieci sieci wirtualnej z maszynami wirtualnymi i usługami aplikacji.
* Równorzędne sieci wirtualne nie są przechodnie.

> [!TIP]
> Po utworzeniu zestawu replik w Azure Portal sieci Komunikacja równorzędna między sieciami wirtualnymi zostanie utworzona.
>
> W razie konieczności można utworzyć sieć wirtualną i podsieć podczas dodawania zestawu replik w Azure Portal. Można też wybrać istniejące zasoby sieci wirtualnej w regionie docelowym dla zestawu replik i zezwolić na automatyczne tworzenie komunikacji równorzędnej, jeśli jeszcze nie istnieją.

## <a name="create-a-replica-set"></a>Tworzenie zestawu replik

Podczas tworzenia domeny zarządzanej, takiej jak *aaddscontoso.com*, zostaje utworzony początkowy zestaw replik. Dodatkowe zestawy replik mają tę samą przestrzeń nazw i konfigurację. Zmiany w usłudze Azure AD DS, w tym konfiguracja, tożsamość użytkownika i poświadczenia, grupy, obiekty zasad grupy, obiekty komputerów i inne zmiany są stosowane do wszystkich zestawów replik w domenie zarządzanej przy użyciu replikacji AD DS.

W tym samouczku utworzysz dodatkowy zestaw replik w regionie świadczenia usługi Azure innym niż początkowy zestaw replik platformy Azure AD DS.

Aby utworzyć dodatkowy zestaw replik, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**.
1. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Po lewej stronie wybierz pozycję **zestawy replik (wersja zapoznawcza)**. Każda domena zarządzana zawiera jeden zestaw replik początkowych w wybranym regionie, jak pokazano na poniższym przykładowym zrzucie ekranu:

    ![Przykładowy zrzut ekranu umożliwiający wyświetlenie i dodanie zestawu replik w Azure Portal](./media/tutorial-create-replica-set/replica-set-list.png)

    Aby utworzyć dodatkowy zestaw replik, wybierz pozycję **+ Dodaj**.

1. W oknie *Dodawanie zestawu replik* wybierz region docelowy, taki jak *Wschodnie stany USA*.

    Wybierz sieć wirtualną w regionie docelowym, na przykład Sieć wirtualna *-Wschodnie*, a następnie wybierz podsieć, taką jak *aadds-Subnet*. W razie potrzeby wybierz pozycję **Utwórz nowy** , aby dodać sieć wirtualną w regionie docelowym, a następnie **Zarządzaj** , aby utworzyć podsieć dla AD DS platformy Azure.

    Jeśli jeszcze nie istnieją, Komunikacja równorzędna sieci wirtualnej platformy Azure jest tworzona automatycznie między istniejącą siecią wirtualną i docelową siecią wirtualną.

    Poniższy przykładowy zrzut ekranu przedstawia proces tworzenia nowego zestawu replik w *regionie Wschodnie stany USA*:

    ![Przykładowy zrzut ekranu, aby utworzyć zestaw replik w Azure Portal](./media/tutorial-create-replica-set/create-replica-set.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

Proces tworzenia repliki trwa jakiś czas, ponieważ zasoby są tworzone w regionie docelowym. Sama domena zarządzana jest następnie replikowana przy użyciu AD DS replikacji.

Zestaw replik zgłasza, że *Inicjowanie obsługi* jest kontynuowane, jak pokazano na poniższym przykładowym zrzucie ekranu. Po zakończeniu zestaw replik pokazuje jako *uruchomiony*.

![Przykładowy zrzut ekranu stanu wdrożenia zestawu replik w Azure Portal](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Usuń zestaw replik

Domena zarządzana jest obecnie ograniczona do czterech replik — zestawu replik początkowych i trzech dodatkowych zestawów replik. Jeśli zestaw replik nie jest już potrzebny lub jeśli chcesz utworzyć zestaw replik w innym regionie, możesz usunąć niepotrzebne zestawy replik.

> [!IMPORTANT]
> Nie można usunąć ostatniego zestawu replik w domenie zarządzanej.

Aby usunąć zestaw replik, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**.
1. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Po lewej stronie wybierz pozycję **zestawy replik (wersja zapoznawcza)**. Z listy zestawów replik wybierz **menu kontekstowe** obok zestawu replik, który chcesz usunąć.
1. Z menu kontekstowego wybierz pozycję **Usuń** , a następnie potwierdź, że chcesz usunąć zestaw replik.

> [!NOTE]
> Usunięcie zestawu replik może być czasochłonną operacją.

Jeśli nie potrzebujesz już sieci wirtualnej lub komunikacji równorzędnej używanej przez zestaw replik, możesz również usunąć te zasoby. Upewnij się, że żadne inne zasoby aplikacji w innym regionie nie potrzebują połączeń sieciowych przed ich usunięciem.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie komunikacji równorzędnej sieci wirtualnej
> * Tworzenie zestawu replik w innym regionie geograficznym
> * Usuń zestaw replik

Aby uzyskać więcej informacji koncepcyjnych, Dowiedz się, jak działają zestawy replik na platformie Azure AD DS.

> [!div class="nextstepaction"]
> [Replika — koncepcje i funkcje][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
