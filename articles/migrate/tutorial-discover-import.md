---
title: Ocenianie serwerów lokalnych przy użyciu zaimportowanego pliku CSV z oceną serwera Azure Migrate
description: Opisuje sposób odnajdywania serwerów lokalnych na potrzeby migracji na platformę Azure przy użyciu zaimportowanego pliku CSV w ramach oceny serwera Azure Migrate
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: dfa7ee941e2c373b02fe5fb2f2a648a60a677670
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753114"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Samouczek: ocenianie serwerów przy użyciu zaimportowanego pliku CSV

W ramach kursu migracji na platformę Azure można wykryć spisy i obciążenia lokalne. 

W tym samouczku pokazano, jak ocenić maszyny lokalne za pomocą narzędzia do oceny serwera Azure Migrate:, używając zaimportowanego pliku wartości rozdzielanych przecinkami (CSV). 

W przypadku korzystania z pliku CSV nie trzeba konfigurować urządzenia Azure Migrate w celu odnajdywania i oceniania serwerów. Możesz kontrolować dane, które udostępniasz w pliku, a większość danych jest opcjonalna. Ta metoda jest przydatna, jeśli:

- Chcesz utworzyć szybką, wstępną ocenę przed wdrożeniem urządzenia.
- Nie można wdrożyć urządzenia usługi Azure Migrate w organizacji.
- Nie można udostępnić poświadczeń zezwalających na dostęp do serwerów lokalnych.
- Ograniczenia zabezpieczeń uniemożliwiają zbieranie i wysyłanie danych zebranych przez urządzenie do platformy Azure.

> [!NOTE]
> Nie można migrować serwerów zaimportowanych przy użyciu pliku CSV.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj konto platformy Azure
> * Konfigurowanie projektu usługi Azure Migrate
> * Przygotuj plik CSV
> * Zaimportuj plik
> * Ocenianie serwerów

> [!NOTE]
> Samouczki przedstawiają najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych, tam gdzie to możliwe. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

- Można dodać do 20 000 serwerów w pojedynczym pliku CSV i w projekcie Azure Migrate. 
- Nazwy systemu operacyjnego określone w pliku CSV muszą zawierać i odpowiadać [nazwom obsługiwanym](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć projekt Azure Migrate, musisz mieć konto z:
- Uprawnienia współautora lub właściciela w ramach subskrypcji platformy Azure.
- Uprawnienia do rejestrowania aplikacji Azure Active Directory.

Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:

1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi** wybierz pozycję **subskrypcje**.

    ![Wyszukaj w polu wyszukiwania subskrypcję platformy Azure](./media/tutorial-discover-import/search-subscription.png)

2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate. 
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp** Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli** kliknij pozycję **Dodaj**.

    ![Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę](./media/tutorial-discover-import/azure-account-access.png)

6. W obszarze **Dodaj przypisanie roli** wybierz rolę współautor lub właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta](./media/tutorial-discover-import/assign-role.png)

7. W portalu Wyszukaj użytkowników, a w obszarze **usługi** wybierz pozycję **Użytkownicy**.
8. W obszarze **Ustawienia użytkownika** Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

    ![Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Konfigurowanie projektu

Skonfiguruj nowy projekt Azure Migrate, jeśli go nie masz.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd** wybierz pozycję **Utwórz projekt**.
5. W obszarze **Utwórz projekt** wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu** Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pola nazwy i regionu projektu](./media/tutorial-discover-import/new-project.png)

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony.

**Azure Migrate: Narzędzie do oceny serwera** jest domyślnie dodawane do nowego projektu.

