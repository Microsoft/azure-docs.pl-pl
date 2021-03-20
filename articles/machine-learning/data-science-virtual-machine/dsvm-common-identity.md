---
title: Skonfiguruj wspólną tożsamość
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak tworzyć popularne konta użytkowników, które mogą być używane w wielu Virtual Machinesach analizy danych. Można użyć Azure Active Directory lub Active Directory lokalnych do uwierzytelniania użytkowników w Data Science Virtual Machine.
keywords: głębokie uczenie się, AI, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analiza geoprzestrzenna, proces nauki o danych zespołowych
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 43ef0f55f0b0f3ff9008f0ddf240ad0ea4f37df6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519732"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Skonfiguruj wspólną tożsamość na Data Science Virtual Machine

Na Microsoft Azure maszynę wirtualną (VM), w tym Data Science Virtual Machine (DSVM), tworzysz konta użytkowników lokalnych podczas aprowizacji maszyny wirtualnej. Następnie użytkownicy uwierzytelniają się na maszynie wirtualnej przy użyciu tych poświadczeń. Jeśli masz wiele maszyn wirtualnych, do których użytkownicy potrzebują dostępu, zarządzanie poświadczeniami może być bardzo uciążliwe. Doskonałe rozwiązanie polega na wdrożeniu wspólnych kont użytkowników i zarządzania za pośrednictwem dostawcy tożsamości opartego na standardach. Korzystając z tej metody, można użyć jednego zestawu poświadczeń w celu uzyskania dostępu do wielu zasobów na platformie Azure, w tym wielu DSVMs.

Active Directory to popularny dostawca tożsamości i jest obsługiwany na platformie Azure jako usługa w chmurze i jako katalog lokalny. Korzystając z Azure Active Directory (Azure AD) lub Active Directory lokalnych, można uwierzytelniać użytkowników w autonomicznym DSVM lub klastrze DSVMs w zestawie skalowania maszyn wirtualnych platformy Azure. Można to zrobić przez przyłączenie wystąpień DSVM do domeny Active Directory.

Jeśli masz już Active Directory, możesz użyć jej jako wspólnego dostawcy tożsamości. Jeśli nie masz Active Directory, możesz uruchomić zarządzane wystąpienie Active Directory na platformie Azure za pomocą [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) (AD DS platformy Azure).

Dokumentacja [usługi Azure AD](../../active-directory/index.yml) zawiera szczegółowe [instrukcje dotyczące zarządzania](../../active-directory/hybrid/whatis-hybrid-identity.md), w tym wskazówki dotyczące łączenia usługi Azure AD z katalogiem lokalnym, jeśli go masz.

W tym artykule opisano sposób konfigurowania w pełni zarządzanej usługi domeny Active Directory na platformie Azure przy użyciu usługi Azure AD DS. Następnie można przyłączyć się do DSVMs do domeny zarządzanej Active Directory. Takie podejście umożliwia użytkownikom dostęp do puli DSVMs (i innych zasobów platformy Azure) za pomocą wspólnego konta użytkownika i poświadczeń.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Skonfiguruj w pełni zarządzaną domenę Active Directory na platformie Azure

Usługa Azure AD DS ułatwia zarządzanie tożsamościami przez udostępnienie w pełni zarządzanej usługi na platformie Azure. W tej domenie Active Directory zarządzasz użytkownikami i grupami. Aby skonfigurować domenę Active Directory i konta użytkowników hostowane na platformie Azure, wykonaj następujące kroki:

