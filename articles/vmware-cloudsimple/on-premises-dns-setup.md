---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Konfigurowanie usługi DNS dla chmury prywatnej CloudSimple
description: Opisuje sposób konfigurowania rozpoznawania nazw DNS na potrzeby dostępu do programu vCenter Server w chmurze prywatnej CloudSimple z lokalnych stacji roboczych
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63822050512421895b0cfed08fb141f77da20b03
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899256"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Konfigurowanie systemu DNS do rozpoznawania nazw dla prywatnego dostępu vCenter w chmurze z lokalnych stacji roboczych

Aby uzyskać dostęp do serwera vCenter w chmurze prywatnej CloudSimple z lokalnych stacji roboczych, należy skonfigurować rozpoznawanie adresów DNS, aby serwer vCenter mógł zostać rozkierowany przy użyciu nazwy hosta i adresu IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Uzyskaj adres IP serwera DNS dla chmury prywatnej

1. Zaloguj się do [portalu CloudSimple](access-cloudsimple-portal.md).

2. Przejdź do **zasobów**  >  **chmury prywatne** i wybierz chmurę prywatną, z którą chcesz nawiązać połączenie.

3. Na stronie **Podsumowanie** w chmurze prywatnej w obszarze **podstawowe informacje** Skopiuj adres IP serwera DNS w chmurze prywatnej.

    ![Serwery DNS w chmurze prywatnej](media/private-cloud-dns-server.png)


Użyj dowolnej z tych opcji dla konfiguracji DNS.

* [Utwórz strefę na serwerze DNS dla *. cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Utwórz usługę przesyłania dalej warunkowego na lokalnym serwerze DNS, aby rozwiązać ten problem *. cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Utwórz strefę na serwerze DNS dla *. cloudsimple.io

Można skonfigurować strefę jako strefę zastępczą i wskazać serwery DNS w chmurze prywatnej do rozpoznawania nazw. Ta sekcja zawiera informacje dotyczące korzystania z serwera DNS BIND lub serwera DNS systemu Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Tworzenie strefy na serwerze DNS BIND

Określony plik i parametry do skonfigurowania mogą się różnić w zależności od konfiguracji poszczególnych ustawień DNS.

Na przykład dla domyślnej konfiguracji serwera BIND Edytuj plik/etc/named.conf na serwerze DNS i Dodaj następujące informacje o strefie.

> [!NOTE]
>Ten artykuł zawiera odwołania do warunku podrzędnego, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Tworzenie strefy na serwerze DNS systemu Microsoft Windows

1. Kliknij prawym przyciskiem myszy serwer DNS i wybierz pozycję **Nowa strefa**. 
  
    ![Zrzut ekranu, który podświetla opcję menu Nowa strefa.](media/DNS01.png)
2. Wybierz pozycję **strefa zastępcza** i kliknij przycisk **dalej**.

    ![Zrzut ekranu, który podświetla opcję strefy zastępczej.](media/DNS02.png)
3. Wybierz odpowiednią opcję w zależności od środowiska, a następnie kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawiający opcje replikacji danych strefy.](media/DNS03.png)
4. Wybierz pozycję **Strefa wyszukiwania do przodu** , a następnie kliknij przycisk **dalej**.

    ![Zrzut ekranu, który podświetla opcję strefy wyszukiwania do przodu.](media/DNS01.png)
5. Wprowadź nazwę strefy, a następnie kliknij przycisk **dalej**.

    ![Zrzut ekranu pokazujący, gdzie wprowadzić nazwę strefy.](media/DNS05.png)
6. Wprowadź adresy IP serwerów DNS dla chmury prywatnej uzyskanej z portalu CloudSimple.

    ![Nowa strefa](media/DNS06.png)
7. W razie potrzeby kliknij przycisk **dalej** , aby zakończyć pracę Instalatora kreatora.

## <a name="create-a-conditional-forwarder"></a>Tworzenie usługi przesyłania dalej warunkowego

Usługa przesyłania dalej warunkowego przekazuje wszystkie żądania rozpoznawania nazw DNS do wywskazanego serwera. W przypadku tej konfiguracji każde żądanie do *. cloudsimple.io jest przekazywane do serwerów DNS znajdujących się w chmurze prywatnej. W poniższych przykładach pokazano, jak skonfigurować usługi przesyłania dalej na różnych typach serwerów DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Tworzenie usługi przesyłania dalej warunkowej na serwerze DNS BIND

Określony plik i parametry do skonfigurowania mogą się różnić w zależności od konfiguracji poszczególnych ustawień DNS.

Na przykład dla domyślnej konfiguracji serwera BIND Edytuj plik/etc/named.conf na serwerze DNS i Dodaj następujące informacje warunkowego przekazywania.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Tworzenie usługi przesyłania dalej warunkowej na serwerze DNS systemu Microsoft Windows

1. Otwórz Menedżera DNS na serwerze DNS.
2. Kliknij prawym przyciskiem myszy pozycję **usługi przesyłania dalej warunkowe** i wybierz opcję dodania nowej usługi przesyłania dalej warunkowego.

    ![Warunkowy serwer usługi przesyłania dalej 1 systemu Windows](media/DNS08.png)
3. Wprowadź domenę DNS i adres IP serwerów DNS w chmurze prywatnej, a następnie kliknij przycisk **OK**.