![Zostanie wyświetlona strona narzędzia do oceny serwera, która jest domyślnie dodawana](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Przygotowywanie pliku CSV

Pobierz szablon CSV i dodaj do niego informacje o serwerze.

### <a name="download-the-template"></a>Pobieranie szablonu

1. W obszarze **Cele migracji** > **Serwery** > **Azure Migrate: Server Assessment** wybierz pozycję **Odnajdź**.
2. W obszarze **Odnajdywanie maszyn** wybierz pozycję **Importuj przy użyciu pliku CSV**.
3. Wybierz pozycję **Pobierz**, aby pobrać szablon CSV. Alternatywnie możesz [pobrać go bezpośrednio](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Pobieranie szablonu CSV](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Dodawanie informacji o serwerze

Zbierz dane serwera i dodaj je do pliku CSV.

- Aby zebrać dane, można je wyeksportować z narzędzi używanych do zarządzania serwerem lokalnym, takich jak VMware vSphere lub baza danych zarządzania konfiguracją (CMDB).
- Aby przejrzeć przykładowe dane, pobierz nasz [przykładowy plik](https://go.microsoft.com/fwlink/?linkid=2108405).

Poniższa tabela zawiera podsumowanie pól plików do wypełnienia:

**Nazwa pola** | **Obowiązkowe** | **Szczegóły**
--- | --- | ---
**Nazwa serwera** | Yes | Zalecamy określenie w pełni kwalifikowanej nazwy domeny (FQDN).
**Adres IP** | Nie | Adres serwera.
**Rdzenie** | Yes | Liczba rdzeni procesora przydzielonych do serwera.
**Pamięć** | Yes | Całkowita ilość pamięci RAM (w MB) przydzielona do serwera.
**Nazwa systemu operacyjnego** | Yes | System operacyjny serwera. <br/> Nazwy systemów operacyjnych, które są zgodne lub zawierają nazwy z [tej](#supported-operating-system-names) listy, są rozpoznawane w trakcie oceny.
**Wersja systemu operacyjnego** | Nie | Wersja systemu operacyjnego serwera.
**Architektura systemu operacyjnego** | Nie | Architektura systemu operacyjnego serwera <br/> Prawidłowe wartości to: x64, x86, amd64, 32-bitowy lub 64-bitowy
**Liczba dysków** | Nie | Niepotrzebna, jeśli podano szczegóły dysku.
**Rozmiar dysku 1**  | Nie | Maksymalny rozmiar dysku (w GB).<br/>Możesz dodać szczegóły dla większej liczby dysków, [dodając kolumny](#add-multiple-disks) w szablonie. Można dodać maksymalnie osiem dysków.
**Operacje odczytu dysku 1** | Nie | Operacje odczytu dysku na sekundę.
**Operacje zapisu dysku 1** | Nie | Operacje zapisu dysku na sekundę.
**Przepływność odczytu dysku 1** | Nie | Dane odczytane z dysku na sekundę (w MB na sekundę).
**Przepływność zapisu dysku 1** | Nie | Dane zapisywane na dysku na sekundę (w MB na sekundę).
**Procent użycia procesora CPU** | Nie | Wartość procentowa użycia procesora CPU.
**Procent użycia pamięci** | Nie | Wartość procentowa użycia pamięci RAM.
**Łączna liczba operacji odczytu dysku** | Nie | Operacje odczytu z dysku na sekundę.
**Łączna liczba operacji zapisu na dysku** | Nie | Operacje zapisu na dysku na sekundę.
**Łączna przepływność odczytu dysków** | Nie | Dane odczytane z dysku (MB na sekundę).
**Łączna przepływność zapisu dysków** | Nie | Dane zapisywane na dysku, w MEGABAJTach na sekundę.
**Przepływność ruchu przychodzącego w sieci** | Nie | Dane odebrane przez serwer (w MB na sekundę).
**Przepływność ruchu wychodzącego w sieci** | Nie | Dane przesyłane przez serwer (w MB na sekundę).
**Typ oprogramowania układowego** | Nie | Oprogramowanie układowe serwera. Wartości to „BIOS” lub „UEFI”.
**Adres MAC**| Nie | Adres MAC serwera.


### <a name="add-operating-systems"></a>Dodawanie systemów operacyjnych

Narzędzie oceny rozpoznaje określone nazwy systemów operacyjnych. Każda określona nazwa musi dokładnie odpowiadać jednemu z ciągów na [liście obsługiwanych nazw](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Dodawanie wielu dysków

Szablon zawiera domyślne pola dla pierwszego dysku. Możesz dodać podobne kolumny dla maksymalnie ośmiu dysków.

Na przykład aby określić wszystkie pola dla drugiego dysku, dodaj następujące kolumny:

- Rozmiar dysku 2
- Operacje odczytu dysku 2
- Operacje zapisu dysku 2
- Przepływność odczytu dysku 2
- Przepływność zapisu na dysku 2


## <a name="import-the-server-information"></a>Importowanie informacji o serwerze

Po dodaniu informacji do szablonu CSV Zaimportuj plik CSV do oceny serwera.

1. W usłudze Azure Migrate, w obszarze **Odnajdywanie maszyn** przejdź do ukończonego szablonu.
2. Wybierz pozycję **Importuj**.
3. Zostanie wyświetlony stan importowania.
    - Jeśli w stanie pojawiły się ostrzeżenia, możesz je naprawić lub kontynuować bez ich rozwiązywania.
    - Aby poprawić dokładność oceny, popraw informacje o serwerze zgodnie z opisem w ostrzeżeniach.
    - Aby wyświetlić i naprawić ostrzeżenia, wybierz pozycję **Pobierz plik CSV ze szczegółami ostrzeżenia**. Ta operacja pobiera plik CSV wraz z ostrzeżeniami. Przejrzyj ostrzeżenia i rozwiąż problemy zgodnie z wymaganiami.
    - Jeśli w stanie pojawią się błędy, co powoduje, że stan importu wskazuje **Niepowodzenie**, należy naprawić te błędy przed kontynuowaniem importowania:
        1. Pobierz plik CSV, który zawiera teraz szczegóły błędu.
        1. Przejrzyj błędy i rozwiąż je w razie potrzeby. 
        1. Ponownie przekaż zmodyfikowany plik.
4. Gdy stan importowania ma wartość **Ukończono**, informacje o serwerze zostały zaimportowane. Odśwież, jeśli proces importowania nie zostanie ukończony.

## <a name="update-server-information"></a>Aktualizowanie informacji o serwerze

Możesz zaktualizować informacje dotyczące serwera, importując ponownie dane serwera przy użyciu tej samej **nazwy serwera**. Nie można zmodyfikować pola **Nazwa serwera**. Usuwanie serwerów nie jest obecnie obsługiwane.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Aby sprawdzić, czy serwery są wyświetlane w witrynie Azure Portal po wykonaniu operacji odnajdywania:

1. Otwórz pulpit nawigacyjny usługi Azure Migrate.
2. Na stronie **Azure Migrate — Serwery** > **Azure Migrate: Server Assessment** wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.
3. Wybierz kartę **Na podstawie importu**.



## <a name="supported-operating-system-names"></a>Obsługiwane nazwy systemów operacyjnych

Nazwy systemu operacyjnego podane w pliku CSV muszą zawierać i być zgodne. W przeciwnym razie nie będzie można ich ocenić. 

**A – H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 z dodatkiem R2<br/>Windows Server 2012<br/>Windows Server 2012 z dodatkiem R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utworzono projekt Azure Migrate 
> * Odnalezione serwery przy użyciu zaimportowanego pliku CSV. Teraz wykonaj ocenę [migracji maszyn wirtualnych VMware do maszyn wirtualnych platformy Azure](./tutorial-assess-vmware-azure-vm.md).