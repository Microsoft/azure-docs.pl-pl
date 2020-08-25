---
title: Samouczek — informacje na temat uzyskiwania dostępu do chmury prywatnej
description: Dowiedz się, jak uzyskać dostęp do chmury prywatnej rozwiązania Azure VMware
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 73226c6aa567dc5fbe18251bed4812637664a02c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750537"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-private-cloud"></a>Samouczek: informacje na temat uzyskiwania dostępu do chmury prywatnej rozwiązania Azure VMware

W trakcie okresu zapoznawczego rozwiązanie Azure VMware nie pozwala na zarządzanie chmurą prywatną przy użyciu lokalnego serwera vCenter. Musisz wykonać dodatkową konfigurację i połączenie z lokalnym wystąpieniem programu vCenter za pomocą pola skoku. 

W tym samouczku utworzysz maszynę wirtualną z systemem Windows dla pola skoku w grupie zasobów utworzonej w poprzednim samouczku samouczka [: Konfigurowanie sieci dla swojej chmury prywatnej VMware na platformie Azure](tutorial-configure-networking.md) i logowanie do programu vCenter. Jest to maszyna wirtualna w tej samej sieci wirtualnej, która została utworzona, i zapewnia dostęp do programu vCenter i NSX Manager. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows do użycia w celu nawiązania połączenia z programem vCenter
> * Zaloguj się do programu vCenter z maszyny wirtualnej

## <a name="create-a-new-windows-virtual-machine"></a>Tworzenie nowej maszyny wirtualnej z systemem Windows

1. W grupie zasobów wybierz pozycję **+ Dodaj** , a następnie wyszukaj i wybierz pozycję **Microsoft Windows 10**, a następnie kliknij pozycję **Utwórz**.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Dodaj nową maszynę wirtualną z systemem Windows 10 dla serwera przesiadkowego." border="true":::

1. Wprowadź wymagane informacje w polach, a następnie wybierz pozycję **Przegląd + Utwórz**. Aby uzyskać więcej informacji na temat pól, zobacz poniższą tabelę.

   | Pole | Wartość |
   | --- | --- |
   | **Subskrypcja** | Ta wartość jest już wypełniona z subskrypcją, do której należy Grupa zasobów. |
   | **Grupa zasobów** | Ta wartość jest już wypełniona dla bieżącej grupy zasobów. Powinna to być grupa zasobów utworzona w poprzednim samouczku. |
   | **Nazwa maszyny wirtualnej** | Wprowadź unikatową nazwę maszyny wirtualnej. |
   | **Region** | Wybierz lokalizację geograficzną maszyny wirtualnej. |
   | **Opcje dostępności** | Pozostaw wybraną wartość domyślną. |
   | **Obraz** | Wybierz obraz maszyny wirtualnej. |
   | **Rozmiar** | Pozostaw wartość domyślną rozmiar. |
   | **Authentication type** (Typ uwierzytelniania)  | Wybierz pozycję **hasło**. |
   | **Nazwa użytkownika** | Wprowadź nazwę użytkownika, aby zalogować się do maszyny wirtualnej. |
   | **Hasło** | Wprowadź hasło, aby zalogować się do maszyny wirtualnej. |
   | **Potwierdź hasło** | Wprowadź hasło, aby zalogować się do maszyny wirtualnej. |
   | **Publiczne porty ruchu przychodzącego** | Wybierz pozycję **Brak**. W przypadku wybrania opcji Brak można użyć [dostępu JIT](../security-center/security-center-just-in-time.md#jit-configure) , aby kontrolować dostęp do maszyny wirtualnej tylko wtedy, gdy chcesz uzyskać do niej dostęp.  |

1. Po wprowadzeniu odpowiednich informacji kliknij przycisk **Przegląd + Utwórz**. 
1. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby rozpocząć proces tworzenia maszyny wirtualnej.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Utwórz nową maszynę wirtualną z systemem Windows 10 dla serwera przesiadkowego." border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Nawiązywanie połączenia z lokalnym programem vCenter w chmurze prywatnej

1. W polu skoku Zaloguj się, aby vSphere klienta przy użyciu programu VMware vCenter SSO. Zaloguj się do klienta vSphere przy użyciu nazwy użytkownika administratora chmury; Zaakceptuj zagrożenie bezpieczeństwa i Kontynuuj, gdy zobaczysz ostrzeżenie dotyczące potencjalnego zagrożenia bezpieczeństwa; Zaloguj się do programu VMware vCenter przy użyciu poświadczeń logowania jednokrotnego i sprawdź, czy interfejs użytkownika został pomyślnie wyświetlony.

1. W Azure Portal Wybierz chmurę prywatną, a następnie w widoku **Przegląd** wybierz pozycję **tożsamość > domyślna**. Wyświetlane są adresy URL i poświadczenia logowania dla usługi Private Cloud vCenter i NSX-T.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Wyświetlaj adresy URL i poświadczenia usługi Cloud webvcenter i NSX Manager." border="true":::

1. Przejdź do maszyny wirtualnej utworzonej w poprzednim kroku i Połącz się z maszyną wirtualną. Aby uzyskać szczegółowe instrukcje dotyczące nawiązywania połączenia z maszyną wirtualną, zobacz [nawiązywanie połączenia z maszyną wirtualną](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

1. Na maszynie wirtualnej z systemem Windows otwórz przeglądarkę i przejdź do adresu URL menedżera vCenter i NSX-T na dwóch kartach. Na karcie vCenter wprowadź `cloudadmin@vmcp.local` poświadczenia użytkownika z poprzedniego kroku.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Zaloguj się do usługi vCenter w chmurze prywatnej." border="true":::

   :::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="Portal vCenter." border="true":::

1. Na drugiej karcie przeglądarki Zaloguj się do Menedżera NSX-T.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Na drugiej karcie przeglądarki Zaloguj się do Menedżera NSX-T." border="true":::

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Windows do użycia w celu nawiązania połączenia z programem vCenter
> * Zaloguj się do programu vCenter z maszyny wirtualnej

Przejdź do następnego samouczka, aby dowiedzieć się, jak skalować chmurę prywatną rozwiązania Azure VMware.

> [!div class="nextstepaction"]
> [Skalowanie chmury prywatnej rozwiązania Azure VMware](tutorial-scale-private-cloud.md)
