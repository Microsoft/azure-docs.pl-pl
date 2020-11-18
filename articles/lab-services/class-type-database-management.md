---
title: Skonfiguruj laboratorium do uczenia zarządzania bazami danych dla relacyjnych baz danych | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium do uczenia się zarządzania relacyjnymi bazami danych.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: bed43e586beff890f0aa8947140ae7d8e50439f3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659832"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Konfigurowanie laboratorium do uczenia zarządzania bazami danych dla relacyjnych baz danych

W tym artykule opisano sposób konfigurowania laboratorium dla podstawowej klasy zarządzania bazami danych w Azure Lab Services. Pojęcia dotyczące baz danych są jednym z kursów wprowadzających w większości działów nauki komputerowych w szkole. Structured Query Language (SQL) to międzynarodowy standard. SQL to standardowy język zarządzania bazami danych, w tym dodawanie, uzyskiwanie dostępu do zawartości i zarządzanie nią w bazie danych.  Jest to najbardziej zanotowane dla szybkiego przetwarzania, sprawdzonej niezawodności, prostoty i elastyczności.

W tym artykule przedstawiono sposób konfigurowania szablonu maszyny wirtualnej w laboratorium z serwerem bazy danych MySQL i serwerem SQL Server 2019.  [MySQL](https://www.mysql.com/) to dostępny bezpłatnie system zarządzania relacyjnymi bazami danych typu Open Source (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) to Najnowsza wersja RDBMS firmy Microsoft.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby rozpocząć pracę z tym laboratorium, musisz zacząć korzystać z subskrypcji platformy Azure i konta laboratorium. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/). Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md).  Możesz również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia opisane w poniższej tabeli dla konta laboratorium. Aby uzyskać więcej informacji na temat włączania obrazów z witryny Marketplace, zobacz [Określanie obrazów z portalu Marketplace dostępnych dla twórców laboratorium](./specify-marketplace-images.md).

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
|Obraz z witryny Marketplace| Włącz obraz "SQL Server 2019 standard w systemie Windows Server 2019" do użycia w ramach Twojego konta laboratorium.|

### <a name="lab-settings"></a>Ustawienia laboratorium

W poniższej tabeli przedstawiono ustawienia, które należy wykonać podczas konfigurowania laboratorium zajęć.  Aby uzyskać więcej informacji na temat tworzenia laboratorium klasy, zobacz [Samouczek dotyczący konfigurowania środowiska laboratoryjnego](tutorial-setup-classroom-lab.md).

| Ustawienia laboratorium | Wartość/instrukcje |
| ------------ | ------------------ |
|Rozmiar maszyny wirtualnej| Średnia. Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analizy.|
|Obraz maszyny wirtualnej| SQL Server 2019 standard w systemie Windows Server 2019|

## <a name="template-machine-configuration"></a>Konfiguracja komputera szablonu

Aby zainstalować MySQL w systemie Windows Server 2019, można wykonać kroki opisane w temacie [Instalowanie i uruchamianie serwera społeczności MySQL na maszynie wirtualnej](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%252fazure%252fvirtual-machines%252fwindows%252fclassic%252ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 jest wstępnie zainstalowany w obrazie maszyny wirtualnej, który został wybrany podczas tworzenia nowego laboratorium.

## <a name="cost-estimate"></a>Oszacowanie kosztów

Przyjrzyjmy się możliwemu szacunkowi kosztów dla tej klasy.  Będziemy używać klasy 25 studentów.  Zaplanowana godzina klasy wynosi 20 godzin.  Ponadto każdy student otrzymuje limit 10 godzin dla prac domowych lub przydziałów poza zaplanowanym czasem klasy.  Wybrany rozmiar maszyny wirtualnej to średnia, czyli 42 jednostek laboratorium.

Oto przykład możliwego oszacowania kosztów dla tej klasy:

25 studentów \* (20 zaplanowanych godzin + 10 godzin przydziału) \* 0,42 USD za godzinę = 315,00 USD

Więcej szczegółowych informacji na temat cen można znaleźć w temacie [Azure Lab Services Cennik](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Wniosek

W tym artykule opisano kroki niezbędne do utworzenia laboratorium dla podstawowych pojęć związanych z zarządzaniem bazami danych przy użyciu programu MySQL i SQL Server. Możesz użyć podobnej konfiguracji dla innych klas baz danych.

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)