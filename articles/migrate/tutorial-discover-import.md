---
title: Odnajdywanie serwerów lokalnych przy użyciu zaimportowanego pliku CSV z oceną serwera Azure Migrate
description: Opisuje sposób odnajdywania serwerów lokalnych na potrzeby migracji na platformę Azure przy użyciu zaimportowanego pliku CSV w ramach oceny serwera Azure Migrate
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: 6526961df225e4f347216428141e8217043161df
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064401"
---
# <a name="tutorial-discover-servers-using-an-imported-csv-file"></a>Samouczek: odnajdywanie serwerów przy użyciu zaimportowanego pliku CSV

W ramach kursu migracji na platformę Azure można wykryć spisy i obciążenia lokalne. 

W tym samouczku przedstawiono sposób odnajdywania lokalnych maszyn wirtualnych VMware za pomocą narzędzia do oceny serwera Azure Migrate:, przy użyciu zaimportowanego pliku wartości rozdzielanych przecinkami (CSV). 

W przypadku korzystania z pliku CSV nie trzeba konfigurować urządzenia Azure Migrate w celu odnajdywania serwerów. Możesz kontrolować dane, które udostępniasz w pliku, a większość danych jest opcjonalna. Ta metoda jest przydatna, jeśli:

- Przed wdrożeniem urządzenia należy utworzyć szybką, początkową ocenę.
- Nie można wdrożyć urządzenia Azure Migrate w organizacji.
- Nie można udostępniać poświadczeń zezwalających na dostęp do serwerów lokalnych.
- Ograniczenia zabezpieczeń uniemożliwiają zbieranie i wysyłanie danych zebranych przez urządzenie na platformę Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj konto platformy Azure
> * Skonfiguruj projekt Azure Migrate.
> * Przygotuj plik CSV
> * Zaimportuj plik.

> [!NOTE]
> Samouczki przedstawiają najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych, tam gdzie to możliwe. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

