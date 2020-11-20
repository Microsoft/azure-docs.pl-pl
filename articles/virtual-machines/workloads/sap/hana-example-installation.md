---
title: Jak zainstalować platformę HANA na SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Jak zainstalować platformę HANA na SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 994130ce2ecb4578a8d34f1f77b310bb558d3c7a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967911"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instalowanie platformy HANA na SAP HANA na platformie Azure (duże wystąpienia)

Aby zainstalować program HANA na SAP HANA na platformie Azure (duże wystąpienia), należy najpierw wykonać następujące czynności:
- Firma Microsoft udostępnia wszystkie dane do wdrożenia na SAP HANA dużym wystąpieniu.
- Otrzymasz SAP HANA duże wystąpienie od firmy Microsoft.
- Tworzysz sieć wirtualną platformy Azure, która jest połączona z siecią lokalną.
- Obwód usługi ExpressRoute dla dużych wystąpień HANA należy podłączyć do tej samej sieci wirtualnej platformy Azure.
- Zainstaluj maszynę wirtualną platformy Azure, której używasz jako pola skoku dla dużych wystąpień HANA.
- Upewnij się, że możesz nawiązać połączenie z poziomu pola skoku z jednostką dużego wystąpienia platformy HANA i odwrotnie.
- Sprawdź, czy są zainstalowane wszystkie wymagane pakiety i poprawki.
- Zapoznaj się z informacjami na temat oprogramowania SAP i dokumentacją dotyczącą instalacji platformy HANA w używanym systemie operacyjnym. Upewnij się, że wybrana wersja platformy HANA jest obsługiwana w wersji systemu operacyjnego.

W następnej sekcji przedstawiono przykład pobierania pakietów instalacyjnych platformy HANA do maszyny wirtualnej z polem skoku. W takim przypadku system operacyjny to Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Pobierz bity instalacji SAP HANA
Jednostki dużego wystąpienia HANA nie są bezpośrednio połączone z Internetem. Nie można bezpośrednio pobrać pakietów instalacyjnych z oprogramowania SAP na maszynę wirtualną usługi HANA o dużym wystąpieniu. Zamiast tego należy pobrać pakiety do maszyny wirtualnej z polem skoku.

Potrzebujesz produktu SAP S-User lub innego użytkownika, który umożliwia dostęp do portalu SAP Marketplace.

1. Zaloguj się i przejdź do [witryny SAP Service Marketplace](https://support.sap.com/en/index.html). Wybierz pozycję **Pobierz**  >  **instalacje oprogramowania i Uaktualnij**  >  **według alfabetycznego indeksu**. Następnie wybierz pozycję **w obszarze H – SAP HANA platform Edition**  >  **SAP HANA instalacji wersji 2,0 platformy**  >  **Installation**. Pobierz pliki pokazane na poniższym zrzucie ekranu.

   ![Zrzut ekranu przedstawiający pliki do pobrania](./media/hana-installation/image16_download_hana.PNG)

2. W tym przykładzie pobrano pakiety instalacyjne SAP HANA 2,0. Na maszynie wirtualnej Azure skoku Box rozwiń archiwa samodzielne do katalogu, jak pokazano poniżej.

   ![Zrzut ekranu przedstawiający archiwum do samodzielnego wyodrębniania](./media/hana-installation/image17_extract_hana.PNG)

3. Ponieważ archiwa są wyodrębniane, Skopiuj katalog utworzony przez ekstrakcję (w tym przypadku 51052030). Skopiuj katalog z woluminu/Hana/Shared o dużej instancji HANA do utworzonego katalogu.

   > [!Important]
   > Nie należy kopiować pakietów instalacyjnych do głównej lub rozruchowej jednostki LUN, ponieważ spacja jest ograniczona i musi być również używana przez inne procesy.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Zainstaluj SAP HANA w jednostce dużego wystąpienia HANA
Aby zainstalować SAP HANA, zaloguj się jako użytkownik root. Tylko element główny ma wystarczające uprawnienia do instalacji SAP HANA. Ustawianie uprawnień do katalogu skopiowanego do/Hana/Shared.

```
chmod –R 744 <Installation bits folder>
```

Jeśli chcesz zainstalować SAP HANA przy użyciu graficznego interfejsu użytkownika, należy zainstalować pakiet GTK2 w dużych wystąpieniach platformy HANA. Aby sprawdzić, czy jest zainstalowana, uruchom następujące polecenie:

```
rpm –qa | grep gtk2
```

(W kolejnych krokach zostanie pokazany Instalator SAP HANA z graficznym interfejsem użytkownika).

Przejdź do katalogu instalacyjnego i przejdź do katalogu podrzędnego HDB_LCM_LINUX_X86_64. 

Z tego katalogu, uruchom:

```
./hdblcmgui 
```
W tym momencie przechodzisz przez sekwencję ekranów, w których podano dane instalacji. W tym przykładzie instalujemy serwer bazy danych SAP HANA i składniki klienta SAP HANA. W związku z tym wybór jest **SAP HANA bazy danych**.

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA, z wybraną SAP HANA bazą danych](./media/hana-installation/image18_hana_selection.PNG)

