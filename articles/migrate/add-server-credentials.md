---
title: Podaj poświadczenia serwera w celu odnajdywania spisu oprogramowania, zależności i wystąpień SQL Server oraz baz danych
description: Dowiedz się, jak podać poświadczenia serwera w Menedżerze konfiguracji urządzenia
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: e72d509f71704bbf8608543df5e819a9b8783935
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562073"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-and-sql-server-instances-and-databases"></a>Podaj poświadczenia serwera w celu odnajdywania spisu oprogramowania, zależności i wystąpień SQL Server oraz baz danych

Postępuj zgodnie z tym artykułem, aby dowiedzieć się, jak dodać wiele poświadczeń serwera w Menedżerze konfiguracji urządzeń w celu przeprowadzenia spisu oprogramowania (odnajdywania zainstalowanych aplikacji), analizy zależności bez agentów i odnajdywania SQL Server wystąpień i baz danych.

> [!Note]
> Odnajdywanie i Ocena SQL Server wystąpień i baz danych działających w środowisku VMware jest teraz w wersji zapoznawczej. Aby wypróbować tę funkcję, użyj [**tego linku**](https://aka.ms/AzureMigrate/SQL) w celu utworzenia projektu w regionie **Australia Wschodnia**. Jeśli masz już projekt w regionie Australia Wschodnia i chcesz wypróbować tę funkcję, upewnij się, że zostały spełnione te [**wymagania wstępne**](how-to-discover-sql-existing-project.md) w portalu.

[Urządzenie Azure Migrate](migrate-appliance.md) jest lekkim urządzeniem używanym przez Azure Migrate: odnajdywanie i ocenianie w celu odnajdywania serwerów lokalnych działających w środowisku VMware i wysyłania metadanych konfiguracji i wydajności serwera do platformy Azure. Urządzenia można także używać do wykonywania spisu oprogramowania, analizy zależności bez agenta oraz odnajdywania wystąpień SQL Server i baz danych.

Jeśli chcesz korzystać z tych funkcji, możesz podać poświadczenia serwera, wykonując poniższe kroki. Urządzenie podejmie próbę automatycznego mapowania poświadczeń do serwerów w celu wykonania funkcji odnajdywania.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Dodawanie poświadczeń dla serwerów z systemem w środowisku VMware

### <a name="types-of-server-credentials-supported"></a>Typy obsługiwanych poświadczeń serwera

Można dodać wiele poświadczeń serwera w Menedżerze konfiguracji urządzenia, które mogą być poświadczeniami domeny, domenami nienależącymi do domeny (Windows lub Linux) lub SQL Server uwierzytelniania.

Typy obsługiwanych poświadczeń serwera są wymienione w poniższej tabeli:

Typ poświadczeń | Opis
--- | ---
**Poświadczenia domeny** | Możesz dodać **poświadczenia domeny** , wybierając opcję z listy rozwijanej w trybie modalnym **Dodaj poświadczenia** . <br/><br/> Aby podać poświadczenia domeny, należy określić **nazwę domeny** , która musi być podana w formacie FQDN (na przykład prod.Corp.contoso.com). <br/><br/> Należy również określić przyjazną nazwę dla poświadczeń, nazwy użytkownika i hasła. <br/><br/> Dodane poświadczenia domeny zostaną automatycznie zweryfikowane pod kątem autentyczności względem Active Directory domeny. Jest to zapobiegać wszelkim blokadom konta, gdy urządzenie próbuje zamapować poświadczenia domeny na odnalezione serwery. <br/><br/> Urządzenie nie będzie podejmować próby mapowania poświadczeń domeny, których weryfikacja nie powiodła się. Musisz mieć co najmniej jeden pomyślnie zweryfikowane poświadczenie domeny lub co najmniej jedno poświadczenie niebędące domeną do przeprowadzenia spisu oprogramowania.<br/><br/>Poświadczenia domeny mapowane automatycznie na serwery z systemem Windows będą używane do wykonywania spisu oprogramowania i mogą być również używane do odnajdywania SQL Server wystąpień i baz danych _(Jeśli na serwerach SQL został skonfigurowany tryb uwierzytelniania systemu Windows)_.<br/> [Dowiedz się więcej](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) o typach uwierzytelniania obsługiwanych przez serwery SQL.
**Poświadczenia nienależące do domeny (Windows/Linux)** | Możesz dodać **system Windows (niebędący domeną)** lub **Linux (niebędący domeną)** , wybierając opcję wymagane z listy rozwijanej w obszarze **Dodawanie poświadczeń** modalnych. <br/><br/> Należy określić przyjazną nazwę dla poświadczeń, nazwy użytkownika i hasła.
**Poświadczenia uwierzytelniania SQL Server** | Możesz dodać poświadczenia **uwierzytelniania SQL Server** , wybierając opcję z listy rozwijanej w trybie modalnym **Dodaj poświadczenia** . <br/><br/> Należy określić przyjazną nazwę dla poświadczeń, nazwy użytkownika i hasła. <br/><br/> Możesz dodać ten typ poświadczeń, aby odnaleźć SQL Server wystąpienia i bazy danych działające w środowisku programu VMware, jeśli na serwerach SQL został skonfigurowany tryb uwierzytelniania SQL Server.<br/> [Dowiedz się więcej](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) o typach uwierzytelniania obsługiwanych przez serwery SQL.<br/><br/> Musisz podać co najmniej jeden pomyślnie zweryfikowane poświadczenie domeny lub co najmniej jedno poświadczenie systemu Windows (niebędące domeną domeny), aby urządzenie mogły ukończyć Spis oprogramowania w celu odnalezienia programu SQL Server zainstalowanego na serwerach przed użyciem poświadczeń uwierzytelniania SQL Server w celu odnalezienia SQL Server wystąpień i baz danych.

Sprawdź uprawnienia wymagane w poświadczeniach systemu Windows/Linux, aby wykonać Spis oprogramowania, analizę zależności bez agenta i odnajdź SQL Server wystąpienia i bazy danych programu.

### <a name="required-permissions"></a>Wymagane uprawnienia

W poniższej tabeli wymieniono uprawnienia wymagane na poświadczeniach serwera, które są dostępne na urządzeniu w celu wykonywania odpowiednich funkcji:

Cecha | poświadczeń systemu Windows | Poświadczenia systemu Linux
---| ---| ---
**Zapasy oprogramowania** | Konto użytkownika-gościa | Regularne/normalne konto użytkownika (uprawnienia dostępu inne niż sudo)
**Odnajdywanie wystąpień SQL Server i baz danych** | Konto użytkownika, które jest członkiem roli serwera sysadmin. | _Obecnie nieobsługiwane_
**Analiza zależności bez agenta** | Domena lub konto nienależące do domeny (lokalne) z uprawnieniami administracyjnymi | Konto użytkownika root lub <br/> konto z tymi uprawnieniami dla plików/bin/netstat i/bin/ls: CAP_DAC_READ_SEARCH i CAP_SYS_PTRACE.<br/><br/> Te funkcje należy ustawić przy użyciu następujących poleceń: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Zalecane praktyki dostarczania poświadczeń

- Zaleca się utworzenie dedykowanego konta użytkownika domeny z [wymaganymi uprawnieniami](add-server-credentials.md#required-permissions), które ma zakres do wykonywania spisu oprogramowania, analizy zależności bez agenta oraz odnajdywania wystąpień SQL Server i baz danych na odpowiednich serwerach.
- Zaleca się dostarczenie co najmniej jednego pomyślnie zweryfikowanego poświadczenia domeny lub co najmniej jedno poświadczenie niebędące domeną w celu zainicjowania spisu oprogramowania.
- Aby odnaleźć SQL Server wystąpienia i bazy danych, można podać poświadczenia domeny, jeśli na serwerach SQL został skonfigurowany tryb uwierzytelniania systemu Windows.
- Możesz również podać poświadczenia uwierzytelniania SQL Server, jeśli skonfigurowano tryb uwierzytelniania SQL Server na serwerach SQL, ale zaleca się dostarczenie co najmniej jednego pomyślnie zweryfikowanego poświadczenia domeny lub co najmniej jednego poświadczenia systemu Windows (niebędącego domeną domeny), aby urządzenie mogło najpierw zakończyć Spis oprogramowania.

## <a name="credentials-handling-on-appliance"></a>Obsługa poświadczeń na urządzeniu

- Wszystkie poświadczenia podane w Menedżerze konfiguracji urządzenia są przechowywane lokalnie na serwerze urządzeń i nie są wysyłane do platformy Azure.
- Poświadczenia przechowywane na serwerze urządzeń są szyfrowane przy użyciu interfejsu API ochrony danych (DPAPI).
- Po dodaniu poświadczeń urządzenie podejmie próbę automatycznego mapowania poświadczeń w celu przeprowadzenia odnajdywania na odpowiednich serwerach.
- Urządzenie korzysta z poświadczeń automatycznie mapowanych na serwer dla wszystkich kolejnych cykli odnajdywania, aż poświadczenia będą mogły pobrać wymagane dane odnajdywania. Jeśli poświadczenia przestaną działać, urządzenie ponownie podejmie próbę mapowania z listy dodanych poświadczeń i kontynuuje odnajdywanie na serwerze.
- Dodane poświadczenia domeny zostaną automatycznie zweryfikowane pod kątem autentyczności względem Active Directory domeny. Jest to zapobiegać wszelkim blokadom konta, gdy urządzenie próbuje zamapować poświadczenia domeny na odnalezione serwery. Urządzenie nie będzie podejmować próby mapowania poświadczeń domeny, których weryfikacja nie powiodła się.
- Jeśli urządzenie nie może zmapować żadnej domeny lub poświadczeń nienależących do domeny dla serwera, dla serwera w projekcie zostanie wyświetlony stan "poświadczenia niedostępne".

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkami dotyczącymi [odnajdywania serwerów z systemem w środowisku programu VMware](tutorial-discover-vmware.md)