- Można dodać do 20 000 serwerów w pojedynczym pliku CSV i w projekcie Azure Migrate. 
- Nazwy systemu operacyjnego określone w pliku CSV muszą zawierać i odpowiadać [nazwom obsługiwanym](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Przygotowywanie konta użytkownika platformy Azure

Aby utworzyć projekt Azure Migrate i zarejestrować urządzenie Azure Migrate, musisz mieć konto z:
- Uprawnienia współautora lub właściciela w ramach subskrypcji platformy Azure.
- Uprawnienia do rejestrowania aplikacji Azure Active Directory.

Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji. Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać uprawnienia w następujący sposób:

1. W Azure Portal Wyszukaj "subskrypcje", a w obszarze **usługi**wybierz pozycję **subskrypcje**.

    ![Wyszukaj w polu wyszukiwania subskrypcję platformy Azure](./media/tutorial-discover-import/search-subscription.png)

2. Na stronie **subskrypcje** wybierz subskrypcję, w której chcesz utworzyć projekt Azure Migrate. 
3. W subskrypcji wybierz pozycję **Kontrola dostępu (IAM)**  >  **sprawdzanie dostępu**.
4. W obszarze **Sprawdź dostęp**Wyszukaj odpowiednie konto użytkownika.
5. W obszarze **Dodaj przypisanie roli**kliknij pozycję **Dodaj**.

    ![Wyszukaj konto użytkownika, aby sprawdzić dostęp i przypisać rolę](./media/tutorial-discover-import/azure-account-access.png)

6. W obszarze **Dodaj przypisanie roli**wybierz rolę współautor lub właściciela, a następnie wybierz konto (azmigrateuser w naszym przykładzie). Następnie kliknij przycisk **Zapisz**.

    ![Otwiera stronę Dodawanie przypisania roli w celu przypisania roli do konta](./media/tutorial-discover-import/assign-role.png)

7. W portalu Wyszukaj użytkowników, a w obszarze **usługi**wybierz pozycję **Użytkownicy**.
8. W obszarze **Ustawienia użytkownika**Sprawdź, czy użytkownicy usługi Azure AD mogą rejestrować aplikacje (domyślnie ustawione na **wartość tak** ).

    ![Sprawdź ustawienia użytkownika, które użytkownicy mogą rejestrować Active Directory aplikacje](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Konfigurowanie projektu

Skonfiguruj nowy projekt Azure Migrate.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Przegląd**wybierz pozycję **Utwórz projekt**.
5. W obszarze **Utwórz projekt**wybierz swoją subskrypcję platformy Azure i grupę zasobów. Utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **szczegóły projektu**Określ nazwę projektu i geografię, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pola nazwy i regionu projektu](./media/tutorial-discover-import/new-project.png)

7. Wybierz przycisk **Utwórz**.
8. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony.

**Azure Migrate: Narzędzie do oceny serwera** jest domyślnie dodawane do nowego projektu.

![Zostanie wyświetlona strona narzędzia do oceny serwera, która jest domyślnie dodawana](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Przygotowywanie woluminu CSV

Pobierz szablon CSV i Dodaj do niego informacje o serwerze.

### <a name="download-the-template"></a>Pobieranie szablonu

1. W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Ocena serwera**, wybierz pozycję **odkryj**.
2. W obszarze **odnajdywanie maszyn**wybierz pozycję **Importuj przy użyciu woluminu CSV**.
3. Wybierz pozycję **Pobierz** , aby pobrać szablon woluminu CSV. Alternatywnie możesz [pobrać go bezpośrednio](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Pobierz szablon CSV](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Dodawanie informacji o serwerze

Zbierz dane serwera i Dodaj je do pliku CSV.

- Aby zebrać dane, można wyeksportować je z narzędzi używanych do zarządzania lokalnym serwerem, takich jak VMware vSphere lub baza danych zarządzania konfiguracją (CMDB).
- Aby przejrzeć przykładowe dane, Pobierz nasz [przykładowy plik](https://go.microsoft.com/fwlink/?linkid=2108405).

Poniższa tabela zawiera podsumowanie pól plików do wypełnienia:

**Nazwa pola** | **Wypełnione** | **Szczegóły**
--- | --- | ---
**Nazwa serwera** | Tak | Zalecamy określenie w pełni kwalifikowanej nazwy domeny (FQDN).
**Adres IP** | Nie | Adres serwera.
**Rdzenie** | Tak | Liczba rdzeni procesora przypisanych do serwera.
**Pamięć** | Tak | Całkowita ilość pamięci RAM w MB przypisana do serwera.
**Nazwa systemu operacyjnego** | Tak | System operacyjny serwera. <br/> Nazwy systemu operacyjnego, które pasują lub zawierają nazwy na [tej](#supported-operating-system-names) liście, są rozpoznawane przez ocenę.
**Wersja systemu operacyjnego** | Nie | Wersja systemu operacyjnego serwera.
**Architektura systemu operacyjnego** | Nie | Architektura systemu operacyjnego serwera <br/> Prawidłowe wartości to: x64, x86, amd64, 32-bitowa lub 64-bitowy
**Liczba dysków** | Nie | Niepotrzebna, jeśli podano szczegółowe szczegóły dysku.
**Rozmiar dysku 1**  | Nie | Maksymalny rozmiar dysku (w GB).<br/>Możesz dodać szczegóły do większej liczby dysków, [dodając kolumny](#add-multiple-disks) w szablonie. Można dodać maksymalnie osiem dysków.
**Operacje odczytu z dysku 1** | Nie | Operacje odczytu z dysku na sekundę.
**Operacje zapisu na dysku 1** | Nie | Operacje zapisu na dysku na sekundę.
**Przepływność odczytu dysku 1** | Nie | Dane odczytane z dysku na sekundę, w MEGABAJTach na sekundę.
**Przepływność zapisu na dysku 1** | Nie | Dane zapisywane na dysku na sekundę, w MEGABAJTach na sekundę.
**Procent użycia procesora CPU** | Nie | Procent użycia procesora CPU.
**Procent wykorzystania pamięci** | Nie | Procent używanej pamięci RAM.
**Całkowita liczba odczytywanych dysków** | Nie | Operacje odczytu z dysku na sekundę.
**Całkowita liczba dysków — operacje zapisu** | Nie | Operacje zapisu na dysku na sekundę.
**Całkowita liczba dysków do odczytu** | Nie | Dane odczytane z dysku (MB na sekundę).
**Całkowita przepustowość zapisu dysków** | Nie | Dane zapisywane na dysku, w MEGABAJTach na sekundę.
**Sieć w przepływności** | Nie | Dane odebrane przez serwer, w MEGABAJTach na sekundę.
**Przepływność w sieci** | Nie | Dane przesyłane przez serwer, w MEGABAJTach na sekundę.
**Typ oprogramowania układowego** | Nie | Oprogramowanie układowe serwera. Wartości mogą być "BIOS" lub "UEFI".
**Adres MAC**| Nie | Adres MAC serwera.


### <a name="add-operating-systems"></a>Dodawanie systemów operacyjnych

Ocena rozpoznaje nazwy określonych systemów operacyjnych. Każda określona nazwa musi dokładnie odpowiadać jednemu z ciągów z [listy obsługiwanych nazw](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Dodaj wiele dysków

Szablon zawiera domyślne pola dla pierwszego dysku. Możesz dodać podobne kolumny dla maksymalnie ośmiu dysków.

Na przykład aby określić wszystkie pola dla drugiego dysku, Dodaj następujące kolumny:

- Rozmiar dysku 2
- Operacje odczytu z dysku 2
- Operacje zapisu na dysku 2
- Przepływność odczytu dysku 2
- Przepływność zapisu na dysku 2


## <a name="import-the-server-information"></a>Importuj informacje o serwerze

Po dodaniu informacji do szablonu CSV zaimportuj serwery do oceny serwera.

1. W Azure Migrate w obszarze **odnajdywanie maszyn**przejdź do ukończonego szablonu.
2. Wybierz pozycję **Importuj**.
3. Stan importowania jest pokazywany.
    - Jeśli w stanie pojawiły się ostrzeżenia, możesz je rozwiązać lub kontynuować bez rozwiązywania problemów.
    - Aby poprawić dokładność oceny, Popraw informacje o serwerze zgodnie z opisem w ostrzeżeniach.
    - Aby wyświetlić i naprawić ostrzeżenia, wybierz pozycję **Pobierz szczegóły ostrzeżenia. Wolumin CSV**. Ta operacja pobiera wolumin CSV z zawartymi ostrzeżeniami. Przejrzyj ostrzeżenia i rozwiąż problemy zgodnie z wymaganiami.
    - Jeśli błędy pojawiają się w stanie, w związku z czym stan importu **nie powiodło się**, należy usunąć te błędy przed kontynuowaniem importowania:
        1. Pobierz plik CSV, który zawiera teraz szczegóły błędu.
        1. Przejrzyj błędy i Rozwiąż je w razie potrzeby. 
        1. Ponownie Przekaż zmodyfikowany plik.
4. Po **zakończeniu**stanu importu informacje o serwerze zostały zaimportowane.

## <a name="update-server-information"></a>Aktualizowanie informacji o serwerze

Możesz zaktualizować informacje o serwerze, importując dane dla serwera z tą samą **nazwą serwera**. Nie można zmodyfikować pola **Nazwa serwera** . Usuwanie serwerów nie jest obecnie obsługiwane.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Aby sprawdzić, czy serwery są wyświetlane w Azure Portal po przeprowadzeniu odnajdywania:

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. Na stronie **Azure Migrate serwerów**  >  **Azure Migrate: Ocena serwera** wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.
3. Wybierz kartę **Importuj na podstawie** .



## <a name="supported-operating-system-names"></a>Obsługiwane nazwy systemu operacyjnego

Nazwy systemu operacyjnego podane w pliku CSV muszą zawierać i być zgodne. W przeciwnym razie nie będzie można ich ocenić. 

**A – H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | SYSTEM OPERACYJNY IBM OS/2<br/>systemu<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO dla systemu UNIX 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>System Windows 3<br/>Windows 7<br/>Windows 8<br/>System Windows 95<br/>Windows 98<br/>System Windows NT<br/>System Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 z dodatkiem R2<br/>Windows Server 2012<br/>Windows Server 2012 z dodatkiem R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Próg systemu Windows Server<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utworzono projekt Azure Migrate 
> * Odnalezione serwery przy użyciu zaimportowanego pliku CSV. Teraz wykonaj ocenę [migracji maszyn wirtualnych VMware do maszyn wirtualnych platformy Azure](tutorial-assess-vmware.md).