Na następnym ekranie wybierz pozycję **Zainstaluj nowy system**.

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z zaznaczoną opcją Zainstaluj nowy system](./media/hana-installation/image19_select_new.PNG)

Następnie wybierz spośród kilku dodatkowych składników, które można zainstalować.

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z listą dodatkowych składników](./media/hana-installation/image20_select_components.PNG)

W tym miejscu wybieramy klienta SAP HANA i SAP HANA Studio. Instalujemy również wystąpienie skalowalne w górę. Następnie wybierz opcję **system z pojedynczym hostem**. 

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z wybranym systemem jednego hosta](./media/hana-installation/image21_single_host.PNG)

Następnie podaj dane.

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z polami właściwości systemu do zdefiniowania](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako identyfikator systemu HANA (SID), musisz podać ten sam identyfikator SID, który został dostarczony przez firmę Microsoft w przypadku uporządkowania wdrożenia dużego wystąpienia platformy HANA. Wybranie innego identyfikatora SID powoduje, że instalacja nie powiedzie się z powodu problemów z uprawnieniami dostępu na różnych woluminach.

Dla ścieżki instalacji użyj katalogu/Hana/Shared. W następnym kroku podajesz lokalizacje plików danych HANA i plików dziennika HANA.


![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z polami danych i obszaru dziennika](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Identyfikator SID określony podczas definiowania właściwości systemu (dwa ekrany temu) powinien być zgodny z identyfikatorem SID punktów instalacji. Jeśli występuje niezgodność, wróć i Dostosuj identyfikator SID do wartości znajdującej się w punktach instalacji.

W następnym kroku zapoznaj się z nazwą hosta i ostatecznie Popraw ją. 

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z nazwą hosta](./media/hana-installation/image24_review_host_name.PNG)

W następnym kroku należy również pobrać dane wprowadzone do firmy Microsoft podczas uporządkowania wdrożenia dużego wystąpienia platformy HANA. 

![Zrzut ekranu przedstawiający Zarządzanie cyklem życia SAP HANA przy użyciu pól administratora systemu do definiowania](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Podaj **Identyfikator użytkownika administratora systemu** i **Identyfikator grupy użytkowników** zgodnie z podaną firmą Microsoft, gdy planujesz wdrożenie jednostki. W przeciwnym razie instalacja SAP HANA w jednostce dużego wystąpienia HANA nie powiedzie się.

Dwa następne ekrany nie są wyświetlane w tym miejscu. Umożliwiają one podanie hasła dla użytkownika systemu bazy danych SAP HANA i hasła użytkownika sapadm. Ten drugi jest używany przez agenta hosta SAP, który jest instalowany jako część wystąpienia bazy danych SAP HANA.

Po zdefiniowaniu hasła zostanie wyświetlony ekran potwierdzenia. Sprawdź wszystkie wymienione dane i Kontynuuj instalację. Zobaczysz ekran postępu, który dokumentuje postęp instalacji, np.:

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z wskaźnikami postępu instalacji](./media/hana-installation/image27_show_progress.PNG)

Po zakończeniu instalacji powinien zostać wyświetlony ekran podobny do tego:

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA, wskazujący, że instalacja została zakończona](./media/hana-installation/image28_install_finished.PNG)

Wystąpienie SAP HANA powinno być teraz uruchomione i gotowe do użycia. Powinno być możliwe nawiązanie połączenia z usługą SAP HANA Studio. Upewnij się również, że sprawdzane są i stosowane są najnowsze aktualizacje.


## <a name="next-steps"></a>Następne kroki

- [SAP HANA — duże wystąpienia wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md)