1. W Azure Portal Dodaj użytkownika do Active Directory: 

   1. Zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
    
   1. Wybierz pozycję **Azure Active Directory**, a następnie **Użytkownicy i grupy**.
    
   1. W obszarze **Użytkownicy i grupy** wybierz pozycję **Wszyscy użytkownicy**, a następnie wybierz pozycję **nowy użytkownik**.
   
        Zostanie otwarte okienko **użytkownika** :
      
        ![Okienko "użytkownik"](./media/add-user.png)
    
   1. Wprowadź dane użytkownika, na przykład **Nazwisko** i **Nazwę użytkownika**. Częścią nazwy domeny musi być początkowa domyślna nazwa domeny "[nazwa domeny]. onmicrosoft. com" lub zweryfikowana, Niefederacyjna [nazwa domeny niestandardowej](../../active-directory/fundamentals/add-custom-domain.md) , taka jak "contoso.com".
    
   1. Skopiuj lub w inny sposób zanotuj wygenerowane hasło użytkownika, aby przekazać je użytkownikowi po ukończeniu tego procesu.
    
   1. Opcjonalnie możesz otworzyć sekcje **Profil**, **Grupy** lub **Rola katalogu** dla użytkownika i uzupełnić w nich informacje. 
    
   1. W obszarze **użytkownik** wybierz pozycję **Utwórz**.
    
   1. Bezpiecznie Rozpowszechnij wygenerowane hasło do nowego użytkownika, aby mogli się zalogować.

1. Utwórz wystąpienie usługi Azure AD DS. Postępuj zgodnie z instrukcjami w artykule  [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md) (sekcja "Tworzenie wystąpienia i Konfigurowanie ustawień podstawowych"). Ważne jest, aby zaktualizować istniejące hasła użytkowników w Active Directory, aby hasło w usłudze Azure AD DS zostało zsynchronizowane. Ważne jest również, aby dodać serwer DNS do usługi Azure AD DS, zgodnie z opisem w sekcji "Wypełnij pola w oknie podstawy Azure Portal, aby utworzyć wystąpienie usługi Azure AD DS".

1. Utwórz oddzielną podsieć DSVM w sieci wirtualnej utworzonej w sekcji "Tworzenie i Konfigurowanie sieci wirtualnej" w poprzednim kroku.
1. Utwórz co najmniej jedno wystąpienie DSVM w podsieci DSVM.
1. Postępuj zgodnie z [instrukcjami](../../active-directory-domain-services/join-ubuntu-linux-vm.md) , aby dodać DSVM do Active Directory. 
1. Zainstaluj udział Azure Files w celu hostowania katalogu macierzystego lub notesu, aby można było zainstalować ten obszar roboczy na dowolnym komputerze. (Jeśli potrzebujesz ścisłych uprawnień na poziomie pliku, musisz mieć sieciowy system plików [NFS] uruchomiony na co najmniej jednej maszyny wirtualnej).

   1. [Utwórz udział Azure Files](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Zainstaluj ten udział w systemie Linux DSVM. Po wybraniu opcji **Połącz** dla udziału Azure Files na koncie magazynu w Azure Portal zostanie wyświetlony polecenie Uruchom w powłoce bash na DSVM systemu Linux. Polecenie wygląda następująco:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Załóżmy na przykład, że zainstalowano udział Azure Files w/Data/Workspace. Teraz utwórz katalogi dla każdego z użytkowników w udziale:/Data/Workspace/user1,/Data/Workspace/User2 i tak dalej. Utwórz `notebooks` katalog w obszarze roboczym każdego użytkownika. 
1. Utwórz linki symboliczne dla `notebooks` programu w programie `$HOME/userx/notebooks/remote` .   

Teraz użytkownicy znajdują się w wystąpieniu Active Directory hostowanym na platformie Azure. Przy użyciu poświadczeń Active Directory użytkownicy mogą logować się do dowolnego DSVM (SSH lub JupyterHub) dołączonego do usługi Azure AD DS. Ponieważ obszar roboczy użytkownika znajduje się w udziale Azure Files, użytkownicy mają dostęp do swoich notesów i innych pracy z dowolnego DSVM, gdy korzystają z JupyterHub.

W przypadku skalowania automatycznego można użyć zestawu skalowania maszyn wirtualnych, aby utworzyć pulę maszyn wirtualnych, które są połączone z domeną w ten sposób i z zainstalowanym dyskiem udostępnionym. Użytkownicy mogą logować się na dowolnym komputerze z zestawu skalowania maszyn wirtualnych i mieć dostęp do udostępnionego dysku, na którym są zapisane ich notesy. 

## <a name="next-steps"></a>Następne kroki

* [Bezpieczne przechowywanie poświadczeń w celu uzyskania dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)