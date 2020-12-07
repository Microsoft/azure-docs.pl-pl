---
title: Rozwiązywanie problemów z projektami usługi Azure Migrate
description: Pomaga rozwiązywać problemy związane z tworzeniem projektów Azure Migrate i zarządzanie nimi.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/01/2020
ms.openlocfilehash: f68a57d3780f388488d48835f322ff04ab7c7187
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753386"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Rozwiązywanie problemów z projektami usługi Azure Migrate

Ten artykuł pomaga rozwiązywać problemy podczas tworzenia projektów [Azure Migrate](migrate-services-overview.md) i zarządzania nimi.

## <a name="how-to-add-new-project"></a>Jak dodać nowy projekt?

W ramach subskrypcji można mieć wiele projektów Azure Migrate. [Dowiedz się, jak](./create-manage-projects.md) utworzyć projekt po raz pierwszy lub [dodać dodatkowe](create-manage-projects.md#create-additional-projects) projekty.

## <a name="what-azure-permissions-are-needed"></a>Jakie uprawnienia platformy Azure są potrzebne?

Aby utworzyć projekt Azure Migrate, musisz mieć uprawnienia współautora lub właściciela w ramach subskrypcji.

## <a name="cant-find-a-project"></a>Nie można znaleźć projektu

Znalezienie istniejącego projektu Azure Migrate zależy od tego, czy używana jest bieżąca lub stara wersja Azure Migrate. [Postępuj zgodnie z instrukcjami](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Nie można znaleźć lokalizacji geograficznej

Projekt Azure Migrate można utworzyć w obszarze obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i dla [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="what-are-vm-limits"></a>Co to są limity maszyn wirtualnych?

Można ocenić do 35 000 maszyn wirtualnych VMware lub do 35 000 maszyn wirtualnych funkcji Hyper-V w jednym projekcie. Projekt może zawierać zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, a także limity oceny.

## <a name="can-i-upgrade-old-project"></a>Czy mogę uaktualnić stary projekt?

Nie można zaktualizować projektów z poprzedniej wersji Azure Migrate. Należy [utworzyć nowy projekt](./create-manage-projects.md)i dodać do niego narzędzia.

## <a name="cant-create-a-project"></a>Nie można utworzyć projektu

Jeśli spróbujesz utworzyć projekt i napotkasz błąd wdrożenia:

- Spróbuj ponownie utworzyć projekt w przypadku, gdy jest to błąd przejściowy. W obszarze **wdrożenia** kliknij pozycję **Wdróż ponownie** , aby spróbować ponownie.
- Sprawdź, czy masz uprawnienia współautora lub właściciela w ramach subskrypcji.
- Jeśli wdrażasz program w nowo dodanym obszarze geograficznym, poczekaj chwilę i spróbuj ponownie.
- Jeśli zostanie wyświetlony komunikat o błędzie "żądania muszą zawierać nagłówki tożsamości użytkownika", może to wskazywać, że nie masz dostępu do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji. W takim przypadku:
    - Gdy po raz pierwszy dodasz do dzierżawy usługi Azure AD, otrzymasz wiadomość e-mail z zaproszeniem do dołączenia do dzierżawy.
    - Zaakceptuj zaproszenie, które ma zostać dodane do dzierżawy.
    - Jeśli nie widzisz wiadomości e-mail, skontaktuj się z użytkownikiem w celu uzyskania dostępu do dzierżawy i poproś o [ponowne wysłanie zaproszenia](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) do Ciebie.
    - Po odebraniu wiadomości e-mail z zaproszeniem Otwórz ją i wybierz link, aby zaakceptować zaproszenie. Następnie wyloguj się z Azure Portal i zaloguj się ponownie. (odświeżanie przeglądarki nie będzie działało). Następnie można rozpocząć tworzenie projektu migracji.

## <a name="how-do-i-delete-a-project"></a>Jak mogę usunąć projektu

[Postępuj zgodnie z tymi instrukcjami](create-manage-projects.md#delete-a-project) , aby usunąć projekt. Należy pamiętać, że po usunięciu projektu, zarówno projekt, jak i metadane dotyczące odnalezionych maszyn w projekcie zostaną usunięte.

## <a name="added-tools-dont-show"></a>Dodane narzędzia nie są wyświetlane

Upewnij się, że wybrano odpowiedni projekt. Na **serwerach** Azure Migrate Hub > lub w **bazach danych** kliknij pozycję **Zmień** obok pozycji **Migruj projekt (Zmień)** w prawym górnym rogu ekranu. Wybierz poprawną subskrypcję i nazwę projektu, > **OK**. Strona powinna zostać odświeżona przy użyciu dodanych narzędzi wybranego projektu.

## <a name="next-steps"></a>Następne kroki

Dodaj narzędzia do [oceny](how-to-assess.md) lub [migracji](how-to-migrate.md) do Azure Migrate projektów.