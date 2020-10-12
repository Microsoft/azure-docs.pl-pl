---
title: SQL Server on Linux często zadawane pytania dotyczące maszyn wirtualnych | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące uruchamiania SQL Server on Linux maszyn wirtualnych.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: afd36eb079b6e5f29fa664050cded3c5ddd17d8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84669467"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Często zadawane pytania dotyczące SQL Server on Linux maszyn wirtualnych
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Ten artykuł zawiera odpowiedzi na niektóre z najczęstszych pytań dotyczących uruchamiania [SQL Server on Linux maszyn wirtualnych](sql-server-on-linux-vm-what-is-iaas-overview.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Rastrow

1. **Jakie są dostępne obrazy z galerii maszyn wirtualnych SQL Server?**

   Platforma Azure obsługuje obrazy maszyn wirtualnych dla wszystkich obsługiwanych głównych wersji SQL Server we wszystkich wersjach systemu Linux i Windows. Aby uzyskać więcej informacji, zobacz kompletna lista [obrazów maszyn wirtualnych](sql-server-on-linux-vm-what-is-iaas-overview.md#create) z systemem Linux i [obrazów maszyn wirtualnych z systemem Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

1. **Czy istnieją zaktualizowane obrazy galerii maszyn wirtualnych SQL Server?**

   Co dwa miesiące SQL Server obrazy w galerii maszyn wirtualnych są aktualizowane przy użyciu najnowszych aktualizacji systemu Linux i Windows. W przypadku obrazów systemu Linux obejmuje to najnowsze aktualizacje systemowe. W przypadku obrazów systemu Windows obejmuje to wszystkie aktualizacje oznaczone jako ważne w Windows Update, w tym ważne SQL Server aktualizacje zabezpieczeń i dodatki Service Pack. SQL Server aktualizacje zbiorcze są obsługiwane inaczej dla systemów Linux i Windows. W przypadku systemu Linux w odświeżeniu są również uwzględniane SQL Server aktualizacje zbiorcze. Jednak w tej chwili maszyny wirtualne z systemem Windows nie są aktualizowane przy użyciu aktualizacji zbiorczych SQL Server lub systemu Windows Server.

1. **Jakie powiązane pakiety SQL Server są również zainstalowane?**

   Aby wyświetlić SQL Server pakiety, które są instalowane domyślnie na maszynach wirtualnych SQL Server on Linux, zobacz [zainstalowane pakiety](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **Czy SQL Server obrazy maszyn wirtualnych zostaną usunięte z galerii?**

   Tak. System Azure obsługuje tylko jeden obraz na wersję główną i wydanie. Na przykład po wydaniu nowego dodatku Service Pack SQL Server platforma Azure dodaje nowy obraz do galerii dla tego dodatku Service Pack. Obraz SQL Server dla poprzedniego dodatku Service Pack zostanie natychmiast usunięty z Azure Portal. Jest on jednak nadal dostępny do aprowizacji z programu PowerShell w ciągu następnych trzech miesięcy. Po trzech miesiącach Poprzedni obraz dodatku Service Pack nie jest już dostępny. Te zasady usuwania również będą stosowane, jeśli wersja SQL Server nie będzie obsługiwana, gdy osiągnie koniec cyklu życia.

## <a name="creation"></a>Tworzenie

1. **Jak mogę utworzyć maszynę wirtualną z systemem Linux przy użyciu SQL Server?**

   Najłatwiej jest utworzyć maszynę wirtualną z systemem Linux, która zawiera SQL Server. Aby zapoznać się z samouczkiem dotyczącym rejestracji w usłudze Azure i tworzenia SQL Server maszyny wirtualnej z poziomu portalu, zobacz temat [udostępnianie maszyny wirtualnej z systemem Linux, która działa SQL Server w Azure Portal](sql-vm-create-portal-quickstart.md). Istnieje również możliwość ręcznej instalacji SQL Server na maszynie wirtualnej z bezpłatną licencjonowaną wersją (Developer lub Express) lub przez ponowne użycie licencji lokalnej. W przypadku posiadania własnej licencji musisz mieć [Przenośność licencji w ramach programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Dlaczego nie mogę zainicjować obsługi administracyjnej maszyny wirtualnej RHEL lub SLES SQL Server z subskrypcją platformy Azure, która ma limit wydatków?**

   Maszyny wirtualne RHEL i SLES wymagają subskrypcji bez limitu wydatków oraz zweryfikowanej metody płatności (zazwyczaj karty kredytowej) skojarzonej z subskrypcją. Jeśli zainicjujesz maszynę wirtualną RHEL lub SLES bez usuwania limitu wydatków, subskrypcja zostanie wyłączona i wszystkie maszyny wirtualne/usługi zostaną zatrzymane. Jeśli uruchamiasz ten stan, aby ponownie włączyć subskrypcję, [Usuń limit wydatków](https://account.windowsazure.com/subscriptions). Pozostałe środki zostaną przywrócone dla bieżącego cyklu rozliczeniowego, ale dopłata za RHEL lub SLES maszyny wirtualnej zostanie przeprowadzona z kartą kredytową, jeśli zdecydujesz się na ich ponowne uruchomienie i kontynuowanie działania.

## <a name="licensing"></a>Licencjonowanie

1. **Jak mogę zainstalować moją licencjonowaną kopię programu SQL Server na maszynie wirtualnej platformy Azure?**

   Najpierw utwórz maszynę wirtualną obsługującą system operacyjny Linux. Następnie uruchom [SQL Server kroki instalacji](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) dla dystrybucji systemu Linux. Jeśli nie instalujesz jednej z licencjonowanych wersji SQL Server, musisz także mieć licencję SQL Server i [Przenośność licencji w ramach programu Software Assurance na platformie Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Czy istnieją obrazy maszyn wirtualnych z systemem Linux (BYOL) dla SQL Server?**

   W tej chwili nie ma BYOL obrazów maszyn wirtualnych z systemem Linux dla SQL Server. Można jednak ręcznie zainstalować SQL Server na maszynie wirtualnej z systemem Linux, zgodnie z opisem w poprzednich pytaniach.

1. **Czy mogę zmienić maszynę wirtualną tak, aby używać mojej licencji programu SQL Server, jeśli została ona utworzona z jednego z obrazów z galerii w modelu płatności zgodnie z rzeczywistym użyciem?**

   Nie. Nie można przełączyć się z licencjonowania z opcją płatność na sekundę do korzystania z własnej licencji. Musisz utworzyć nową maszynę wirtualną z systemem Linux, zainstalować SQL Server i zmigrować dane. Zobacz poprzednie pytanie, aby uzyskać więcej informacji na temat nadawanie własnej licencji.

## <a name="administration"></a>Administracja

1. **Czy mogę zarządzać maszyną wirtualną z systemem Linux, SQL Server z SQL Server Management Studio (SSMS)?**

   Tak, ale program SSMS jest obecnie narzędziem wyłącznie dla systemu Windows. Musisz połączyć się zdalnie z komputera z systemem Windows, aby używać programu SSMS z maszynami wirtualnymi z systemem Linux, na których działa SQL Server W systemie Linux w nowym narzędziu [MSSQL-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) można wykonywać wiele zadań administracyjnych. Aby uzyskać międzyplatformowe narzędzie do zarządzania bazami danych, zobacz [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is).

1. **Czy mogę usunąć całkowicie program SQL Server z maszyny wirtualnej z programem SQL Server?**

   Tak, ale nadal będzie naliczana opłata za maszynę wirtualną SQL Server, zgodnie z opisem w temacie [wskazówki dotyczące cen dla SQL Server maszyn wirtualnych platformy Azure](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Jeśli program SQL Server nie jest już potrzebny, można wdrożyć nową maszynę wirtualną, a następnie migrować dane i aplikacje do nowej maszyny wirtualnej. Następnie można usunąć maszynę wirtualną z programem SQL Server.

## <a name="updating-and-patching"></a>Aktualizowanie i stosowanie poprawek

1. **Jak mogę uaktualnić do nowej wersji lub wydania SQL Server na maszynie wirtualnej platformy Azure?**

   obecnie nie są dostępne żadne uaktualnienia w miejscu dla programu SQL Server uruchomionego na maszynie wirtualnej platformy Azure. Utwórz nową maszynę wirtualną platformy Azure z odpowiednią wersją programu SQL Server, a następnie migruj bazy danych na nowy serwer przy użyciu [standardowych technik migracji danych](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Ogólne

1. **Czy SQL Server rozwiązania o wysokiej dostępności są obsługiwane na maszynach wirtualnych platformy Azure?**

   Nie w tej chwili. Zawsze włączone grupy dostępności i klaster trybu failover wymagają rozwiązania klastrowego w systemie Linux, takiego jak Pacemaker. Obsługiwane dystrybucje systemu Linux dla SQL Server nie obsługują ich dodatków o wysokiej dostępności w chmurze.

## <a name="resources"></a>Zasoby

**Maszyny wirtualne z systemem Linux**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Inicjowanie obsługi administracyjnej SQL Server na maszynie wirtualnej z systemem Linux](sql-vm-create-portal-quickstart.md)
* [Dokumentacja SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Maszyny wirtualne z systemem Windows**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Inicjowanie obsługi administracyjnej SQL Server na maszynie wirtualnej z systemem Windows](../windows/sql-vm-create-portal-quickstart.md)
* [Często zadawane pytania (Windows)](../windows/frequently-asked-questions-faq.md)